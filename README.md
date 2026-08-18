# migration_plan - 海外求职与定居作战手册
## Germany / New Zealand
### 目标：Senior Distributed Systems / Data Platform / AI Infrastructure Engineer

---

# 0. 我的基本情况

## Education

- 1994 born
- 2014: Nanchang University
    - Major: Information Security
- 2017–2020: University of Limerick
    - MSc Software Engineering
- 2019: General Motors Ireland
    - Software Engineering Intern
    - Angular / C#
- 2020: COVID期间回中国

## Work Experience

### Zhipu AI / 智谱华章
- 2020–2021
- Software Engineer
- 短期经历
- Resume中弱化，不作为主线

### DiDi
- 2021.02–2022.10
- Backend / Distributed Systems Engineer
- Advertising platform
- Ad delivery
- Attribution
- OCPX
- DSP
- Go
- Kafka
- DDMQ
- Flink
- Hive
- MongoDB
- Elasticsearch
- ClickHouse
- MySQL
- 大规模实时数据处理

### Tesla Shanghai
- 2022.10–2022.12
- Go Developer
- 非核心经历
- 因工作内容与长期职业方向不匹配离开
- Resume可以弱化或放入 Additional Experience
- 绝不修改实际时间

### Dell EMC
- 2022.12–2024.10
- Software Engineer
- Go
- Python
- HCI / distributed infrastructure
- POC / infrastructure-related systems
- Team restructuring / China business adjustment

### Bilibili / B站
- 2025.02–2025.06
- 短期经历
- Resume中弱化
- 可以归入 Additional Experience
- 如被问，真实解释团队/转正/组织变化

### RELX / LexisNexis
- 2025.06–Present
- Data Engineer / Data Platform Engineer
- AWS
- Lambda
- Flink
- Java
- Python
- Paimon
- Hudi
- Dgraph
- Solr
- Qdrant
- Large-scale document processing
- Passage extraction
- Metadata generation
- Vector generation
- Search / retrieval infrastructure

## Personal Project

### Quantitative Research Platform

- Market data ingestion
- Daily + intraday data
- Backtesting
- Factor research
- Portfolio analysis
- Python
- Go
- MySQL
- ClickHouse
- Data pipeline
- Strategy evaluation

---

# 1. 最终职业定位

## PRIMARY POSITIONING

不要把自己定位成：

> Golang Developer

也不要主要定位成：

> Generic Data Engineer

最终定位：

> Senior Distributed Systems / Data Platform Engineer

或者：

> Senior Backend Engineer – Distributed Systems / Data Infrastructure

或者：

> Senior Data Infrastructure / Streaming Engineer

AI相关岗位则进一步定位为：

> AI Infrastructure / Agent Platform Engineer

---

# 2. 我的职业故事

必须形成以下统一叙事：

DiDi
→ large-scale real-time advertising infrastructure

↓

Go + Kafka + Flink
→ distributed systems / event-driven architecture

↓

Dell EMC
→ infrastructure / distributed systems

↓

RELX
→ cloud data platform / streaming / retrieval infrastructure

↓

AWS + Flink + Lambda
→ AI / document processing infrastructure

↓

Passage + Metadata + Embedding
→ AI data / retrieval infrastructure

↓

Solr + Qdrant
→ search / vector retrieval

↓

LLM Harness / Agent Platform
→ AI execution infrastructure

最终职业故事：

> Distributed Systems
> → Streaming
> → Data Infrastructure
> → AI Data Platform
> → Retrieval Infrastructure
> → Agent Infrastructure

---

# 3. Resume原则

## 3.1 绝不伪造

可以：

- 精简
- 弱化
- 合并描述
- 使用 Selected Experience
- 突出主要经历
- 把短经历放到 Additional Experience

不可以：

- 修改入职日期
- 修改离职日期
- 制造不存在的连续就业
- 把看过代码写成主导开发
- 把团队成果说成个人成果
- 虚构技术贡献

---

# 4. Resume结构

## Header

Name
Location: Shanghai, China
Open to relocation: Germany / Netherlands / New Zealand / Australia
Email
LinkedIn
GitHub

不要写：

> Looking for a job in Europe

应该写：

> Open to relocation to Germany / New Zealand

---

# 5. Professional Summary

目标：

3–5行。

必须出现：

- 6+ years software/data engineering experience
- Go
- Distributed Systems
- Kafka
- Flink
- AWS
- Data Platform
- Streaming
- AI infrastructure / retrieval

示例结构：

> Software/Data Engineer with 6+ years of experience building distributed backend and large-scale data systems. Strong background in Go, Kafka, Flink, AWS and real-time data processing, with experience spanning advertising infrastructure, distributed systems and AI data platforms. Currently working on cloud-based document processing, metadata/passage extraction, embeddings and search/vector retrieval infrastructure. Interested in Senior Distributed Systems, Data Platform and AI Infrastructure roles.

---

# 6. 技术能力路线

## Level 1 — 必须达到 Senior

### Go

- [ ] goroutine
- [ ] channel
- [ ] select
- [ ] mutex
- [ ] RWMutex
- [ ] atomic
- [ ] WaitGroup
- [ ] worker pool
- [ ] context
- [ ] cancellation
- [ ] graceful shutdown
- [ ] backpressure
- [ ] error handling
- [ ] interface
- [ ] generics
- [ ] memory allocation
- [ ] escape analysis
- [ ] GC
- [ ] G-M-P
- [ ] scheduler
- [ ] pprof
- [ ] race detector
- [ ] CPU profiling
- [ ] memory profiling

