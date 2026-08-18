# GO_SENIOR_INTERVIEW_CHECKLIST

# Senior Go / Production Go Interview Preparation

## 0. 定位

目标岗位：

> Senior Backend Engineer / Senior Distributed Systems Engineer / Data Platform Engineer / Streaming Engineer / AI Infrastructure Engineer

核心目标不是：

> “背完 Go 八股。”

而是达到：

> 能写 Go、能理解 Go runtime、能写并发程序、能构建生产服务、能排查线上故障、能做性能优化、能读复杂代码、能设计可演进的 Go 服务。

这份文档与主 README 配合使用：

```text
README
  ↓
Distributed Systems / Kafka / Flink / AWS / System Design

GO_SENIOR_INTERVIEW_CHECKLIST
  ↓
Go Language / Runtime / Concurrency / Production / Debugging / Practical Coding
```

---

# 1. Go Interview Master Checklist

## Language

- [ ] Variables / constants
- [ ] Basic types
- [ ] Arrays
- [ ] Slices
- [ ] Maps
- [ ] Structs
- [ ] Methods
- [ ] Pointers
- [ ] Interfaces
- [ ] Embedding
- [ ] Type assertions
- [ ] Type switches
- [ ] Functions
- [ ] Closures
- [ ] Defer
- [ ] Panic / recover
- [ ] Errors
- [ ] Generics
- [ ] Reflection
- [ ] Unsafe basics

## Runtime

- [ ] Stack
- [ ] Heap
- [ ] Escape analysis
- [ ] Allocation
- [ ] Garbage collection
- [ ] G-M-P scheduler
- [ ] Goroutine scheduling
- [ ] Preemption
- [ ] Syscalls
- [ ] Network poller
- [ ] CPU profile
- [ ] Heap profile
- [ ] Goroutine profile
- [ ] Mutex profile
- [ ] Block profile

## Concurrency

- [ ] Goroutine
- [ ] Channel
- [ ] Select
- [ ] Mutex
- [ ] RWMutex
- [ ] Atomic
- [ ] WaitGroup
- [ ] Once
- [ ] Cond
- [ ] Pool
- [ ] Context
- [ ] Cancellation
- [ ] Worker pool
- [ ] Pipeline
- [ ] Fan-out / Fan-in
- [ ] Semaphore
- [ ] Backpressure

## Production Service

- [ ] HTTP server
- [ ] HTTP client
- [ ] gRPC / RPC
- [ ] Middleware
- [ ] Connection pooling
- [ ] Timeouts
- [ ] Retries
- [ ] Exponential backoff
- [ ] Jitter
- [ ] Circuit breaker
- [ ] Rate limiting
- [ ] Bulkhead isolation
- [ ] Idempotency
- [ ] Graceful shutdown
- [ ] Health check
- [ ] Readiness
- [ ] Liveness
- [ ] Configuration
- [ ] Secrets
- [ ] Structured logging
- [ ] Metrics
- [ ] Tracing

## Debugging

- [ ] Crash
- [ ] Panic
- [ ] Deadlock
- [ ] Data race
- [ ] Goroutine leak
- [ ] Memory leak / retention
- [ ] CPU spike
- [ ] GC spike
- [ ] Latency spike
- [ ] Connection exhaustion
- [ ] File descriptor exhaustion
- [ ] Retry storm
- [ ] Queue growth
- [ ] OOM

---

# 2. Go Language Fundamentals

## 2.1 Slice

必须理解：

- [ ] Pointer
- [ ] Length
- [ ] Capacity
- [ ] Backing array
- [ ] `append`
- [ ] Reallocation
- [ ] Slice aliasing
- [ ] Full slice expression
- [ ] Sub-slice retention

面试题：

- [ ] Why can appending to one slice affect another slice?
- [ ] Why can a small sub-slice retain a huge backing array?
- [ ] When does `append` allocate?
- [ ] How would you prevent unwanted aliasing?
- [ ] Why is `append` amortized efficient?

Practical challenge：

- [ ] Implement a slice-based ring buffer
- [ ] Diagnose unexpected mutation caused by shared backing arrays
- [ ] Diagnose memory retention caused by sub-slices

---

# 3. Map

- [ ] Map lookup
- [ ] Insert
- [ ] Delete
- [ ] Zero value
- [ ] Nil map
- [ ] Concurrent access
- [ ] Hashing concept
- [ ] Growth / evacuation concept

必须明确：

> A map is not safe for concurrent writes without synchronization.

面试追问：

- [ ] What happens when multiple goroutines write to a map?
- [ ] When would you use `sync.Map`?
- [ ] Why not use `sync.Map` everywhere?
- [ ] Can you take the address of a map element?

Practical：

- [ ] Build thread-safe cache with `map + mutex`
- [ ] Benchmark `map + RWMutex` vs `sync.Map`

---

# 4. Interface

必须真正理解：

- [ ] Interface type
- [ ] Concrete value
- [ ] Dynamic type
- [ ] Dynamic value
- [ ] Type assertion
- [ ] Type switch
- [ ] Nil interface
- [ ] Typed nil
- [ ] Method set
- [ ] Pointer receiver
- [ ] Value receiver

经典问题：

```go
var p *MyError = nil
var err error = p
```

必须知道为什么：

```text
err != nil
```

这是 Go Senior 面试高频坑。

继续准备：

- [ ] Interface pollution
- [ ] Small interfaces
- [ ] Accept interfaces / return concrete types
- [ ] Mockability
- [ ] Dependency inversion

---

# 5. Error Handling

- [ ] `error`
- [ ] Sentinel error
- [ ] Typed error
- [ ] Wrapping
- [ ] `errors.Is`
- [ ] `errors.As`
- [ ] `%w`
- [ ] Error context
- [ ] Retry classification

