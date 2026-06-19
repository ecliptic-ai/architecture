---
description: The new Firebase Studio is a cloud-based, agentic development environment with everything developers need to create production-quality, AI-powered apps.
title: Firebase Studio lets you build full-stack AI apps with Gemini | Google Cloud Blog
image: https://storage.googleapis.com/gweb-cloudblog-publish/images/Firebase_CloudNext_Hero_02.max-2500x2500.jpg
---

# Introducing Firebase Studio and agentic developer tools to build with Gemini

April 9, 2025

##### Jeanine Banks

VP/GM, Developer X

Millions of developers use Firebase to engage their users, powering over 70 billion instances of apps every day, everywhere — from mobile devices and web browsers, to embedded platforms and agentic experiences. But full-stack development is evolving quickly, and the rise of generative AI has transformed not only how apps are built, but also what types of apps are possible. This drives greater complexity, and puts developers under immense pressure to keep up with many new technologies that they need to manually stitch together. Meanwhile, businesses of all sizes are seeking ways to make AI app development cycles more efficient, deliver quality software, and get to market faster.

Today at Google Cloud Next, we're introducing a suite of new capabilities that transforms **Firebase** into an end-to-end platform to accelerate the complete application lifecycle. The new [Firebase Studio](https://firebase.google.com/studio), available to everyone in preview, is a cloud-based, agentic development environment powered by Gemini that includes everything developers need to create and publish production-quality AI apps quickly, all in one place. Several more updates across the [Firebase platform](https://firebase.google.com/) are helping developers unleash their modern, data-driven apps on [Google Cloud](https://cloud.google.com/). These announcements will empower developers to forge new paths for building AI applications across multiple platforms.

### **Meet Firebase Studio**

