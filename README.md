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

## Step-by-Step Execution Trace

| **Natural Language Query**: "Find me all Costco bulk orders from last month where the customer text complaint mentions delivery damage, and give me a nested JSON of the product details paired with the severity."

**SQL Query**
```
SELECT 
    order_id,
    JSON_OBJECT(
        'product_details', product_details,
        'incident_analysis', AI_EXTRACT(complaint_prose, DamageAnalysisSchema)
    ) AS nested_output
FROM costco_orders
WHERE order_type = 'BULK'
  AND order_date BETWEEN '2026-04-01' AND '2026-04-30';
```

**SQLAlchemy ORM**
```
from sqlalchemy import func, and_
from pydantic import BaseModel, Field

# 1. Define the target Pydantic schema passed to the middleware
class DamageAnalysisSchema(BaseModel):
    severity: str = Field(description="Severity of delivery damage: LOW, MEDIUM, CRITICAL")
    mentions_damage: bool = Field(description="True if delivery damage is explicitly mentioned")

# 2. Build the abstraction query using the SQLAlchemy execution factory
query = (
    session.query(
        CostcoOrder.order_id,
        func.json_object(
            'product_details', CostcoOrder.product_details,
            'incident_analysis', func.ai_extract(CostcoOrder.complaint_prose, DamageAnalysisSchema)
        ).label('nested_output')
    )
    .filter(
        and_(
            CostcoOrder.order_type == 'BULK',
            CostcoOrder.order_date.between('2026-04-01', '2026-04-30')
        )
    )
)
```

### A. Plan Parser
The parser receives the raw string, normalizes the input, maps it against database schemas, and constructs the initial internal logical representation.

**A.1.1 Lexer & Tokenizer**
How it works for this query: Scans the raw character stream of the natural language prompt and chunks it into atomic linguistic strings. It flags key terms like "Costco", "bulk", "last month", "complaint", "delivery damage", and "nested JSON", readying them for syntax parsing.

**A.1.2 Grammar Analyzer & AST Generator**
How it works for this query: Takes the tokenized strings and checks them against structural grammar rules. It instantly recognizes that this is an unstructured free-text request rather than an exact SQL block, and routes the token tree directly to sub-component A.1.3 to execute a schema mapping sequence.

**A.1.3 NL-to-SQL Semantic Mapping Sub-Component**
How it works for this query: Pulls the database metadata catalog for the target table (e.g., costco_orders).

Maps "bulk orders" to a structural database filter: WHERE order_type = 'BULK'.

Calculates "last month" based on the current date (May 2026), translating it into a relational time range: WHERE order_date >= '2026-04-01' AND order_date <= '2026-04-30'.

Identifies that "customer text complaint" maps to a large, unindexed column named complaint_prose.

Binds an implicit Pydantic Schema (class DamageAnalysis(BaseModel): severity: str) to extract the requested severity from the raw text blocks.

**A.1.4 Semantic Type Checker & Resolver**
How it works for this query: Verifies that order_type, order_date, complaint_prose, and product_details are valid, active database column identifiers. It registers an internal TransformationNode that marks product_details and the upcoming AI-generated severity parameter to be combined downline into a nested JSON object.

### B. Two-Phase Cost-Based Optimizer (CBO)
The CBO ingests the parsed logical plan and evaluates how to execute it across relational tables and AI model endpoints with the lowest possible latency and cost.

**B.2.1 Phase 1: Relational Planner (Algebraic Rewriter)** 
How it works for this query: Executes an aggressive Predicate Pushdown. It completely ignores the expensive language models at first, forcing the database engine to run the indexing filters (order_type = 'BULK' and the April 2026 date window) at the lowest layer of the storage engine. If Costco has 10,000,000 historical orders, this rule-based step instantly prunes the active dataset down to 5,000 rows. It also executes Projection Pruning, dropping unused columns (like customer names or shipping addresses) from memory.

**B.2.2 Phase 2: Semantic Planner & Cascade Router**
How it works for this query: Takes the remaining 5,000 rows and designs the model pipeline for the complaint_prose text evaluation. It establishes a multi-tier Model Cascade Architecture:Tier 1 (Local): An open-weights 8B parameter model running locally inside the data center to handle clear-cut complaints.Tier 2 (Cloud API): A high-end frontier model endpoint to handle complex, long, or highly ambiguous complaint arguments.

**B.2.3 Cost Estimation Model**
How it works for this query: Runs a dynamic programming optimization loop to assign a physical cost projection to the plan. It evaluates historical database performance and expected cache hit ratios ($H_i$) to minimize the total system overhead:

$$Cost_{total}(P) = \sum_{i \in P} N_i \times \left[ (1 - H_i) \cdot \left( C_{model}(i) + \lambda \cdot (L_{expected}(i) + \delta_{rate}) \right) + H_i \cdot \left( C_{cache} + \lambda \cdot L_{cache} \right) \right]$$