面试问题：

- [ ] Which errors should be retried?
- [ ] How do you preserve root cause?
- [ ] When is a custom error type useful?
- [ ] Should every layer wrap errors?
- [ ] How do you expose errors over HTTP / RPC?

Production challenge：

> A service retries all errors and suddenly melts the downstream.

需要识别：

- [ ] Retry classification
- [ ] Backoff
- [ ] Jitter
- [ ] Maximum attempts
- [ ] Total deadline
- [ ] Idempotency

---

# 6. Defer / Panic / Recover

- [ ] Defer evaluation timing
- [ ] Defer execution order
- [ ] Named return interaction
- [ ] Panic propagation
- [ ] Recover scope
- [ ] Recover only in deferred function

经典问题：

- [ ] What happens when `panic` occurs in a child goroutine?
- [ ] Can the parent goroutine recover it?
- [ ] Where should panic recovery be used in a server?
- [ ] Should every panic be recovered?

Production：

- [ ] HTTP middleware panic recovery
- [ ] Worker panic recovery
- [ ] Panic metrics
- [ ] Crash vs recover trade-off

---

# 7. Generics

- [ ] Type parameters
- [ ] Constraints
- [ ] Type sets
- [ ] Generic functions
- [ ] Generic types
- [ ] When generics improve code
- [ ] When generics hurt readability

Interview：

- [ ] Why use generics instead of interface?
- [ ] When would you prefer concrete code?
- [ ] Where are generics useful in infrastructure libraries?

Practical：

- [ ] Generic worker queue
- [ ] Generic retry helper
- [ ] Generic cache abstraction
- [ ] Generic pipeline stage

---

# 8. Reflection / Unsafe

## Reflection

- [ ] `reflect.Type`
- [ ] `reflect.Value`
- [ ] Dynamic invocation
- [ ] Performance cost
- [ ] JSON / ORM style use cases

## Unsafe

- [ ] Why unsafe exists
- [ ] Pointer conversion risks
- [ ] Memory layout assumptions
- [ ] GC interaction risks
- [ ] Why normal production code should minimize it

面试目标：

> Understand what it enables and why it is dangerous; do not memorize tricks.

---

# 9. Goroutine

## Fundamentals

- [ ] What is a goroutine?
- [ ] Goroutine stack
- [ ] Stack growth
- [ ] Scheduling
- [ ] Blocking
- [ ] Parking
- [ ] Preemption

## Practical

- [ ] Goroutine per request
- [ ] Worker pool
- [ ] Fan-out / fan-in
- [ ] Background worker
- [ ] Periodic task

## Senior Questions

- [ ] When is goroutine-per-task acceptable?
- [ ] When do you need bounded concurrency?
- [ ] What causes goroutine leaks?
- [ ] How do you detect goroutine leaks?
- [ ] What happens to blocked goroutines?
- [ ] How does cancellation propagate?

## Rare / Production Bugs

- [ ] Goroutine waiting forever on channel
- [ ] Goroutine waiting forever on mutex
- [ ] Goroutine stuck on network read
- [ ] Goroutine blocked because consumer disappeared
- [ ] Background goroutine survives request cancellation
- [ ] Goroutine captures unexpectedly large object graph

---

# 10. Channel

## Fundamentals

- [ ] Unbuffered channel
- [ ] Buffered channel
- [ ] Send
- [ ] Receive
- [ ] Close
- [ ] Nil channel
- [ ] Closed channel behavior
- [ ] Directional channel

必须理解：

```text
nil channel
closed channel
full buffered channel
empty buffered channel
```

这些状态在 `select` 中行为不同。

## Interview Questions

- [ ] Who should close a channel?
- [ ] Is closing a channel required?
- [ ] What happens when sending to a closed channel?
- [ ] What happens when receiving from a closed channel?
- [ ] What happens with a nil channel?
- [ ] Can multiple goroutines close the same channel safely?

## Practical Challenges

- [ ] Build producer/consumer queue
- [ ] Build bounded worker pool
- [ ] Build fan-in
- [ ] Build fan-out
- [ ] Implement cancellation
- [ ] Implement timeout using `select`

---

# 11. Select

- [ ] Multiple cases
- [ ] Blocking behavior
- [ ] Default case
- [ ] Timeout
- [ ] Cancellation
- [ ] Fairness concept
- [ ] Busy loop risk

经典 bug：

```go
for {
    select {
    default:
    }
}
```

需要理解为什么可能造成 CPU spin。

Practical：

- [ ] Timeout wrapper
- [ ] Cancellation-aware worker
- [ ] Multi-source event loop
- [ ] Shutdown coordinator

---

# 12. Mutex / RWMutex / Atomic

## Mutex

- [ ] Critical section
- [ ] Lock ordering
- [ ] Contention
- [ ] Deadlock

## RWMutex

- [ ] Read-heavy workloads
- [ ] Write contention
- [ ] When RWMutex can be worse than Mutex

## Atomic

- [ ] Atomic load/store
- [ ] Compare-and-swap
- [ ] Counters
- [ ] State flags
- [ ] Lock-free thinking

Senior questions：

- [ ] Why not use atomic everywhere?
- [ ] When is a mutex clearer?
- [ ] What is false sharing?
- [ ] How do you measure lock contention?

---

# 13. WaitGroup / Once / Cond / Pool

## WaitGroup

- [ ] Add before goroutine starts
- [ ] Done exactly once
- [ ] Wait semantics
- [ ] Reuse restrictions

经典错误：

- [ ] `Add` after `Wait` races with lifecycle
- [ ] Missing `Done`
- [ ] Double `Done`

## Once