### Go Production Engineering

- [ ] HTTP
- [ ] RPC
- [ ] middleware
- [ ] timeout
- [ ] retry
- [ ] exponential backoff
- [ ] circuit breaker
- [ ] rate limiting
- [ ] connection pool
- [ ] idempotency
- [ ] graceful degradation
- [ ] structured logging
- [ ] metrics
- [ ] tracing

---

# 7. Distributed Systems

## Fundamentals

- [ ] CAP
- [ ] consistency
- [ ] availability
- [ ] partition tolerance
- [ ] strong consistency
- [ ] eventual consistency
- [ ] linearizability
- [ ] quorum
- [ ] replication
- [ ] leader election
- [ ] leader/follower
- [ ] sharding
- [ ] partitioning
- [ ] rebalancing
- [ ] failover
- [ ] split brain
- [ ] distributed locking
- [ ] clock problems

## Reliability

- [ ] timeout
- [ ] retry
- [ ] backoff
- [ ] idempotency
- [ ] duplicate requests
- [ ] duplicate messages
- [ ] ordering
- [ ] replay
- [ ] dead letter queue
- [ ] backpressure
- [ ] load shedding
- [ ] graceful degradation
- [ ] disaster recovery

## Senior level

必须能够回答：

- How do you make a distributed system resilient to partial failures?
- How do you handle duplicate messages?
- How do you guarantee ordering?
- What happens if downstream is unavailable?
- How do you design idempotent APIs?
- How do you scale horizontally?
- Where should state live?
- What happens during failover?
- How would you debug a latency spike?

---

# 8. Kafka

## Core

- [ ] topic
- [ ] partition
- [ ] broker
- [ ] producer
- [ ] consumer
- [ ] consumer group
- [ ] offset
- [ ] replication
- [ ] leader
- [ ] follower
- [ ] ISR

## Advanced

- [ ] partition key design
- [ ] ordering
- [ ] consumer rebalance
- [ ] consumer lag
- [ ] at-most-once
- [ ] at-least-once
- [ ] exactly-once
- [ ] idempotent producer
- [ ] transactional producer
- [ ] log compaction
- [ ] retention
- [ ] segment
- [ ] replication recovery
- [ ] partition skew

## System Design

能够设计：

> 10M events/sec Kafka platform

并回答：

- [ ] How many partitions?
- [ ] How to choose partition key?
- [ ] What happens when a broker dies?
- [ ] How to handle consumer lag?
- [ ] How to preserve ordering?
- [ ] How to deal with duplicated events?
- [ ] How to replay?
- [ ] How to handle hot partitions?

---

# 9. Flink

这是我的核心竞争力之一。

## Fundamentals

- [ ] Source
- [ ] Sink
- [ ] Operator
- [ ] Transformation
- [ ] Parallelism
- [ ] TaskManager
- [ ] JobManager
- [ ] Task Slot
- [ ] Operator Chain

## Time

- [ ] processing time
- [ ] event time
- [ ] watermark
- [ ] out-of-order events
- [ ] late events

## State

- [ ] keyed state
- [ ] operator state
- [ ] state TTL
- [ ] state backend
- [ ] RocksDB
- [ ] checkpoint
- [ ] savepoint

## Reliability

- [ ] checkpoint barrier
- [ ] exactly-once
- [ ] recovery
- [ ] restart strategy
- [ ] failure recovery
- [ ] backpressure
- [ ] rescaling

## Production

- [ ] skew
- [ ] hot key
- [ ] memory problem
- [ ] checkpoint latency
- [ ] checkpoint failure
- [ ] state growth
- [ ] backpressure
- [ ] slow sink
- [ ] asynchronous I/O

## Lakehouse

- [ ] Paimon
- [ ] Hudi
- [ ] schema evolution
- [ ] compaction
- [ ] partitioning
- [ ] snapshots
- [ ] CDC

---

# 10. AWS

## Core

- [ ] IAM
- [ ] IAM Role
- [ ] S3
- [ ] Lambda
- [ ] EC2
- [ ] VPC
- [ ] Security Group
- [ ] CloudWatch

## Data

- [ ] MSK
- [ ] Kinesis
- [ ] Glue
- [ ] Athena
- [ ] EMR
- [ ] Redshift

## Containers

- [ ] EKS
- [ ] ECS
- [ ] ECR

## Architecture

- [ ] Multi-AZ
- [ ] HA
- [ ] autoscaling
- [ ] IAM least privilege
- [ ] network security
- [ ] disaster recovery
- [ ] cost optimization
- [ ] observability

必须能够设计：

> Kafka/Flink/AWS based large-scale data platform

---

# 11. Kubernetes

## Fundamentals

- [ ] Pod
- [ ] Deployment
- [ ] StatefulSet
- [ ] DaemonSet
- [ ] Service
- [ ] ConfigMap
- [ ] Secret
- [ ] Namespace

## Scheduling

- [ ] scheduler
- [ ] request
- [ ] limit
- [ ] node affinity
- [ ] taint
- [ ] toleration

## Networking

- [ ] Service
- [ ] ClusterIP
- [ ] NodePort
- [ ] Ingress
- [ ] DNS
- [ ] CNI

## Storage

- [ ] PV
- [ ] PVC
- [ ] StorageClass

## Production

- [ ] liveness
- [ ] readiness
- [ ] rolling update
- [ ] HPA
- [ ] PDB
- [ ] Helm

## Architecture

