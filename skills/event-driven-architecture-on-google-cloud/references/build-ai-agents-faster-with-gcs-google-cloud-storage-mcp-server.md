---
description: In this blog, we will share three examples of agents built by customers using GCS, and then share how you can securely and reliably connect your agents to GCS using Model Context Protocol (MCP).
title: Build AI agents faster with GCS (Google Cloud Storage) MCP server | Google Cloud Blog
image: https://storage.googleapis.com/gweb-cloudblog-publish/images/Hero-image.max-2500x2500.png
---

# Connecting AI agents with unstructured data using Google Cloud Storage MCP Servers

June 2, 2026

##### Himanshu Kohli

Product Manager, Google Cloud

##### Manjul Sahay

Product Manager, Google Cloud

Google Cloud Storage (GCS) is a foundational component of the modern agentic tech stack and the preferred home for unstructured data at scale. As enterprises deploy agents in production, the critical focus has shifted to turning data into context and building secure, standardized integrations to access context. This is the core of smart storage: making unstructured data inherently agent-ready by turning passive objects into rich context for reasoning. Whether it’s automating complex financial workflows or diagnosing system failures in seconds, AI success now depends on how seamlessly agents can leverage this intelligence to make smart, high-stakes decisions.

In this blog, we will share **three** examples of agents built by customers using GCS, and then share how you can securely and reliably connect your agents to GCS using [Model Context Protocol](https://modelcontextprotocol.io/docs/getting-started/intro) (MCP). Combined with smart storage features like auto annotations and object contexts, GCS MCP server makes the whole agent deployment process easy and simple.

### Real-world agent success on Google Cloud Storage

We are seeing incredible innovation from customers leveraging MCP and Google’s agentic tech stack to solve complex business problems:

- **Palo Alto Networks** built the Strata Co-Pilot agent, a screen-aware AI assistant that guides network security administrators through complex configuration flows—either by highlighting steps or executing them directly. The agent is powered by the Gemini Live API, with GCS serving as its “historical memory” connected via the GCS MCP server.
- **Airwallex** developed an AI Assistant that understands user context, answers questions, and executes workflows on their behalf. For example, it can smartly analyze expense policy documents and generate detailed approval workflows - a task that would normally take hours to do manually. GCS and GCS metadata are used by the agent to store documents and the extracted information, respectively.

- **Snap's** Job Optimization Agent analyzes Flink and Spark job specs, metadata, and historical metrics stored on GCS across thousands of jobs to find optimization opportunities, generate cost estimates, and tune configurations. Using this agent, Snap is already seeing investigation time reduced from 30 minutes to 30 seconds!

In all these three agents, the GCS MCP server handles data operations as well as enforces standard RBAC and access policies.

### Connecting agents to GCS using MCP

MCP has rapidly emerged as the universal standard for connecting agents to data sources, but building custom servers from scratch is often a slow, distracting process that diverts focus from innovation. This path introduces significant development overhead and risk, as it forces you to manage everything from authentication and error handling to keeping pace with GCS’s evolving capabilities. To solve this, GCS offers two powerful MCP server options — Remote and Local — allowing you to offload the foundational plumbing and focus on creating value.

**1\. Remote MCP server: Fully-managed**
Connecting your agents to the [Cloud Storage MCP server](https://docs.cloud.google.com/storage/docs/use-cloud-storage-mcp) requires zero infrastructure deployment. By simply pointing your agent configuration to the managed endpoint, you gain immediate access to your unstructured data on GCS, allowing you to scale your agentic workloads effortlessly without the burden of operational overhead.

Because the Cloud Storage MCP server follows the open MCP standard, it works seamlessly with major agentic frameworks like ADK and is compatible with MCP clients. You can easily connect clients like Google Antigravity and Anthropic’s Claude by adding a Custom Connector in the settings. Simply point it to your Cloud Storage MCP endpoint, and you are ready to start building — no complex configuration files required.

Connecting an agent to storage requires robust security and governance. GCS MCP server is built on Google Cloud's standard identity, observability, and security frameworks:

- **Identity-first security**: Authentication is handled entirely through Identity and Access Management (IAM) rather than shared keys. This ensures agents can only access data (buckets and objects) explicitly authorized by the user.
- **Full observability**: To track agent activity, every request and action taken via these MCP servers is logged in Cloud Audit Logs. This provides security teams with a record of every interaction, maintaining visibility alongside ease of access.
- **MCP security - content scanning**: You can optionally configure the MCP endpoint with Google’s content security service, Google Cloud Model Armor. This allows you to implement security controls against common MCP attack vectors—such as direct and indirect prompt injection attacks, MCP Tool poisoning attacks, and malicious URL/SQL injections—as well as prevent the leakage of sensitive data.

Cloud Storage MCP servers are perfect for most production use cases; however, as with all remote servers, you lose the capability to fully customize your MCP tools.

**2\. Local MCP Server: Self-managed for controlled customization**
While the Remote server handles standard data access, Local MCP is the right choice when you need to build **custom tools** specific to your business logic. For example, if your agent needs to perform specialized data transformations—such as redacting PII or adding context from another internal system—whenever it reads a file from GCS, a Local MCP server allows you to define those unique capabilities

The GCS Local MCP server is an open-source [GitHub repository](https://github.com/googleapis/gcloud-mcp/tree/main/packages/storage-mcp) of Google-maintained tools that provides you with a reliable bridge to your data. Here are a few tips to keep in mind while designing custom tools:

- Provide precise, clear descriptions to minimize incorrect invocations by the models
- Implement model-friendly error handling for models to understand their mistakes and self-correct

The GCS Local MCP is now also a part of the [MCP Toolbox for Databases](https://docs.cloud.google.com/storage/docs/pre-built-tools-with-mcp-toolbox), a single open-source repository containing connectors for major data services such as GCS, BigQuery, AlloyDB, Spanner, and Cloud SQL, making it easier to monitor and manage your data ecosystem. The Toolbox offers simplified development with reduced boilerplate code, enhanced security through OAuth2 and OIDC, and end-to-end observability with OpenTelemetry integration.

### Get started

Whether you are optimizing an existing process like Snap or automating workflow creations like Airwallex, your unstructured data is one of your agent's greatest assets.

- Explore the generally available [**GCS Remote MCP Server**](https://docs.cloud.google.com/storage/docs/use-cloud-storage-mcp).
- Check out our GCS Local MCP[**GitHub repository**](https://github.com/googleapis/gcloud-mcp/tree/main/packages/storage-mcp) to start building custom tools today, or use it as part of [**MCP Toolbox for Databases**](https://docs.cloud.google.com/storage/docs/pre-built-tools-with-mcp-toolbox).
- [**Reach out**](mailto:storage-ai@google.com) to us to discuss your Agent use case with GCS data.