- [ ] One-time initialization
- [ ] Lazy initialization
- [ ] Failure semantics

## Cond

- [ ] Predicate-based waiting
- [ ] Signal
- [ ] Broadcast
- [ ] Spurious-style reasoning / re-check predicate

## Pool

- [ ] Object reuse
- [ ] Allocation reduction
- [ ] When reuse is beneficial
- [ ] Why `sync.Pool` is not a general-purpose cache

---

# 14. Context

必须达到 Senior：

- [ ] Deadline
- [ ] Timeout
- [ ] Cancellation
- [ ] Value propagation
- [ ] Parent / child context
- [ ] `WithCancel`
- [ ] `WithTimeout`
- [ ] `WithDeadline`
- [ ] `WithValue`

## Correct Usage

- [ ] Context should usually be first parameter
- [ ] Do not store context inside long-lived structs casually
- [ ] Do not use context values as generic parameter bags
- [ ] Propagate cancellation to downstream calls

## Interview

- [ ] Why does context matter in distributed services?
- [ ] What happens if downstream ignores cancellation?
- [ ] How do you guarantee all workers stop on shutdown?
- [ ] What is the relation between context deadline and network timeout?

---

# 15. Concurrency Patterns

## Worker Pool

- [ ] Fixed workers
- [ ] Bounded queue
- [ ] Job result
- [ ] Error propagation
- [ ] Cancellation
- [ ] Shutdown

## Fan-out / Fan-in

- [ ] Parallel work
- [ ] Merge results
- [ ] Error handling
- [ ] Ordering

## Pipeline

- [ ] Stage isolation
- [ ] Channel ownership
- [ ] Backpressure
- [ ] Cancellation
- [ ] Error propagation

## Semaphore

- [ ] Limit concurrent requests
- [ ] Avoid resource exhaustion
- [ ] Permit acquisition cancellation

## Singleflight

- [ ] Deduplicate concurrent same-key work
- [ ] Avoid cache stampede
- [ ] Understand when deduplication is safe

---

# 16. Backpressure

这是 Senior Go 非常重要的概念。

必须理解：

```text
Producer rate > Consumer rate
        ↓
Queue growth
        ↓
Memory growth
        ↓
Latency growth
        ↓
Failure amplification
```

准备：

- [ ] Bounded queue
- [ ] Blocking producer
- [ ] Drop strategy
- [ ] Load shedding
- [ ] Priority queue
- [ ] Adaptive concurrency

面试题：

> What happens when downstream is 10x slower than upstream?

> How do you prevent unbounded memory growth?

> Would you block, buffer, drop, or shed load?

---

# 17. Memory

## Allocation

- [ ] Stack allocation
- [ ] Heap allocation
- [ ] Escape analysis
- [ ] Temporary objects
- [ ] Allocation rate

## Slice / Map Memory

- [ ] Backing array retention
- [ ] Capacity growth
- [ ] Large map retention
- [ ] Reuse vs clear

## Memory Retention

经典场景：

```text
1GB byte slice
   ↓
small sub-slice kept in cache
   ↓
1GB backing array stays alive
```

必须会识别。

## Interview

- [ ] How do you reduce allocations?
- [ ] How do you confirm allocation is the bottleneck?
- [ ] What does escape analysis tell you?
- [ ] Why can pooling sometimes make performance worse?

---

# 18. Garbage Collection

- [ ] Marking
- [ ] Sweeping concept
- [ ] Concurrent GC concept
- [ ] GC cycles
- [ ] Allocation pressure
- [ ] GC CPU cost
- [ ] Heap goal concept
- [ ] `GOGC`

不要死背 GC 内部数字。

必须理解：

> More allocation → more GC work → potentially higher CPU / latency.

Production investigation：

- [ ] Observe heap size
- [ ] Observe allocation rate
- [ ] Observe GC pauses / assist behavior
- [ ] Compare CPU profile with heap profile
- [ ] Identify allocation hotspots

---

# 19. Scheduler / G-M-P

必须理解概念：

```text
G = Goroutine
M = OS thread
P = Processor / scheduling resource
```

准备：

- [ ] Runnable goroutine
- [ ] Local run queue
- [ ] Work stealing
- [ ] Blocking syscall
- [ ] Network poller
- [ ] GOMAXPROCS
- [ ] Preemption

面试问题：

- [ ] What happens when a goroutine blocks in a syscall?
- [ ] Why can millions of goroutines be possible?
- [ ] What is the role of P?
- [ ] Why does GOMAXPROCS matter?

---

# 20. HTTP Server

## Basics

- [ ] `net/http`
- [ ] Handler
- [ ] Middleware
- [ ] Request lifecycle
- [ ] Response lifecycle
- [ ] HTTP status
- [ ] Keep-alive

## Production

- [ ] Read timeout
- [ ] Write timeout
- [ ] Idle timeout
- [ ] Header timeout
- [ ] Max body size
- [ ] Connection limit
- [ ] Graceful shutdown

经典问题：

> What happens if a client connects and never finishes sending the request body?

需要考虑：

- [ ] Read timeout
- [ ] Resource exhaustion
- [ ] Slowloris-style behavior

---

# 21. HTTP Client

非常容易被忽略。

必须准备：

- [ ] Client reuse
- [ ] Transport reuse
- [ ] Connection pooling
- [ ] Keep-alive
- [ ] Timeout
- [ ] Context
- [ ] Response body close
- [ ] Retry
- [ ] Redirect behavior

经典 Production Bug：

> HTTP client requests are made repeatedly with a new client/transport every time.

需要能够解释为什么可能导致：

- [ ] Connection churn
- [ ] Socket pressure
- [ ] Lower performance
- [ ] Resource exhaustion

---