- [ ] API Server
- [ ] etcd
- [ ] controller
- [ ] scheduler
- [ ] kubelet

目标：

> 不需要成为Kubernetes专家，但必须能理解生产系统为什么这样运行。

---

# 12. Observability

## Metrics

- [ ] latency
- [ ] throughput
- [ ] error rate
- [ ] saturation
- [ ] RED
- [ ] USE

## Logging

- [ ] structured logging
- [ ] correlation ID
- [ ] log level
- [ ] log aggregation

## Tracing

- [ ] distributed tracing
- [ ] trace
- [ ] span
- [ ] context propagation
- [ ] OpenTelemetry

工具：

- [ ] Prometheus
- [ ] Grafana
- [ ] OpenTelemetry

---

# 13. Data Platform

- [ ] OLTP
- [ ] OLAP
- [ ] Data Warehouse
- [ ] Data Lake
- [ ] Lakehouse
- [ ] Batch
- [ ] Streaming
- [ ] ETL
- [ ] ELT
- [ ] CDC
- [ ] Parquet
- [ ] Arrow
- [ ] Partitioning
- [ ] Compaction
- [ ] Schema Evolution
- [ ] Data Quality
- [ ] Data Lineage

结合现有经验：

- [ ] Paimon
- [ ] Hudi
- [ ] ClickHouse
- [ ] Solr
- [ ] Qdrant

---

# 14. AI / LLM / Agent

目标不是变成 Prompt Engineer。

目标：

> AI Infrastructure / Agent Platform Engineer

---

# 15. LLM基础

- [ ] Transformer
- [ ] Attention
- [ ] Context Window
- [ ] Token
- [ ] Embedding
- [ ] Vector Search
- [ ] RAG
- [ ] Tool Calling
- [ ] Structured Output
- [ ] Function Calling

---

# 16. Agent

## Agent Loop

必须理解：

- [ ] Perceive
- [ ] Decide
- [ ] Act
- [ ] Observe

需要理解：

- [ ] planning
- [ ] tool selection
- [ ] tool execution
- [ ] state
- [ ] memory
- [ ] context
- [ ] retry
- [ ] timeout
- [ ] cancellation
- [ ] maximum iterations
- [ ] handoff
- [ ] sub-agent
- [ ] multi-agent

---

# 17. Harness

必须理解：

- [ ] Agent Loop
- [ ] Orchestration
- [ ] State & Memory
- [ ] Workspace
- [ ] Sandbox
- [ ] Skills
- [ ] Plugins
- [ ] MCP
- [ ] Tools
- [ ] Evaluation
- [ ] Governance
- [ ] Observability
- [ ] Guardrails

重点理解：

> Model capability and Harness engineering are two separate levers.

---

# 18. Harness内部学习方法

利用公司真实代码进行学习。

## Step 1

阅读整个代码结构。

记录：

- [ ] entry point
- [ ] agent loop
- [ ] state
- [ ] memory
- [ ] tool execution
- [ ] plugin
- [ ] skill
- [ ] MCP
- [ ] evaluation
- [ ] observability

## Step 2

本地运行。

- [ ] trace一次完整执行
- [ ] 看一次tool call
- [ ] 看一次retry
- [ ] 看一次error
- [ ] 看一次state变化

## Step 3

询问同事。

不要只问：

> 这段代码干什么？

而要问：

> Why did we choose this design?

> What production problem does this solve?

> What alternatives did we consider?

> What failure modes have we seen?

> How is this evaluated?

> How do we know this change improves the agent?

---

# 19. Harness面试问题

必须能够回答：

- [ ] How did you structure the agent loop?
- [ ] Why does the harness use this state model?
- [ ] How is tool execution handled?
- [ ] What happens when a tool fails?
- [ ] How do you handle timeout?
- [ ] How do you handle retries?
- [ ] How do you avoid duplicate side effects?
- [ ] How do you prevent infinite loops?
- [ ] How do you handle context overflow?
- [ ] How do you persist agent state?
- [ ] How does an agent resume after failure?
- [ ] How do you evaluate the harness?
- [ ] How do you evaluate tool selection?
- [ ] How do you evaluate answer quality?
- [ ] How do you perform regression testing?
- [ ] How do you observe an agent trajectory?
- [ ] How do you control agent cost?
- [ ] How do you secure tool execution?
- [ ] What is the role of MCP?
- [ ] What is the role of Skill?
- [ ] What is the role of Plugin?
- [ ] Why should a Plugin not become a fixed workflow?
- [ ] How do you decide when to add/remove orchestration?

---

# 20. 公司内部 Harness 项目参与策略

目标：

> 从“看代码”
> → “理解”
> → “debug”
> → “贡献”
> → “拥有一个真实ticket”

优先争取：

- [ ] retry
- [ ] timeout
- [ ] error handling
- [ ] observability
- [ ] tracing
- [ ] evaluation case
- [ ] regression test
- [ ] MCP tool
- [ ] Skill
- [ ] state handling
- [ ] guardrail

最终目标：

> 至少拥有 1–3 个真实 Harness contribution。

Resume可以写：

> Contributed to an internal LLM harness for tool-using agents, focusing on [真实贡献].

不能写：

> Designed and built the entire LLM harness.

除非真的做过。

---

# 21. Harness核心知识框架

根据内部 Agent Full Harness 文档建立：

## Architecture

Plugin governs.

Skill guides.

MCP exposes.

Orchestrator composes.

## Runtime

Provisioning
→ Skill matching
→ Tool selection
→ Tool execution
→ Observation
→ More work / final result

