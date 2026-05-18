# cascade-opt
An omnichannel database optimization and abstraction layer designed to compile and execute data extraction workflows containing embedded AI primitives, treating language model inference cost and latency as first-class compilation constraints.

## Summary
cascade-opt is an AI-Native Data Access Layer (DAL) middleware that sits between application runtime environments and relational database structures. It intercepts data access requests—whether submitted as raw SQL strings, object-relational mappings (ORMs), or free-form natural language—and compiles them into highly cost-optimized, execution-ready query plans. Built to handle massive unstructured text processing at scale, cascade-opt automatically balances financial budgets, network latency, and output precision by combining traditional relational engine principles with adaptive model cascading pipelines.

## Problem: 

**The Industry Context**
Enterprises are sitting on petabytes of unstructured text data (customer support logs, global supply chain manifests, medical histories) stored inside relational databases. While Large Language Models (LLMs) provide the cognitive capacity to reason over this text, treating an LLM as a native database user-defined function (UDF) introduces an unprecedented structural crisis: extreme cost asymmetry.

In traditional computing, database operators are virtually free (costing micro-cents and micro-seconds). In an AI-driven data ecosystem, a single foundation model operator is highly expensive and network-bound:
* Traditional Relational Operator: Cost $\approx \$0.0000001$ | Latency $\approx 0.001\text{ms}$
* Frontier Language Model Operator: Cost $\approx \$0.0150000$ | Latency $\approx 800.0\text{ms}$

Because legacy database management systems (DBMS) and standard application Object-Relational Mappings (ORMs) are completely blind to inference economics, they execute queries naively. Running an unoptimized query that evaluates an LLM primitive before a traditional index filter can trigger millions of runaway external API calls—resulting in catastrophic infrastructure bills, token-quota depletion (HTTP 429 errors), and application timeouts.

**The Research Frontier (MLSys)**
This friction point has birthed a red-hot field in Machine Learning Systems (MLSys) research: Test-Time Compute Scaling and Declarative AI Query Compilation (pioneered by recent 2025/2026 literature out of MIT, Stanford, and Berkeley, including Palimpzest, Abacus, and QUEST).

Current computer science research highlights three massive architectural gaps in existing data access layers:
* Lack of AI-Aware Cost-Based Optimization (CBO): Modern database query planners do not treat model inference costs, token boundaries, and variable latencies as first-class physical execution constraints.
* The "All-or-Nothing" Inference Fallacy: Existing frameworks route all text chunks to a single monolithic frontier model, ignoring the fact that a vast majority of database records can be processed accurately by cheap, high-throughput, local open-weights models.
* Context Window Degradation & Token Churn: Naive systems continuously cycle repetitive context (system instructions, static relational schemas) through LLM context windows, failing to optimize for modern hardware-level prompt caching and causing severe semantic performance decay.

cascade-opt was built to solve this convergence gap. It bridges classic relational database engine design with probabilistic machine learning runtime optimization to make large-scale text analytics financially sustainable and architecturally resilient.

## Project Goals & Technical Scope
This repository focuses on advanced database systems engineering, data virtualization, and AI FinOps optimization, covering:

* **Omnichannel Interface Parsing**: A multi-modal parsing subsystem capable of converting raw SQL, programmatic ORM expressions, or natural language prompts into a unified execution-ready AST (Abstract Syntax Tree).

* **Relational Projection & Transmutation**: Internal translation routines that seamlessly handle inline scalar transformations (e.g., column text concatenations like flight_id + '-' + tail_number) and shape structured AI outputs into deeply nested JSON geometries.

* **Relational Predicate Reordering**: A logical query optimizer that forces standard indexing, numerical filters, and date boundaries to run before expensive semantic text operators are ever triggered.

* **Adaptive Model Cascading & Routing**: An infrastructure-aware routing core that pipes text segments through fast, cheap local open-weights models first, automatically escalating anomalies to frontier text APIs based on real-time token confidence distribution scores.

* **Database Execution Core Primitives**: High-throughput, vectorized async volcano iterators, distributed token-bucket semaphore controllers, and a two-tier semantic buffer pool manager to secure systems against rate limits and redundant API token expenditure.

## Architecture
https://drive.google.com/file/d/1M1Tq1iXw4NPY8FJqiCjvz6-NzDzkjeqW/view?usp=sharing

## Business & Application Context
Modern enterprises sit on top of massive relational databases flanked by millions of rows of unstructured text, such as customer communication logs, global supply chain manifests, or mechanical maintenance files. Traditional query mechanisms cannot process this natural language natively, while naive AI wrappers that send entire tables to cloud-hosted LLMs trigger catastrophic cloud infrastructure bills and system-wide latency timeouts.

cascade-opt serves as an intelligent, drop-in proxy layer that generalizes text-based analytical searches. By abstracting away the underlying query format, it allows product developers to build intuitive natural language search bars or clean ORM application logic. The middleware shields the underlying data stack—filtering data sets using native indices first, optimizing text transforms, caching repeated states, and orchestrating a cost-capped model fleet to deliver structured, production-ready payloads safely and economically.