# 22. RPC / gRPC

- [ ] Unary RPC
- [ ] Streaming RPC
- [ ] Deadline
- [ ] Cancellation
- [ ] Interceptors
- [ ] Metadata
- [ ] Retries
- [ ] Load balancing
- [ ] Health checking

面试：

- [ ] How do deadlines propagate across services?
- [ ] Where should retry live?
- [ ] How do you prevent retry amplification?
- [ ] What makes an RPC idempotent?

---

# 23. Connection Pool

必须理解：

```text
Requests
   ↓
Pool
   ↓
Connections
   ↓
Downstream
```

问题：

- [ ] Max connections
- [ ] Idle connections
- [ ] Connection lifetime
- [ ] Queueing
- [ ] Timeout
- [ ] Exhaustion

Production scenario：

> Service latency increases even though CPU is low.

Possible cause：

> Connection pool exhausted.

必须会检查：

- [ ] Pool utilization
- [ ] Wait time
- [ ] Downstream latency
- [ ] Connection churn

---

# 24. Timeout

绝不能只设置一个全局 timeout。

需要区分：

- [ ] Request deadline
- [ ] Queue wait timeout
- [ ] Connection timeout
- [ ] TLS handshake timeout
- [ ] Read timeout
- [ ] Write timeout
- [ ] Downstream RPC timeout
- [ ] Overall deadline

核心原则：

```text
Overall deadline
      ↓
Remaining budget
      ↓
Downstream calls
```

问题：

> If request deadline is 2 seconds, would every downstream call get 2 seconds?

答案通常不是机械地全部设 2 秒。

需要做 deadline budget 分配。

---

# 25. Retry / Backoff / Jitter

## Retryable

- [ ] Temporary network failures
- [ ] Explicit transient errors
- [ ] 429 / rate limits
- [ ] Some 5xx

## Non-retryable

- [ ] Validation error
- [ ] Authentication error
- [ ] Authorization error
- [ ] Deterministic business failure

## Backoff

- [ ] Fixed
- [ ] Exponential
- [ ] Capped exponential
- [ ] Jitter

## Production Failure

```text
Downstream slow
      ↓
Timeout
      ↓
Retry
      ↓
More traffic
      ↓
Downstream even slower
      ↓
Retry storm
```

必须能讲清楚如何打断这个 feedback loop。

---

# 26. Circuit Breaker / Bulkhead

## Circuit Breaker

- [ ] Closed
- [ ] Open
- [ ] Half-open
- [ ] Failure threshold
- [ ] Recovery

## Bulkhead

- [ ] Isolate expensive work
- [ ] Per-dependency concurrency
- [ ] Prevent one downstream from consuming all workers

经典题：

> One dependency becomes slow. How do you prevent it from taking down the entire service?

答案应该想到：

- [ ] Timeout
- [ ] Circuit breaker
- [ ] Bulkhead
- [ ] Concurrency limit
- [ ] Load shedding
- [ ] Fallback

---

# 27. Rate Limiting

准备：

- [ ] Token bucket
- [ ] Leaky bucket
- [ ] Fixed window
- [ ] Sliding window
- [ ] Per-user
- [ ] Per-IP
- [ ] Per-tenant
- [ ] Global limit

Go challenge：

```text
Allow()
Wait(ctx)
```

然后增加：

- [ ] Concurrent safety
- [ ] Burst
- [ ] Distributed Redis version
- [ ] Clock behavior
- [ ] Failure semantics

---

# 28. Idempotency

必须理解：

> Retry without idempotency can create duplicate side effects.

Go service 场景：

```text
HTTP request
 ↓
Kafka publish
 ↓
Database write
```

如果 HTTP 请求 timeout，但 server 已经写成功：

- [ ] What happens on retry?
- [ ] How do you detect duplicate request?
- [ ] Where is idempotency key stored?
- [ ] How long is it retained?

---

# 29. Graceful Shutdown

必须能够从头实现。

流程：

```text
SIGTERM
  ↓
Stop accepting new work
  ↓
Cancel background work
  ↓
Drain in-flight work
  ↓
Flush buffers
  ↓
Close consumers / producers
  ↓
Close DB / connections
  ↓
Exit
```

必须考虑：

- [ ] Timeout for shutdown
- [ ] Stuck goroutine
- [ ] In-flight requests
- [ ] In-flight Kafka messages
- [ ] Buffered logs
- [ ] Metrics export

---

# 30. Observability

## Metrics

必须会设计：

- [ ] Request count
- [ ] Error rate
- [ ] Latency
- [ ] p50
- [ ] p95
- [ ] p99
- [ ] In-flight requests
- [ ] Queue depth
- [ ] Worker utilization
- [ ] Retry count
- [ ] Timeout count
- [ ] Circuit breaker state

## Logs

- [ ] Structured logs
- [ ] Correlation ID
- [ ] Request ID
- [ ] Error context
- [ ] Sampling
- [ ] PII concerns

## Tracing

- [ ] Trace
- [ ] Span
- [ ] Context propagation
- [ ] Downstream spans
- [ ] Error tagging

---

# 31. pprof / Profiling

必须真正操作过，而不是知道名词。

## CPU

- [ ] Identify hot functions
- [ ] CPU profile
- [ ] Compare before / after

## Heap

- [ ] Allocation hotspot
- [ ] Retained memory
- [ ] Large objects
- [ ] Cache growth

## Goroutine

- [ ] Blocked goroutines
- [ ] Goroutine leak
- [ ] Channel wait
- [ ] Mutex wait
- [ ] Network wait

## Mutex

- [ ] Lock contention
- [ ] Hot mutex
- [ ] Critical section too large

## Block

