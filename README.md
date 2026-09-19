# Vermeer Automotive

> A dealer management system for automotive businesses — built as a polyglot microservices architecture with AI integrations.

🌐 **Portfolio write-up:** [yassinc.nl](https://yassinc.nl/)

## Overview

Vermeer Automotive is a dealer management system designed to support the day-to-day workflow of an automotive garage — from a customer's service request through diagnosis, repair and invoicing.

The system was originally developed during **BPSET** as a group project by a team of five Software Engineering students. We designed the application around a polyglot microservices architecture, where individual services were responsible for their own business domain and data.

The project was later extended during my individual **BPSEE** project. For this follow-up, I researched how the existing AI functionality could be moved from external AI providers towards a **self-hosted open-source language model**.

> **My contribution:** During the group project I contributed to the development and integration of the dealer management platform and its AI functionality. In the individual follow-up project, I focused specifically on the AI-service and researched which open-source LLM could replace or complement external AI providers while remaining usable on limited server hardware.

## Architecture

```text
Frontend → API Gateway → Event Bus → Microservices → PostgreSQL (per service)
                                           ↕
                                      AI Service
                                           ↕
                              External / Self-hosted LLM
```

- **Frontend** — Next.js and React application containing the dashboard and workshop interfaces.
- **API Gateway** — central entry point for requests between the frontend and backend services.
- **Event Bus** — asynchronous communication between services using publish/subscribe messaging.
- **Microservices** — independently developed services responsible for individual business domains such as customers, appointments, vehicles, inventory and invoicing.
- **Database** — services manage their own PostgreSQL data instead of sharing one central database.
- **AI Service** — provides AI functionality to the rest of the platform and abstracts the underlying language model or AI provider.

The project deliberately used multiple programming languages and frameworks to explore communication between independently implemented services.

## How a service request runs

1. **Registered** — a service request is created and linked to the customer and vehicle.
2. **Checked in** — the appointment is confirmed and the status change is communicated to other services.
3. **Diagnosis** — the mechanic records findings and can use the AI functionality to assist with a first diagnosis or estimate.
4. **Repair** — required parts are reserved and inventory is updated.
5. **Completed** — each service stores the information belonging to its own domain.
6. **Invoicing** — labour, parts and other costs are combined into the final invoice.
7. **Checked out** — the service request is closed and the completed record remains available in the system.

---

## BPSEE — Self-hosted AI research

After the group project, I continued working with the Vermeer Automotive case during **BPSEE**.

The original system used an AI Service that could communicate with external AI providers. My research focused on whether this functionality could instead run using an **open-source language model hosted on Vermeer Automotive's own infrastructure**.

### Research question

The main challenge was finding a model that provided useful answers for an automotive customer-service context while still being small and fast enough to run on relatively limited hardware.

The test environment consisted of a Hetzner server with:

- **4 CPU cores**
- **8 GB RAM**
- **No dedicated GPU**

Because of these constraints, the research focused on language models of approximately **7 billion parameters or smaller**.

### Models evaluated

Four models were compared:

- **Llama 3.2 3B Instruct**
- **Mistral 7B Instruct v0.3**
- **Qwen 2.5 7B Instruct**
- **Phi-3 Mini**

The models were evaluated using a dataset of **50 automotive customer-service questions**, divided into three categories:

- 12 advice questions
- 18 diagnostic questions
- 20 FAQ questions

Each model was tested twice under the same conditions.

### Evaluation

The comparison focused primarily on:

- relevance of the generated answer;
- response latency;
- suitability for automotive customer-service questions;
- ability to run on CPU-only infrastructure;
- practical suitability for integration into the existing AI Service.

The three strongest models in terms of relevance achieved scores of approximately **0.87–0.89**:

| Model | Relevance | Average latency |
|---|---:|---:|
| Llama 3.2 3B Instruct | ~0.87–0.89 | **4.0 s** |
| Mistral 7B Instruct v0.3 | ~0.87–0.89 | faster than comparable 7B alternatives |
| Qwen 2.5 7B Instruct | ~0.87–0.89 | **11.2 s** |
| Phi-3 Mini | ~0.72 | — |

### Result

**Llama 3.2 3B Instruct** was selected as the primary candidate.

Although several models produced comparable answer quality, Llama 3.2 3B offered the strongest balance between response quality, resource usage and latency on the available CPU-only infrastructure.

**Mistral 7B Instruct** was identified as a suitable alternative when additional model capacity is desired.

An important finding was that model size alone did not determine whether a model was suitable for the application. For this use case, the smaller Llama model provided competitive answer quality while responding substantially faster than some larger alternatives.

## Privacy and self-hosting

The research also considered the privacy implications of running an LLM internally.

Self-hosting can reduce dependence on external AI providers and limits the number of third parties involved in processing business or customer data. However, it does not automatically remove AI-related security and privacy risks.

Relevant risks still include:

- accidental exposure of sensitive information;
- prompt injection and jailbreak attacks;
- model memorisation;
- improper access to internal data;
- insufficient logging and access controls.

The conclusion was therefore not simply that *self-hosted means private*, but that self-hosting gives an organisation **more control over where models and data are processed**, while security measures around the application remain necessary.

---

## Tech stack

`Spring Boot` · `Kotlin` · `F#` · `NestJS` · `Rust` · `Next.js` · `React` · `RabbitMQ` · `PostgreSQL` · `OpenAI` · `Anthropic` · `Llama` · `Mistral` · `Qwen` · `Phi-3`

## What I learned

The two projects covered different sides of software engineering.

During **BPSET**, the focus was primarily on building a distributed software system as a team: defining service boundaries, integrating independently developed components and working with asynchronous communication.

During **BPSEE**, the focus shifted towards engineering research: defining measurable evaluation criteria, benchmarking multiple AI models under the same conditions and translating those results into a technical recommendation for an existing software architecture.

Together, the projects gave me experience with both **building AI-enabled software systems and evaluating the infrastructure and models behind them**.

## A note on the code

This repository is a **case study**.

The original source code is stored in private repositories on the school's GitLab and cannot be published here due to access restrictions. Screenshots and examples use anonymised test data.

I'm happy to discuss the architecture, AI research and my own contribution — reach out via [yassinc.nl](https://yassinc.nl/).
