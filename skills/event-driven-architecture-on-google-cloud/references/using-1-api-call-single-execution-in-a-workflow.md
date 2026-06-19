---
description: Learn how to invoke single execution of API calls with workflows and cloud functions, eliminating errors and saving you time.
title: Using single execution calls with workflows | Google Cloud Blog
image: https://storage.googleapis.com/gweb-cloudblog-publish/images/03_-_Application_Development_SWHuGHU.max-2600x2600.jpg
---

# Making API calls exactly once when using Workflows

May 3, 2024

##### Randy Spruyt

Workflows Team Lead

##### Mete Atamel

Cloud Developer Advocate

## **Introduction**

One challenge with any distributed system, including [Workflows](https://cloud.google.com/workflows), is ensuring that requests sent from one service to another are processed exactly once, when needed; for example, when placing a customer order in a shipping queue, withdrawing funds from a bank account, or processing a payment.

In this blog post, we’ll provide an example of a website invoking Workflows, and Workflows in turn invoking a [Cloud Function](https://cloud.google.com/functions). We’ll show how to make sure both Workflows and the Cloud Function logic only runs once. We’ll also talk about how to invoke Workflows exactly once when using [HTTP callbacks](https://cloud.google.com/blog/topics/developers-practitioners/introducing-workflows-callbacks), [Pub/Sub](https://cloud.google.com/pubsub) messages, or [Cloud Tasks](https://cloud.google.com/tasks).

## **Invoke Workflows exactly once**

Imagine you have an online store and you’re using Workflows to create new orders, save to Firestore, and process payments by calling a Cloud Function:

A new customer order comes in, the website makes an API call to Workflows but receives an error. Two possible scenarios are:

 (1) The request is lost and the workflow is never invoked:

(2) The workflow is invoked and executes successfully, however the response is lost:

How can you make sure the workflow executes once?

To solve this, the website retries the same request. One easy solution is to check if a document already exists in Firestore:

```yaml
main: params: [] steps: - init: assign: - project_id: ${sys.get_env("GOOGLE_CLOUD_PROJECT_ID")} - order_id: "12345" # In practice we would pass in the order ID as a workflow parameter, e.g. ${params[0]} - firestore_collection: "orders" - URL: https://us-central1-<your_project_id>.cloudfunctions.net/processpayment - create_document: try: call: googleapis.firestore.v1.projects.databases.documents.createDocument args: collectionId: ${firestore_collection} parent: ${"projects/" + project_id + "/databases/(default)/documents"} query: documentId: ${order_id} except: as: e steps: - endEarly: return: ${e} # Exception is raised, e.g. ${e.code == 409} if doc already exists - processPayment: ...
```

The `processPayment `step will execute only if a document is successfully created. This is effectively a 1-bit state machine, idempotent, and a valid solution. The downside of this solution is that it’s not extensible. We might want to complete additional work in this handler before changing states, or expand the number of states within the system. Next, let’s continue with a more advanced solution for the same problem.

## **Invoke Cloud Functions from Workflows exactly once**

Let’s see what happens when the workflow uses a Cloud Function to process the payment. You might have the following step to call Cloud Functions:

```yaml
- processPayment: call: http.post args: url: https://us-central1-<your_project_id>.cloudfunctions.net/processpayment auth: type: OIDC
```

By default, Workflows offers **at-most-once** delivery (no retries) with HTTP requests. That’s usually OK because 99.9+% of the time, the call is successful, and a response is received.

In the rare case of failure, a [ConnectionError](https://cloud.google.com/workflows/docs/reference/syntax/error-types#error-tags) might be raised. As in the website-to-workflow situation discussed previously, the workflow can’t tell which scenario occurred. Similarly, you can add retries. 

Let’s add a [default retry policy](https://cloud.google.com/workflows/docs/reference/syntax/retrying#default-retry-policy) to handle this:

```yaml
- processPayment: try: call: http.post args: url: https://us-central1-<your_project_id>.cloudfunctions.net/processpayment auth: type: OIDC retry: ${http.default_retry} # Retries up to 5 times, includes 'ConnectionError'
```

Let's say the second delivery scenario occurs where the request is received by the Cloud Function but the response is lost. By adding retries, Workflows will likely invoke the Cloud Function multiple times. When this happens, how do you ensure that the code in the Cloud Function only runs once? 

You’ll need to add extra logic to the Cloud Function to check and update the payment state in Firestore:

Let’s also assume you want to track the workflow `EXECUTION_ID` in Firestore and use the following `order_state `enum to allow for additional flexibility in payment processing:

```
payment_not_processed // Initial state when an order is created payment_declined // Payment was not successful payment_successful // Payment processed successfully ...
```

You can expand on the previous workflow and call a Cloud Function to process the payment:

```yaml
main: params: [] steps: - init: assign: - project_id: ${sys.get_env("GOOGLE_CLOUD_PROJECT_ID")} - order_id: "12345" # In practice we would pass in the order ID as a workflow parameter, e.g. ${params[0]} - firestore_collection: "orders" - URL: https://us-central1-<your_project_id>.cloudfunctions.net/processpayment - create_document: try: call: googleapis.firestore.v1.projects.databases.documents.createDocument args: collectionId: ${firestore_collection} parent: ${"projects/" + project_id + "/databases/(default)/documents"} query: documentId: ${order_id} body: fields: order_state: # We set an initial state stringValue: "payment_not_processed" workflow_id: # And also track this workflow execution ID stringValue: ${sys.get_env("GOOGLE_CLOUD_WORKFLOW_EXECUTION_ID")} except: as: e steps: - endEarly: return: ${e} # Exception is raised, e.g. ${e.code == 409} if doc already exists - processPayment: try: call: http.post args: url: ${URL} # Might get called multiple times! auth: type: OIDC body: order_id: ${order_id} result: r retry: ${http.default_retry} - returnStep: return: ${r}
```

Here’s the Cloud Function (Node.js v20) that processes the payment:

```javascript
const functions = require('@google-cloud/functions-framework'); const firestore = require('@google-cloud/firestore'); functions.http('helloHttp', (req, res) => { const fs = new firestore.Firestore(); try{ // Reads the current state from Firestore and updates it within the same transaction to make this handler idempotent. Using a transaction is important. Note: It could be run multiple times but will only be committed once. return fs.runTransaction(t => { const docRef = fs.doc("orders/" + req.body.order_id); return t.get(docRef).then(doc => { console.log(doc, '=>', doc); var state = doc.data().order_state // Only process the order if we haven't already if (state == "payment_not_processed") { // Do payment stuff, e.g. debit account from another Firestore document // ... // state = "payment_successful" t.update(docRef, {order_state: state}) res.status(200).send(state); return } res.status(200).send("request ignored, state already: " + state); }); }).then(result => { console.log('Transaction result: ', result); }); } catch (e) { console.log('Transaction failure:', e); } });
```

`package.json`

```json
{ "dependencies": { "@google-cloud/functions-framework": "^3.3.0", "@google-cloud/firestore": "^7.6.0" } }
```

The key takeaway is that all payment processing work occurs within a **transaction**, making all actions idempotent. The code within the transaction might run multiple times due to Workflows retries, but it’s only committed once. 

## **What about HTTP callbacks, Pub/Sub, Cloud Tasks?**

So far, we’ve talked about how to make website-to-workflow and Workflows to Cloud Functions requests, exactly once. There are other ways of invoking or resuming Workflows such as [HTTP callbacks](https://cloud.google.com/blog/topics/developers-practitioners/introducing-workflows-callbacks), Pub/Sub messages or Cloud Tasks. How do you make those requests exactly once? Let’s take a look.

### **Callbacks**

The good news is that Workflows HTTP callbacks are fully idempotent by default. It’s safe to retry a callback if it fails. For example:

```yaml
- createCallbackStep: call: events.create_callback_endpoint args: http_callback_method: "POST" result: callback_details - sendOutURL: call: http.post args: url: "https://your-endpoint.com/foo" body: callback_to_use: ${callback_details.url} ... - callbackWaitStep: call: events.await_callback args: callback: ${callback_details}
```

Let’s assume that the first callback returns an error to the external caller. Based on the error, the caller might not know if the workflow callback was received, and should retry the callback. On the second callback, the caller will receive one of the following HTTP status codes:

* **429** indicates that the first callback was received successfully. The second callback is rejected by the workflow.
* **200** indicates that the second callback was received successfully. The first callback was either never received, or was received and processed successfully. If the latter, the second callback is not processed because `await_callback `is called only once. The second callback is discarded at the end of the workflow.
* **404** indicates that a callback is not available. Either the first callback was received and processed and the workflow has completed, or the workflow is not running (and has failed, for example). To confirm this, you’ll need to send an API request to query the workflow execution state.

For more details, see [Invoke a workflow exactly once using callbacks](https://cloud.google.com/workflows/docs/creating-callback-endpoints#invoke-once).

### **Pub/Sub messages** 

When using Pub/Sub to [trigger](https://cloud.google.com/workflows/docs/trigger-workflow-eventarc) a new workflow execution, Pub/Sub uses **at-least-once** delivery with Workflows, and will retry on any delivery failure. Pub/Sub messages are automatically deduplicated. You don’t need to worry about duplicate deliveries in that time window (24 hours).

### **Cloud Tasks**

Cloud Tasks is commonly used to [buffer workflow executions](https://cloud.google.com/workflows/docs/tutorials/buffer-workflows-executions) and provides **at-least-once** delivery but it **doesn’t** have message deduplication. Workflow handlers [should be idempotent](https://cloud.google.com/tasks/docs/dual-overview).

## **Conclusion**

Exactly-once request processing is a hard problem. In this blog post, we’ve outlined some scenarios where you might need exactly-once request processing when you’re using Workflows. We also provided some ideas on how you can implement it. The exact solution will depend on the actual use case and the services involved.