- [ ] Blocking operations
- [ ] Channel blocking
- [ ] Synchronization bottlenecks

## Practical Exercises

- [ ] Create CPU hotspot and find it
- [ ] Create memory growth and find it
- [ ] Create goroutine leak and find it
- [ ] Create lock contention and find it

---

# 32. Race Detector

必须熟练：

```text
go test -race ./...
```

训练：

- [ ] Concurrent map access
- [ ] Shared counter
- [ ] Shared slice
- [ ] Loop variable capture
- [ ] Unsafe state publication

面试题：

> Race detector finds a race. What do you do next?

要求：

- [ ] Reproduce
- [ ] Identify conflicting accesses
- [ ] Understand ownership
- [ ] Choose mutex / atomic / channel
- [ ] Add regression test

---

# 33. Deadlock

经典模式：

```text
G1: Lock(A) → Lock(B)
G2: Lock(B) → Lock(A)
```

其他模式：

- [ ] Channel cycle
- [ ] Missing receiver
- [ ] Missing sender
- [ ] WaitGroup wait forever
- [ ] Closing wrong channel
- [ ] Nested lock ordering

排查：

- [ ] Goroutine dump
- [ ] Mutex profile
- [ ] Lock ordering review
- [ ] Reproduce under load

---

# 34. Goroutine Leak

必须能列举至少 8 种：

- [ ] Blocked send
- [ ] Blocked receive
- [ ] Forgotten cancellation
- [ ] Infinite ticker loop
- [ ] Network read without deadline
- [ ] Worker waiting on never-closed channel
- [ ] Retry loop without exit condition
- [ ] Background task outliving request

排查：

- [ ] `runtime.NumGoroutine`
- [ ] pprof goroutine dump
- [ ] Compare stack patterns
- [ ] Check lifecycle ownership

---

# 35. Memory Retention / Leak

区分：

> “真正 leak” 和 “对象仍然被引用所以不能回收”。

案例：

- [ ] Sub-slice retention
- [ ] Map never cleaned
- [ ] Cache without eviction
- [ ] Goroutine retaining request data
- [ ] Closure retaining huge object
- [ ] Global singleton retaining data
- [ ] Buffer reuse mistakes

---

# 36. CPU / Latency Debugging

## Incident

> p99 suddenly increases from 50ms to 2 seconds.

流程：

```text
Metrics
 ↓
Trace
 ↓
CPU / Memory
 ↓
GC
 ↓
Goroutines
 ↓
Locks
 ↓
Downstream
 ↓
Connection pool
 ↓
Retry
```

不能一上来就：

> “Maybe GC.”

必须先建立 evidence。

---

# 37. Production Incident Scenarios

## Scenario 1 — CPU 100%

检查：

- [ ] CPU profile
- [ ] Infinite loop
- [ ] Busy select default
- [ ] Serialization
- [ ] Regex
- [ ] Compression
- [ ] Excessive allocations
- [ ] Logging
- [ ] Lock spinning / contention

## Scenario 2 — Memory constantly grows

- [ ] Heap profile
- [ ] Retained objects
- [ ] Cache
- [ ] Slice retention
- [ ] Map
- [ ] Goroutine stacks
- [ ] Buffers

## Scenario 3 — OOM after traffic spike

- [ ] In-flight requests
- [ ] Queue growth
- [ ] Connection count
- [ ] Request bodies
- [ ] Retry amplification
- [ ] Unbounded concurrency

## Scenario 4 — Latency high but CPU low

- [ ] Waiting on locks
- [ ] Network
- [ ] Connection pool
- [ ] Queue
- [ ] Downstream service
- [ ] Disk / filesystem
- [ ] Rate limit

## Scenario 5 — Error rate low but user complaints high

- [ ] Timeouts
- [ ] High p99
- [ ] Partial failures
- [ ] Retry delays
- [ ] Queue waiting
- [ ] Correctness issues

## Scenario 6 — Deployment causes memory spike

- [ ] Configuration change
- [ ] Traffic pattern
- [ ] Different code path
- [ ] Cache warmup
- [ ] Concurrency
- [ ] Allocation regression

---

# 38. HTTP / RPC Production Bugs

准备真实代码审查题：

### Bug Pattern A

每个 request 新建 HTTP client。

检查：

- [ ] Transport reuse
- [ ] Connection pooling
- [ ] Socket churn

### Bug Pattern B

忘记关闭 response body。

检查：

- [ ] Resource leak
- [ ] Connection reuse

### Bug Pattern C

没有 timeout。

检查：

- [ ] Stuck requests
- [ ] Goroutine growth
- [ ] Connection exhaustion

### Bug Pattern D

所有错误都 retry。

检查：

- [ ] Retry storm
- [ ] Duplicate side effect
- [ ] Downstream overload

---

# 39. Kafka + Go

这是你的重点。

## Producer

- [ ] Batching
- [ ] Compression
- [ ] Acks
- [ ] Idempotence
- [ ] Retry
- [ ] Partitioning
- [ ] Ordering

## Consumer

- [ ] Poll loop
- [ ] Offset
- [ ] Commit
- [ ] Rebalance
- [ ] Consumer lag
- [ ] Worker pool
- [ ] Backpressure
- [ ] Retry
- [ ] DLQ

## Senior Questions

> What happens if processing succeeds but offset commit fails?

> What happens if offset commit succeeds but processing side effect is not durable?

> How do you avoid duplicate side effects?

> How do you preserve ordering while using multiple workers?

> How do you shut down a consumer gracefully?

---

# 40. Go + Database

## MySQL / PostgreSQL style services

- [ ] Connection pool
- [ ] Transaction
- [ ] Context cancellation
- [ ] Query timeout
- [ ] Prepared statements
- [ ] Batch insert
- [ ] Pagination
- [ ] Connection exhaustion

