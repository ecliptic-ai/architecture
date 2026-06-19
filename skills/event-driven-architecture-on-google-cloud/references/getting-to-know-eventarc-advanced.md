---
description: Learn how Eventarc Advanced restores governance to microservices and AI agents-based eventing, while preserving developer agility.
title: Getting to know Eventarc Advanced | Google Cloud Blog
image: https://storage.googleapis.com/gweb-cloudblog-publish/images/0_zjIbf2O.max-2500x2500.jpg
---

# Centralized policy meets distributed logic: Getting to know Eventarc Advanced

February 27, 2026

##### Milen Kovachev

Staff Software Engineer

Enterprise architects often face a fundamental dilemma: choosing between developer agility and organizational control. Development teams need to move fast and deploy independent microservices without waiting for permission. Security and compliance teams need to be safe, and ensure that data flow is observable and governed by policies.

That’s why we built [**Eventarc Advanced**](https://docs.cloud.google.com/eventarc/advanced/docs/overview)**,** a serverless eventing platform and the evolution of [**Eventarc Standard**](https://docs.cloud.google.com/eventarc/standard/docs/overview). Eventarc Advanced providesan improved architectural pattern for the modern cloud, where **centralized policy meets distributed logic**. By clearly separating the governance layer (the "bus") from the processing layer (the "pipeline"), Eventarc Advanced gives SecOps teams the visibility and control they demand, while freeing developers to choreograph AI agents and build event-driven applications with the autonomy they want. [Eventarc Advanced became generally available](https://cloud.google.com/blog/products/application-modernization/eventarc-advanced-orchestrates-complex-microservices-environments?e=48754805) in August 2025\. 

In this blog, we take a deeper look at the evolution of integration architectures — from service buses, to microservices, to where we are today — and go into depth with a real-world example. Let’s jump in. 

### **The evolution of integration architectures**

To understand the value of this new pattern, it helps to look at where we came from and why previous architecture patterns forced a compromise.

**The centralized bottleneck of the** **Enterprise Service Bus**

One early integration architecture approach was the **Enterprise Service Bus (ESB)**, which prioritized centralized control. The ESB emerged to solve the "spaghetti architecture" of point-to-point integrations by providing a centralized communication layer that standardized how disparate systems interact. However, it often introduced serious pitfalls.

The primary issue was what’s referred to as a centralized logic trap. Organizations frequently embedded complex business logic — transformations and orchestration — directly into the governance layer. The resulting middleware layer was opaque, with critical business rules hidden from the developers who owned the services.

Consequently, integration changes typically required the intervention of a central middleware team. Development teams lost autonomy, forced to queue behind integration specialists to ship even minor features, often waiting weeks for updates.

**Microservices’ governance gap**

To address this, the industry shifted toward **microservices** (often described as "smart endpoints and dumb pipes"), distributing logic to give teams the autonomy they were looking for. For synchronous traffic (REST, gRPC), tools like API gateways and service meshes restored a layer of governance by enforcing policies like authentication and rate limiting at the infrastructure level.

However, as architectures shifted to **Event-Driven Architecture (EDA)** for greater resilience and decoupling, a new gap emerged. In a distributed, asynchronous world, centralized control often vanished. This created a **governance gap** where SecOps teams struggled to maintain order. Three issues emerged to the forefront:

* **The visibility void**: Without a central policy, shadow IT services could silently subscribe to sensitive events without detection.
* **The policy problem**: Enforcing data residency or PII masking is nearly impossible when the broker treats every message as an opaque blob.
* **The dependency risk**: Without clear contracts, changing an event schema risks silently breaking unknown downstream consumers.

### A new pattern: Centralized policy, distributed logic

Eventarc Advanced addresses the trade-off between control and speed with a novel architectural pattern: **centralized policy meets distributed logic**.

Eventarc Advanced maps these distinct responsibilities to two specific architectural resources that each correspond to a distinct role:

* **The** **bus:** This governance layer is a managed, centralized hub where platform administrators enforce global constraints before events are routed. It synthesizes the centralized routing of the legacy ESB with the modern security architecture of a service mesh. It handles Identity and Access Management (IAM), including content-based access control, to strictly define who can publish, and integrates with [VPC Service Controls](https://docs.cloud.google.com/vpc-service-controls/docs/overview) to prevent data exfiltration.
* **The pipeline:** Think of this distributed, team-owned resource as developers’ integration logic layer. This is where eventing patterns for AI agents and microservices are unlocked, allowing developers to configure event flow and delivery according to their specific business logic. Unlike many service meshes that treat data as opaque bits, the pipeline understands content. Developers can transform events, convert payloads between formats (like JSON to Avro), and configure retry policies and authentication independently.

In other words, by decoupling these duties, Eventarc Advanced provides the control of an ESB with the agility of microservices and the resilience of modern event-driven architectures.

### **How it works: A retail event mesh example**

A typical Eventarc Advanced solution can be implemented with minimal configuration, providing a streamlined experience for both administrative governance and distributed integration logic. To see this model in practice, let's look at a real-world implementation of **a** retail event mesh**.**

Imagine an ecosystem at a global retailer with four autonomous teams in charge of the following services:

* **Commerce**
* **Finance**
* **Logistics**
* **Intelligence (AI Insights Agent)**

In a traditional setup, aligning these teams is difficult. The Intelligence team wants access to everything for their models, Finance wants to lock everything down for compliance, Logistics just needs a stable schema to ship boxes, and Commerce needs to roll out new features at a moment’s notice.

**The foundation: Built on CloudEvents**

Eventarc Advanced uses a data model based on the open [CloudEvents standard](https://cloudevents.io/), which can carry any type of payload. This helps ensure governance and discoverability while retaining flexibility. In our example, before a single event is published, the platform administrator mandates that every message must contain standard attributes and a specific custom extension for governance. 

In this example, every event on the bus must carry the following attributes:

* `type`: Standard identifiers for the event instance (e.g., `com.retail.order.created`)
* `source`: A standard attribute identifying the producer (e.g., `//commerce/frontend`)
* `data_sensitivity`: A custom extension attribute to categorize risk

In addition, the organization defines three data sensitivity levels:

* `restricted` **(High)**: Severe risk data like Credit Card Tokens or Tax IDs
* `confidential` **(Medium)**: PII like home addresses
* `general` **(Low)**: Safe operational data like Order IDs

This standardized metadata layer allows the bus to enforce policies based on specific attribute names — checking who sent the data (`source`) and what kind of data it is (`data_sensitivity`).

**The workflow**

With this model, the lifecycle of a single order becomes a secure flow where sensitivity changes at every step.

1. **Order placement**: The **Commerce** service publishes `order.created` to the Bus. The event’s data sensitivity is tagged as `general`. The **AI Insights Agent** service subscribes to analyze market trends.
2. **Payment authorization**: The **Commerce** service publishes `payment.authorized` tagged as `restricted` (containing a secure token). The **Finance** service subscribes to capture the token and executes the charge.
3. **Settlement**: The **Finance** service publishes `payment.success` tagged as `general`, signaling the transaction is safe to fulfill without exposing financial secrets. **Logistics** subscribes to ship the box, and **Intelligence AI Insights Agent** is triggered to evaluate market trends for the next supply chain cycle.
4. **Fulfillment**: The **Logistics** service publishes `shipment.ready` tagged as `confidential `(containing the customer phone number). The **Logistics** own notification pipeline subscribes to it to trigger an SMS notification.

In a legacy architecture, mixing PCI, PII, and operational data on a single bus would be a compliance nightmare. With Eventarc Advanced, it’s a solved problem.

### **The bus: the governance layer**

The platform administrator implements a **secure strategy** on the bus. Rather than blindly trusting internal services, they enforce global policies that inspect these CloudEvents attributes using **fine-grained access control (FGAC)**.

**Enforcing source integrity**

To ensure a compromised service cannot spoof events, the bus administrator enforces the producer's identity to match the source attribute.

For example, a bus policy can state that only the principal `sa-commerce@retail.com` can publish events that match the expression `message.source.startsWith("//commerce/")`. If the Intelligence AI Insights Agent service tries to publish an event claiming to be from `//commerce/payments`, the bus rejects the request.

**Enforcing a data classification**

To ensure every event is categorized, the bus administrator requires that every payload received by the bus includes a valid sensitivity attribute. A bus policy can check that `message.data_sensitivity` is one of `['general', 'confidential', 'restricted']`. This guarantees that the event mesh contains only classified, governance-ready data.

### **The Pipeline: the logic layer - autonomous team innovation**

With the security posture established on the bus, development teams can then use **pipelines** to solve complex integration challenges entirely within their own domains. Let’s take a look at a few of these challenges.

**Schema-aware formats conversion and payload transformation**

The Logistics team decides to upgrade their warehouse robots to use high-efficiency **protocol buffers**. Instead of forcing the Finance team to change their JSON output (which would break other consumers), Logistics configures a **transformation** step in their own pipeline.

A typical `com.retail.payment.success` event from Finance arrives as JSON:

```json
{ "id": "89d5663e-789e-4d9f-a65f-f7d83742d987", "source": "//finance/ledger", "type": "com.retail.payment.success", "data_sensitivity": "general", "datacontenttype": "application/json", "data": { "order_number": "ORD-2023-8841", "total_amount": 249.99, "currency": "USD", "transaction_id": "tx_77382910", "status": "SETTLED" } }
```

The warehouse robots service expects a binary Protobuf message:

```protobuf
message PaymentConfirmed { string order_id = 1; double insured_value = 2; string currency_code = 3; string ledger_reference = 4; }
```

The Logistics team configures their pipeline to accept `json` as input and output to `protobuf`. To map the data, they use **Common Expression Language (CEL)** to configure a **transformation**:

```
// CEL Transformation to Construct the target Protobuf message { "order_id": message.data.order_number, // 110% of total to cover replacement cost "insured_value": message.data.total_amount * 1.1, // Standardize currency to uppercase "currency_code": message.data.currency.upperAscii(), "ledger_reference": message.data.transaction_id, }
```

This transformation not only maps the input but also applies business logic — calculating the insured value and normalizing the currency code. The Logistics team implements this modernization without a single meeting with the Finance team.

**Agentic workflows: Filtering and triggering AI agents** 

Eventarc Advanced enables agentic workflows by allowing pipelines to communicate directly with AI agents using open standard protocols like [Agent2Agent (A2A)](https://github.com/a2aproject/A2A) and [Model Context Protocol (MCP)](https://modelcontextprotocol.io/), while also offering rich capabilities like filtering to optimize when those agents are invoked.

The Intelligence team uses a pipeline they name `ai-insights` and the **A2A protocol** to connect with an **AI Insights Agent** that proactively analyzes market trends based on placed orders. Because the agent’s processing is resource-intensive, the team uses a filter to only invoke the agent for high-value orders that warrant deeper analysis.

The pipeline filter is configured with the following expression:

```
message.type == "order.created" && double(message.amount) > 5000.0
```

When the filter is passed, the pipeline uses a **HTTP Message Destination Binding (MDB)** expression to directly trigger the agent. By defining a CEL template, the pipeline handles the complexity of constructing a native A2A `SendMessage` request to the **AI strategic insights agent**. This allows the agent to receive a **conversational prompt** derived from technical event data without any manual "glue code":

```json
{ "headers": headers.merge({ "Content-Type": "application/json", "A2A-Version": "1.0" }), "body": { "jsonrpc": "2.0", "id": message.id, "method": "message/send", "params": { "message": { "messageId": message.id, "role": "ROLE_USER", "parts": [ { "text": "Analyze Order " + message.data.order_number + " for market trends." } ] } } } }
```

A similar prompt message can be crafted for other popular agentic communication protocols like MCP.

This combination of filtering and agentic protocol translation ensures that AI resources are used precisely where they add value. The Intelligence team implements this independently – without writing ingestion code or coordinating with the Commerce or Admin team. The agent can then publish its own strategic recommendation back to the bus, enabling a choreography of AI experts that turns standard cloud events into competitive intelligence.

**Advanced API request modeling**

When a shipment is ready, the Logistics team uses a pipeline to send an SMS using a legacy gateway API. Integrating with legacy third-party APIs often requires writing "glue code" services just to format requests.

The Logistics team eliminates this maintenance burden by configuring a dedicated pipeline to fully construct the exact request expected by the legacy service. 

They use a **HTTP Message Destination Binding** CEL expression, which standardizes the phone number and maps it to the `X-SMS-To` HTTP header required by the API. It also construct the SMS text:

```json
{ "headers": { "X-SMS-To", message.data.phone.matches('^\\+1') ? message.data.phone : '+1' + message.data.phone }, "body": { "sms_text": "Order " + message.data.order_id + " shipped!" } }
```

Finally, they configure a robust retry policy (linear backoff, max five attempts) directly on the pipeline, so that temporary network interruptions don't result in lost notifications. In addition to HTTP endpoints, the pipeline supports guaranteed delivery and out-of-the-box authentication for destinations like Cloud Run, Pub/Sub, Bus, Workflows, and over 200 Google services.

### **The future of agile integration**

Eventarc Advanced closes an important gap in event-driven architectures: It brings the same level of maturity to asynchronous communication by introducing the pattern of **centralized policy, distributed logic**.

* **For the Platform team**, Eventarc Advanced provides assurance that a bus can strictly enforce integrity and confidentiality on every message, bringing "service-mesh-like" security to the event layer.
* **For the developer**, it restores autonomy. The pipeline allows teams to filter, transform, convert, and route events to fit their specific needs, enabling them to treat events as first-class products rather than opaque artifacts.

This architecture lays the foundation for the next generation of intelligent applications. A secure, typed, and trustworthy event mesh can serve as the backbone for generative AI agents and real-time analytics, allowing you to safely expose business context to the systems that need it most.

### **Get started**

Don't let governance slow down your innovation. Here are some Eventarc Advanced resources to get you on your way:

* **Learn more:** Dive into the full capabilities of the Bus and Pipeline in the [Eventarc Advanced documentation](https://cloud.google.com/eventarc/docs).
* **Get hands-on:** Deploy the "Retail Event Mesh" scenario yourself and explore enterprise patterns with our [Quickstarts and Tutorials](https://cloud.google.com/eventarc/docs/quickstarts).
* **Start building:** Go to the [Google Cloud console](https://console.cloud.google.com/eventarc) to configure your first bus and pipeline today.
* **Let's talk:** Have a complex enterprise use case? [Contact Google Cloud Sales](https://cloud.google.com/contact) to discuss how Eventarc Advanced fits into your broader integration strategy.
