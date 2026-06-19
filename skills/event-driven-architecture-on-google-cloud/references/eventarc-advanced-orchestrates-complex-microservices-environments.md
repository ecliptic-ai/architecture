---
description: Eventarc Advanced is a simple, unified messaging platform that can intelligently filter, transform, and centrally govern the flow of information to build event-driven architectures.
title: Eventarc Advanced orchestrates complex microservices environments | Google Cloud Blog
image: https://storage.googleapis.com/gweb-cloudblog-publish/images/04_-_Application_Modernization_APTQ0a0.max-2600x2600.jpg
---

# Simplify complex eventing at Scale with Eventarc Advanced

August 29, 2025

##### Vidya Nagarajan Raman

Director of Product Management

##### Raj Duraisamy

Product Manager

Modern application development requires organizations to invest not only in scale but also in simplification and central governance. This means more than message routing; it requires a simple, unified messaging platform that can intelligently filter, transform, and govern the flow of information in real-time, taming complexity all in one place.

Today, we are excited to announce the general availability of [Eventarc Advanced](https://cloud.google.com/eventarc/advanced/docs), a unified, serverless eventing platform that goes beyond simple routing by combining real-time filtering, transformation, management, and delivery in one place — for a complex, multi-source event-driven architecture.

### **Evolving Eventarc to handle complexity**

Eventarc Advanced is an evolution of [Eventarc Standard](https://cloud.google.com/eventarc/docs) and offers out-of-the-box integration patterns to simplify your eventing needs.

With Eventarc Advanced, organizations can

* **Integrate existing services using Publish API** and leverage Google Cloud events to build sophisticated event-driven applications.
* **Centrally manage, secure, and observe** the flow of messages across services with support for per-message fine-grained access control.
* **Intelligently route** messages to appropriate destinations based on flexible message criteria.
* **Transform and convert** events in real-time, with support for multiple payload formats and built-in capability to transform event attributes.
* **Publish to Google Cloud services** using HTTP binding.

With Eventarc Advanced, you can build sophisticated eventing systems. In contrast, Eventarc Standard is best for simple one-to-one eventing needs involving Google Cloud events ([comparison](https://cloud.google.com/eventarc/docs#features-comparison-table)).

Eventarc Advanced’s key technical features include:

* **Publish API** to ingest custom and third-party messages using CloudEvents format ([details](https://cloud.google.com/eventarc/advanced/docs/publish-events/publish-events-direct-format)).
* **Message bus** that acts as the central nervous system of your event-driven architecture, providing centralized [observability](https://cloud.google.com/eventarc/advanced/docs/monitor), security and management. Message bus is based on **Envoy** and uses the policy engine of Cloud Load Balancers and Cloud Service Mesh.
   * Your existing systems can publish messages to a central message bus that can be intelligently routed to appropriate consumers based on flexible criteria. The message bus simplifies event management and reduces operational overhead.
   * You can gain insights into your message flows with centralized monitoring, logging, and tracing capabilities. Logs are captured in Cloud Logging, providing detailed information about event processing and errors.
* **Out-of-the-box event mediation capabilities** to adapt messages on the fly without modifying your source or destination services, and to handle different events through support for multiple payload formats (Avro, JSON, Protobuf) and built-in capability to transform event attributes.
   * Eventarc Advanced incorporates error-handling by offering reliable event delivery and graceful recovery from transient failures.

### **Empowering developers and operators**

We designed Eventarc Advanced to cater to the needs of both **developers** and **operators**:

* **“Simplicity” for developers:** Focus on building your core application features, not on complex event routing logic. Eventarc Advanced provides a unified API and a consistent experience, letting you build decoupled, reliable, and scalable services including real-time transformations.
* **“Centralized governance” for platform operators:** Simplify the setup and management of your eventing infrastructure. Centralized governance across projects / teams, plus monitoring and logging make it easier to identify and resolve issues, reducing operational overhead.

### **How Eventarc Advanced works**

Imagine an order processing system where orders are created, payments are processed, and items are shipped. Each action is an "event," and in a complex system, managing this flow can be challenging. This is where Eventarc Advanced comes in. It provides a centralized way to manage, observe, and route all your application's events. Let's explore how it works.

**Set up your message bus** 
At the heart of Eventarc Advanced is a message bus that acts as the central nervous system for your event-driven application. Every event, regardless of its origin, is sent to the message bus to be analyzed and routed. This central hub is where you can define security policies, controlling exactly who can send events and what kind are allowed.

In our example, you would create a message bus to receive all order-related events. Whether an order is newly created, its payment is confirmed, or its status changes to "shipped," the events land here.

**Connect your event sources** 
Next, connect your sources that generate order events. Event sources are the services and applications that generate events and feed them into your message bus. Eventarc Advanced makes this easy, supporting a wide range of sources, including:

* Google API events
* External apps or custom systems via Publish API

In our example, the event source could be a custom service using the Publish API. Every time a new order is saved or an existing one is updated, it automatically sends an event to your message bus.

**Configure pipelines and destinations** 
This is another area where Eventarc Advanced shines. With events flowing into your message bus, you can configure pipelines to intelligently route them to the correct destinations, allowing you to filter, transform, and direct events with precision.

In the above example,

* **New order notification:** You can set up a filter that looks for events with status: "new". This pipeline routes these events to a notification service that sends an order confirmation email to the customer.
* **Fraud detection:** For high-value orders (e.g., amount > $1000), you can apply a transformation and route it to a specialized fraud detection service for analysis.

### **Unlocking new possibilities**

Eventarc Advanced opens up new possibilities for your applications and workflows:

* **Large-scale application integration:** Connect numerous services and agents, enabling them to communicate asynchronously and reliably, even across different event formats and schemas.
* **Event streaming for AI and analytics:** Handle the influx of data from IoT devices and AI workloads by filtering and transforming them before feeding them into your analytics pipelines.
* **Hybrid and multi-cloud deployments:** Extend your event-driven architectures beyond Google Cloud, integrating with on-premises systems and other cloud providers.

### **What's next**

As today’s applications become increasingly agentic, distributed and data-driven, the need for efficient and secure event orchestration is more critical than ever. With upcoming native support for [Service Extensions](https://cloud.google.com/service-extensions/docs/overview) to insert custom code into the data path and services like Model Armor, Eventarc Advanced’s message bus provides security and networking controls for agent communications. 

Eventarc Advanced is available today. To learn more about Eventarc Advanced, see the [documentation](https://cloud.google.com/eventarc/advanced/docs/overview). To learn more about event-driven architectures, visit our [Architecture Center](https://cloud.google.com/architecture) based on Google Cloud best practices. Get ready to take your event-driven architectures to the next level!