## Production Scenarios

- [ ] DB latency increases
- [ ] Pool exhausted
- [ ] Long transaction
- [ ] Deadlock in DB
- [ ] N+1 queries
- [ ] Retry causes duplicate writes

---

# 41. Go + Cache

## Local Cache

- [ ] Mutex
- [ ] TTL
- [ ] LRU
- [ ] Eviction
- [ ] Stampede
- [ ] Singleflight

## Distributed Cache

- [ ] Redis
- [ ] Timeout
- [ ] Retry
- [ ] Serialization
- [ ] Hot keys
- [ ] Cache invalidation

经典问题：

> Cache is usually easy; cache invalidation and failure semantics are not.

---

# 42. Go + Object Storage / Data Platform

结合你的 AWS / Data Platform 背景准备：

```text
S3
 ↓
Go service
 ↓
Queue
 ↓
Workers
 ↓
Parser
 ↓
Metadata
 ↓
Vector / Search
```

问题：

- [ ] Huge object
- [ ] Partial download
- [ ] Retry
- [ ] Range read
- [ ] Checksum
- [ ] Duplicate processing
- [ ] Idempotency
- [ ] Concurrent processing
- [ ] Memory limit

---

# 43. Go Service Architecture

一个成熟 Go 服务至少应该能讨论：

```text
HTTP / RPC
   ↓
Handler
   ↓
Service
   ↓
Domain Logic
   ↓
Repository / Client
   ↓
DB / Kafka / External APIs
```

但不要机械套：

> handler / service / repository

必须解释为什么这么拆。

面试：

- [ ] Where should validation live?
- [ ] Where should retry live?
- [ ] Where should transaction boundaries live?
- [ ] Where should metrics be emitted?
- [ ] How do you avoid circular dependencies?
- [ ] How do you keep interfaces small?

---

# 44. Configuration / Dependency Injection

- [ ] Config loading
- [ ] Environment variables
- [ ] Config validation
- [ ] Secret handling
- [ ] Dependency injection
- [ ] Constructor functions
- [ ] Lifecycle management

Senior questions：

- [ ] How do you test without real Kafka / DB?
- [ ] Where are dependencies created?
- [ ] Who owns their shutdown?
- [ ] How do you avoid global state?

---

# 45. Testing

## Unit

- [ ] Table-driven tests
- [ ] Edge cases
- [ ] Error paths
- [ ] Context cancellation
- [ ] Concurrency tests

## Integration

- [ ] DB
- [ ] Kafka
- [ ] HTTP
- [ ] Object storage

## Race

- [ ] `go test -race`

## Benchmark

- [ ] `go test -bench`
- [ ] Benchmark allocation
- [ ] Benchmark contention

## Failure Injection

- [ ] Downstream timeout
- [ ] Kafka unavailable
- [ ] DB unavailable
- [ ] Partial failure
- [ ] Worker crash

---

# 46. Benchmarking

必须知道：

- [ ] Throughput
- [ ] Latency
- [ ] Allocation count
- [ ] Allocation bytes
- [ ] Contention

不要只看：

> “This version is faster.”

应该问：

> Faster under what workload?

需要比较：

- [ ] Small input
- [ ] Large input
- [ ] Concurrent input
- [ ] Read-heavy
- [ ] Write-heavy
- [ ] Cold cache
- [ ] Warm cache

---

# 47. Practical Coding Challenges — Core Set

必须手写：

- [ ] Worker pool
- [ ] Fan-in / fan-out
- [ ] Pipeline
- [ ] Rate limiter
- [ ] TTL cache
- [ ] LRU cache
- [ ] Singleflight-like deduplication
- [ ] Retry framework
- [ ] Circuit breaker
- [ ] Priority queue
- [ ] Bounded queue
- [ ] Delayed queue
- [ ] Concurrent map
- [ ] Pub/sub
- [ ] Connection pool
- [ ] Graceful shutdown coordinator

---

# 48. Practical Coding Challenge — Worker Pool

Requirements：

```text
Submit(job)
Start()
Stop()
Wait()
```

必须支持：

- [ ] Worker count
- [ ] Queue capacity
- [ ] Backpressure
- [ ] Context
- [ ] Error collection
- [ ] Panic recovery
- [ ] Shutdown
- [ ] No job loss during normal shutdown
- [ ] No goroutine leak

Follow-ups：

- [ ] How do you prioritize jobs?
- [ ] How do you cancel one job?
- [ ] How do you cancel all jobs?
- [ ] How do you expose metrics?
- [ ] How do you make it distributed?

---

# 49. Practical Coding Challenge — Rate Limiter

Requirements：

- [ ] Token bucket
- [ ] Thread safety
- [ ] Burst support
- [ ] Context-aware wait
- [ ] Metrics

Follow-ups：

- [ ] Distributed implementation
- [ ] Clock issues
- [ ] Redis unavailable
- [ ] Hot tenant

---

# 50. Practical Coding Challenge — TTL Cache

Requirements：

- [ ] `Get`
- [ ] `Set`
- [ ] `Delete`
- [ ] TTL
- [ ] Expiration
- [ ] Concurrent safety
- [ ] Eviction

Follow-ups：

- [ ] LRU
- [ ] Stampede protection
- [ ] Distributed invalidation
- [ ] Memory pressure

---

# 51. Practical Coding Challenge — Retry Library

Requirements：

- [ ] Context-aware
- [ ] Retry predicate
- [ ] Backoff
- [ ] Jitter
- [ ] Max attempts
- [ ] Total deadline

Follow-ups：

- [ ] Idempotency
- [ ] Rate limit response
- [ ] Server-provided retry-after
- [ ] Observability

