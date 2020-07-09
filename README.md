
# Stackdriver Sandbox (Alpha)

Stackdriver Sandbox is an open-source tool that helps practitioners to learn Service Reliability Engineering practices from Google and apply them on their cloud services using Stackdriver. It is based on [Hipster Shop](https://github.com/GoogleCloudPlatform/microservices-demo), a cloud-native microservices application.

Sandbox offers:

* **Demo Service** - an application built using microservices architecture on modern, cloud-native stack.
* **One-click deployment**  - a script handles the work of deploying the service to Google Cloud Platform.
* **Load Generator** - a component that produces synthetic traffic on a demo service.
* (Soon) **SRE Runbook** - pre-built routine procedures for operating the deployed sample service that follow best SRE practices using Stackdriver.

## Why Sandbox

Google Stackdriver is a suite of tools that helps you gain full observability of your code and applications. You might want to take Stackdriver to a "test drive" in order to answer the question, "will it work for my application needs"? The most effective way to learn is by testing the tool in "real-life" conditions, but without risking a production system. With Sandbox, we provide a tool that automatically provisions a new demo cluster, which receives traffic, simulating real users. Practicioners can experiment with various Stackdriver tools to solve problems and accomplish standard SRE tasks in a sandboxed environment.

## Getting Started

* Using Sandbox
  * [Prerequisites](#Prerequisites)
  * [Set Up](#Set-Up)
  * [Next Steps](#Next-Steps)
  * [Clean Up](#Clean-Up)
* [Service Overview](#Service-Overview)
  * [Screenshots](#Screenshots)
  * [Architecture](#Architecture)
* Contribute code to Sandbox
  * [Running locally](#Running-locally)
  * [Running on GKE](#Running-on-GKE)
  * [Using static images](#Using-static-images)
  * [GKE with Istio](#GKE-with-Istio)

## Using Sandbox

### Prerequisites

* Create and enable [Cloud Billing Account](https://cloud.google.com/billing/docs/how-to/manage-billing-account).

### Set Up

1. Click the Cloud Shell button for automated one-click installation of a new Stackdriver Sandbox cluster in a new Google Cloud Project.

[![Open in Cloud Shell](http://www.gstatic.com/cloudssh/images/open-btn.svg)](https://console.cloud.google.com/cloudshell/editor?cloudshell_git_repo=https://github.com/GoogleCloudPlatform/stackdriver-sandbox.git&cloudshell_git_branch=master&cloudshell_working_dir=terraform&cloudshell_image=gcr.io/cloudshell-images/cloudshell)

1. In the Cloud Shell command prompt, type:

```bash
./install.sh
```

### Next Steps

* Explore your Sandbox deployment and its [architecture](#Service-Overview)
* Follow the [User Guide](/docs/README.md) to start using Stackdriver
* Learn more about Stackdriver using [Code Labs](https://codelabs.developers.google.com/gcp-next/?cat=Monitoring)

### Clean Up

When you are done using Stackdriver Sandbox, you can tear down the environment by deleting the GCP project that was set up for you. This can be accomplished in any of the following ways:

* Use the Stackdriver Sandbox `destroy` script:

```bash
./destroy.sh
```

* If you no longer have the Stackdriver Sandbox files downloaded, delete your project manually using `gcloud`

```bash
gcloud projects delete $YOUR_PROJECT_ID
```

* Delete your project through Google Cloud Console's [Resource Manager web interface](https://console.cloud.google.com/cloud-resource-manager)

## Service Overview

This project contains a 10-tier microservices application. It is a
web-based e-commerce app called **“Hipster Shop”**, where users can browse items,
add them to the cart, and purchase them.

### Screenshots

| Home Page | Checkout Screen |
|-----------|-----------------|
| [![Screenshot of store homepage](./docs/img/hipster-shop-frontend-1.png)](./docs/img/hipster-shop-frontend-1.png) | [![Screenshot of checkout screen](./docs/img/hipster-shop-frontend-2.png)](./docs/img/hipster-shop-frontend-2.png) |

### Service Architecture

**Hipster Shop** is composed of many microservices, written in different languages, that talk to each other over gRPC.
>**We are not endorsing the architecture of Hipster Shop as the best way to build such a shop!**
> The architecture is optimized for learning purposes and includes modern stack: Kubernetes, GKE, Istio,
> Stackdriver, gRPC, OpenCensus, and similar cloud-native technologies.

[![Architecture of
microservices](./docs/img/architecture-diagram.png)](./docs/img/architecture-diagram.png)

Find the **Protocol Buffers Descriptions** in the [`./pb` directory](./pb).

| Service | Language | Description |
|---------|----------|-------------|
| [frontend](./src/frontend) | Go | Exposes an HTTP server to serve the website. Does not require signup/login, and generates session IDs for all users automatically. |
| [cartservice](./src/cartservice) |  C# | Manages the items in the user's shipping cart by using Redis. |
| [productcatalogservice](./src/productcatalogservice) | Go | Provides the list of products from a JSON file and the ability to search and retrieve products. |
| [currencyservice](./src/currencyservice) | Node.js | Converts one currency to another, using real values fetched from  the European Central Bank. It's the highest QPS service. |
| [paymentservice](./src/paymentservice) | Node.js | Charges the given credit card info (hypothetically😇) with the given amount and returns a transaction ID. |
| [shippingservice](./src/shippingservice) | Go | Gives shipping-cost estimates based on the shopping cart. Ships items to the given address (hypothetically😇). |
| [emailservice](./src/emailservice) | Python | Sends users an order-confirmation email (hypothetically😇). |
| [checkoutservice](./src/checkoutservice) | Go | Retrieves a user's cart, prepares the order, and orchestrates payment, shipping, and email notification. |
| [recommendationservice](./src/recommendationservice) | Python | Recommends other products based on what's in the user's cart. |
| [adservice](./src/adservice) | Java | Provides text ads based on given context words. |
| [loadgenerator](./src/loadgenerator) | Python/Locust | Continuously sends requests that imitate realistic shopping flows to the frontend. |

### Technologies

* **[Kubernetes](https://kubernetes.io)/[GKE](https://cloud.google.com/kubernetes-engine/):**
  The app is designed to run on Google Kubernetes Engine.
* **[gRPC](https://grpc.io):** Microservices use a high volume of gRPC calls to
  communicate to each other.
* **[OpenCensus](https://opencensus.io/) Tracing:** Most services are
  instrumented using OpenCensus trace interceptors for gRPC/HTTP.
* **[Stackdriver APM](https://cloud.google.com/stackdriver/):** Many services
  are instrumented with **Profiling**, **Tracing** and **Debugging**.
  **Metrics** and **Context Graph** out of the box.
* **[Skaffold](https://github.com/GoogleContainerTools/skaffold):** A tool used for doing repeatable deployments. You can deploy to Kubernetes with a single command using Skaffold.
* **Synthetic Load Generation:** The application demo comes with dedicated load generation service that creates realistic usage patterns on Hipster Shop website using
  [Locust](https://locust.io/) load generator.

## For Developers
If you are a developer and want to contribute to the Sandbox, you can refer to [CONTIBUTING.md](CONTRIBUTING.md).

---

This is not an official Google project.