Over the past year, we launched many new services, including Gemini in Firebase, Genkit, and Project IDX (a fork of [Code OSS](https://github.com/microsoft/vscode)), to make building AI apps faster and easier. We’re taking a significant step forward with the launch of **Firebase Studio**, which fuses all of these capabilities together with Firebase services and the creative power of Gemini into a new, natively agentic experience.

Firebase Studio helps you build full-stack AI apps

For new apps, choose from over 60 pre-built templates or get started with the **App Prototyping agent**. It assists you with designing your app — such as the UI, API schema, and AI flows — all using natural language, images, drawing tools, and screenshots. Keep prompting to iterate on your prototype, and when ready, deploy it directly to Firebase App Hosting. Share a URL to a fully functional version of your prototype to get feedback or run experiments. Monitor the usage and behavior at a glance or jump into the Firebase Console for more detailed monitoring. At any time, you can open your app inside a Firebase Studio coding workspacewith a single click and no additional setup. There, you can refine the architecture and expand features to prepare for production deployment.

**Coding workspaces** also enable you to:

- **Simplify coding workflows:** Write code and test features, all with assistance from [Gemini in Firebase](https://firebase.google.com/docs/gemini-in-firebase) at every step of the way. Complete a variety of tasks like debugging, testing, refactoring, explaining, and documenting code with ease.
- **Enhance existing apps:** Import existing codebases from your local machine or git-based repositories, including GitHub, GitLab, and Bitbucket. Create custom templates for your preferred tech stacks to share across your team.
- **Create full-stack experiences:** Customize and evolve all aspects of your apps, from AI model inference, agents, and retrieval augmented generation (RAG), to the user experience, business logic, database, and more. Easily expose and integrate tools such as APIs and microservices to your AI apps.
- **Work with familiar tools:** Bring along your specific configurations, such as system tools, extensions, and environment variables to tailor your workspaces. Access thousands of extensions from the [Open VSX Registry](https://open-vsx.org/).
- **Flexible deployment options:** Set up your app to run on the cloud with built-in integrations to Firebase backend services and Google Cloud Run. You can also deploy on your own custom infrastructure.

Firebase Studio is currently available with three workspaces at no cost during preview. Members of the [Google Developer Program](https://developers.google.com/program) get up to 30 workspaces. Check out [Firebase Studio](http://studio.firebase.google.com) today.

#### Try Google Cloud for free

Start building with $300 in free credits for new customers. All customers get free usage of 20+ products, up to monthly limits.

[Get started for free ](https://console.cloud.google.com/freetrial?redirectPath=/welcome)

### **Engage AI agents throughout your entire workflow**

We’re also providing early access to [Gemini Code Assist agents](https://codeassist.google/products/business) from within Firebase Studio. For example, you might invoke the **Migration agent** in Firebase Studio to help you migrate your code between versions of Java; the **AI Testing agent** to run adversarial tests against AI models to uncover and fix potentially harmful outputs; and the **Code Documentation agent** to chat with a wiki-style knowledge base about your code to ease the onboarding of new team members.

Join the Gemini Code Assist agents wait list today via the [Google Developer Program](https://developers.google.com/profile/badges/community/sdlcagents/gca-agents).

AI Testing agent, accessible via Firebase Studio

[Firebase App Distribution](https://firebase.google.com/products/app-distribution) is a unified mobile app testing service for running manual and automated tests. The new **App Testing agent** in Firebase App Distribution can simulate real-world user interactions with your app. For example, you can write a test that sets a goal to “Find a trip to Greece.” The App Testing Agent will use Gemini to formulate a plan to achieve that goal, and run it on virtual or physical devices, navigating your UI and producing detailed pass/fail results with intuitive rationales and visuals of the paths that the agent chose. The App Testing agent is available now in preview and you can try it out on your Android app today, with more platforms coming this year.

The new App Testing agent in Firebase App Distribution

### **Create new AI app experiences**

A lack of best practices and standards create challenges when integrating cutting-edge AI features into your applications. That’s why we’re continuing to invest in robust frameworks, SDKs, and tooling to help streamline the development process, freeing you to focus on crafting truly engaging and innovative user interactions.

**Expanded language support for Genkit**  
[**Genkit**](http://genkit.dev) helps reduce the complexity of building, testing, and monitoring your apps’ AI features. Develop powerful agentic experiences with support for structured output, tool calling, human-in-the-loop interactions, retrieval augmented generation (RAG), [Model Context Protocol](https://modelcontextprotocol.io/introduction) (MCP), and multi-model orchestration. Today, we’re making it easier to do that in your preferred language, by introducing early support for Python and expanded support for Go. Access Gemini models, Imagen 3, and additional models such as Llama and Mistral through Vertex Model Garden, plus self-hosted models with Ollama, and a growing ecosystem of third-party models using community plugins.

Try [this template](https://studio.firebase.google.com/new/genkit) in Firebase Studio to build with Genkit.

Genkit helps you build AI-powered features

**New models through Vertex AI in Firebase**  
[**Vertex AI in Firebase**](https://firebase.google.com/products/vertex-ai-in-firebase) lets developers integrate generative AI into their applications by providing a streamlined, secure SDK. It’s used by thousands of apps today, such as [Meal Planner](https://android-developers.googleblog.com/2024/10/gemini-api-showcase-of-innovative-android-apps.html#:~:text=The%20team%20behind-,Meal%20Planner,-%2C%20a%20meal%20planner), a meal planning and shopping list management app; [Life](https://play.google.com/store/apps/details?id=com.hitbytes.minidiarynotes), an AI-powered diary assistant; [HiiKER](https://play.google.com/store/apps/details?id=com.waymarkedtrails.hiiker), an offline hiking maps provider; and [Waveful](https://play.google.com/store/apps/details?id=app.waveful&hl=en%5FUS), a social media app for creators. In March, we added support for [Imagen 3 models](https://firebase.blog/posts/2025/03/imagen3-support-on-vertex-ai-sdks) (Imagen 3 and Imagen 3 Fast), in addition to the Gemini family of models, which lets you add image generation directly to your Android, iOS, Flutter, and Web applications. Today, we are adding support for the Live API for Gemini models, enabling more conversational interactions in apps, like allowing customers to ask audio questions and get responses.

The Live API for Gemini models used via Vertex AI in Firebase

### **Accelerate modern, data-driven apps**

We’re also providing you with greater control of your app architecture and deployment processes with Firebase Data Connect and Firebase App Hosting, now generally available.

**Build sophisticated apps with Firebase Data Connect**  
[**Firebase Data Connect**](https://firebase.google.com/products/data-connect)offers the robust reliability of Google Cloud SQL for PostgreSQL with instant GraphQL APIs and type-safe SDKs. Build a wide range of experiences, like social media apps with complex user relationships, e-commerce platforms with large product catalogs, or personalized recommendations with built-in vector search.

Data Connect now helps you to:

- **Easily generate schemas and queries:** Use Gemini in Firebase to automatically generate your Data Connect schemas, queries, mutations, and client SDKs, significantly speeding up backend development.
- **Leverage expanded query capabilities:** Data Connect now has expanded query power with native aggregation support for deeper data insights, atomic data modifications, and transactions with server value expressions, helping to ensure data integrity across complex operations.
- **Build with web frameworks:** Enjoy tight integration and streamlined data handling with generated type-safe hooks and components for web frameworks, enabling rapid development of dynamic, data-driven applications.

Firebase Data Connect in Firebase Studio

**Deploy with Firebase App Hosting**
[Firebase App Hosting](https://firebase.google.com/products/app-hosting) is an opinionated, git-centric hosting solution for modern, full-stack web apps. App Hosting accelerates time-to-market by managing your app’s entire stack, from the build, to the CDN, to server-side rendering. You push to GitHub, and App Hosting figures out the rest. App Hosting is built on enterprise-grade Google Cloud services: Cloud Build, Cloud Run, Cloud CDN, and more.

With this release of App Hosting, you can:

- **Easily test and troubleshoot builds**: App Hosting now has a local emulator, and improved error messages to help you get ahead of and troubleshoot build failures.
- **Recover from production incidents in seconds:** Use App Hosting’s **new monitoring** dashboard to understand your app’s performance and health, and instantly roll back to a previous version if you spot a regression.
- **Connect to a Virtual Private Cloud (VPC):** Give your app access to backend services in your Google Cloud project that are not accessible on a public IP address (e.g., caching content with Cloud Memorystore, or accessing data from non-Firebase databases).

New monitoring dashboard in Firebase App Hosting

Get ready to reimagine not just how you build your apps, but also what kinds of apps you can build! Learn more about these products and more on the [Firebase blog](https://firebase.blog/). We can’t wait to see what you create with the Firebase platform.