---

# 52. Practical Coding Challenge — Concurrent Pipeline

要求：

```text
Producer
 ↓
N workers
 ↓
Transformer
 ↓
Writer
```

问题：

- [ ] How do you stop it?
- [ ] What if one stage is slow?
- [ ] What if output order matters?
- [ ] What if one worker panics?
- [ ] What if downstream fails?

---

# 53. Complex Project A — Distributed Job Scheduler

## MVP

```text
API
 ↓
Scheduler
 ↓
Queue
 ↓
Workers
```

## Phase 2

- [ ] Persistence
- [ ] Retry
- [ ] Timeout
- [ ] Cancellation
- [ ] Task state

## Phase 3

- [ ] Multiple scheduler instances
- [ ] Leader election
- [ ] Failover
- [ ] Duplicate execution protection
- [ ] Idempotency

## Phase 4

- [ ] Metrics
- [ ] Tracing
- [ ] Dashboard
- [ ] Load test
- [ ] Chaos test

---

# 54. Complex Project B — High Performance Gateway

功能：

- [ ] Routing
- [ ] Auth
- [ ] Rate limiting
- [ ] Retry
- [ ] Circuit breaker
- [ ] Connection pooling
- [ ] Observability

Load scenarios：

- [ ] Normal traffic
- [ ] Burst traffic
- [ ] Slow backend
- [ ] Backend down
- [ ] Partial packet loss
- [ ] Retry storm

---

# 55. Complex Project C — Kafka Processing Service

```text
Kafka
 ↓
Consumer
 ↓
Worker Pool
 ↓
Processor
 ↓
DB / Search / Object Storage
```

必须研究：

- [ ] Partition ordering
- [ ] Offset commit
- [ ] Retry
- [ ] DLQ
- [ ] Rebalance
- [ ] Backpressure
- [ ] Idempotency
- [ ] Graceful shutdown

---

# 56. Complex Project D — Document Processing Service

这个项目最适合你的简历主线。

```text
S3
 ↓
Queue
 ↓
Go workers
 ↓
Document parser
 ↓
Passage extractor
 ↓
Metadata generator
 ↓
Embedding
 ↓
Search / Vector DB
```

必须加入：

- [ ] Large files
- [ ] Retry
- [ ] Partial processing
- [ ] Duplicate document
- [ ] Idempotency
- [ ] Memory limits
- [ ] Backpressure
- [ ] Observability
- [ ] Cost control

---

# 57. Rare but Valuable Go Questions

这些问题不一定每次面，但 Senior 层应该有准备：

- [ ] Typed nil interface
- [ ] Slice aliasing
- [ ] Slice backing-array retention
- [ ] Loop variable capture
- [ ] Concurrent map panic
- [ ] Nil channel behavior
- [ ] Closed channel behavior
- [ ] Send on closed channel
- [ ] WaitGroup misuse
- [ ] Context cancellation not propagated
- [ ] `time.Ticker` lifecycle
- [ ] HTTP response body leak
- [ ] Unbounded goroutine creation
- [ ] Unbounded channel / queue
- [ ] `select default` CPU spin
- [ ] Mutex lock ordering deadlock
- [ ] Retry storm
- [ ] Connection pool exhaustion
- [ ] Global state contamination in tests
- [ ] Data race hidden by low traffic
- [ ] Object retention through closure
- [ ] Oversized buffers retained
- [ ] False sharing
- [ ] Excessive serialization cost
- [ ] Logging itself becoming a bottleneck

---

# 58. Go Code Review Interview

给自己随机找一段 30–100 行 Go 代码，必须回答：

## Correctness

- [ ] Is it correct?
- [ ] What edge cases are missing?

## Concurrency

- [ ] Race?
- [ ] Deadlock?
- [ ] Goroutine leak?
- [ ] Unbounded concurrency?

## Lifecycle

- [ ] Who owns this goroutine?
- [ ] Who closes this channel?
- [ ] Who cancels this context?
- [ ] Who closes this resource?

## Performance

- [ ] Unnecessary allocation?
- [ ] Lock contention?
- [ ] Serialization overhead?
- [ ] Repeated network calls?

## Production

- [ ] Timeout?
- [ ] Retry?
- [ ] Idempotency?
- [ ] Observability?
- [ ] Graceful shutdown?

---

# 59. Go Production Incident Interview

训练到可以直接回答：

> “Tell me about a difficult production issue in Go.”

推荐回答框架：

```text
Context
 ↓
Symptom
 ↓
Impact
 ↓
Initial hypotheses
 ↓
Evidence
 ↓
Root cause
 ↓
Fix
 ↓
Validation
 ↓
Prevention
```

必须避免：

> “I restarted the service and it went away.”

应该重点讲：

> how you narrowed the problem down.

---

# 60. Senior Go Questions — Architecture

- [ ] How would you structure a large Go service?
- [ ] How do you decide package boundaries?
- [ ] Where should interfaces live?
- [ ] How do you avoid circular dependencies?
- [ ] How do you manage dependency lifecycle?
- [ ] How do you test external dependencies?
- [ ] How do you build for graceful shutdown?
- [ ] How do you expose metrics?
- [ ] How do you propagate context?
- [ ] How do you prevent one dependency from taking down the service?

---

# 61. Senior Go Questions — Performance

- [ ] CPU is 90%. What first?
- [ ] CPU is low but latency is high. Why?
- [ ] Memory keeps increasing. How investigate?
- [ ] GC is consuming significant CPU. What next?
- [ ] Goroutines increase continuously. What next?
- [ ] Throughput drops after a new release. How compare?
- [ ] p99 increases while p50 stays flat. What does that suggest?
- [ ] What if only one tenant is slow?
- [ ] What if lock contention affects only one endpoint?