## Trust Boundary

- [ ] authoritative grounding
- [ ] permissions
- [ ] auditability
- [ ] human review
- [ ] sandbox
- [ ] policy enforcement

## Evaluation

- [ ] intended-use prompts
- [ ] observed-use prompts
- [ ] held-out variations
- [ ] boundary prompts
- [ ] regression cases
- [ ] expert review
- [ ] automated rating
- [ ] deeper benchmarks
- [ ] continuous evaluation

---

# 22. MCP

- [ ] MCP Server
- [ ] MCP Tool
- [ ] Tool schema
- [ ] Input schema
- [ ] Output schema
- [ ] discovery
- [ ] use-when
- [ ] do-not-use-when
- [ ] examples
- [ ] permissions
- [ ] authorization
- [ ] retry
- [ ] idempotency
- [ ] timeout
- [ ] cancellation
- [ ] partial result
- [ ] no result
- [ ] provenance
- [ ] observability
- [ ] auditability

必须理解：

> MCP不是“把整个数据库暴露给Agent”。

更好的设计是：

> one MCP Tool = one reliable, testable business action.

---

# 23. System Design

这是整个求职准备的最高优先级之一。

必须准备：

- [ ] URL Shortener
- [ ] Rate Limiter
- [ ] Distributed Lock
- [ ] Notification System
- [ ] Message Queue
- [ ] Kafka Platform
- [ ] Real-time Analytics
- [ ] Log Aggregation
- [ ] Metrics Platform
- [ ] Distributed Scheduler
- [ ] Data Pipeline
- [ ] Streaming Processing
- [ ] Search System
- [ ] Vector Search
- [ ] RAG Platform
- [ ] Document Processing System
- [ ] Agent Platform
- [ ] LLM Harness
- [ ] Tool Execution Platform
- [ ] Evaluation Platform

---

# 24. 必须深挖自己的3个真实项目

## Project A — DiDi Advertising Platform

必须能讲：

- [ ] architecture
- [ ] traffic
- [ ] QPS
- [ ] Kafka
- [ ] Flink
- [ ] attribution
- [ ] OCPX
- [ ] consistency
- [ ] idempotency
- [ ] latency
- [ ] failure
- [ ] scaling
- [ ] monitoring

准备5分钟版本。

准备15分钟版本。

准备30分钟深挖版本。

---

# 25. Project B — RELX AI/Data Platform

必须能讲：

Documents
→ AWS
→ Lambda/Flink
→ parsing
→ passage
→ metadata
→ embeddings
→ Solr
→ Qdrant

重点：

- [ ] architecture
- [ ] throughput
- [ ] document size
- [ ] parallel processing
- [ ] retries
- [ ] failure handling
- [ ] data consistency
- [ ] embedding generation
- [ ] vector storage
- [ ] search
- [ ] metadata
- [ ] observability
- [ ] cost

---

# 26. Project C — Personal Quant Platform

必须能讲：

- [ ] data ingestion
- [ ] data storage
- [ ] MySQL
- [ ] ClickHouse
- [ ] historical data
- [ ] intraday data
- [ ] factor engine
- [ ] backtesting
- [ ] strategy evaluation
- [ ] performance
- [ ] data quality

不要主要讲：

> 炒股赚了多少钱。

重点讲：

> engineering architecture.

---

# 27. Coding / Algorithms

目标：

> 足够通过德国 / 新西兰 / 欧洲 Senior Software Engineer 的 coding screen。

核心认知：

> 算法不是我的核心卖点，但算法是进入 Senior Backend / Distributed Systems 面试流程的基础门槛。

不要把准备方式做成：

> “刷很多 Hard”。

应该做到：

> Easy 稳定秒杀 + Medium 稳定解决 + 少量高频 Hard 理解 + Practical Coding 能写生产代码。

---

## 27.1 欧洲 Senior Coding 的目标

### 基础目标

- [ ] 能在 10–15 分钟内完成常见 Easy
- [ ] 能在 20–30 分钟内完成典型 Medium
- [ ] 能清楚解释时间复杂度
- [ ] 能清楚解释空间复杂度
- [ ] 能主动讨论 edge cases
- [ ] 能先给 brute force，再给 optimized solution
- [ ] 能边写边解释思路
- [ ] 能在卡住时与面试官沟通
- [ ] 能接受 interviewer hint 后继续完成
- [ ] 能用 Go 写出干净、可运行、可测试的代码

### 高级目标

- [ ] 能完成典型并发 coding
- [ ] 能完成简单 cache / queue / scheduler
- [ ] 能完成 rate limiter
- [ ] 能完成 worker pool
- [ ] 能处理 cancellation / timeout
- [ ] 能识别 race / deadlock / goroutine leak
- [ ] 能从 coding 问题自然过渡到系统设计

---

## 27.2 不追求刷 1000 题

目标数量：

> LeetCode 精选 100–150 题。

但真正的评价标准不是题量，而是：

- [ ] 同一种 pattern 可以迁移到新题
- [ ] 不依赖题目原答案记忆
- [ ] 能自己识别数据结构
- [ ] 能自己识别 algorithmic pattern
- [ ] 能解释 trade-off
- [ ] 能处理边界条件
- [ ] 能写出没有明显 bug 的 Go

建议结构：

```text
100–150 problems
       ↓
20–30 core patterns
       ↓
每种 pattern 3–6 道代表题
       ↓
高频题重复复习
```

---

## 27.3 Algorithm Topics

### Arrays / Hashing

