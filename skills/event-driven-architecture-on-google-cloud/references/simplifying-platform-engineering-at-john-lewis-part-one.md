---
description: Learn how John Lewis transformed its e-commerce platform with Google Cloud, GKE, and a simplified developer platform engineering approach.
title: Simplifying platform engineering at John Lewis - part one | Google Cloud Blog
image: https://storage.googleapis.com/gweb-cloudblog-publish/images/03_-_Application_Development_SWHuGHU.max-2600x2600.jpg
---

# Using Platform Engineering to simplify the developer experience - part one

June 26, 2025

##### Darren Evans

EMEA Practice Solutions Lead, Application Platform

##### Alex Moss

Principal Platform Engineer, John Lewis Partnership

**Editor's note:** This is part one of the story. After you’re finished reading, head over to [part two](https://cloud.google.com/blog/products/application-development/simplifying-platform-engineering-at-john-lewis-part-two). 

---

In 2017, John Lewis, a major UK retailer with a £2.5bn annual online turnover, was hampered by its monolithic e-commerce platform. This outdated approach led to significant cross-team dependencies, cumbersome and infrequent releases (monthly at best), and excessive manual testing, all further hindered by complex on-premises infrastructure. What was needed were some bold decisions to drive a quick and significant transformation.

The John Lewis engineers knew there was a better way. Working with Google Cloud, they modernized their e-commerce operations with [Google Kubernetes Engine](https://cloud.google.com/kubernetes-engine). They started with the frontend, and started to see results fast: the frontend was moved onto Google Cloud in mere months, releases to the frontend browser journey started to happen weekly, and the business gladly backed expansion into other areas.

At the same time, the team had a broader strategy in mind: to take [a platform engineering approach](https://cloud.google.com/solutions/platform-engineering), creating many product teams who built their own microservices to replace the functionality of the legacy commerce engine, as well as creating brand new experiences for customers. 

And so The John Lewis Digital Platform was born. The vision was to empower development teams and arm them with the tools and processes they needed to go to market fast, with full ownership of their own business services. The team’s motto? "You Build It. You Run It. You Own It." This decentralization of development and operational responsibilities would also enable the team to scale. 

This article features insights from Principal Platform Engineer Alex Moss, who delves into their strategy, platform build, and key learnings of John Lewis’ journey to modernize and streamline its operations with platform engineering — so you can begin to think about how you might apply platform engineering to your own organization.

#### Try Google Cloud for free

Start building with $300 in free credits for new customers. All customers get free usage of 20+ products, up to monthly limits.

[Get started for free ](https://console.cloud.google.com/freetrial?redirectPath=/welcome)

### **Step 1: From monolithic to multi-tenant**

In order to make this happen, John Lewis needed to adopt a multi-tenant architecture — one tenant for each business service, allowing each owning team to work independently without risk to others -- and thereby permitting the Platform team to give the team a greater degree of freedom.

Knowing that the business' primary objective was to greatly increase the number of product teams helped inform our initial design thinking, positioning ourselves to enable many independent teams even though we only had a handful of tenants. 

This foundational design has served us very well and is largely unchanged now, seven years later. Central to the multi-tenant concept is what we chose to term a "Service" — a logical business application, usually composed of several microservices plus components for storing data.

We largely position our platform as a “bring your own container” experience, but encourage teams to make use of other Google Cloud services — particularly for handling state. Adopting services like Firestore and Pub/Sub reduces the complexity that our platform team has to work with, particularly for areas like resilience and disaster recovery. We also favor Kubernetes over compute products like Cloud Run because it strikes the right balance for us between enabling development teams to have freedom whilst allowing our platform to drive certain certain behaviours, e.g., the right level of guardrails, without introducing too much friction.

On our platform, Product Teams (i.e., tenants) have a large amount of control over their own Namespaces and Projects. This allows them to prototype, build, and ultimately operate, their workloads without dependency on others — a crucial element of enabling scale. 

Our early-adopter teams were extremely helpful in helping evolve the platform; they were accepting of the lack of features and willing to develop their own solutions, and provided very rich feedback on whether we were building something that met their needs.

The first tenant to adopt the platform was rebuilding the [johnlewis.com](http://johnlewis.com), search capability, replacing a commercial-off-the-shelf solution. This team was staffed with experienced engineers familiar with modern software development and the advantages of a microservice-based architecture. They quickly identified the need for supporting services for their application to store data and asynchronously communicate between their components. They worked with the Platform Team to identify options, and were onboard with our desire to lean into Google Cloud native services to avoid running our own databases or messaging. This led to us adopting Cloud Datastore and Pub/Sub for our first features that extended beyond Google Kubernetes Engine.

### **All roads lead to success**

A risk with a platform that allows very high team autonomy is that it can turn into a bit of a wild-west of technology choices and implementation patterns. To handle this, but to do so in a way that remained developer-centric, we adopted the concept of a **paved road,**  analogous to a “golden path.” 

We found that the paved road approach made it easier to:

* build useful platform features to help developers do things rapidly and safely
* share approaches and techniques, and engineers to move between teams
* demonstrate to the wider organisation that teams are following required practices (which we do by building assurance capabilities, **not** by gating release)

The concept of the paved road permeates most of what the platform builds, and has inspired other areas of the John Lewis Partnership beyond the John Lewis Digital space.

Our paved road is powered by two key features to enable simplification for teams:

1. **The Paved Road Pipeline**. This operates on the whole Service and drives capabilities such as Google Cloud resource provisioning and observability tools.
2. **The Microservice CRD**. As the name implies, this is an abstraction at the microservice level. The majority of the benefit here is in making it easier for teams to work with Kubernetes.

Whilst both features were created with the developer experience in mind, we discovered that they also hold a number of benefits for the platform team too.

The Paved Road Pipeline is driven by a configuration file — in yaml (of course!) — which we call the Service Definition. This allows **the team that owns the tenancy** to describe, through easy-to-reason-about configuration, what they would like the platform to provide for them. Supporting documentation and examples help them understand what can be achieved. Pushes to this file then drive a CI/CD pipeline for a number of platform-owned jobs, which we refer to as provisioners. These provisioners are microservices-like themselves in that they are independently releasable and generally focus on performing one task well. Here are some examples of our provisioners and what they can do:

* Create Google Cloud resources in a tenant’s Project. For example, [Buckets](https://cloud.google.com/storage/docs/creating-buckets), [PubSub](https://cloud.google.com/pubsub/docs/overview), and [Firestore](https://firebase.google.com/docs/firestore) — amongst many others
* Configure platform-provided dashboards and custom dashboards based on golden-signal and self-instrumented metrics
* Tune alert configurations for a given microservice’s SLOs, and the incident response behaviour for those alerts

Our product teams are therefore freed from the need to familiarize themselves deeply with how Google Cloud resource provisioning works, or Infrastructure-as-Code (IaC) tooling for that matter. Our preferred technologies and good practices can be curated by our experts, and developers can focus on building differentiating software for the business, while remaining fully in control of what is provisioned and when.

Earlier, we mentioned that this approach has the added benefit of being something that the platform team can rely upon to build their own features. The configuration updated by teams for their Service can be combined with metadata about their team and surfaced via an API and events published to Pub/Sub. This can then drive updates to other features like incident response and security tooling, pre-provision documentation repositories, and more. This is an example of how something that was originally intended as a means to help teams avoid writing their own IaC can also be used to make it easier for us to build platform features, further improving the value-add — without the developer even needing to be aware of it!

We think this approach is also more scalable than providing pre-built Terraform modules for teams to use. That approach still burdens teams with being familiar with Terraform, and versioning and dependency complexities can create maintenance headaches for platform engineers. Instead, we provide an easy-to-reason-about API and **deliberately burden the platform team,** ensuring that the Service provides all the functionality our tenants require. This abstraction also means we can make significant refactoring choices if we need to.

Adopting this approach also results in a broad consistency in technologies across our platform. For example, why would a team implement Kafka when the platform makes creating resources in Pub/Sub so easy? When you consider that this spans not just the runtime components that assemble into a working business service, but also all the ancillary needs for operating that software — resilience engineering, monitoring & alerting, incident response, security tooling, service management, and so on— this has a massive amplifying effect on our engineers’ productivity. All of these areas have full paved road capabilities on the John Lewis Digital Platform, reducing the cognitive load for teams in recognizing the need for, identifying appropriate options, and then implementing technology or processes to use them.

That being said, one of the reasons we particularly like the paved road concept is because it doesn't preclude teams choosing to "go off-road." A paved road shouldn’t be mandatory, but it should be compelling to use, so that engineers aren’t tempted to do something else. Preventing use of other approaches risks stifling innovation and the temptation to think the features you've built are "good enough." The paved road challenges our Platform Engineers to keep improving their product so that it continues to meet our Developers' changing needs. Likewise, development teams tempted to go off-road are put off by the increasing burden of replicating powerful platform features. 

The needs of our Engineers don’t remain fixed, and Google Cloud are of course releasing new capabilities all the time, so we have extended the analogy to include a “dusty path” representing brand new platform features that aren’t as feature-rich as we’d like (perhaps they lack self-service provisioning or out-the-box observability). Teams are trusted to try different options and make use of Google Cloud products that we haven't yet paved. The Paved Road Pipeline allows for this experimentation - what we term "snowflaking". We then have an unofficial "rule of three", whereby if we notice at least 3 teams requesting the same feature, we move to make the use of it self-service.

At the other end of the scale, teams can go completely solo — which we refer to as “crazy paving” — and might be needed to support wild experimentation or to accommodate a workload which cannot comply with the platform’s expectations for safe operation. Solutions in this space are generally not long-lived.

In this article, we've covered how John Lewis revolutionized its e-commerce operations by adopting a multi-tenant, "paved road" approach to platform engineering. We explored how this strategy empowered development teams and streamlined their ability to provision Google Cloud resources and deploy operational and security features.

In [part 2](https://cloud.google.com/blog/products/application-development/simplifying-platform-engineering-at-john-lewis-part-two?e=48754805) of this series, we'll dive deeper into how John Lewis further simplified the developer experience by introducing the Microservice CRD. You'll discover how this custom Kubernetes abstraction significantly reduced the complexity of working with Kubernetes at the component level, leading to faster development cycles and enhanced operational efficiency.

To learn more about shifting down with platform engineering on Google Cloud, you can find more information available [here](https://cloud.google.com/solutions/platform-engineering). To learn more about how Google Kubernetes Engine (GKE) empowers developers to effortlessly deploy, scale, and manage containerized applications with its fully managed, robust, and intelligent Kubernetes service, you can find more information [here](https://cloud.google.com/kubernetes-engine).