---

# 62. Senior Go Questions — Reliability

- [ ] How do you make a Go service resilient?
- [ ] How do you handle partial failure?
- [ ] How do you handle downstream overload?
- [ ] How do you avoid retry storms?
- [ ] How do you guarantee request deadlines?
- [ ] How do you drain traffic during deployment?
- [ ] How do you preserve work during shutdown?
- [ ] How do you make side effects idempotent?

---

# 63. Senior Go Questions — Distributed Systems

- [ ] How do you implement a distributed lock?
- [ ] How do you implement a distributed rate limiter?
- [ ] How do you guarantee at-least-once processing?
- [ ] How do you deal with duplicate messages?
- [ ] How do you preserve ordering?
- [ ] How do you recover after worker failure?
- [ ] How do you persist task state?
- [ ] How do you make a scheduler highly available?

---

# 64. Interview Follow-up Ladder

任何一个 Go 问题，都训练成这种深度：

```text
What is it?
   ↓
How does it work?
   ↓
Why use it?
   ↓
What can go wrong?
   ↓
How do you test it?
   ↓
How do you monitor it?
   ↓
How does it behave under load?
   ↓
How does it fail?
   ↓
How do you recover?
   ↓
How would you redesign it?
```

例如：

> Worker Pool

不要只会：

> “It limits concurrency.”

需要继续：

> Why bounded concurrency?

> What happens when the queue is full?

> How do you cancel one job?

> How do you cancel all jobs?

> How do you prevent leaks?

> How do you measure utilization?

> How do you distribute it?

---

# 65. Go Interview Study Plan

## Phase 1 — 7 Days: Language + Concurrency

- [ ] Slice
- [ ] Map
- [ ] Interface
- [ ] Error
- [ ] Defer
- [ ] Goroutine
- [ ] Channel
- [ ] Select
- [ ] Mutex
- [ ] Atomic
- [ ] WaitGroup
- [ ] Context

每天：

- [ ] 1–2 concepts
- [ ] 2 small code exercises
- [ ] 1 bug analysis

## Phase 2 — 7 Days: Runtime + Performance

- [ ] Escape analysis
- [ ] GC
- [ ] Scheduler
- [ ] pprof
- [ ] Race detector
- [ ] Benchmark

实践：

- [ ] CPU hotspot
- [ ] Memory growth
- [ ] Goroutine leak
- [ ] Lock contention

## Phase 3 — 7–10 Days: Production Go

- [ ] HTTP server
- [ ] HTTP client
- [ ] RPC
- [ ] Timeout
- [ ] Retry
- [ ] Rate limit
- [ ] Circuit breaker
- [ ] Idempotency
- [ ] Graceful shutdown
- [ ] Observability

## Phase 4 — 10–14 Days: Practical Coding

- [ ] Worker pool
- [ ] Pipeline
- [ ] Cache
- [ ] Rate limiter
- [ ] Retry library
- [ ] Scheduler
- [ ] Concurrent queue

## Phase 5 — 2–3 Weeks: Complex Service

选一个：

- [ ] Distributed Job Scheduler
- [ ] Kafka Processing Service
- [ ] High Performance Gateway
- [ ] Document Processing Service

必须有：

- [ ] Tests
- [ ] Benchmarks
- [ ] Metrics
- [ ] Tracing
- [ ] Failure injection
- [ ] Load test
- [ ] README architecture

## Phase 6 — Interview Mode

每周：

- [ ] 2 coding interviews
- [ ] 1 Go practical coding
- [ ] 1 production debugging
- [ ] 1 system design
- [ ] 1 code review

---

# 66. Final Go Readiness Checklist

## Language

- [ ] Go fundamentals strong
- [ ] Slice / map / interface no major gaps
- [ ] Error handling natural

## Concurrency

- [ ] Goroutine
- [ ] Channel
- [ ] Mutex
- [ ] Atomic
- [ ] Context
- [ ] Worker pool
- [ ] Pipeline
- [ ] Backpressure

## Runtime

- [ ] GC
- [ ] Scheduler
- [ ] Escape analysis
- [ ] pprof
- [ ] Race detector

## Production

- [ ] HTTP
- [ ] RPC
- [ ] Timeout
- [ ] Retry
- [ ] Rate limiting
- [ ] Circuit breaker
- [ ] Idempotency
- [ ] Shutdown
- [ ] Observability

## Debugging

- [ ] Deadlock
- [ ] Race
- [ ] Goroutine leak
- [ ] Memory retention
- [ ] CPU spike
- [ ] Latency spike
- [ ] Connection exhaustion

## Engineering

- [ ] Practical coding
- [ ] Code review
- [ ] Performance optimization
- [ ] Failure injection
- [ ] Load testing
- [ ] Distributed systems bridge

## Interview

- [ ] Explain design choices
- [ ] Discuss trade-offs
- [ ] Diagnose failure modes
- [ ] Explain how to measure improvements
- [ ] Admit uncertainty accurately
- [ ] Think aloud without rambling

---

# 67. The Standard I Want to Reach

最终不是：

> “我背过很多 Go 面试题。”

而应该是：

> “Give me a Go service, a production incident, or a concurrency problem, and I can reason about it from code level to runtime level to system level.”

对应能力：

```text
Go syntax
   ↓
Go runtime
   ↓
Concurrency
   ↓
Production service
   ↓
Failure modes
   ↓
Observability
   ↓
Performance
   ↓
Distributed systems
   ↓
Senior engineering judgment
```

最终目标：

> Go 不只是简历上的语言，而是你进入 Senior Distributed Systems / Data Platform / AI Infrastructure 岗位时最强的工程能力之一。