- [ ] Array traversal
- [ ] Prefix sum
- [ ] HashMap counting
- [ ] Frequency map
- [ ] Deduplication
- [ ] HashSet
- [ ] Index mapping
- [ ] Two Sum pattern
- [ ] Grouping pattern

### Two Pointers

- [ ] Opposite pointers
- [ ] Fast / slow pointers
- [ ] Partition
- [ ] Remove duplicates
- [ ] Linked list cycle
- [ ] In-place modification

### Sliding Window

- [ ] Fixed-size window
- [ ] Variable-size window
- [ ] Frequency tracking
- [ ] Longest / shortest valid window
- [ ] Monotonic condition
- [ ] Window shrink logic

### Binary Search

- [ ] Classic binary search
- [ ] Lower bound
- [ ] Upper bound
- [ ] Search insertion position
- [ ] Search on answer
- [ ] Rotated array
- [ ] Binary search over feasible solution

### Strings

- [ ] Frequency counting
- [ ] Character window
- [ ] String normalization
- [ ] Parsing
- [ ] Tokenization
- [ ] String builder usage in Go

### Stack / Queue

- [ ] Stack simulation
- [ ] Monotonic stack
- [ ] Queue simulation
- [ ] BFS queue
- [ ] Deque concept

### Heap / Priority Queue

- [ ] Top K
- [ ] Merge K sorted sequences
- [ ] Streaming median
- [ ] Scheduling
- [ ] Min heap / max heap
- [ ] Go heap.Interface

### Linked List

- [ ] Reverse list
- [ ] Fast / slow pointer
- [ ] Cycle detection
- [ ] Merge lists
- [ ] Remove node
- [ ] Reorder list

### Tree

- [ ] DFS
- [ ] BFS
- [ ] Preorder
- [ ] Inorder
- [ ] Postorder
- [ ] Level order
- [ ] Binary search tree
- [ ] Lowest common ancestor
- [ ] Tree depth
- [ ] Tree validation

### Graph

- [ ] BFS
- [ ] DFS
- [ ] Visited set
- [ ] Connected components
- [ ] Cycle detection
- [ ] Topological sort
- [ ] Shortest path concept
- [ ] Dijkstra basics
- [ ] Union Find

### Intervals

- [ ] Merge intervals
- [ ] Overlap detection
- [ ] Scheduling
- [ ] Meeting rooms
- [ ] Sweep line concept

### Backtracking

- [ ] Permutations
- [ ] Combinations
- [ ] Subsets
- [ ] Constraint search
- [ ] Pruning

### Dynamic Programming

只要求基础能力：

- [ ] 1D DP
- [ ] 2D DP
- [ ] State definition
- [ ] Transition
- [ ] Base case
- [ ] Memoization
- [ ] Tabulation

不建议把大量时间投入到非常复杂的 DP。

---

## 27.4 Complexity

必须熟练：

- [ ] O(1)
- [ ] O(log n)
- [ ] O(n)
- [ ] O(n log n)
- [ ] O(n²)
- [ ] O(2^n)
- [ ] O(n!)

必须能够解释：

> Why is this solution O(n)?

> Why does sorting change the complexity?

> Can we reduce memory usage?

> Is this bound amortized or worst-case?

---

## 27.5 Go Coding Requirements

算法题必须使用 Go 训练，而不是 Python。

### Go 标准动作

- [ ] `map` 熟练
- [ ] `slice` 熟练
- [ ] `sort.Slice`
- [ ] `container/heap`
- [ ] string / rune 差异
- [ ] bytes.Buffer / strings.Builder
- [ ] pointer 基础
- [ ] struct
- [ ] method
- [ ] interface 基础
- [ ] 自定义 queue / stack
- [ ] 自定义 heap

### 写代码要求

- [ ] 变量命名清楚
- [ ] 函数职责单一
- [ ] 不写不必要的抽象
- [ ] 不滥用 interface
- [ ] 不制造无意义 helper
- [ ] 先保证正确性，再优化
- [ ] 能主动补 edge cases

---

## 27.6 Coding Interview Protocol

每道题固定使用：

```text
1. Clarify
2. Examples
3. Brute force
4. Identify bottleneck
5. Optimize
6. Complexity
7. Implement
8. Test
9. Edge cases
10. Follow-up
```

### Clarify

- [ ] Input size?
- [ ] Duplicates?
- [ ] Negative values?
- [ ] Empty input?
- [ ] Ordering requirement?
- [ ] Memory constraint?
- [ ] Mutable input?

### Implement

不要一上来就写代码。

先说：

> “I think a sliding-window approach works here because...”

或者：

> “The key observation is that...”

### Test

至少主动测试：

- [ ] Empty input
- [ ] One element
- [ ] Duplicate values
- [ ] Already sorted
- [ ] Reverse sorted
- [ ] Maximum boundary
- [ ] Negative values if applicable

---

## 27.7 Practical Coding

这是我的重点补充。

Senior Backend Engineer 面试不应该只准备 LeetCode。

必须准备：

### Challenge 01 — Worker Pool

实现：

```text
Submit(job)
Worker()
Result()
Shutdown()
```

要求：

- [ ] Fixed worker count
- [ ] Bounded queue
- [ ] Backpressure
- [ ] Context cancellation
- [ ] Error propagation
- [ ] Graceful shutdown
- [ ] Panic recovery
- [ ] No goroutine leak

### Challenge 02 — Rate Limiter

实现：

```text
Allow()
Wait(ctx)
```

然后追问：