The CBO estimates that the 5,000 rows can be shrunk via local cache matches, assigning an optimized execution roadmap that guarantees the final output matches the requested accuracy constraint.

### C. Async Volcano Iterator Layer
The Iterator Layer governs the live physical execution loop, pulling data rows from storage and feeding them through the processing pipelines non-blockingly.

**C.3.1 Vectorized Iterator Interface (AsyncIterator)**
How it works for this query: Opens up the runtime thread execution stream by calling open(). When the master framework requests data records via next(), the iterator issues asynchronous, non-blocking futures. If a specific complaint text block takes 900ms to evaluate via an AI call, the execution thread does not sit idle; it immediately pivots to handle parallel rows in the pipeline.

**C.3.2 Row-Batching Engine (VectorBuffer)**
How it works for this query: Prevents the framework from making thousands of tiny, inefficient, sequential I/O loops. It collects the pruned records coming from the Costco database and groups them into uniform vectorized batches of 64 rows at a time, preparing them for bulk cache lookups and array-based model inference.

**C.3.3 Relational Projection Operator (ProjectionOp)**
How it works for this query: Sits at the peak of the iterator structure. As individual rows finish processing, this operator grabs the database's native product_details column string, intercepts the AI-extracted severity token, and packages them together at runtime into a deeply nested JSON object structure to fulfill the exact shape requested by the user.

**C.3.4 MVCC Snapshot Engine**
How it works for this query: Places an isolation lock over the query lifecycle based on Multi-Version Concurrency Control. Because evaluating 5,000 rows of large text data can take time, this engine ensures that if a separate warehouse thread alters or updates an order’s complaint file while the query is currently running, the iterator reads the historical data from the Undo Log, ensuring a consistent, uncorrupted transaction worldview.

### D. Token-Bucket Semaphore Lock
The Semaphore Lock acts as an infrastructure firewall, ensuring outbound model streams do not overload vendor rate thresholds or trigger application crashes.

**D.4.1 Rate Limit Monitor**
How it works for this query: Actively tracks live network metadata headers returned by the cloud model endpoints. It updates the internal system state with exactly how many Requests-Per-Minute (RPM) and Tokens-Per-Minute (TPM) are currently remaining in the company’s enterprise allowance.

**D.4.2 Distributed Token Bucket**
How it works for this query: Before a batch of 64 Costco rows can be dispatched to an external model endpoint, this thread-safe component counts the total characters in those 64 complaint_prose text blocks. It calculates that the batch requires, for example, 45,000 tokens. It checks the token bucket; if tokens are available, they are atomically deducted, and the batch is approved for immediate launch.

**D.4.3 Adaptive Backpressure Controller & Degrader**
How it works for this query: If other large corporate data jobs have completely drained the Token Bucket, this component intercepts the stalled batch. To prevent system-wide latency timeouts, it applies an exponential backoff with randomized jitter to delay requests smoothly. If the delay threatens to breach the query's total latency budget, it activates the Model Degradation circuit, bypassing the external cloud model entirely and routing those rows to the local open-weights tier to keep the system moving.

### E. Semantic Buffer Pool
The Buffer Pool serves as the primary acceleration layer, bypassing language models completely if an identical text analysis has been completed previously.

**E.5.1 Deterministic Key Hash Generator**
How it works for this query: Iterates through every incoming complaint_prose text field in the batch. For a specific row, it cleans whitespace, joins it with the Pydantic schema parameters and prompt metadata, and runs a SHA-256 cryptographic hashing algorithm. If a customer wrote a boilerplate complaint string like "Box arrived completely smashed by delivery driver", this results in a predictable, reproducible hash key signature.

**E.5.2 L1 Cache Layer (Thread-Safe Memory)**
How it works for this query: Takes the generated SHA-256 key signature and instantly performs a lookup in local system RAM. Guided by a Least Frequently Used (LFU) eviction layout, if that exact text block was evaluated earlier in the transaction loop, it retrieves the calculated severity in <1ms, bypassing the AI pipeline entirely.

**E.5.3 L2 Cache Layer (Redis Shared Cache Adapter)**
How it works for this query: If the local L1 RAM reports a cache miss, the engine fires a network lookup to a shared enterprise Redis cluster using the same hash signature. If another node in the corporate network processed that identical complaint text on a separate query yesterday, the Redis adapter pulls the cached json array payload in ~2ms with zero API token costs.

**E.5.4 Cache Eviction & Invalidation Controller**
How it works for this query: Coordinates memory footprint limits and data updates. If a customer service agent manually alters or deletes an order’s complaint record in the main Costco database, this sub-component flags the corresponding SHA-256 hash key as "dirty" and purges it from both the L1 local RAM and L2 Redis clusters to ensure the cache never serves stale or outdated insights.
