# Go / Golang Senior Backend & Distributed Systems 面试准备 Checklist

> **目标岗位**
>
> - Senior Backend Engineer
> - Senior Go Engineer
> - Senior Distributed Systems Engineer
> - Senior Data Infrastructure Engineer
> - Streaming / Data Platform Engineer
> - AI Infrastructure / Agent Platform Engineer
>
> **目标面试市场**
>
> - Germany
> - Netherlands
> - New Zealand
> - Australia
> - European remote / international teams
>
> **核心目标**
>
> 不是“把 Go 语法重新过一遍”。
>
> 而是达到：
>
> > **能够设计、实现、调试、优化和解释一个真实生产环境中的 Go Service。**
>
> 面试官最终应该相信：
>
> > “这个人不仅会写 Go，而且遇到 production incident、并发问题、性能问题、网络问题、数据库问题、消息堆积、资源耗尽时，知道怎么定位和解决。”
>
> ---
>
> ## 0. 总体能力模型
>
> Go 准备分成 8 个层级：
>
> ```text
> Level 1  Go Language
>       ↓
> Level 2  Go Runtime / Memory / Scheduler
>       ↓
> Level 3  Concurrency
>       ↓
> Level 4  Network / IO / Storage
>       ↓
> Level 5  Production Service Engineering
>       ↓
> Level 6  Performance / Debugging / Observability
>       ↓
> Level 7  Distributed Systems
>       ↓
> Level 8  Real-world Architecture / Incident / Design
> ```
>
> 最终目标不是：
>
> > “我知道 channel 有 buffered / unbuffered。”
>
> 而是：
>
> > “为什么这个服务会出现 goroutine 数量持续上涨？为什么 p99 在高峰期恶化？为什么连接池耗尽？为什么一个 Kafka consumer lag 会导致整个服务 OOM？为什么把 worker 数从 100 调到 1000 后吞吐反而下降？”
>
> ---
>
> # 1. 第一阶段：Go Language Fundamentals
>
> ## 1.1 Basic Syntax
>
> - [ ] package
> - [ ] import
> - [ ] variable
> - [ ] constant
> - [ ] iota
> - [ ] pointer
> - [ ] struct
> - [ ] method
> - [ ] function
> - [ ] multiple return values
> - [ ] named return
> - [ ] defer
> - [ ] panic
> - [ ] recover
> - [ ] error
> - [ ] interface
> - [ ] type assertion
> - [ ] type switch
> - [ ] embedding
> - [ ] package visibility
> - [ ] zero value
>
> ### 必须达到
>
> - [ ] 不查语法也能写
> - [ ] 能解释每种 feature 的 trade-off
> - [ ] 不依赖 IDE 自动补全才能完成基本 coding
>
> ---
>
> # 2. Slice / Map / String / Array
>
> ## Slice
>
> - [ ] len
> - [ ] cap
> - [ ] append
> - [ ] reslicing
> - [ ] slice sharing
> - [ ] underlying array
> - [ ] capacity growth
> - [ ] copy
> - [ ] nil slice
> - [ ] empty slice
> - [ ] memory retention
>
> ### 面试题
>
> ```text
> Why can a small slice keep a huge amount of memory alive?
>
> What happens when append exceeds capacity?
>
> Does modifying a subslice modify the original slice?
>
> What is the difference between nil slice and empty slice?
>
> How would you safely return a slice without exposing internal mutable storage?
> ```
>
> ---
>
> ## Map
>
> - [ ] hash map
> - [ ] key constraints
> - [ ] zero value lookup
> - [ ] delete
> - [ ] iteration
> - [ ] map growth
> - [ ] map concurrency safety
> - [ ] concurrent read/write problem
>
> ### Practical
>
> - [ ] 如何实现 concurrent map？
> - [ ] mutex vs RWMutex
> - [ ] sharded map
> - [ ] sync.Map
> - [ ] immutable snapshot
>
> ---
>
> ## String / []byte
>
> - [ ] string immutable
> - [ ] []byte mutable
> - [ ] conversion cost
> - [ ] allocation
> - [ ] UTF-8
> - [ ] rune
> - [ ] byte
> - [ ] string builder
> - [ ] bytes.Buffer
>
> ### Performance
>
> - [ ] fmt.Sprintf vs strconv
> - [ ] repeated string conversion
> - [ ] buffer reuse
> - [ ] allocation reduction
>
> ---
>
> # 3. Interface
>
> 这是 Go 面试中非常容易被问深的部分。
>
> - [ ] interface representation
> - [ ] dynamic type
> - [ ] dynamic value
> - [ ] nil interface
> - [ ] typed nil
> - [ ] pointer receiver
> - [ ] value receiver
> - [ ] interface satisfaction
> - [ ] implicit implementation
> - [ ] interface embedding
>
> ## 必须写过
>
> ```go
> var x interface{} = (*MyType)(nil)
>
> fmt.Println(x == nil)
> ```
>
> 能解释为什么结果不是直觉上的 `true`。
>
> ### 必须能回答
>
> - [ ] Why is `interface != nil` sometimes surprising?
> - [ ] When should you use an interface?
> - [ ] Why can excessive interfaces make a Go codebase harder to maintain?
> - [ ] Why is a pointer to an interface usually unnecessary?
> - [ ] When should a method use pointer receiver?
> - [ ] What happens when a value implements an interface through pointer receiver methods?
>
> ---
>
> # 4. Error Handling
>
> ## 基础
>
> - [ ] error interface
> - [ ] errors.New
> - [ ] fmt.Errorf
> - [ ] `%w`
> - [ ] errors.Is
> - [ ] errors.As
> - [ ] sentinel error
> - [ ] custom error type
> - [ ] wrapping
> - [ ] error chain
>
> ## Senior level
>
> 必须理解：
>
> ```text
> Error creation
>      ↓
> Error propagation
>      ↓
> Error classification
>      ↓
> Error logging
>      ↓
> Error mapping
>      ↓
> Retry / fallback / alerting
> ```
>
> ### 面试问题
>
> - [ ] When should you wrap an error?
> - [ ] When should you expose a concrete error type?
> - [ ] Where should errors be logged?
> - [ ] Why is logging the same error at every layer bad?
> - [ ] Which errors are retryable?
> - [ ] Which errors are fatal?
> - [ ] How do you preserve error context across RPC boundaries?
>
> ---
>
> # 5. Generics
>
> - [ ] type parameters
> - [ ] constraints
> - [ ] comparable
> - [ ] generic functions
> - [ ] generic types
> - [ ] interface constraints
> - [ ] when generics improve code
> - [ ] when generics make code harder to understand
>
> ### Senior原则
>
> 不要为了“使用 generics”而使用 generics。
>
> 能解释：
>
> > “Why did you use generics instead of interface?”
>
> 或：
>
> > “Why did you deliberately not use generics here?”
>
> ---
>
> # 6. Reflection
>
> - [ ] reflect.Type
> - [ ] reflect.Value
> - [ ] Kind
> - [ ] Interface
> - [ ] Set
> - [ ] CanSet
> - [ ] tags
> - [ ] runtime cost
> - [ ] unsafe interaction
>
> ### 必须知道
>
> - [ ] reflection 很强
> - [ ] reflection 通常让代码更复杂
> - [ ] 可以产生 runtime error
> - [ ] 热路径中需要谨慎
>
> ### 实践题
>
> > 实现一个简化版 struct validator。
>
> 支持：
>
> ```text
> `validate:"required"`
> `validate:"min=3"`
> `validate:"max=100"`
> ```
>
> 然后讨论：
>
> - [ ] reflection cost
> - [ ] caching metadata
> - [ ] concurrency
> - [ ] invalid input
> - [ ] API design
>
> ---
>
> # 7. defer / panic / recover
>
> - [ ] defer execution order
> - [ ] LIFO
> - [ ] defer arguments evaluated when defer statement executes
> - [ ] named return interaction
> - [ ] panic propagation
> - [ ] recover only in deferred function
> - [ ] goroutine boundary
>
> ### 高频题
>
> ```text
> Does recover in one goroutine recover a panic in another goroutine?
> ```
>
> 必须明确回答：
>
> > No.
>
> 每个 goroutine 的 panic / recover 边界需要单独考虑。
>
> ---
>
> # 8. 第二阶段：Go Memory Model
>
> ## 必须掌握
>
> - [ ] data race
> - [ ] happens-before
> - [ ] synchronization
> - [ ] atomic operations
> - [ ] mutex synchronization
> - [ ] channel synchronization
> - [ ] goroutine creation synchronization
> - [ ] race-free program
> - [ ] sequential consistency
>
> 官方 Memory Model 是这一阶段的必读材料。
>
> ### 必须回答
>
> ```text
> Why is this unsafe?
>
> var x int
>
> go func() {
>     x = 1
> }()
>
> fmt.Println(x)
> ```
>
> 进一步追问：
>
> ```text
> What if we add Sleep?
>
> What if we use WaitGroup?
>
> What if we use atomic?
>
> What if x is inside a struct?
> ```
>
> ---
>
> # 9. sync / sync.atomic
>
> ## Mutex
>
> - [ ] Mutex
> - [ ] RWMutex
> - [ ] lock contention
> - [ ] critical section
> - [ ] deadlock
> - [ ] starvation
> - [ ] copy-after-use problem
>
> ## WaitGroup
>
> - [ ] Add
> - [ ] Done
> - [ ] Wait
> - [ ] lifecycle correctness
> - [ ] Add after Wait 风险
>
> ## Once
>
> - [ ] sync.Once
> - [ ] lazy initialization
> - [ ] initialization failure
>
> ## Cond
>
> - [ ] condition variable
> - [ ] wait
> - [ ] signal
> - [ ] broadcast
>
> ## Pool
>
> - [ ] sync.Pool
> - [ ] temporary objects
> - [ ] reuse
> - [ ] GC semantics
> - [ ] why Pool is not a general cache
>
> ## atomic
>
> - [ ] Load
> - [ ] Store
> - [ ] Add
> - [ ] Swap
> - [ ] CompareAndSwap
> - [ ] atomic pointer
> - [ ] atomic.Value
> - [ ] typed atomics
> - [ ] memory ordering implications
>
> ---
>
> # 10. Channel
>
> ## 基础
>
> - [ ] unbuffered channel
> - [ ] buffered channel
> - [ ] send
> - [ ] receive
> - [ ] close
> - [ ] range
> - [ ] select
> - [ ] nil channel
>
> ## 必须掌握的行为
>
> ```text
> send on nil channel
> receive from nil channel
> send on closed channel
> receive from closed channel
> close nil channel
> close closed channel
> ```
>
> ## 面试代码题
>
> - [ ] 实现 producer / consumer
> - [ ] 实现 worker pool
> - [ ] 实现 fan-out
> - [ ] 实现 fan-in
> - [ ] 实现 pipeline
> - [ ] 实现 cancellation
> - [ ] 实现 bounded concurrency
>
> ---
>
> # 11. select
>
> - [ ] multiple channels
> - [ ] default
> - [ ] timeout
> - [ ] cancellation
> - [ ] closed channel
> - [ ] nil channel trick
> - [ ] fairness / random selection considerations
>
> ### Practical
>
> ```text
> select {
> case job := <-jobs:
>     ...
> case <-ctx.Done():
>     ...
> case <-time.After(time.Second):
>     ...
> }
> ```
>
> 能解释：
>
> - [ ] timeout semantics
> - [ ] resource allocation
> - [ ] leak risk
> - [ ] cancellation semantics
>
> ---
>
> # 12. Goroutine Lifecycle
>
> 这是 Go Senior 面试非常重要的部分。
>
> 任何 goroutine 都必须回答：
>
> ```text
> Who starts it?
> Who owns it?
> What does it do?
> What blocks it?
> How does it stop?
> Who waits for it?
> What happens if downstream fails?
> ```
>
> ### 禁止思维
>
> ```go
> go doSomething()
> ```
>
> 然后就不管了。
>
> ### 必须检查
>
> - [ ] lifecycle
> - [ ] ownership
> - [ ] cancellation
> - [ ] shutdown
> - [ ] error propagation
> - [ ] panic handling
> - [ ] wait
>
> ---
>
> # 13. Context
>
> - [ ] context.Background
> - [ ] context.TODO
> - [ ] WithCancel
> - [ ] WithTimeout
> - [ ] WithDeadline
> - [ ] WithValue
> - [ ] Done
> - [ ] Err
> - [ ] Deadline
>
> ## Senior-level理解
>
> Context 不只是：
>
> > “超时控制器”
>
> 它实际上用于在 goroutine / API boundary 之间传播：
>
> - [ ] cancellation
> - [ ] deadline
> - [ ] request-scoped metadata
>
> 官方资料强调，request 中衍生出来的 goroutine 在请求取消或超时后应该能够及时退出，以便释放资源。
>
> ### 必须避免
>
> - [ ] 把 Context 存进长期存活的 struct
> - [ ] 随便使用 context.WithValue
> - [ ] 不传递 deadline
> - [ ] 忽略 ctx.Done()
> - [ ] background goroutine 没有 cancellation
>
> ---
>
> # 14. Worker Pool
>
> 必须亲自写至少 3 个版本。
>
> ## Version 1
>
> ```text
> jobs channel
> workers
> results channel
> ```
>
> ## Version 2
>
> 加入：
>
> - [ ] context
> - [ ] cancellation
> - [ ] error propagation
> - [ ] bounded concurrency
>
> ## Version 3
>
> 加入：
>
> - [ ] retry
> - [ ] exponential backoff
> - [ ] per-job timeout
> - [ ] metrics
> - [ ] queue length
> - [ ] worker utilization
> - [ ] graceful shutdown
>
> ### 深挖
>
> - [ ] What if workers are slower than producers?
> - [ ] What if jobs never finish?
> - [ ] What if result consumer stops?
> - [ ] What if shutdown happens while jobs are queued?
> - [ ] What if one job panics?
> - [ ] What if downstream becomes slow?
>
> ---
>
> # 15. Backpressure
>
> 必须真正理解。
>
> ```text
> Producer
>    ↓
> Queue
>    ↓
> Workers
>    ↓
> Slow Downstream
> ```
>
> 如果：
>
> ```text
> producer rate > consumer rate
> ```
>
> 系统必然出现：
>
> - [ ] queue growth
> - [ ] memory growth
> - [ ] latency growth
> - [ ] downstream pressure
> - [ ] eventual failure
>
> ### 需要掌握
>
> - [ ] bounded queue
> - [ ] queue length monitoring
> - [ ] blocking
> - [ ] dropping
> - [ ] sampling
> - [ ] batching
> - [ ] rate limiting
> - [ ] load shedding
> - [ ] priority queue
>
> ---
>
> # 16. Goroutine Leak
>
> 必须能找出以下代码中的 leak。
>
> ```go
> ch := make(chan Result)
>
> go func() {
>     result := doWork()
>     ch <- result
> }()
>
> select {
> case result := <-ch:
>     return result
> case <-ctx.Done():
>     return ctx.Err()
> }
> ```
>
> 如果 receiver 因 cancellation 退出，而 goroutine 后续仍然阻塞在：
>
> ```go
> ch <- result
> ```
>
> 就可能产生 goroutine leak。
>
> ### 进一步练习
>
> - [ ] 如何修复？
> - [ ] buffered channel 是否解决？
> - [ ] context 如何传播？
> - [ ] worker 如何退出？
> - [ ] 生产环境如何发现？
>
> ---
>
> # 17. Scheduler / Runtime
>
> ## G-M-P
>
> - [ ] G
> - [ ] M
> - [ ] P
> - [ ] run queue
> - [ ] local run queue
> - [ ] global run queue
> - [ ] work stealing
> - [ ] parking
> - [ ] wakeup
> - [ ] preemption
> - [ ] syscall
> - [ ] blocking
>
> ### 必须理解
>
> ```text
> Goroutine
>     ↓
> Go Scheduler
>     ↓
> P
>     ↓
> M
>     ↓
> OS Thread
> ```
>
> 不要求你背 runtime 源码。
>
> 但必须回答：
>
> - [ ] Why are goroutines cheaper than OS threads?
> - [ ] What does GOMAXPROCS mean?
> - [ ] Why can too many runnable goroutines hurt?
> - [ ] What happens when a goroutine blocks?
> - [ ] How does scheduler handle CPU-bound workloads?
> - [ ] What happens with blocking syscalls?
>
> ---
>
> # 18. GOMAXPROCS / Container
>
> 必须知道现代 Go 环境里的实际问题：
>
> > Container CPU limit 与实际机器 CPU 核数可能不同。
>
> Go 1.25 开始，Linux 下默认 `GOMAXPROCS` 会考虑 cgroup CPU bandwidth limit，并会根据环境变化周期性调整；因此不能再只按传统“机器 logical CPU 数”理解这个问题。
>
> ### Practical Interview
>
> ```text
> Pod:
> CPU limit = 2
> Node:
> 64 CPUs
>
> Why can this matter for latency?
> ```
>
> 深挖：
>
> - [ ] CPU throttling
> - [ ] GOMAXPROCS
> - [ ] scheduler
> - [ ] GC
> - [ ] tail latency
> - [ ] Kubernetes CPU limit
>
> ---
>
> # 19. Escape Analysis
>
> - [ ] stack allocation
> - [ ] heap allocation
> - [ ] escape to heap
> - [ ] pointer escape
> - [ ] interface escape
> - [ ] closure capture
> - [ ] return pointer
> - [ ] compiler optimization
>
> 必须实际运行：
>
> ```bash
> go build -gcflags="-m"
> ```
>
> ### 练习
>
> 写 5 个函数：
>
> - [ ] stack allocation example
> - [ ] heap escape example
> - [ ] interface escape example
> - [ ] closure capture
> - [ ] returning pointer
>
> 看编译器怎么分析。
>
> ---
>
> # 20. Garbage Collection
>
> - [ ] concurrent GC
> - [ ] mark
> - [ ] sweep
> - [ ] allocation rate
> - [ ] GC assist
> - [ ] GC pressure
> - [ ] heap growth
> - [ ] object lifetime
> - [ ] memory limit
> - [ ] GOGC
> - [ ] GOMEMLIMIT
>
> ### 面试重点
>
> 不需要变成 GC researcher。
>
> 但是必须能解释：
>
> ```text
> QPS ↑
> allocation/request ↑
>       ↓
> allocation rate ↑
>       ↓
> GC pressure ↑
>       ↓
> CPU overhead ↑
>       ↓
> latency ↑
> ```
>
> ### Practical Incident
>
> > “Go service 的 RSS 从 2GB 慢慢上涨到 8GB，最终 OOM。”
>
> 不能直接回答：
>
> > “GC 出问题了。”
>
> 正确分析方向：
>
> - [ ] heap profile
> - [ ] live objects
> - [ ] allocation profile
> - [ ] object retention
> - [ ] goroutine stacks
> - [ ] caches
> - [ ] buffers
> - [ ] request bodies
> - [ ] queue
> - [ ] external memory
> - [ ] mmap
> - [ ] container memory limit
>
> ---
>
> # 21. Go Profiling
>
> 必须真正用过：
>
> - [ ] pprof
> - [ ] CPU profile
> - [ ] heap profile
> - [ ] goroutine profile
> - [ ] mutex profile
> - [ ] block profile
> - [ ] trace
> - [ ] benchmark profile
>
> 官方诊断文档明确将 `pprof` 作为 Go runtime 的核心 profiling 能力之一，并提供 CPU / heap 等 profile。
>
> ### 练习命令
>
> ```bash
> go test -cpuprofile cpu.out
> go test -memprofile mem.out
> go tool pprof cpu.out
> ```
>
> 以及生产服务：
>
> ```text
> /debug/pprof/
> ```
>
> ### 必须学会看
>
> - [ ] top
> - [ ] top10
> - [ ] list
> - [ ] web
> - [ ] flame graph
>
> ---
>
> # 22. Race Detector
>
> 每个生产级 Go 工程都应该理解：
>
> ```bash
> go test -race ./...
> ```
>
> 必须知道：
>
> - [ ] race detector 如何帮助发现 race
> - [ ] race 只能发现实际运行到的竞争
> - [ ] race-enabled binary 成本明显更高
> - [ ] 应该配合 integration / load tests
>
> ### 实战
>
> 故意制造：
>
> - [ ] map race
> - [ ] counter race
> - [ ] shared struct race
> - [ ] slice race
> - [ ] config reload race
>
> 然后使用 `-race` 找出来。
>
> ---
>
> # 23. Testing
>
> ## Unit Testing
>
> - [ ] table-driven tests
> - [ ] subtests
> - [ ] test helpers
> - [ ] mocks
> - [ ] fake
> - [ ] dependency injection
> - [ ] deterministic tests
>
> ## Integration Testing
>
> - [ ] database
> - [ ] Kafka
> - [ ] Redis
> - [ ] HTTP
> - [ ] filesystem
> - [ ] external services
>
> ## Contract Testing
>
> - [ ] API contract
> - [ ] schema
> - [ ] compatibility
>
> ## Load Testing
>
> - [ ] throughput
> - [ ] latency
> - [ ] saturation
> - [ ] concurrency
>
> ---
>
> # 24. Benchmark
>
> 必须会：
>
> ```bash
> go test -bench=.
> ```
>
> 以及：
>
> - [ ] `testing.B`
> - [ ] `b.N`
> - [ ] `ReportAllocs`
> - [ ] `-benchmem`
> - [ ] CPU profile
> - [ ] memory profile
>
> ### Practical Benchmark
>
> 比较：
>
> ```text
> fmt.Sprintf
> strconv
> strings.Builder
> bytes.Buffer
> preallocated slice
> append without capacity
> sync.Pool
> ```
>
> 不要只记结果。
>
> 必须解释：
>
> > **Why?**
>
> ---
>
> # 25. Fuzzing
>
> Go 原生 fuzzing 已经进入标准工具链。
>
> 学习：
>
> - [ ] `testing.F`
> - [ ] seed corpus
> - [ ] coverage guidance
> - [ ] crash reproduction
> - [ ] corpus minimization
> - [ ] regression corpus
>
> ### 最适合我的场景
>
> - [ ] document parser
> - [ ] JSON parser
> - [ ] query parser
> - [ ] URL parser
> - [ ] binary protocol parser
> - [ ] metadata parser
> - [ ] text chunker
>
> ---
>
> # 26. HTTP Server
>
> ## 必须熟练
>
> - [ ] net/http
> - [ ] Handler
> - [ ] ServeMux
> - [ ] middleware
> - [ ] Request
> - [ ] ResponseWriter
> - [ ] headers
> - [ ] status codes
> - [ ] JSON
> - [ ] streaming response
>
> ## Production Timeout
>
> 必须理解：
>
> - [ ] ReadHeaderTimeout
> - [ ] ReadTimeout
> - [ ] WriteTimeout
> - [ ] IdleTimeout
> - [ ] request context
>
> ### 为什么不能裸写
>
> ```go
> http.ListenAndServe(...)
> ```
>
> 然后认为“生产没问题”。
>
> 必须讨论：
>
> - [ ] slow clients
> - [ ] connection exhaustion
> - [ ] idle connections
> - [ ] request cancellation
> - [ ] graceful shutdown
>
> ---
>
> # 27. Graceful Shutdown
>
> 必须自己实现一个 HTTP service：
>
> ```text
> SIGTERM
>     ↓
> stop accepting new requests
>     ↓
> cancel background workers
>     ↓
> drain in-flight requests
>     ↓
> flush logs / metrics
>     ↓
> close DB / Kafka / clients
>     ↓
> exit
> ```
>
> ### 面试问题
>
> - [ ] What happens when Kubernetes sends SIGTERM?
> - [ ] How long should the service wait?
> - [ ] What if a request is stuck?
> - [ ] What if Kafka consumer is processing a large message?
> - [ ] How do you guarantee cleanup?
>
> ---
>
> # 28. HTTP Client
>
> 必须知道：
>
> - [ ] connection reuse
> - [ ] Transport
> - [ ] KeepAlive
> - [ ] connection pool
> - [ ] MaxIdleConns
> - [ ] MaxIdleConnsPerHost
> - [ ] IdleConnTimeout
> - [ ] timeout
> - [ ] retry
> - [ ] context
> - [ ] response body close
>
> ### 高频事故
>
> ```text
> forgot resp.Body.Close()
>        ↓
> connection reuse degrades
>        ↓
> connection pool exhaustion
>        ↓
> latency spikes
> ```
>
> 必须能定位这类问题。
>
> ---
>
> # 29. RPC / gRPC
>
> - [ ] protobuf
> - [ ] serialization
> - [ ] unary RPC
> - [ ] streaming RPC
> - [ ] deadline
> - [ ] cancellation
> - [ ] metadata
> - [ ] interceptors
> - [ ] retries
> - [ ] idempotency
> - [ ] status codes
> - [ ] connection reuse
>
> ### 深挖
>
> > “Retrying an RPC is always safe?”
>
> 必须回答：
>
> > No.
>
> 是否安全取决于：
>
> - [ ] operation semantics
> - [ ] idempotency
> - [ ] side effects
> - [ ] timeout ambiguity
>
> ---
>
> # 30. Database / database/sql
>
> 必须理解：
>
> ```text
> sql.DB != single DB connection
> ```
>
> `sql.DB` 是并发安全的 database handle，并管理底层 connection pool。
>
> 学习：
>
> - [ ] MaxOpenConns
> - [ ] MaxIdleConns
> - [ ] ConnMaxLifetime
> - [ ] ConnMaxIdleTime
> - [ ] DB.Stats()
> - [ ] transaction
> - [ ] BeginTx
> - [ ] QueryContext
> - [ ] ExecContext
> - [ ] connection leaks
>
> ### 高级问题
>
> > Why can increasing MaxOpenConns make the system worse?
>
> 因为：
>
> ```text
> more concurrency
>        ↓
> more DB connections
>        ↓
> DB saturation
>        ↓
> query latency
>        ↓
> request queue
>        ↓
> retries
>        ↓
> more load
>        ↓
> cascading failure
> ```
>
> ---
>
> # 31. Transaction
>
> - [ ] ACID
> - [ ] isolation
> - [ ] deadlock
> - [ ] rollback
> - [ ] commit ambiguity
> - [ ] retries
> - [ ] idempotency
>
> ### Practical
>
> 设计：
>
> ```text
> CreateOrder()
>     ↓
> deduct inventory
>     ↓
> write order
>     ↓
> publish event
> ```
>
> 然后讨论：
>
> - [ ] DB commit success but Kafka publish fails
> - [ ] Kafka publish succeeds but response lost
> - [ ] client retries
> - [ ] duplicate request
> - [ ] exactly once illusion
>
> ---
>
> # 32. Connection Pool
>
> 必须能解释：
>
> ```text
> Pool too small
>     ↓
> waiting
>     ↓
> latency
>
> Pool too large
>     ↓
> downstream saturation
>     ↓
> contention
>     ↓
> collapse
> ```
>
> 实践：
>
> - [ ] DB pool
> - [ ] HTTP pool
> - [ ] gRPC pool
> - [ ] Kafka producer connections
> - [ ] Redis pool
>
> ---
>
> # 33. Kafka + Go
>
> 这是你必须结合实际经验深入准备的方向。
>
> ## Producer
>
> - [ ] batching
> - [ ] compression
> - [ ] flush interval
> - [ ] acks
> - [ ] retries
> - [ ] idempotence
> - [ ] throughput
> - [ ] ordering
>
> ## Consumer
>
> - [ ] consumer group
> - [ ] partition
> - [ ] offset
> - [ ] commit
> - [ ] rebalance
> - [ ] lag
> - [ ] parallelism
> - [ ] backpressure
> - [ ] retry
> - [ ] DLQ
>
> ### Practical Challenge
>
> 设计：
>
> ```text
> 1M messages/sec
>       ↓
> Go consumer service
>       ↓
> 500 workers
>       ↓
> downstream DB
> ```
>
> 然后问：
>
> - [ ] What if DB slows down?
> - [ ] What if one partition becomes hot?
> - [ ] What if consumer crashes after DB write but before offset commit?
> - [ ] How do you deduplicate?
> - [ ] How do you replay?
> - [ ] How do you prevent OOM?
>
> ---
>
> # 34. Kafka Offset / Idempotency
>
> 必须非常熟。
>
> 经典流程：
>
> ```text
> consume message
>     ↓
> process
>     ↓
> write result
>     ↓
> commit offset
> ```
>
> 如果：
>
> ```text
> write result SUCCESS
> offset commit FAIL
> ```
>
> 那么重新消费。
>
> 所以必须理解：
>
> > At-least-once delivery + idempotent processing
>
> 往往比试图构造一个“神奇 exactly-once service”更加现实。
>
> ---
>
> # 35. Redis
>
> - [ ] connection pool
> - [ ] timeout
> - [ ] pipelining
> - [ ] cache-aside
> - [ ] TTL
> - [ ] cache stampede
> - [ ] cache penetration
> - [ ] hot key
> - [ ] distributed lock
> - [ ] pub/sub
> - [ ] streams
>
> ### Go 实践
>
> 写：
>
> ```text
> Cache.Get()
> Cache.Set()
> Cache.Delete()
> ```
>
> 然后加入：
>
> - [ ] context
> - [ ] timeout
> - [ ] metrics
> - [ ] retry
> - [ ] fallback
>
> ---
>
> # 36. Logging
>
> - [ ] structured logging
> - [ ] log level
> - [ ] request ID
> - [ ] trace ID
> - [ ] user / tenant ID
> - [ ] sampling
> - [ ] sensitive data
> - [ ] cardinality
>
> ### Senior级理解
>
> “日志越多越好”是错误思路。
>
> 应该考虑：
>
> ```text
> usefulness
> cost
> cardinality
> privacy
> queryability
> sampling
> ```
>
> ---
>
> # 37. Metrics
>
> 最少：
>
> ```text
> request_count
> request_latency
> error_count
> queue_length
> goroutines
> memory
> CPU
> GC
> DB pool
> Kafka lag
> retries
> timeouts
> ```
>
> ### RED
>
> - [ ] Rate
> - [ ] Errors
> - [ ] Duration
>
> ### USE
>
> - [ ] Utilization
> - [ ] Saturation
> - [ ] Errors
>
> ---
>
> # 38. Distributed Tracing
>
> - [ ] trace
> - [ ] span
> - [ ] parent-child
> - [ ] propagation
> - [ ] context
> - [ ] sampling
> - [ ] baggage
>
> 目标：
>
> 能追踪：
>
> ```text
> HTTP
>  ↓
> Go Service
>  ↓
> Kafka
>  ↓
> Flink
>  ↓
> Database
>  ↓
> downstream API
> ```
>
> ---
>
> # 39. OpenTelemetry
>
> - [ ] metrics
> - [ ] traces
> - [ ] logs
> - [ ] instrumentation
> - [ ] exporter
> - [ ] collector
> - [ ] sampling
>
> Practical：
>
> - [ ] 给 Go HTTP service 加 tracing
> - [ ] 给 DB call 加 span
> - [ ] 给 Kafka processing 加 span
> - [ ] 加 request correlation
>
> ---
>
> # 40. Production Failure Modes
>
> 这一部分是整个 Go 准备最重要的内容之一。
>
> 不要只背 API。
>
> 要建立：
>
> > **Symptom → Hypothesis → Evidence → Root Cause → Fix → Prevention**
>
> ---
>
> ## Incident 1：CPU 100%
>
> 症状：
>
> ```text
> CPU = 100%
> p99 ↑
> throughput ↓
> ```
>
> 分析：
>
> - [ ] application CPU?
> - [ ] GC?
> - [ ] serialization?
> - [ ] regex?
> - [ ] JSON?
> - [ ] lock contention?
> - [ ] busy loop?
> - [ ] scheduler?
> - [ ] excessive goroutines?
>
> 工具：
>
> - [ ] metrics
> - [ ] pprof CPU
> - [ ] trace
> - [ ] benchmark
>
> ---
>
> ## Incident 2：Memory Leak
>
> - [ ] heap profile
> - [ ] goroutine profile
> - [ ] retained objects
> - [ ] caches
> - [ ] global variables
> - [ ] slices
> - [ ] buffers
> - [ ] goroutine leak
> - [ ] queue buildup
> - [ ] request body
>
> ---
>
> ## Incident 3：Goroutine Explosion
>
> ```text
> goroutines:
> 5k
> 20k
> 100k
> ```
>
> 必须调查：
>
> - [ ] blocked channel
> - [ ] stuck network request
> - [ ] missing timeout
> - [ ] missing cancellation
> - [ ] unbounded worker
> - [ ] retry loop
> - [ ] ticker leak
> - [ ] background task
>
> ---
>
> ## Incident 4：Latency Suddenly Doubles
>
> ```text
> p50 stable
> p95 ↑
> p99 ↑↑
> ```
>
> 思考：
>
> - [ ] downstream latency
> - [ ] CPU throttling
> - [ ] GC
> - [ ] queueing
> - [ ] connection pool
> - [ ] lock contention
> - [ ] Kafka lag
> - [ ] retries
> - [ ] noisy neighbor
>
> ---
>
> ## Incident 5：DB Connection Exhaustion
>
> ```text
> DB:
> max connections reached
> ```
>
> Go service：
>
> ```text
> WaitCount ↑
> WaitDuration ↑
> ```
>
> 分析：
>
> - [ ] pool too small
> - [ ] pool too large
> - [ ] slow queries
> - [ ] missing Rows.Close
> - [ ] transaction leak
> - [ ] downstream lock
> - [ ] retry amplification
>
> ---
>
> ## Incident 6：Kafka Lag Suddenly Grows
>
> ```text
> consumer lag ↑↑
> ```
>
> 分析：
>
> - [ ] processing slowed
> - [ ] downstream DB slow
> - [ ] partition skew
> - [ ] consumer rebalance
> - [ ] fewer consumers
> - [ ] GC
> - [ ] network issue
> - [ ] external API
>
> ---
>
> # 41. Retry Storm
>
> 这是 Senior 面试很值得准备的题。
>
> ```text
> downstream failure
>       ↓
> retries
>       ↓
> more load
>       ↓
> more failures
>       ↓
> more retries
>       ↓
> cascading failure
> ```
>
> 必须掌握：
>
> - [ ] exponential backoff
> - [ ] jitter
> - [ ] retry budget
> - [ ] max retry count
> - [ ] timeout
> - [ ] circuit breaker
> - [ ] idempotency
> - [ ] load shedding
>
> ---
>
> # 42. Timeout Hierarchy
>
> 必须理解：
>
> ```text
> Global request deadline
>        ↓
> Service budget
>        ↓
> DB timeout
>        ↓
> RPC timeout
>        ↓
> External API timeout
> ```
>
> 错误设计：
>
> ```text
> parent timeout = 5s
> DB timeout = 10s
> ```
>
> child timeout 不应该脱离 parent deadline。
>
> ---
>
> # 43. Distributed Idempotency
>
> 必须自己设计：
>
> ```text
> POST /payment
> ```
>
> 请求可能：
>
> - [ ] duplicate
> - [ ] retry
> - [ ] timeout
> - [ ] response lost
> - [ ] network partition
>
> 需要：
>
> - [ ] idempotency key
> - [ ] durable state
> - [ ] unique constraint
> - [ ] retry-safe semantics
>
> ---
>
> # 44. Practical Coding Problems
>
> 这一部分不要使用纯 LeetCode 模式。
>
> 需要写真正的 Go service code。
>
> ---
>
> ## Challenge 1：Worker Pool
>
> 实现：
>
> ```text
> Submit(job)
> Start(N)
> Stop()
> ```
>
> 要求：
>
> - [ ] bounded concurrency
> - [ ] context
> - [ ] graceful shutdown
> - [ ] error propagation
> - [ ] metrics
> - [ ] no goroutine leak
> - [ ] backpressure
>
> ---
>
> ## Challenge 2：Rate Limiter
>
> 实现：
>
> ```text
> Allow()
> ```
>
> 版本：
>
> - [ ] fixed window
> - [ ] sliding window
> - [ ] token bucket
> - [ ] leaky bucket
> - [ ] concurrent implementation
>
> 深挖：
>
> - [ ] atomic vs mutex
> - [ ] distributed version
> - [ ] Redis version
>
> ---
>
> ## Challenge 3：TTL Cache
>
> 实现：
>
> ```text
> Get(key)
> Set(key, value, ttl)
> Delete(key)
> ```
>
> 要求：
>
> - [ ] concurrent
> - [ ] TTL
> - [ ] cleanup
> - [ ] bounded memory
> - [ ] LRU discussion
> - [ ] stampede protection
>
> ---
>
> ## Challenge 4：Singleflight
>
> 场景：
>
> ```text
> 10,000 requests
>       ↓
> same key
>       ↓
> expensive DB call
> ```
>
> 目标：
>
> ```text
> 10,000 requests
>        ↓
> one expensive call
>        ↓
> shared result
> ```
>
> 必须讨论：
>
> - [ ] cancellation
> - [ ] error propagation
> - [ ] cache
> - [ ] timeout
>
> ---
>
> ## Challenge 5：Concurrent Map
>
> 实现：
>
> - [ ] mutex map
> - [ ] RWMutex map
> - [ ] sharded map
> - [ ] atomic snapshot
>
> 然后 benchmark：
>
> ```text
> read-heavy
> write-heavy
> mixed
> high contention
> ```
>
> ---
>
> ## Challenge 6：Bounded Queue
>
> API：
>
> ```text
> Enqueue()
> Dequeue()
> Close()
> Len()
> ```
>
> 讨论：
>
> - [ ] blocking
> - [ ] timeout
> - [ ] drop policy
> - [ ] fairness
> - [ ] graceful shutdown
>
> ---
>
> ## Challenge 7：Scheduler
>
> 写一个简单 scheduler：
>
> ```text
> Submit(task)
> RunAt(time)
> Cancel(task)
> Retry(task)
> ```
>
> 深挖：
>
> - [ ] priority queue
> - [ ] timer
> - [ ] worker pool
> - [ ] persistence
> - [ ] crash recovery
> - [ ] distributed scheduler
>
> ---
>
> ## Challenge 8：HTTP Client Pool
>
> 写：
>
> ```text
> Call(ctx, endpoint)
> ```
>
> 加入：
>
> - [ ] timeout
> - [ ] retry
> - [ ] backoff
> - [ ] circuit breaker
> - [ ] connection reuse
> - [ ] metrics
> - [ ] tracing
>
> ---
>
> # 45. Complex Go Projects
>
> 这一部分必须认真练。
>
> ---
>
> ## Project A：High-QPS API Service
>
> 目标：
>
> ```text
> 100k QPS
> ```
>
> 包含：
>
> ```text
> Load Balancer
>      ↓
> Go Service
>      ↓
> Redis
>      ↓
> MySQL
>      ↓
> Kafka
> ```
>
> 要求：
>
> - [ ] latency target
> - [ ] error budget
> - [ ] horizontal scaling
> - [ ] connection pools
> - [ ] caching
> - [ ] backpressure
> - [ ] observability
>
> ---
>
> ## Project B：Kafka Processing Service
>
> ```text
> Kafka
>   ↓
> Go Consumer
>   ↓
> Worker Pool
>   ↓
> DB / HTTP
> ```
>
> 加入：
>
> - [ ] retry
> - [ ] DLQ
> - [ ] idempotency
> - [ ] offset management
> - [ ] graceful shutdown
> - [ ] metrics
>
> ---
>
> ## Project C：Document Processing Service
>
> 非常适合你的现有经历。
>
> ```text
> S3
>  ↓
> Go service
>  ↓
> Parse
>  ↓
> Chunk
>  ↓
> Metadata
>  ↓
> Kafka
>  ↓
> Embedding / Search
> ```
>
> 加入：
>
> - [ ] large files
> - [ ] streaming IO
> - [ ] retry
> - [ ] deduplication
> - [ ] idempotency
> - [ ] partial failure
> - [ ] concurrency control
> - [ ] backpressure
> - [ ] cost control
>
> ---
>
> ## Project D：Agent Tool Execution Service
>
> 这是 Go + AI Infrastructure 非常适合的结合题。
>
> ```text
> Agent
>   ↓
> Tool Request
>   ↓
> Go Tool Gateway
>   ↓
> Authorization
>   ↓
> Rate Limit
>   ↓
> Execution
>   ↓
> Result
>   ↓
> Agent
> ```
>
> 必须设计：
>
> - [ ] timeout
> - [ ] cancellation
> - [ ] retry
> - [ ] idempotency
> - [ ] permission
> - [ ] audit
> - [ ] tracing
> - [ ] tool schema
> - [ ] result size limit
> - [ ] concurrency limit
> - [ ] sandbox boundary
>
> ---
>
> # 46. Rare / Difficult Go Problems
>
> 这些不一定每场都会问，但 Senior 面试非常适合主动准备。
>
> ---
>
> ## Rare 1：Goroutine leak
>
> 能定位：
>
> - [ ] blocked send
> - [ ] blocked receive
> - [ ] missing cancellation
> - [ ] never-ending ticker
> - [ ] retry loop
> - [ ] dead worker
>
> ---
>
> ## Rare 2：Deadlock
>
> 准备案例：
>
> ```text
> Lock A → Lock B
> Lock B → Lock A
> ```
>
> 以及：
>
> - [ ] channel deadlock
> - [ ] nested lock
> - [ ] WaitGroup misuse
> - [ ] blocked transaction
>
> ---
>
> ## Rare 3：Livelock
>
> 多个 goroutine 都在运行，但没有有效进展。
>
> 能解释：
>
> > “CPU is high, goroutines are runnable, but throughput is low.”
>
> ---
>
> ## Rare 4：Starvation
>
> - [ ] mutex contention
> - [ ] scheduler
> - [ ] queue fairness
> - [ ] priority inversion
>
> ---
>
> ## Rare 5：Thundering Herd
>
> ```text
> cache expires
>      ↓
> 100k requests
>      ↓
> 100k DB calls
> ```
>
> 解决：
>
> - [ ] singleflight
> - [ ] stale-while-revalidate
> - [ ] request coalescing
> - [ ] jitter
>
> ---
>
> ## Rare 6：Memory Retention
>
> 某个小 slice 持有一个巨大 backing array。
>
> 必须能解释：
>
> > “The object is technically reachable, so GC cannot reclaim the backing array.”
>
> ---
>
> ## Rare 7：Unsafe
>
> 不要求大量使用，但必须知道：
>
> - [ ] pointer conversion
> - [ ] alignment
> - [ ] memory model implications
> - [ ] zero-copy
> - [ ] string/byte conversion
> - [ ] why unsafe is dangerous
>
> ---
>
> ## Rare 8：Cgo
>
> - [ ] C boundary
> - [ ] blocking
> - [ ] memory ownership
> - [ ] performance
> - [ ] scheduling implications
> - [ ] deployment complexity
>
> ---
>
> # 47. Go Service Security
>
> - [ ] input validation
> - [ ] SQL injection
> - [ ] SSRF
> - [ ] path traversal
> - [ ] command injection
> - [ ] insecure deserialization
> - [ ] auth
> - [ ] authorization
> - [ ] secrets
> - [ ] TLS
> - [ ] certificate validation
> - [ ] dependency vulnerabilities
>
> 工具：
>
> - [ ] `govulncheck`
> - [ ] `go test -race`
> - [ ] fuzzing
> - [ ] staticcheck
>
> ---
>
> # 48. Go Modules / Dependency Management
>
> - [ ] go.mod
> - [ ] go.sum
> - [ ] semantic versioning
> - [ ] MVS
> - [ ] replace
> - [ ] retract
> - [ ] indirect dependency
> - [ ] module graph
> - [ ] workspace
> - [ ] private modules
> - [ ] GOPROXY
> - [ ] dependency integrity
>
> 必须会：
>
> ```bash
> go mod tidy
> go list -m all
> go list -m -u all
> go mod graph
> ```
>
> ---
>
> # 49. Go Project Structure
>
> 不要死背：
>
> ```text
> cmd/
> internal/
> pkg/
> ```
>
> 要理解：
>
> > Project structure should follow dependency boundaries and ownership boundaries.
>
> 一个大型 Go service 至少需要能解释：
>
> ```text
> cmd/
>     service/
>
> internal/
>     handler/
>     service/
>     repository/
>     client/
>     consumer/
>     producer/
>     config/
>     metrics/
>     middleware/
>
> api/
>     proto/
> ```
>
> 但要理解：
>
> > folder layout is a consequence of architecture, not architecture itself.
>
> ---
>
> # 50. Dependency Injection
>
> - [ ] constructor injection
> - [ ] interface injection
> - [ ] functional options
> - [ ] manual DI
> - [ ] DI framework
>
> 优先理解：
>
> > Go 里通常 manual dependency injection 已经非常强，不需要为了 DI 而使用复杂 framework。
>
> ---
>
> # 51. Configuration
>
> - [ ] env
> - [ ] config file
> - [ ] command line
> - [ ] dynamic config
> - [ ] config validation
> - [ ] secrets
> - [ ] immutable config
> - [ ] hot reload
>
> ### 深挖
>
> > Dynamic reload of configuration can itself introduce race conditions.
>
> 实现：
>
> - [ ] atomic snapshot
> - [ ] immutable configuration
> - [ ] copy-on-write
>
> ---
>
> # 52. Logging / Metrics / Tracing 三件套
>
> 每个自己的 Go Project 都必须包含：
>
> ```text
> Logger
> Metrics
> Tracing
> ```
>
> 一个 request：
>
> ```text
> trace_id
>    ↓
> HTTP
>    ↓
> service
>    ↓
> DB
>    ↓
> Kafka
> ```
>
> 能完整观察。
>
> ---
>
> # 53. Production Readiness Checklist
>
> 一个 Go Service 上线前：
>
> ### Runtime
>
> - [ ] CPU limits
> - [ ] memory limits
> - [ ] GOMAXPROCS
> - [ ] GC behavior
>
> ### Networking
>
> - [ ] read timeout
> - [ ] write timeout
> - [ ] idle timeout
> - [ ] connection reuse
> - [ ] TLS
>
> ### Concurrency
>
> - [ ] goroutine ownership
> - [ ] cancellation
> - [ ] no leak
> - [ ] bounded worker pool
>
> ### Database
>
> - [ ] pool settings
> - [ ] transaction
> - [ ] timeout
> - [ ] query metrics
>
> ### Kafka
>
> - [ ] retry
> - [ ] offset
> - [ ] lag metrics
> - [ ] rebalance
>
> ### Reliability
>
> - [ ] retry
> - [ ] timeout
> - [ ] circuit breaker
> - [ ] idempotency
> - [ ] graceful degradation
>
> ### Observability
>
> - [ ] metrics
> - [ ] logs
> - [ ] tracing
> - [ ] profiling
>
> ### Security
>
> - [ ] secrets
> - [ ] dependency scan
> - [ ] input validation
> - [ ] auth
> - [ ] authz
>
> ### Deployment
>
> - [ ] health check
> - [ ] readiness
> - [ ] graceful shutdown
> - [ ] rolling update
> - [ ] rollback
>
> ---
>
> # 54. Debugging Playbook
>
> 遇到线上问题，不允许第一反应：
>
> > “应该是 X。”
>
> 应该按照：
>
> ```text
> Symptom
>     ↓
> Scope
>     ↓
> Recent changes
>     ↓
> Metrics
>     ↓
> Logs
>     ↓
> Traces
>     ↓
> Profiles
>     ↓
> Hypothesis
>     ↓
> Experiment
>     ↓
> Root Cause
>     ↓
> Fix
>     ↓
> Prevention
> ```
>
> ---
>
> # 55. Debugging Scenario Bank
>
> 必须练习口头分析以下问题。
>
> ### Case A
>
> > p99 suddenly increased from 100ms to 2s.
>
> ### Case B
>
> > memory keeps increasing but heap seems stable.
>
> ### Case C
>
> > goroutines increase from 2k to 50k.
>
> ### Case D
>
> > Kafka lag grows every afternoon.
>
> ### Case E
>
> > database max connections reached.
>
> ### Case F
>
> > CPU is 80% but application throughput is low.
>
> ### Case G
>
> > one Kubernetes pod is much slower than others.
>
> ### Case H
>
> > retry traffic is larger than original traffic.
>
> ### Case I
>
> > one endpoint occasionally takes 30 seconds.
>
> ### Case J
>
> > service hangs during deployment.
>
> ### Case K
>
> > after enabling a new cache, memory usage doubles.
>
> ### Case L
>
> > after increasing worker count, throughput drops.
>
> ### Case M
>
> > after changing JSON serialization, CPU increases 40%.
>
> ### Case N
>
> > service passes all tests but production gets data races.
>
> ### Case O
>
> > one Kafka partition has massive lag while others are healthy.
>
> ---
>
> # 56. Senior Interview Question Bank
>
> ## Go Fundamentals
>
> - [ ] What is the difference between slice length and capacity?
> - [ ] When does append allocate?
> - [ ] How does interface work?
> - [ ] What is typed nil?
> - [ ] Value receiver vs pointer receiver?
> - [ ] When do values escape to heap?
> - [ ] How does defer work?
> - [ ] What happens during panic?
>
> ---
>
> ## Concurrency
>
> - [ ] Goroutine vs thread?
> - [ ] Channel vs mutex?
> - [ ] When should you not use channels?
> - [ ] What causes goroutine leaks?
> - [ ] How do you cancel workers?
> - [ ] How do you implement bounded concurrency?
> - [ ] How do you detect races?
> - [ ] What is happens-before?
>
> ---
>
> ## Runtime
>
> - [ ] Explain G-M-P.
> - [ ] What does GOMAXPROCS mean?
> - [ ] How does GC affect latency?
> - [ ] What is escape analysis?
> - [ ] What causes allocation pressure?
>
> ---
>
> ## Production
>
> - [ ] How do you implement graceful shutdown?
> - [ ] How do you choose timeout?
> - [ ] How do you choose retry count?
> - [ ] How do you avoid retry storms?
> - [ ] How do you prevent goroutine leaks?
> - [ ] How do you debug memory growth?
> - [ ] How do you debug CPU spikes?
>
> ---
>
> ## Distributed Systems
>
> - [ ] How do you make an operation idempotent?
> - [ ] How do you deal with duplicate messages?
> - [ ] What does at-least-once mean?
> - [ ] How do you handle partial failure?
> - [ ] How do you deal with ordering?
> - [ ] How do you recover from downstream failure?
> - [ ] How do you prevent cascading failures?
>
> ---
>
> # 57. Practical “What Would You Do?” Questions
>
> 这些题必须达到可以现场连续讲 5–10 分钟。
>
> ### Q1
>
> > You have a Go service receiving 50k requests/sec. Downstream DB can only handle 5k requests/sec. What do you do?
>
> 必须谈：
>
> - [ ] queue
> - [ ] backpressure
> - [ ] cache
> - [ ] batching
> - [ ] rate limiting
> - [ ] load shedding
> - [ ] eventual consistency
> - [ ] durability
>
> ---
>
> ### Q2
>
> > Your service occasionally takes 10 seconds although normal latency is 50ms.
>
> 必须调查：
>
> - [ ] p99 / p999
> - [ ] GC
> - [ ] downstream
> - [ ] connection pool
> - [ ] lock
> - [ ] queue
> - [ ] retry
> - [ ] scheduler
>
> ---
>
> ### Q3
>
> > A worker pool becomes slower after increasing workers from 100 to 1000.
>
> 讨论：
>
> - [ ] downstream saturation
> - [ ] lock contention
> - [ ] scheduler overhead
> - [ ] CPU
> - [ ] context switching
> - [ ] queue contention
> - [ ] DB pool
>
> ---
>
> ### Q4
>
> > Kafka consumer is always behind.
>
> 不应该直接：
>
> > “Add more consumers.”
>
> 先看：
>
> - [ ] partition count
> - [ ] processing latency
> - [ ] downstream
> - [ ] skew
> - [ ] rebalance
> - [ ] GC
> - [ ] CPU
>
> ---
>
> ### Q5
>
> > Kubernetes deployment never finishes because pods don't terminate.
>
> 调查：
>
> - [ ] SIGTERM handling
> - [ ] background goroutines
> - [ ] server Shutdown
> - [ ] stuck requests
> - [ ] Kafka consumer
> - [ ] finalizers
> - [ ] preStop
> - [ ] terminationGracePeriod
>
> ---
>
> # 58. Go + Distributed Systems 综合题
>
> 这是最终训练目标。
>
> ---
>
> ## Design 1：Distributed Job Scheduler
>
> ```text
> API
> ↓
> Scheduler
> ↓
> Queue
> ↓
> Workers
> ↓
> Executor
> ```
>
> 必须讨论：
>
> - [ ] leader election
> - [ ] sharding
> - [ ] task ownership
> - [ ] retry
> - [ ] idempotency
> - [ ] task state
> - [ ] crash recovery
> - [ ] duplicate execution
> - [ ] scheduling fairness
>
> ---
>
> ## Design 2：High-throughput Event Ingestion
>
> ```text
> HTTP
> ↓
> Go Gateway
> ↓
> Kafka
> ↓
> Flink
> ↓
> Storage
> ```
>
> 必须讨论：
>
> - [ ] throughput
> - [ ] ordering
> - [ ] partition
> - [ ] backpressure
> - [ ] retries
> - [ ] duplicate
> - [ ] observability
> - [ ] cost
>
> ---
>
> ## Design 3：Document Processing Platform
>
> ```text
> Object Storage
>      ↓
> Go Orchestrator
>      ↓
> Queue
>      ↓
> Workers
>      ↓
> Parser
>      ↓
> Metadata
>      ↓
> Embedding
>      ↓
> Search / Vector DB
> ```
>
> 必须讨论：
>
> - [ ] large documents
> - [ ] partial failure
> - [ ] retry
> - [ ] deduplication
> - [ ] idempotency
> - [ ] processing status
> - [ ] scheduling
> - [ ] resource limits
> - [ ] multi-tenant
>
> ---
>
> # 59. Go Coding Interview
>
> 不要只刷 LeetCode。
>
> 至少准备：
>
> - [ ] LRU cache
> - [ ] TTL cache
> - [ ] rate limiter
> - [ ] worker pool
> - [ ] bounded queue
> - [ ] concurrent map
> - [ ] producer/consumer
> - [ ] retry executor
> - [ ] circuit breaker
> - [ ] singleflight
> - [ ] scheduler
> - [ ] connection pool
> - [ ] log aggregator
> - [ ] rolling counter
> - [ ] metrics collector
>
> ---
>
> # 60. 每道 Practical Coding 题的统一要求
>
> 写完第一版以后，不要停止。
>
> 必须继续问：
>
> ```text
> Correctness
>     ↓
> Concurrency
>     ↓
> Failure
>     ↓
> Timeout
>     ↓
> Cancellation
>     ↓
> Backpressure
>     ↓
> Observability
>     ↓
> Performance
>     ↓
> Testing
>     ↓
> Production
> ```
>
> ---
>
> # 61. Go Code Review 能力
>
> 面试官可能直接给你一段代码：
>
> ```go
> func Process(items []Item) {
>     for _, item := range items {
>         go func() {
>             doSomething(item)
>         }()
>     }
> }
> ```
>
> 你要主动 review：
>
> - [ ] goroutine lifetime
> - [ ] closure capture
> - [ ] unbounded concurrency
> - [ ] no error propagation
> - [ ] no cancellation
> - [ ] no backpressure
> - [ ] no shutdown
> - [ ] no observability
>
> ---
>
> # 62. Architecture Review 能力
>
> 拿到一个 Go service repo：
>
> 第一轮不要马上读函数。
>
> 先找：
>
> ```text
> entrypoint
> ↓
> dependency graph
> ↓
> request path
> ↓
> async path
> ↓
> storage
> ↓
> external systems
> ↓
> failure boundaries
> ↓
> observability
> ```
>
> ---
>
> # 63. 真实项目阅读方法
>
> 对自己的 DiDi / Dell / RELX 项目做一次：
>
> ```text
> Architecture Reverse Engineering
> ```
>
> 对每个 Go service 建立：
>
> ### Service Overview
>
> ```text
> Input
> ↓
> Business Logic
> ↓
> Async
> ↓
> Storage
> ↓
> External Dependency
> ```
>
> ### Dependency Table
>
> | Dependency | Protocol | Timeout | Retry | Failure Mode | Owner |
> |---|---|---|---|---|---|
> | Kafka | Kafka | ? | ? | lag | ? |
> | DB | SQL | ? | ? | unavailable | ? |
> | Redis | TCP | ? | ? | timeout | ? |
> | RPC | gRPC/HTTP | ? | ? | partial failure | ? |
>
> ### Runtime Table
>
> | Component | Goroutines | Queue | State | Shutdown | Metrics |
> |---|---:|---|---|---|---|
> | Consumer | ? | ? | ? | ? | ? |
> | Worker | ? | ? | ? | ? | ? |
> | HTTP | ? | ? | ? | ? | ? |
>
> ---
>
> # 64. 真实事故复盘模板
>
> 对历史上你经历过的任何线上问题，使用：
>
> ```text
> 1. Context
> 2. Expected behavior
> 3. Actual behavior
> 4. Blast radius
> 5. Initial hypothesis
> 6. Evidence
> 7. Root cause
> 8. Why existing monitoring didn't catch it
> 9. Fix
> 10. Rollout
> 11. Validation
> 12. Prevention
> ```
>
> 面试时不要只讲：
>
> > “我们修了 bug。”
>
> 应该讲：
>
> > “The interesting part was not fixing the bug itself, but identifying why the system allowed this failure mode to remain invisible until production.”
>
> ---
>
> # 65. Go Interview Story Bank
>
> 至少准备 10 个真实故事。
>
> - [ ] 最严重的 production bug
> - [ ] 最难 debug 的问题
> - [ ] 性能优化
> - [ ] concurrency bug
> - [ ] data consistency bug
> - [ ] Kafka issue
> - [ ] database issue
> - [ ] system scaling
> - [ ] design disagreement
> - [ ] technical trade-off
>
> 每个故事准备：
>
> ```text
> 30 sec
> 2 min
> 5 min
> 15 min
> ```
>
> ---
>
> # 66. 典型深挖路径
>
> 面试官：
>
> > Tell me about a Go service you built.
>
> 第一层：
>
> > What did it do?
>
> 第二层：
>
> > How much traffic?
>
> 第三层：
>
> > Why Go?
>
> 第四层：
>
> > Why this concurrency model?
>
> 第五层：
>
> > What happens if Kafka is unavailable?
>
> 第六层：
>
> > What happens if DB becomes slow?
>
> 第七层：
>
> > How do you observe it?
>
> 第八层：
>
> > How do you know where the bottleneck is?
>
> 第九层：
>
> > What was the hardest production issue?
>
> 第十层：
>
> > What would you redesign now?
>
> 必须训练到这里都能继续回答。
>
> ---
>
> # 67. “为什么 Go？” 必须准备的答案
>
> 不要回答：
>
> > Go is simple and fast.
>
> 应该从：
>
> - [ ] concurrency
> - [ ] deployment
> - [ ] tooling
> - [ ] standard library
> - [ ] static typing
> - [ ] performance
> - [ ] operational simplicity
> - [ ] team maintainability
>
> 讨论。
>
> 进一步：
>
> > Why not Java?
>
> > Why not Rust?
>
> > Why not Python?
>
> 需要从具体系统约束回答，而不是语言宗教。
>
> ---
>
> # 68. Go vs Java
>
> 结合你的背景必须能回答：
>
> ```text
> Go:
> lightweight concurrency
> simple deployment
> low operational complexity
>
> Java:
> mature ecosystem
> JVM
> larger enterprise ecosystem
> rich runtime
> mature frameworks
> ```
>
> 关键不是证明 Go 好。
>
> 而是：
>
> > **为什么在某个系统约束下选择它。**
>
> ---
>
> # 69. Go vs Rust
>
> 必须能讨论：
>
> - [ ] memory safety
> - [ ] ownership
> - [ ] compile-time guarantees
> - [ ] developer productivity
> - [ ] runtime
> - [ ] team skill
> - [ ] ecosystem
> - [ ] latency
> - [ ] unsafe requirements
>
> ---
>
> # 70. Go 服务代码质量
>
> 每个项目都应该：
>
> - [ ] gofmt
> - [ ] go vet
> - [ ] staticcheck
> - [ ] golangci-lint
> - [ ] unit test
> - [ ] integration test
> - [ ] race test
> - [ ] benchmark
> - [ ] fuzz test where appropriate
> - [ ] vulnerability scan
>
> Uber Go Style Guide 值得完整阅读，因为它把很多“真正的大型 Go codebase 会遇到的问题”整理成了工程规则，包括 error handling、goroutine lifecycle、receiver、interface、performance、linting 等。
>
> ---
>
> # 71. 必须完成的 8 个 Mini Projects
>
> ## Mini Project 1
>
> ### Concurrent HTTP Fetcher
>
> - [ ] concurrency limit
> - [ ] timeout
> - [ ] cancellation
> - [ ] retry
> - [ ] metrics
> - [ ] graceful shutdown
>
> ---
>
> ## Mini Project 2
>
> ### Worker Pool
>
> - [ ] queue
> - [ ] workers
> - [ ] cancellation
> - [ ] backpressure
> - [ ] error propagation
>
> ---
>
> ## Mini Project 3
>
> ### TTL Cache
>
> - [ ] concurrent
> - [ ] TTL
> - [ ] cleanup
> - [ ] benchmark
>
> ---
>
> ## Mini Project 4
>
> ### Kafka Consumer
>
> - [ ] consumer
> - [ ] worker pool
> - [ ] offset
> - [ ] retry
> - [ ] DLQ
> - [ ] metrics
>
> ---
>
> ## Mini Project 5
>
> ### High QPS API
>
> - [ ] HTTP
> - [ ] Redis
> - [ ] DB
> - [ ] rate limiting
> - [ ] metrics
> - [ ] tracing
>
> ---
>
> ## Mini Project 6
>
> ### Distributed Scheduler
>
> - [ ] task state
> - [ ] retry
> - [ ] leader election discussion
> - [ ] persistence
> - [ ] recovery
>
> ---
>
> ## Mini Project 7
>
> ### Document Processing Worker
>
> - [ ] S3
> - [ ] streaming
> - [ ] parser
> - [ ] chunker
> - [ ] queue
> - [ ] concurrency
> - [ ] retry
>
> ---
>
> ## Mini Project 8
>
> ### Agent Tool Gateway
>
> - [ ] auth
> - [ ] rate limiting
> - [ ] timeout
> - [ ] cancellation
> - [ ] audit
> - [ ] tracing
> - [ ] result validation
>
> ---
>
> # 72. 每周实践计划
>
> ## Week 1 — Language
>
> - [ ] slice
> - [ ] map
> - [ ] interface
> - [ ] error
> - [ ] pointer
> - [ ] generic
>
> 输出：
>
> > 写 30 个 Go 小题。
>
> ---
>
> ## Week 2 — Concurrency
>
> - [ ] goroutine
> - [ ] channel
> - [ ] select
> - [ ] mutex
> - [ ] atomic
> - [ ] context
>
> 输出：
>
> > Worker Pool + Pipeline + Rate Limiter
>
> ---
>
> ## Week 3 — Runtime
>
> - [ ] G-M-P
> - [ ] scheduler
> - [ ] escape
> - [ ] GC
> - [ ] GOMAXPROCS
>
> 输出：
>
> > 5 个 benchmark + 2 个 pprof 实验
>
> ---
>
> ## Week 4 — Network
>
> - [ ] HTTP
> - [ ] TCP
> - [ ] HTTP client
> - [ ] gRPC
> - [ ] timeout
> - [ ] graceful shutdown
>
> 输出：
>
> > Production-style HTTP Service
>
> ---
>
> ## Week 5 — Storage
>
> - [ ] MySQL
> - [ ] Redis
> - [ ] transactions
> - [ ] pool
> - [ ] retry
>
> 输出：
>
> > API + DB + Redis
>
> ---
>
> ## Week 6 — Kafka
>
> - [ ] Producer
> - [ ] Consumer
> - [ ] Offset
> - [ ] Retry
> - [ ] Lag
> - [ ] Idempotency
>
> 输出：
>
> > Kafka Processing Service
>
> ---
>
> ## Week 7 — Production
>
> - [ ] metrics
> - [ ] tracing
> - [ ] logging
> - [ ] pprof
> - [ ] race
> - [ ] fuzz
> - [ ] security
>
> 输出：
>
> > Production-ready Go Service
>
> ---
>
> ## Week 8 — Distributed Systems
>
> - [ ] consistency
> - [ ] replication
> - [ ] partitioning
> - [ ] retry
> - [ ] idempotency
> - [ ] failure
>
> 输出：
>
> > 3 个 System Design
>
> ---
>
> ## Week 9 — Real Incidents
>
> 每天随机抽一道：
>
> - [ ] CPU
> - [ ] memory
> - [ ] goroutine
> - [ ] Kafka
> - [ ] DB
> - [ ] network
> - [ ] GC
> - [ ] deployment
>
> 输出：
>
> > 5 分钟英文口头分析
>
> ---
>
> ## Week 10 — Interview Simulation
>
> - [ ] Go coding
> - [ ] Go internals
> - [ ] debugging
> - [ ] system design
> - [ ] behavioral
>
> 每天：
>
> ```text
> 1 coding
> 1 Go deep dive
> 1 incident
> 1 system design
> ```
>
> ---
>
> # 73. 英文面试回答模板
>
> 对技术问题尽量采用：
>
> ```text
> First, I would clarify the failure mode.
>
> Then I would look at the relevant metrics.
>
> My first hypotheses would be...
>
> I would validate them using...
>
> If the hypothesis is confirmed...
>
> The short-term mitigation would be...
>
> The long-term fix would be...
>
> And I would add monitoring / tests to prevent regression.
> ```
>
> 这比直接：
>
> > “I think the problem is GC.”
>
> 更符合 Senior Engineer 的表达方式。
>
> ---
>
> # 74. Go 面试英文词汇
>
> 必须能够自然使用：
>
> - [ ] contention
> - [ ] bottleneck
> - [ ] saturation
> - [ ] backpressure
> - [ ] fan-out
> - [ ] fan-in
> - [ ] cancellation
> - [ ] deadline
> - [ ] idempotency
> - [ ] duplicate processing
> - [ ] partial failure
> - [ ] tail latency
> - [ ] throughput
> - [ ] allocation pressure
> - [ ] memory retention
> - [ ] goroutine leak
> - [ ] connection exhaustion
> - [ ] cascading failure
> - [ ] graceful degradation
> - [ ] load shedding
> - [ ] observability
> - [ ] blast radius
> - [ ] root cause
>
> ---
>
> # 75. Extra Reading — 官方 Go 内容
>
> ## 必读
>
> - [ ] **Effective Go**
>   - 重点：interfaces / concurrency / errors / idioms
>
> - [ ] **Go Memory Model**
>   - 重点：data race / happens-before / synchronization / DRF-SC
>
> - [ ] **Go Diagnostics**
>   - 重点：pprof / profiling / tracing / diagnostics
>
> - [ ] **Go GC Guide**
>   - 重点：GOGC / GOMEMLIMIT / allocation / heap / GC behavior
>
> - [ ] **Managing Connections**
>   - 重点：database/sql connection pool / MaxOpenConns / MaxIdleConns / DB.Stats
>
> - [ ] **Go Fuzzing**
>   - 重点：native fuzzing / corpus / coverage / regression
>
> - [ ] **Go Security Best Practices**
>   - 重点：govulncheck / fuzzing / race detector / dependency security
>
> ---
>
> # 76. Extra Reading — Go Blog
>
> ## 强烈推荐
>
> - [ ] **Go Concurrency Patterns: Context**
>
> 重点：
>
> ```text
> cancellation
> deadline
> request-scoped values
> goroutine lifecycle
> ```
>
> - [ ] **Go Concurrency Patterns: Pipelines and cancellation**
>
> 重点：
>
> ```text
> pipeline
> fan-out
> fan-in
> cancellation
> failure propagation
> ```
>
> - [ ] **Container-aware GOMAXPROCS**
>
> 重点：
>
> ```text
> Kubernetes
> cgroup
> CPU limits
> throttling
> tail latency
> GOMAXPROCS
> ```
>
> - [ ] **Go 1.25 Release Notes**
>
> 至少了解：
>
> - [ ] container-aware GOMAXPROCS
> - [ ] testing/synctest
> - [ ] runtime changes
> - [ ] language / tooling changes
>
> ---
>
> # 77. Extra Reading — Go Style / Engineering
>
> ## Uber Go Style Guide
>
> 重点阅读：
>
> - [ ] interfaces
> - [ ] receivers
> - [ ] errors
> - [ ] defer
> - [ ] goroutines
> - [ ] mutable globals
> - [ ] performance
> - [ ] test patterns
> - [ ] linting
>
> 不要只读“代码风格”。
>
> 重点观察：
>
> > **为什么大型 Go 团队会形成这些规则？**
>
> ---
>
> # 78. Extra Reading — 进一步搜索方向
>
> 建议长期搜索这些主题：
>
> ```text
> Go scheduler deep dive
> Go GC internals
> Go escape analysis
> Go memory model
> Go runtime pprof
> Go production performance
> Go tail latency
> Go goroutine leak
> Go distributed systems
> Go Kafka production
> Go graceful shutdown
> Go database connection pool
> Go HTTP timeout production
> Go retry backoff jitter
> Go circuit breaker
> Go OpenTelemetry
> Go Kubernetes CPU throttling
> ```
>
> ---
>
> # 79. 推荐长期关注的网站 / 内容源
>
> - [ ] Go.dev
> - [ ] Go Blog
> - [ ] Go Wiki
> - [ ] pkg.go.dev
> - [ ] Go proposal repository
> - [ ] Uber Go engineering / Uber Go Style Guide
> - [ ] Cloudflare engineering
> - [ ] Cockroach Labs engineering
> - [ ] Grafana engineering
> - [ ] Cloudflare Go performance articles
> - [ ] Kubernetes engineering / SIG Node
> - [ ] Kafka engineering documentation
>
> 阅读这些资料时，不要只是看：
>
> > “How to use X”
>
> 更应该关注：
>
> > “What production failure made engineers design X this way?”
>
> ---
>
> # 80. 最终 Go 能力矩阵
>
> ## Level A — Fluent
>
> - [ ] Go syntax
> - [ ] slice
> - [ ] map
> - [ ] interface
> - [ ] error
> - [ ] generic
>
> ## Level B — Strong
>
> - [ ] goroutine
> - [ ] channel
> - [ ] select
> - [ ] context
> - [ ] mutex
> - [ ] atomic
> - [ ] worker pool
> - [ ] pipeline
>
> ## Level C — Senior
>
> - [ ] memory model
> - [ ] runtime
> - [ ] GC
> - [ ] scheduler
> - [ ] profiling
> - [ ] race
> - [ ] benchmark
>
> ## Level D — Production
>
> - [ ] HTTP
> - [ ] RPC
> - [ ] DB
> - [ ] Kafka
> - [ ] Redis
> - [ ] timeout
> - [ ] retry
> - [ ] idempotency
> - [ ] graceful shutdown
>
> ## Level E — Distributed Systems
>
> - [ ] consistency
> - [ ] partitioning
> - [ ] replication
> - [ ] backpressure
> - [ ] failure isolation
> - [ ] load shedding
> - [ ] cascading failure
>
> ## Level F — Senior Interview
>
> - [ ] debugging
> - [ ] incident analysis
> - [ ] architecture review
> - [ ] trade-offs
> - [ ] system design
> - [ ] production stories
>
> ---
>
> # 81. 最终验收标准
>
> 当以下全部可以做到，才认为 Go 准备基本完成。
>
> ### Language
>
> - [ ] 能写
> - [ ] 能解释
> - [ ] 能 debug
>
> ### Concurrency
>
> - [ ] 能设计
> - [ ] 能发现 race
> - [ ] 能发现 leak
> - [ ] 能处理 cancellation
>
> ### Runtime
>
> - [ ] 能解释 scheduler
> - [ ] 能解释 GC
> - [ ] 能使用 pprof
>
> ### Production
>
> - [ ] 能处理 timeout
> - [ ] 能处理 retry
> - [ ] 能处理 DB pool
> - [ ] 能处理 Kafka lag
> - [ ] 能处理 graceful shutdown
>
> ### Distributed Systems
>
> - [ ] 能解释 partial failure
> - [ ] 能解释 idempotency
> - [ ] 能解释 duplicate
> - [ ] 能解释 backpressure
>
> ### System Design
>
> - [ ] 能设计 Go service
> - [ ] 能设计 streaming system
> - [ ] 能设计 data platform
> - [ ] 能设计 agent/tool execution service
>
> ### Interview
>
> - [ ] 30 sec answer
> - [ ] 2 min answer
> - [ ] 5 min answer
> - [ ] 15 min deep dive
>
> ---
>
> # 82. 最终目标
>
> 最终不要把自己的 Go 能力描述成：
>
> > “I have several years of Go experience.”
>
> 而应该做到面试中自然呈现：
>
> > “I have built and operated Go services in distributed systems, and I’m comfortable reasoning about concurrency, failure isolation, backpressure, performance, observability and production debugging.”
>
> 更进一步：
>
> ```text
> Go syntax
>      ↓
> Go runtime
>      ↓
> Concurrency
>      ↓
> Production service
>      ↓
> Distributed system
>      ↓
> Data platform
>      ↓
> AI / Agent infrastructure
> ```
>
> 这条链路就是本次 Go 准备的主线。
>
> **最终标准不是“我能回答多少 Go 面试题”。**
>
> 最终标准是：
>
> > **给我一个陌生的 Go service，我可以快速理解它；给我一个 production incident，我可以系统定位它；给我一个系统需求，我可以设计它；给我一段有问题的并发代码，我可以解释为什么错；给我一个性能问题，我可以用 evidence 而不是猜测去定位它。**
>
> ---
>
> # 83. 我的 Go 最终训练顺序
>
> ```text
> ① Go Language
>       ↓
> ② Slice / Map / Interface / Error
>       ↓
> ③ Goroutine / Channel / Select
>       ↓
> ④ Mutex / Atomic / Context
>       ↓
> ⑤ Memory Model
>       ↓
> ⑥ Scheduler / G-M-P
>       ↓
> ⑦ Escape Analysis / GC
>       ↓
> ⑧ pprof / race / benchmark / fuzz
>       ↓
> ⑨ HTTP / RPC / DB
>       ↓
> ⑩ Kafka / Redis
>       ↓
> ⑪ Timeout / Retry / Idempotency
>       ↓
> ⑫ Backpressure / Load Shedding
>       ↓
> ⑬ Observability
>       ↓
> ⑭ Production Incidents
>       ↓
> ⑮ Distributed Systems
>       ↓
> ⑯ System Design
>       ↓
> ⑰ Real Project Deep Dive
>       ↓
> ⑱ English Interview Simulation
> ```
>
> ---
>
> # 84. 每天执行模板
>
> 每天 2–3 小时：
>
> ```text
> 30 min  Go internals / reading
>
> 45 min  Practical coding
>
> 30 min  Production / debugging scenario
>
> 30 min  Distributed systems
>
> 30 min  English explanation
> ```
>
> 每周至少：
>
> ```text
> 1 个 Go mini-project
> 1 个 production incident drill
> 1 个 system design
> 1 次英文 mock interview
> 1 次真实项目 code archaeology
> ```
>
> ---
>
> # 85. 最重要的原则
>
> 不要把 Go 准备做成：
>
> ```text
> syntax
> ↓
> LeetCode
> ↓
> interview
> ```
>
> 应该做成：
>
> ```text
> Language
> ↓
> Runtime
> ↓
> Concurrency
> ↓
> Service
> ↓
> Failure
> ↓
> Performance
> ↓
> Distributed Systems
> ↓
> Architecture
> ```
>
> 对你的背景尤其应该把重点放在：
>
> ```text
> Go
> +
> Kafka
> +
> Flink
> +
> MySQL / Redis / ClickHouse
> +
> AWS
> +
> Kubernetes
> +
> Observability
> +
> Distributed Systems
> +
> AI / Agent Infrastructure
> ```
>
> 这样你的 Go 不是一个孤立技能，而会成为：
>
> > **Distributed Systems / Data Infrastructure / AI Infrastructure Engineer 的核心实现语言。**