- [ ] Token bucket
- [ ] Leaky bucket
- [ ] Sliding window
- [ ] Concurrent safety
- [ ] Burst handling
- [ ] Distributed version

### Challenge 03 — Concurrent Cache

实现：

```text
Get()
Set()
Delete()
```

继续扩展：

- [ ] TTL
- [ ] LRU
- [ ] Lock contention
- [ ] Cache stampede
- [ ] Single-flight
- [ ] Eviction policy

### Challenge 04 — Retry Framework

实现：

```text
Retry(ctx, operation, policy)
```

必须讨论：

- [ ] Retryable errors
- [ ] Non-retryable errors
- [ ] Exponential backoff
- [ ] Jitter
- [ ] Maximum attempts
- [ ] Total timeout
- [ ] Idempotency

### Challenge 05 — Concurrent Pipeline

```text
Input
 ↓
Parser
 ↓
Transformer
 ↓
Validator
 ↓
Writer
```

要求：

- [ ] Fan-out
- [ ] Fan-in
- [ ] Bounded concurrency
- [ ] Backpressure
- [ ] Cancellation
- [ ] Ordering
- [ ] Error propagation
- [ ] Retry

---

## 27.8 Go + Distributed Systems Coding

重点准备这些跨层问题：

### 1. In-memory queue

- [ ] Thread safe
- [ ] Blocking / non-blocking
- [ ] Bounded queue
- [ ] Full behavior
- [ ] Shutdown behavior

### 2. Local scheduler

- [ ] Delayed job
- [ ] Cron-like execution
- [ ] Retry
- [ ] Cancellation
- [ ] Concurrent execution limit

### 3. Distributed scheduler

- [ ] Leader election
- [ ] Task ownership
- [ ] Duplicate execution
- [ ] Failover
- [ ] Persistence
- [ ] Idempotency

### 4. Distributed lock

- [ ] Lock acquisition
- [ ] Lease
- [ ] Expiry
- [ ] Renewal
- [ ] Failure during ownership
- [ ] Fencing token concept

### 5. Kafka consumer service

- [ ] Worker pool
- [ ] Offset management
- [ ] Ordering
- [ ] Retry
- [ ] DLQ
- [ ] Rebalance
- [ ] Graceful shutdown
- [ ] Backpressure

---

## 27.9 Production Coding Scenarios

### Scenario A — 100k Goroutines

问题：

> Your Go service suddenly has 100k goroutines. CPU is only 20%. What do you investigate?

必须能够想到：

- [ ] Goroutine dump
- [ ] pprof
- [ ] Blocked goroutines
- [ ] Channel blocking
- [ ] Network I/O
- [ ] Mutex contention
- [ ] Goroutine leak
- [ ] Context cancellation

### Scenario B — Memory 2GB → 15GB

- [ ] Heap profile
- [ ] Allocation profile
- [ ] Object retention
- [ ] Cache growth
- [ ] Slice retention
- [ ] Map growth
- [ ] Large buffers
- [ ] Goroutine references
- [ ] GC behavior

### Scenario C — p99 20ms → 2s

- [ ] Check p50 / p95 / p99
- [ ] Distributed trace
- [ ] CPU
- [ ] Memory
- [ ] GC
- [ ] Goroutines
- [ ] Downstream latency
- [ ] Connection pool
- [ ] Retry amplification
- [ ] Network

### Scenario D — Service occasionally deadlocks

- [ ] Goroutine dump
- [ ] Mutex profiles
- [ ] Lock ordering
- [ ] Channel cycle
- [ ] WaitGroup misuse
- [ ] Nested locks
- [ ] Context blocked operation

---

## 27.10 Complex Projects

至少做 2 个，最好 3 个。

### Project A — Mini Distributed Job System

```text
Client
 ↓
API
 ↓
Scheduler
 ↓
Queue
 ↓
Workers
 ↓
Executor
```

必须支持：

- [ ] Submit task
- [ ] Task state
- [ ] Retry
- [ ] Timeout
- [ ] Cancellation
- [ ] Worker failure
- [ ] Duplicate execution
- [ ] Persistence
- [ ] Metrics
- [ ] Graceful shutdown

### Project B — High Performance HTTP Gateway

```text
Client
 ↓
Gateway
 ├── Auth
 ├── Routing
 ├── Rate Limit
 ├── Retry
 ├── Circuit Breaker
 └── Observability
 ↓
Backend
```

测试：

- [ ] 10k RPS
- [ ] Slow backend
- [ ] Backend failure
- [ ] Connection exhaustion
- [ ] Retry storm
- [ ] Load spike

### Project C — Document Processing Engine

```text
S3
 ↓
Queue
 ↓
Go Workers
 ↓
Parser
 ↓
Passage Extraction
 ↓
Metadata
 ↓
Embedding
 ↓
Vector DB
```

必须处理：

- [ ] Duplicate document
- [ ] Huge document
- [ ] Worker crash
- [ ] Partial failure
- [ ] Downstream timeout
- [ ] Retry storm
- [ ] Memory leak
- [ ] Stuck goroutine
- [ ] Observability

---

## 27.11 Code Review

训练自己每天读一段 Go 代码，并回答：

- [ ] Is there a data race?
- [ ] Is there a goroutine leak?
- [ ] Can this deadlock?
- [ ] Is context propagated correctly?
- [ ] Can this panic?
- [ ] Is error wrapped correctly?
- [ ] Is there unnecessary allocation?
- [ ] Is there a memory retention issue?
- [ ] Is the retry safe?
- [ ] Is the operation idempotent?
- [ ] Is shutdown correct?
- [ ] Is observability sufficient?

