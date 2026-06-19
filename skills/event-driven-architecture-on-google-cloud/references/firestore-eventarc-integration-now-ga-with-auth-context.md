---
description: The integration of Eventarc into Firestore includes a new Auth Context extension that embeds metadata about who triggered a document change.
title: Firestore-Eventarc integration now GA with Auth Context | Google Cloud Blog
image: https://storage.googleapis.com/gweb-cloudblog-publish/images/10_-_Databases.max-2600x2600.jpg
---

# Firestore integration with Eventarc reaches GA with Auth Context

May 13, 2024

##### Josué Urbina

Software Engineer, Firestore

##### Hansi Mou

Software Engineer, Firestore

Creating event-driven architectures using [Eventarc](https://cloud.google.com/blog/topics/developers-practitioners/eventarc-unified-eventing-experience-google-cloud) together with [Firestore](https://firebase.google.com/docs/firestore) is an increasingly popular pattern. Recently, the [Firestore integration with Eventarc](https://cloud.google.com/datastore/docs/eventarc) became generally available, adding new functionality. You can now register multiple Cloud Functions in different regions against a multi-regional Firestore database for increased reliability, and there are new event types, including the [Auth Context extension for CloudEvents](https://github.com/cloudevents/spec/blob/main/cloudevents/extensions/authcontext.md).

Determining who or what — the user, a service account, the system, or a third-party — is making a modification to a Firestore document as a change event has long been a top-requested feature. With the new Firestore event types with Auth Context extension, events now embed metadata about the principal that triggered a document change in the open and portable [CloudEvents](https://github.com/googleapis/google-cloudevents) format.

### **Example walkthrough**

Let’s say that you want to have different logic to process events in the destinations for different auth contexts (i.e. **unauthenticated** or **system**). To set up your trigger, navigate to the [Eventarc section of the Google Cloud console](https://cloud.google.com/functions/docs/calling/eventarc#deployment). You’ll need to create a new [trigger](https://cloud.google.com/datastore/docs/eventarc) for Firestore using the associated event types that include authentication information. These event types end with the suffix \*.withAuthContext. We’ll want to capture newly written entities, so we’ll select **google.cloud.firestore.document.v1.written.withAuthContext** events:

You can specify additional filters, which ensures only desirable events from a specified database and collection are delivered. In this case, we filter for events from the **(default)** database, and for documents of the collection **Ops**.

On the same screen, you’ll also need to specify a destination. Triggering events can be delivered to any number of supported Eventarc destinations, like [Cloud Run](https://cloud.google.com/eventarc/docs/run/route-trigger-cloud-firestore), [Cloud Functions (2nd gen)](https://cloud.google.com/functions/docs/calling/cloud-firestore), and [Google Kubernetes Engine](https://cloud.google.com/eventarc/docs/gke/route-trigger-cloud-firestore). Let’s say we have a Cloud Run service named **demo** that exposes an HTTP endpoint to receive the events. You can configure your trigger as follows:

That’s it! When any write operation is applied to your **(default)** database with the collection **Ops**, a CloudEvent with the Auth Context is delivered to the configured Cloud Run service **demo** almost immediately. You can inspect the `authtype` attribute as defined in [Auth Context extension](https://github.com/cloudevents/spec/blob/main/cloudevents/extensions/authcontext.md) to identify **unauthenticated** and **system** types as shown in <https://cloud.google.com/firestore/docs/extend-with-functions-2nd-gen#event%5Fattributes> .

## **Next steps**

For more information on how to set up and configure Firestore triggers, check out our [documentation](https://cloud.google.com/datastore/docs/eventarc).

---

Thanks to both Minh Nguyen, Senior Product Manager Lead for Firestore and Juan Lara, Senior Technical Writer for Firestore, for their contributions to this blog post.