---

## 27.12 Algorithm + System Design Bridge

面试官经常会从 coding 向 system design 继续追问。

例如：

```text
Implement rate limiter
        ↓
How do you make it concurrent-safe?
        ↓
How do you distribute it?
        ↓
What if Redis fails?
        ↓
How do you handle clock skew?
        ↓
How do you monitor it?
```

或者：

```text
Implement worker pool
        ↓
What if queue is full?
        ↓
How do you apply backpressure?
        ↓
How do you retry jobs?
        ↓
How do you avoid duplicate processing?
        ↓
How do you persist job state?
```

必须训练：

> Coding → Concurrency → Reliability → Distributed Systems

---

## 27.13 训练计划

### Phase 1 — 1–2 Weeks

目标：coding 基础恢复。

每天：

- [ ] 2 Easy
- [ ] 1 Medium
- [ ] 15 min complexity review

### Phase 2 — 2–4 Weeks

每天：

- [ ] 1 Medium
- [ ] 1 Practical Go challenge
- [ ] 1 code review question

### Phase 3 — 4–6 Weeks

每周：

- [ ] 3 Medium
- [ ] 1 Practical challenge
- [ ] 1 concurrency challenge
- [ ] 1 mock coding interview
- [ ] 1 production debugging exercise

### Phase 4 — Interview Mode

停止大量刷新题。

重点：

- [ ] Timed Medium
- [ ] Practical coding
- [ ] Go concurrency
- [ ] Debugging
- [ ] System design follow-up

---

## 27.14 Final Coding Standard

达到以下标准才算准备完成：

- [ ] Easy 基本稳定
- [ ] Medium 大部分能独立完成
- [ ] 不依赖记忆题解
- [ ] 能解释复杂度
- [ ] 能主动测试 edge cases
- [ ] Go 写法自然
- [ ] 能写并发代码
- [ ] 能读懂别人并发代码
- [ ] 能定位 goroutine / race / deadlock 问题
- [ ] 能把 coding 问题延伸到 production design

最终目标：

> Algorithm is no longer a risk.
>
> Go coding becomes a strength.
>
> System design remains the primary differentiator.

---

# 28. System Design和Coding的比例

针对我的目标岗位：

- [ ] System Design: 35%
- [ ] Distributed Systems: 20%
- [ ] Go/Kafka/Flink: 15%
- [ ] AWS/Kubernetes: 10%
- [ ] Coding/Algorithms: 10%
- [ ] Behavioral: 5%
- [ ] AI/Agent: 5%

这个比例不是行业统计，而是针对我个人背景和目标职位设计。

---

# 29. 英语

不能只看英文视频。

我要从：

> Input English

升级到：

> Technical Output English

---

# 30. 每日英语

## 30–60分钟

### Listening

20–30分钟

- [ ] technical podcast
- [ ] engineering talk
- [ ] interview
- [ ] conference talk

### Speaking

20分钟

当天选择一个技术主题：

- Kafka
- Flink
- Go
- AWS
- Kubernetes
- Distributed Systems
- Harness

连续讲：

> 3–5 minutes

不要看稿。

### Correction

5–10分钟

让LLM：

- [ ] correct grammar
- [ ] improve phrasing
- [ ] identify unnatural wording
- [ ] ask follow-up questions

---

# 31. 英语面试训练

每周至少：

- [ ] 2次 technical interview
- [ ] 1次 system design interview
- [ ] 1次 behavioral interview

必须练：

> Tell me about yourself.

> Tell me about your current project.

> Tell me about a difficult production issue.

> Tell me about a system you designed.

> Why did you choose Kafka?

> Why Flink?

> How did you scale the system?

> What went wrong?

> What would you redesign?

---

# 32. 英语技术表达模板

必须能自然说：

### Architecture

> The system consists of several major components...

### Trade-off

> We considered two approaches...

### Decision

> We chose this approach because...

### Failure

> The main failure mode we had to account for was...

### Scaling

> As traffic increased, the bottleneck shifted to...

### Improvement

> In hindsight, I would redesign...

### Uncertainty

> I'm not completely sure about the implementation detail, but based on the architecture...

不要假装知道。

Senior Engineer可以说：

> I don't know, but here's how I would investigate it.

---

# 33. Behavioral Interview

准备至少10个STAR故事：

- [ ] most difficult project
- [ ] production incident
- [ ] conflict with teammate
- [ ] disagreement with manager
- [ ] deadline pressure
- [ ] project failure
- [ ] technical trade-off
- [ ] performance optimization
- [ ] architecture decision
- [ ] learning something quickly
- [ ] ownership
- [ ] mistake
- [ ] mentoring
- [ ] ambiguous requirement

---

# 34. 工作频繁跳槽的解释

不要主动强调：

> I changed jobs frequently.

准备统一解释：

### Tesla

> The role turned out to be significantly different from the software infrastructure direction I wanted to pursue, so I made a relatively early career adjustment.

### Bilibili

> The team was going through organizational and conversion changes, so I decided to continue my search rather than wait for an uncertain internal outcome.

核心：

> 短经历不是重点。

不要长篇解释。

---

# 35. Tell Me About Yourself

目标结构：

1. current role
2. major previous experience
3. technical specialization
4. current direction
5. why this position

时间：

> 60–90 seconds

不要从大学开始讲。

---

# 36. 德国求职路线

## Priority

1. Germany
2. Netherlands
3. New Zealand
4. Australia

---

# 37. Germany Target Roles

搜索：

- [ ] Senior Software Engineer
- [ ] Senior Backend Engineer
- [ ] Senior Go Engineer
- [ ] Distributed Systems Engineer
- [ ] Data Platform Engineer
- [ ] Data Infrastructure Engineer
- [ ] Streaming Engineer
- [ ] Real-time Data Engineer
- [ ] Platform Engineer
- [ ] AI Infrastructure Engineer
- [ ] ML Infrastructure Engineer
- [ ] AI Platform Engineer
- [ ] Agent Infrastructure Engineer

---

# 38. Germany Target Cities

Priority:

- [ ] Berlin
- [ ] Munich
- [ ] Hamburg
- [ ] Frankfurt
- [ ] Cologne
- [ ] Stuttgart

---

# 39. Germany公司类型

重点找：

- [ ] SaaS
- [ ] Cloud
- [ ] AI
- [ ] Data Platform
- [ ] FinTech
- [ ] AdTech
- [ ] E-commerce
- [ ] Enterprise Software
- [ ] Search
- [ ] Cybersecurity
- [ ] AI Infrastructure

---

# 40. Germany Visa Strategy

第一目标：

> Employer-sponsored employment

优先争取：

> EU Blue Card

2026年具体门槛、职业分类、学历匹配和BA审批在拿offer后重新核对官方规则。

当前核心认知：

- [ ] job offer
- [ ] salary threshold
- [ ] qualification matching
- [ ] Blue Card
- [ ] residence permit
- [ ] German learning
- [ ] permanent residence

长期目标：

> Germany PR

德语：

> A1 minimum practical target

最终：

> B1

---

# 41. Germany语言路线

## 0–3个月

- [ ] German A1

## 3–6个月

- [ ] A2

## 6–12个月

- [ ] B1

不用等德语B1再求职。

正确方式：

> English job search + German learning simultaneously.

---

# 42. New Zealand求职路线

核心路线：

> Overseas → NZ job offer → Accredited Employer → AEWV → residence pathway

必须检查：

- [ ] employer accredited
- [ ] job offer
- [ ] job description
- [ ] occupation classification
- [ ] salary
- [ ] Green List
- [ ] qualification
- [ ] experience
- [ ] residence pathway

---

# 43. NZ Target Roles

- [ ] Software Engineer
- [ ] Backend Engineer
- [ ] Senior Software Engineer
- [ ] Developer Programmer
- [ ] Data Engineer
- [ ] Data Platform Engineer
- [ ] Cloud Engineer
- [ ] Platform Engineer
- [ ] Distributed Systems Engineer
- [ ] AI Infrastructure Engineer

重点关注：

> Green List / Tier 1

但必须针对具体offer重新检查当时的薪资和资格要求。

---

# 44. NZ求职原则

不要只投：

> Junior / Mid-level Data Engineer

因为目标是海外求职和长期定居。

优先：

> Senior Software Engineer
> Senior Backend Engineer
> Senior Data Platform Engineer
> Platform Engineer
> AI Infrastructure Engineer

目标是：

> high-value / hard-to-fill / sponsorship-compatible role

---

# 45. NZ公司筛选

优先：

- [ ] Accredited Employer
- [ ] large tech company
- [ ] SaaS
- [ ] cloud
- [ ] data
- [ ] fintech
- [ ] enterprise software
- [ ] AI
- [ ] infrastructure

---

# 46. Job Application Pipeline

维护一个表：

| Company | Country | City | Role | Level | Salary | Visa | Sponsor | Applied | Recruiter | Interview | Result |
|---|---|---|---|---|---|---|---|---|---|---|---|

每周更新。

---

# 47. 每周投递量

不要一口气乱投200个。

第一阶段：

> 10–15 high-quality applications/week

第二阶段：

> 15–25/week

重点：

> Quality > quantity

---

# 48. 投递优先级

## Tier A

岗位高度匹配：

- Go
- Kafka
- Flink
- AWS
- Distributed Systems
- Data Platform
- AI Infrastructure

优先投入。

## Tier B

部分匹配：

- Java
- Python
- Kubernetes
- Data Engineering
- Cloud

## Tier C

完全不匹配。

不投入。

---

# 49. LinkedIn

Headline:

> Senior Distributed Systems / Data Platform Engineer | Go | Kafka | Flink | AWS | Streaming | AI Infrastructure

About重点：

- distributed systems
- streaming
- cloud
- data platform
- AI infrastructure

不要写：

> actively looking for jobs

不要写：

> want to immigrate to Germany

---

# 50. GitHub

至少准备3个项目。

## Project 1

Distributed Event Processing Platform

技术：

- Go
- Kafka
- Flink
- ClickHouse
- Docker
- Kubernetes

## Project 2

AI Document Processing Platform

技术：

- AWS
- Lambda
- Flink
- Object Storage
- Embedding
- Qdrant
- Search

## Project 3

Agent / Harness Demo

技术：

- LLM
- Tool Calling
- MCP
- Agent Loop
- State
- Memory
- Evaluation
- Observability
- Sandbox

---

# 51. Agent项目不要做成玩具Chatbot

不要：

> User → Prompt → GPT → Answer

应该做：

```text
User
 ↓
Agent
 ↓
Perceive
 ↓
Decide
 ↓
Tool Selection
 ↓
Tool Execution
 ↓
Observe
 ↓
State Update
 ↓
Retry / Continue
 ↓
Final Answer