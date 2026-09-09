# LLM + Agentic AI + Harness Engineering 学习路线

> 目标：以“非算法岗 / 非模型训练”为前提，建立能够在德国应聘 **AI Engineer / Agentic AI Engineer / AI Platform Engineer / LLM Engineer / AI-native Engineer / LLM & Agentic Consultant** 的工程能力。
>
> 重点不是“学会调用一个 LLM API”，而是能把 **LLM + tools + context + state + orchestration + eval + observability + security + deployment** 做成一个可以运行、测试、监控、恢复、迭代的生产系统。

---

## 0. 先理解你的目标岗位：你真正应该成为哪一种 Engineer？

### 0.1 “Harness” 不是一个传统、统一的职位名称

到 2026 年，德国招聘市场里已经可以看到 **Agentic AI Engineer、Agentic AI Platform Engineer、AI Platform Engineer、AI Native Engineer、LLM & Agentic AI Consultant** 等职位，而 **harness engineering** 更常作为这些岗位中的一种核心能力，而不是唯一的职位名称。

当前岗位的共同点非常明显：

- Python / backend engineering
- LLM API 与多模型调用
- Tool use / function calling
- RAG / embeddings / vector DB
- Agent orchestration
- MCP / API integration
- Evaluation / benchmark / regression testing
- Observability / tracing / cost / latency
- Guardrails / security / compliance
- Docker / Kubernetes / cloud / CI/CD
- 从 PoC 进入 production
- 对 agent failure 进行 debug，并设计 recovery / retry / human approval

德国近期职位例子：

- Next Ventures Berlin：Agentic AI Architect，明确要求生产级 agent、LLM orchestration、tool-using agents、coding-agent tooling、harness engineering，并把 evals / benchmark、computer-use 作为加分项。
- Accenture DACH Berlin：AI Native Engineer，要求生产级 multi-agent、RAG、tool invocation、memory、observability、eval harness、prompt versioning、cost / safety monitoring。
- NEURA Robotics：Agentic AI Engineer，明确涉及 agent harness、control loop、tool/skill interface、state/memory、retry、guardrails、long-horizon planning、human escalation。
- E.ON：Senior Agentic AI Platform Engineer，要求 LangChain/LangGraph/AutoGen/CrewAI、LLM inference、vector DB、tool orchestration、Docker/Kubernetes、cloud、Terraform、GitOps、observability、security/compliance。
- Hellmann：AI Platform Engineer，强调从“Claude + Python prototype”到可观察、可保护、可运营的生产系统，并涉及 LLM gateway、agent orchestration、MCP、observability、cost tracking。
- Capgemini：AI Platform Engineer，强调 Kubernetes、IaC、GitOps、LLM serving、RAG/vector DB、observability、security，以及 Python/FastAPI。

> 结论：你的目标不应是“成为 Prompt Engineer”，而应是 **AI Systems / Agentic Software Engineer**。算法不是主线，但软件工程、分布式系统、云、测试和 AI 系统工程是主线。

---

# 1. 能力模型总览

建议把整个学习过程理解成 8 层：

```text
L8  Job / Product / Architecture
      ↑
L7  Production / Cloud / CI-CD / Security
      ↑
L6  Eval / Observability / Reliability / LLMOps
      ↑
L5  Harness Engineering
      ↑
L4  Agentic Architecture / Orchestration
      ↑
L3  LLM Application Engineering / RAG / Tool Use
      ↑
L2  Backend / APIs / Data / Async
      ↑
L1  Software Engineering Fundamentals
```

其中最重要的不是每层“知道”，而是做到：

> **Learn → Build → Test → Measure → Deploy → Break → Debug → Improve**

你最终应该能够独立完成一个从自然语言需求到 production-like deployment 的 Agent 系统。

---

# 2. 学习目标与建议周期

假设已有一定编程基础：

| 阶段 | 建议时间 | 目标 |
|---|---:|---|
| Foundation | 4–6 周 | Python / Git / API / Docker / SQL / Linux |
| LLM Engineering | 4–6 周 | LLM API、structured output、tool calling、RAG |
| Agentic Engineering | 6–8 周 | agent loop、workflow、multi-agent、MCP |
| Harness Engineering | 6–8 周 | state、memory、execution、retries、sandbox、HITL |
| Eval + Observability | 4–6 周 | eval harness、tracing、quality/cost/latency metrics |
| Production Platform | 6–10 周 | FastAPI、Docker、K8s、cloud、CI/CD、security |
| Portfolio + Germany Job Prep | 4–8 周 | GitHub、system design、面试、德语/英文 JD |

**全职学习：约 6–9 个月。**

**每周 10–15 小时：约 9–14 个月。**

不建议把时间大量花在数学推导、Transformer 从零实现、训练大模型上；对你的目标岗位，它们属于理解加分项，不是主战场。

---

# 3. Module 1 — Software Engineering Foundation

## 3.1 目标

达到“公司里可以放心让你维护 backend service”的程度。

## 3.2 必学

### Python

- type hints
- dataclass / Pydantic
- exceptions
- context manager
- decorators
- generators
- async / await
- asyncio
- HTTP client
- testing
- logging
- packaging

推荐：

- Python 3.12+
- `uv`
- `ruff`
- `pytest`
- `mypy` 或 pyright
- Pydantic

### Backend

至少能独立使用：

- HTTP
- REST
- JSON
- authentication basics
- OAuth2 / JWT 基础
- FastAPI
- Webhook
- SSE / streaming
- background jobs

### Database

数据库这一块建议不要只学 PostgreSQL。对于 **LLM + Agentic + Harness**，你最终需要理解三类数据基础设施：

| 类别 | 推荐 | 在 Agent 系统中的典型用途 | 优先级 |
|---|---|---|---|
| Relational DB | PostgreSQL | user/task/state/config/audit/job data | ★★★★★ |
| Vector DB | Qdrant / pgvector | semantic retrieval / RAG / long-term knowledge | ★★★★★ |
| Graph DB | Neo4j | entity relationship / knowledge graph / dependency graph / planning context | ★★★★☆ |
| Graph DB 备选 | Dgraph | graph-native experimentation | ★★☆☆☆ |

至少掌握：

- PostgreSQL
- SQL
- transactions
- indexes
- schema design
- JSONB
- connection pooling
- Redis 基础（cache / queue / ephemeral state）

然后补上：

- **Qdrant 或 pgvector：至少一个做到可以独立设计 collection/schema、metadata filter、hybrid/semantic retrieval、upsert/delete、embedding pipeline**
- **Neo4j：至少做到可以建模 entity / relationship / traversal，并能用 Cypher 完成查询和更新**
- graph data modeling、knowledge graph 基础
- vector retrieval 与 graph traversal 的组合思路

> **重点：不是为了“会 4 个 DB”，而是知道 Agent 的不同状态为什么应该放在不同的数据系统里。** PostgreSQL 适合 durable business state，Vector DB 适合 semantic retrieval，Graph DB 适合 relationship-heavy knowledge。

### Engineering workflow

必须熟练：

```text
git branch
→ code
→ unit tests
→ lint/type check
→ commit
→ PR
→ CI
→ deploy
```

## 3.3 Practice

做一个：

> **Async Job API**

用户提交任务 → API 返回 task_id → worker 执行 → PostgreSQL 保存状态 → SSE 推送状态 → retry → error log。

不要涉及 LLM。

### Done 标准

你能解释：

- 为什么 async 有用？
- 什么是 idempotency？
- 为什么 DB transaction 会影响 agent reliability？
- 一个 API 如何支持 retry？
- 一个 worker crash 后怎么办？
- 如何写 integration test？

如果这些问题答不出来，先不要急着进入 multi-agent。

---

# 4. Module 2 — LLM Application Engineering

## 4.1 你需要懂什么程度？

目标不是训练模型，而是理解 **LLM inference 的工程约束**。

必须理解：

- token
- context window
- temperature
- structured output
- tool/function calling
- streaming
- system / developer / user instructions
- model latency
- input/output token cost
- prompt caching 基础
- model fallback
- model routing

知道但不必深入：

- Transformer
- attention
- embedding model
- fine-tuning / LoRA

## 4.2 API abstraction

不要只会一个 provider。

至少接触：

- OpenAI
- Anthropic
- Gemini / Vertex AI
- 一个 open-source model serving（例如 Ollama 或 vLLM）

目标：写一个自己的 `LLMProvider` interface：

```text
LLMProvider
 ├── OpenAIProvider
 ├── AnthropicProvider
 └── LocalProvider
```

### Done 标准

能够解释：

> 为什么线上 Agent 不应该把所有逻辑绑定在一个 LLM provider 上？

至少能从：

- reliability
- latency
- cost
- capability
- data governance

五个维度回答。

---

# 5. Module 3 — Structured Output + Tool Calling

这是 Agentic Engineering 的真正起点。

## 必学

### Structured Output

LLM 输出：

```json
{
  "action": "search_customer",
  "customer_id": "123"
}
```

而不是只输出自然语言。

学习：

- JSON schema
- Pydantic validation
- constrained generation 的基本概念
- schema versioning
- validation failure / retry

### Tool Calling

理解：

```text
User
 ↓
LLM
 ↓
Tool Selection
 ↓
Tool Execution
 ↓
Tool Result
 ↓
LLM
 ↓
Final Answer
```

## Practice Project 1

做一个：

> **Personal Operations Agent**

工具：

- `search_web()`
- `get_weather()`
- `search_calendar()`
- `create_calendar_event()`
- `send_email_draft()`

暂时禁止真实发送邮件，所有危险 tool 都进入 approval queue。

### Done 标准

Agent 能：

- 正确选择 tool
- 参数不完整时追问
- tool 失败时 retry
- 参数 validation failure 时修复
- 产生错误时不假装成功
- 对 destructive action 要求 approval

---

# 6. Module 4 — RAG / Context Engineering

RAG 仍然是企业 Agent 的重要基础能力。

德国招聘信息里，RAG、embedding、vector DB、context engineering 被反复要求。

## 6.1 必学

### Retrieval

- embeddings
- semantic search
- keyword search
- hybrid search
- reranking
- metadata filtering
- chunking
- contextual retrieval

### Vector DB

至少熟练一个：

- **Qdrant**
- PostgreSQL + pgvector
- Weaviate
- Pinecone

对于你的目标岗位，我建议把 **Qdrant + PostgreSQL** 作为主练组合：

> PostgreSQL 管 durable state，Qdrant 管 semantic retrieval。

你需要能独立实现：

```text
document ingestion
→ chunking
→ embedding
→ Qdrant upsert
→ metadata filtering
→ top-k retrieval
→ rerank
→ context assembly
→ answer
```

同时理解什么时候 pgvector 足够、什么时候需要 dedicated vector DB。

### Graph DB / Knowledge Graph

**Graph DB 建议加入你的主学习路线，尤其是 Neo4j。**

你不需要把图数据库学到 DBA 专家级，但要能把它真正放进 Agent architecture。

重点：

- graph vs relational modeling
- nodes / relationships / properties
- Cypher
- graph traversal
- entity resolution 基础
- knowledge graph
- provenance / source links
- dependency graph
- graph-based retrieval
- Graph + Vector 混合检索

推荐顺序：

1. **Neo4j：主学**
2. **Qdrant：主学**
3. Dgraph：了解其 graph-native 思路即可，不必和 Neo4j 做同等深度

### 为什么 Agentic 场景需要 Graph DB？

因为很多企业问题不是：

> “找和这段文字最相似的 10 个 chunk。”

而是：

> “这个客户 → 属于哪个组织 → 依赖哪些服务 → 最近修改过哪些组件 → 哪些 incident 可能受影响？”

这类问题本质上是 **relationship traversal**，Graph DB 往往比纯 vector similarity 更自然。

一个典型 Agent 可以采用：

```text
User
 ↓
Intent
 ↓
Graph traversal → 找关系 / 依赖 / 权限 / entities
 ↓
Vector retrieval → 找相关文档 / tickets / runbooks
 ↓
LLM
 ↓
Tool call
 ↓
PostgreSQL → 持久化任务状态 / audit
```

### Practice：Knowledge Graph Agent

建立一个 IT Incident / Service Dependency Agent：

```text
Service A
 ├── depends_on → Database B
 ├── owned_by → Team C
 ├── deployed_to → Cluster D
 └── documented_by → Runbook E
```

Agent 收到：

> “为什么 Service A 最近频繁失败？”

应该能够：

1. 从 Neo4j 找 dependency chain
2. 从 Qdrant 找 runbook / incident / logs 文档
3. 合并 graph context + vector context
4. 生成 hypothesis
5. 调用查询工具验证
6. 最终输出带 provenance 的诊断结果

### Done 标准

达到这一水平即可：

- 能解释 PostgreSQL / Redis / Qdrant / Neo4j 各自解决什么问题
- 能自己设计 agent state schema
- 能自己设计 vector collection
- 能写基本 Cypher traversal
- 能做 graph + vector hybrid retrieval
- 能解释为什么某个 context 应该从 graph 来，而不是 vector search

## 6.2 Context Engineering

要学会管理：

```text
System instructions
+ user intent
+ retrieved knowledge
+ tool descriptions
+ state
+ previous events
+ constraints
```

核心问题：

> “模型知道什么”不是最重要的；“在这一轮执行中，模型到底应该看到什么”更重要。

## Practice Project 2

做一个：

> **Enterprise Knowledge Agent**

数据：10–50 个 PDF / Markdown / web documents。

功能：

- document ingestion
- chunking
- embedding
- vector search
- citations
- query rewriting
- reranking
- answer generation
- retrieval evaluation

### Done 标准

你可以量化：

- retrieval hit rate
- answer correctness
- citation correctness
- latency
- token cost

不能只说“感觉回答不错”。

---

## 6.5 Agent Data Layer：把 DB 真正放进 Agent Architecture

建议你最终形成下面的心智模型：

```text
                    ┌───────────────┐
                    │     Agent     │
                    └───────┬───────┘
                            │
          ┌─────────────────┼─────────────────┐
          ↓                 ↓                 ↓
   PostgreSQL            Qdrant             Neo4j
   durable state        semantic memory    relationships
   tasks / users        documents           entities
   audit / config       embeddings          dependencies
          │                 │                 │
          └─────────────────┼─────────────────┘
                            ↓
                       Context Builder
                            ↓
                           LLM
```

> Harness Engineering 里最重要的不是“用了哪个 framework”，而是 **state、memory、context、tool execution、audit** 的边界设计。DB 是这些边界的基础设施。

---

# 7. Module 5 — Agentic Architecture

现在进入核心。

Anthropic 的工程实践强调：先选择最简单、可组合的模式，再在确有需要时增加 agent complexity；workflow 更偏确定性，而 agents 让模型动态决定下一步行动。citeturn854264search10

## 7.1 必须掌握的 Agent Patterns

### Pattern A — Single Agent + Tools

```text
LLM
 ├── search
 ├── database
 ├── API
 └── calculator
```

这是第一优先级。

### Pattern B — Router

```text
User
 ↓
Router
 ├── Search Agent
 ├── Data Agent
 └── Coding Agent
```

### Pattern C — Sequential Workflow

```text
Input
 ↓
Planner
 ↓
Researcher
 ↓
Writer
 ↓
Reviewer
```

### Pattern D — Parallel

```text
             ┌→ Agent A ─┐
Task → Split ┤→ Agent B ─┼→ Aggregator
             └→ Agent C ─┘
```

### Pattern E — Supervisor

```text
Supervisor
 ├── Researcher
 ├── Analyst
 └── Executor
```

### Pattern F — Plan → Execute → Verify

```text
Plan
 ↓
Execute
 ↓
Verify
 ↓
Retry / Replan
```

### Pattern G — Human-in-the-loop

```text
Agent
 ↓
Risky Action
 ↓
Human Approval
 ↓
Execute
```

LangGraph 目前把 durable execution、streaming、human-in-the-loop、persistence 等作为 agent orchestration 的基础能力；它支持通过 interrupt 暂停执行并在之后恢复。citeturn854264search4turn854264search11

---

# 8. Module 6 — LangGraph / Agent Frameworks

不要追求“学十个框架”。

## 推荐学习顺序

```text
1. Raw LLM API
2. 自己写 agent loop
3. LangGraph
4. OpenAI Agents SDK
5. MCP
6. 了解 LangChain / AutoGen / CrewAI / PydanticAI
```

### 为什么必须自己写过 Agent Loop？

因为如果直接使用 framework：

```python
agent.run()
```

你很容易不知道：

- state 在哪里？
- retry 在哪里？
- tool call 为什么失败？
- context 在哪里拼？
- token 花在哪里？
- loop 为什么死了？
- 为什么出现重复 tool call？

至少手写一次：

```python
while not finished:
    decision = llm(state)

    if decision.tool_call:
        result = execute_tool(decision)
        state = update_state(state, result)
    else:
        return decision.final_answer
```

然后再用 LangGraph 实现同一系统。

这样你学的是 architecture，而不是 API。

---

# 9. Module 7 — MCP

MCP 应当作为 Agent integration 能力学习，而不是只背协议名。

MCP 的核心之一是让 server 向模型暴露可调用 tools；当前规范也明确强调 tool invocation 的 trust & safety 与 human approval。citeturn854264search7turn854264search13

## 必学

- MCP client
- MCP server
- tools
- resources
- prompts
- schemas
- authentication basics
- authorization / scopes
- tool discovery
- tool safety

## Practice

创建你自己的 MCP Server：

```text
my-company-mcp
 ├── search_customer
 ├── get_invoice
 ├── query_database
 ├── create_ticket
 └── deploy_service
```

并让 Agent 通过 MCP 使用它。

### Done 标准

你能够解释：

> “为什么 MCP 和普通 REST API 不完全是一回事？”

并能从：

- discovery
- schema
- model-controlled tools
- context exposure
- authorization
- human approval

进行解释。

---

# 10. Module 8 — Harness Engineering：你的主线方向

## 10.1 什么是 Harness？

可以把它理解为：

> **给 LLM Agent 提供持续执行能力、工具能力、状态能力、环境能力、安全能力、观测能力和恢复能力的软件系统。**

一个简单模型：

```text
              ┌───────────────────────┐
              │          Agent        │
              │     LLM reasoning     │
              └───────────┬───────────┘
                          │
              ┌───────────▼───────────┐
              │       HARNESS         │
              │                       │
              │ execution loop        │
              │ context management    │
              │ state / memory        │
              │ tool registry         │
              │ retries / recovery    │
              │ guardrails            │
              │ sandbox                │
              │ human approval         │
              │ observability          │
              │ lifecycle hooks        │
              └───────────┬───────────┘
                          │
        ┌─────────────────┼──────────────────┐
        ▼                 ▼                  ▼
      APIs             Database          Runtime
```

OpenAI 在 2026 年进一步把 Agent SDK 描述为更强的 model-native harness，并加入 sandbox execution，同时强调把 harness 与 compute 分离以提高 security、durability 与 scale。citeturn854264search9

## 10.2 Harness 的核心模块

### A. Execution Loop

管理：

- next action
- tool invocation
- stopping condition
- max steps
- timeout
- retry

### B. State

不要把 agent 当成“纯聊天”。

设计：

```text
AgentState
 ├── task
 ├── messages
 ├── plan
 ├── tool_results
 ├── artifacts
 ├── current_step
 ├── error_count
 └── approval_status
```

### C. Memory

区分：

- conversation memory
- task state
- working memory
- long-term memory
- external knowledge

不要把“把所有历史聊天塞进 context”当成 memory architecture。

### D. Tool Registry

每个 tool 应该有：

- name
- description
- input schema
- permissions
- risk level
- timeout
- retry policy
- audit log

### E. Failure Recovery

你必须考虑：

```text
LLM timeout
Tool timeout
Tool returns invalid data
Network failure
Duplicate action
Invalid JSON
Hallucinated parameters
Infinite loop
State corruption
Model provider outage
```

### F. Human Approval

例如：

```text
read email          → automatic
search DB            → automatic
create ticket        → automatic
send email           → approval
transfer money       → approval
production deploy    → approval
```

### G. Sandbox

对于 coding / computer-use agent：

- filesystem isolation
- process isolation
- network restrictions
- resource quotas
- timeout
- credential isolation

这是非常值得你建立的能力，因为 agent + code execution 已经成为现代 harness 的重要方向。OpenAI 的 Agents SDK 也已经提供 sandbox execution 能力。citeturn854264search9

---

# 11. Practice Project 3 — 自己写一个 Agent Harness

这是整个课程最重要的项目。

## 项目名

**MiniAgent Runtime**

目标：不用 LangGraph，自己写一个最小 agent runtime。

### 目录

```text
mini-agent/
├── agent/
│   ├── loop.py
│   ├── state.py
│   ├── planner.py
│   ├── executor.py
│   ├── memory.py
│   ├── tools.py
│   ├── guardrails.py
│   ├── retry.py
│   └── lifecycle.py
├── tools/
├── storage/
├── evals/
├── observability/
├── api/
├── tests/
└── docker/
```

### 必须实现

```text
Task
 ↓
Plan
 ↓
Tool call
 ↓
Tool result
 ↓
State update
 ↓
Next decision
 ↓
Verify
 ↓
Success / Retry / Human approval
```

### 必须加入

- max steps
- timeout
- retry
- backoff
- state persistence
- tool permissions
- tool schema validation
- idempotency key
- trace ID
- audit log
- human approval

### Done 标准

你能回答：

> “Agent 在第 17 步 crash 了，如何从第 17 步恢复？”

> “一个 POST tool 被错误调用两次，怎么避免重复副作用？”

> “Agent 连续 10 次调用同一个工具怎么办？”

> “用户半夜批准一个 action，系统怎样继续昨天暂停的 run？”

这才是 Harness Engineering，而不是 prompt engineering。

---

# 12. Module 9 — Agent Evaluation / Eval Harness

这是最容易被初学者忽略、但德国企业岗位非常看重的一层。

核心思想：

> **不要评估“这个回答看起来不错”，而要评估“这个 Agent 是否完成任务，以及为什么失败”。**

当前招聘信息中，Agent evals / evaluation harness、benchmark、testing、monitoring 都在不断出现；Accenture 的职位甚至明确要求候选人拥有 eval harness 经验。citeturn507175search2

## 9.1 Eval 类型

### Offline Eval

固定 dataset：

```text
input
expected behavior
expected tool
expected output
risk level
```

### Online Eval

生产数据抽样进行：

- success rate
- tool failure rate
- escalation rate
- human correction rate
- latency
- cost

### Regression Eval

每次改 prompt / model / tool 都跑：

```text
v1 → 82%
v2 → 86%
v3 → 79%  ← regression
```

## 9.2 Agent-specific metrics

至少学会统计：

- task success rate
- tool selection accuracy
- tool argument correctness
- completion rate
- retry rate
- average steps
- failed-step rate
- hallucination rate
- citation correctness
- cost per task
- latency per task
- human escalation rate

## Practice

为你的 Harness 创建：

```text
evals/
├── dataset.jsonl
├── evaluator.py
├── runner.py
├── metrics.py
└── reports/
```

每次改代码执行：

```text
100 tasks
→ run agent
→ collect trace
→ evaluate
→ compare with baseline
→ fail CI if regression > threshold
```

---

# 13. Module 10 — Observability / Tracing

Agent 是多步骤系统，因此传统的“看 HTTP 500”远远不够。

必须看到：

```text
Run
 ├── LLM call
 │    ├── input tokens
 │    ├── output tokens
 │    ├── latency
 │    └── model
 ├── tool call
 │    ├── name
 │    ├── args
 │    ├── latency
 │    └── error
 ├── state transition
 └── final result
```

OpenAI Agents SDK 当前内置 tracing，可记录 LLM generations、tool calls、handoffs、guardrails 和自定义事件，并支持开发与生产阶段的调试和监控。citeturn854264search0

## 学习技术

优先：

- OpenTelemetry
- traces
- spans
- metrics
- logs
- Prometheus
- Grafana

AI-specific 可选：

- LangSmith
- Langfuse
- Arize Phoenix
- Braintrust

### Done 标准

用户说：

> “昨天晚上为什么这个任务失败？”

你应该能通过 trace 快速找到：

```text
task
→ model decision
→ tool call
→ API 500
→ retry
→ second call
→ schema failure
→ final failure
```

而不是打开服务器日志慢慢猜。

---

# 14. Module 11 — Security / Guardrails / Governance

德国 enterprise 环境尤其重要。

招聘中已经出现：

- security
- privacy
- compliance
- Responsible AI
- prompt security
- EU AI Act
- ISO-oriented controls

例如 E.ON 的 Agentic AI Platform 职位明确列出 IT Security、Datenschutz、Responsible AI 和 Compliance；Blockbrain 的 platform role 也强调 ISO 27001、EU AI Act 等合规背景。citeturn795761search3turn795761search11

## 必学

### Application Security

- secret management
- OAuth
- IAM
- RBAC
- least privilege
- audit logging

### LLM Security

- prompt injection
- indirect prompt injection
- tool poisoning
- data exfiltration
- excessive agency
- insecure tool design
- output validation

### Agent Security

重点不是“模型能不能回答坏问题”，而是：

> 模型能不能通过工具造成真实世界副作用？

因此要设计：

```text
Permission
→ Policy
→ Validation
→ Approval
→ Execution
→ Audit
```

---

# 15. Module 12 — Production / Cloud / DevOps

如果你的目标是“非算法 + Agentic + Harness”，这里是决定你能不能进入公司生产环境的一层。

## 必学

### Docker

熟练：

- image
- container
- volume
- network
- multi-stage build
- healthcheck

### Kubernetes

达到可以部署 Agent system 的程度：

- Pod
- Deployment
- Service
- ConfigMap
- Secret
- Ingress
- autoscaling basics
- Job / CronJob
- resource limits

### Cloud

三选一主攻：

- AWS
- Azure
- GCP

德国企业环境下，Azure / AWS 都非常常见。

至少掌握：

```text
Compute
Storage
Database
IAM
Networking
Logging
Monitoring
Container runtime
Secrets
```

### IaC

至少：

- Terraform

### CI/CD

至少：

- GitHub Actions 或 GitLab CI

---

# 16. Module 13 — LLMOps / AI Platform

这是从“Agent Developer”向“Agent Platform Engineer”升级的关键。

## 你需要懂

### Model Gateway

```text
Application
   ↓
AI Gateway
   ├── OpenAI
   ├── Anthropic
   ├── Azure
   └── Local Model
```

功能：

- routing
- fallback
- rate limit
- budget
- policy
- logging
- caching

### Inference

理解：

- hosted API
- self-hosted model
- vLLM
- batching
- concurrency
- GPU basics

不要求你成为 GPU kernel 专家。

### Vector / Context Infrastructure

理解：

- pgvector
- vector DB
- embedding service
- retrieval service
- document ingestion

德国的 AI Platform 岗位已经把 LLM gateway、vector store、agent orchestration、observability、cost tracking、MCP 等组合在一起。citeturn795761search0turn795761search1

---

# 17. Module 14 — Multi-Agent Systems

不要过早学习。

正确顺序：

```text
single agent
→ workflow
→ routing
→ planner/executor
→ supervisor
→ multi-agent
```

## 必学

- agent delegation
- shared state
- message passing
- role separation
- coordination
- parallel execution
- conflict resolution
- budget control
- failure containment

### 重点

你需要知道：

> “什么时候不应该做 multi-agent？”

一个好的 Engineer 会主动把复杂系统简化。

---

# 18. Module 15 — Coding Agents / Computer-use Agents

这是你进入 “Agent Harness” 方向后非常值得深入的一条支线。

## 学什么

- coding agent loop
- repository inspection
- file editing
- shell execution
- tests
- git diff
- patch application
- sandbox
- browser-use basics
- computer-use safety

### Practice Project 4

做一个：

> **Repo Repair Agent**

输入：GitHub repo + bug report

Agent：

```text
inspect repo
→ search code
→ make plan
→ edit files
→ run tests
→ inspect failures
→ retry
→ produce patch
```

要求：

- sandbox
- limited network
- command timeout
- filesystem restriction
- test result capture
- diff review
- max iterations
- human approval before git push

这个项目非常适合你的“harness”定位。

---

# 19. Module 16 — Distributed Systems 基础

这是很多“只会 AI framework”的候选人真正的短板。

你至少应该懂：

- queues
- workers
- retries
- idempotency
- eventual consistency
- transactions
- locking
- concurrency
- race conditions
- rate limiting
- backpressure
- caching
- distributed tracing

例如 Agent 做：

```text
Create order
→ charge payment
→ update DB
→ send email
```

如果第 2 步成功、第 3 步失败，怎么办？

Agentic system 一定会遇到类似问题。

---

# 20. Practice Project 5 — Production-grade Agent Platform

最终项目建议做成一个完整作品：

## “Enterprise Operations Agent Platform”

### Use case

企业员工：

> “检查这个客户的合同、订单、未付款 invoice，然后如果满足条件，创建 ticket。”

### Architecture

```text
                    Web UI
                      │
                      ▼
                  FastAPI
                      │
                      ▼
                Agent Runtime
                      │
       ┌──────────────┼───────────────┐
       ▼              ▼               ▼
    Planner          Tools          Memory
       │              │               │
       │         ┌────┼────┐          │
       │         ▼    ▼    ▼          ▼
       │        CRM  DB   Ticket    Postgres
       │
       ▼
     Verify
       │
       ▼
  Human Approval
       │
       ▼
    Execute
       │
       ▼
    Audit Log
```

### Stack

建议：

```text
Python
FastAPI
PostgreSQL
pgvector
Pydantic
LangGraph
MCP
OpenAI / Anthropic
Docker
Kubernetes
OpenTelemetry
Prometheus
Grafana
Terraform
GitHub Actions
```

不需要所有组件在 Day 1 都加入；逐步演进。

---

# 21. 推荐的工程演进路线

你的 GitHub 最好不是 10 个 tutorial repo，而是一个系统不断升级：

```text
v0 — LLM CLI
      ↓
v1 — LLM API service
      ↓
v2 — Tool-calling agent
      ↓
v3 — RAG agent
      ↓
v4 — Stateful workflow
      ↓
v5 — Agent harness
      ↓
v6 — MCP integration
      ↓
v7 — Eval harness
      ↓
v8 — Observability
      ↓
v9 — Human-in-the-loop
      ↓
v10 — Docker
      ↓
v11 — Kubernetes
      ↓
v12 — CI/CD
      ↓
v13 — Security / policy
      ↓
v14 — Production architecture
```

这会比“看了 50 门课程”更有求职价值。

---

# 22. 每一个项目都必须有这 7 个东西

以后不要只写 README：

> “This project is an AI agent using LangGraph.”

应该写成：

### 1. Architecture

画 architecture diagram。

### 2. Design decisions

解释：

> Why LangGraph instead of a custom loop?

> Why pgvector instead of a dedicated vector DB?

### 3. Failure modes

列出至少 10 个失败场景。

### 4. Evaluation

展示：

```text
Task success: 86%
Tool correctness: 93%
Median latency: 4.2s
Cost/task: €0.03
```

数据必须来自真实实验，不要编造。

### 5. Observability

放 trace screenshot / dashboard screenshot。

### 6. Security

解释：

> tool permissions / secrets / approval / injection defense

### 7. Deployment

至少：

```text
Docker compose
```

最好能有：

```text
Kubernetes deployment
```

---

# 23. 你应该掌握到什么程度？

用四档衡量：

| Level | 标准 |
|---|---|
| L0 | 听说过 |
| L1 | 能跟着 tutorial 做 |
| L2 | 不看教程独立写出来 |
| L3 | 能 debug / design / explain trade-offs |
| L4 | 能设计 production system 并 review 他人代码 |

你的目标应该是：

| 技能 | 目标 |
|---|---:|
| Python | L4 |
| Git / testing | L4 |
| HTTP / API | L4 |
| PostgreSQL | L3 |
| FastAPI | L3 |
| Docker | L3 |
| LLM API | L4 |
| Structured Output | L4 |
| Tool Calling | L4 |
| RAG | L3 |
| Agent Architecture | L4 |
| LangGraph | L3 |
| MCP | L3 |
| Harness Engineering | L4 |
| Eval | L4 |
| Observability | L3 |
| Security | L3 |
| Kubernetes | L3 |
| Cloud | L3 |
| Terraform | L2/L3 |
| Multi-agent | L3 |
| Fine-tuning | L1/L2 |
| PyTorch | L1/L2 |
| ML Theory | L1/L2 |

这非常符合“非算法 Agentic/Platform Engineer”的定位。

---

# 24. 德国求职：岗位关键词

不要只搜索：

> “AI Engineer Germany”

建议同时搜索：

```text
Agentic AI Engineer
Agentic AI Software Engineer
Agentic AI Platform Engineer
AI Platform Engineer
LLM Engineer
Generative AI Engineer
Applied AI Engineer
AI Native Engineer
AI Systems Engineer
LLM & Agentic AI Consultant
AI Solutions Engineer
GenAI Platform Engineer
LLMOps Engineer
AI/ML Platform Engineer
```

### Harness 相关搜索

```text
harness engineering
agent harness
AI agent runtime
agent infrastructure
agent platform
coding agent
agent runtime engineer
```

因为 “Harness Engineer” 在现阶段还是比 “AI Engineer / Agentic AI Engineer / AI Platform Engineer” 更小众的命名。

---

# 25. 德国市场：你应该重点关注哪些技术组合？

根据近期岗位，最值得投资的是：

```text
Python
 +
LLM APIs
 +
RAG
 +
Agents
 +
LangGraph / similar orchestration
 +
MCP
 +
Evaluation
 +
Observability
 +
FastAPI
 +
Docker
 +
Kubernetes
 +
Cloud
 +
Security
```

E.ON、Capgemini、Hellmann、HENSOLDT 等近期岗位都明确体现了这种“AI + platform + production engineering”的组合。citeturn795761search0turn795761search3turn795761search5turn795761search12

---

# 26. 德语应该学到什么程度？

这里要非常现实。

德国 Agentic / AI Platform 岗位的语言要求并不统一：

- 有些国际团队偏英文。
- 面向 DACH 客户的 consulting / enterprise roles 经常要求 **German B2–C1，甚至 C1**。
- 例如近期多个德国岗位明确写出 German C1 + English B2 或 German/English C1。citeturn507175search7turn795761search2turn795761search8

因此建议：

```text
English technical communication ≥ B2/C1
German ≥ B1 → B2 → C1
```

求职早期，不必等德语完美再投；但如果你想扩大德国本土 enterprise / consulting 岗位池，B2 会明显提高选择面，C1 更强。

---

# 27. 你需要不要学 Machine Learning / Deep Learning？

需要，但只学到“能够和 ML Engineer 正常合作”的程度。

## 应该懂

- train / validation / test
- overfitting
- embeddings
- classification
- ranking
- precision / recall
- model inference
- fine-tuning 基本概念
- LoRA 基本概念
- evaluation

## 可以暂缓

- 从零实现 Transformer
- CUDA kernel
- distributed model training
- RLHF 数学细节
- diffusion architecture

除非你以后转算法岗位。

---

# 28. AI Coding Tools：也要成为生产力的一部分

当前德国部分 AI-native 岗位已经把 Claude Code、Cursor、GitHub Copilot、Codex、skills、hooks 等 coding-agent tooling 列入工作内容。citeturn507175search0turn795761search8

你的学习方式建议变成：

```text
AI → 生成初稿
你 → review
AI → tests
你 → architecture review
AI → refactor
你 → security review
AI → documentation
```

不是：

```text
AI → generate code → copy paste → done
```

尤其要练：

- repository-level prompting
- context selection
- code review
- test generation
- debugging
- refactoring
- migration
- writing specs

---

# 29. 面试准备

最终你会被问的，很可能不是：

> “Transformer 的 attention 怎么计算？”

而是：

### Agent Architecture

> Design an agent that can execute long-running business tasks.

### Tool Safety

> How would you prevent an agent from sending an email twice?

### Reliability

> What happens when a tool fails after a side effect has happened?

### Evaluation

> How do you know your agent improved after changing the prompt?

### RAG

> How would you diagnose poor retrieval quality?

### Observability

> How do you debug a failed multi-step run?

### Scaling

> How would you run 10,000 agent tasks per hour?

### Security

> How do you defend against indirect prompt injection?

### MCP

> How would you securely expose enterprise systems to an agent?

### Kubernetes

> How would you deploy an agent worker with retries and autoscaling?

### Architecture

> Why do we even need agents here? Why not a deterministic workflow?

最后一个问题特别重要。

---

# 30. 每周学习方法

不要采用：

```text
Monday: 看课程
Tuesday: 看课程
Wednesday: 看课程
Thursday: 看课程
Friday: 看课程
Saturday: 看课程
Sunday: 总结
```

推荐：

```text
20% Learn
30% Build
20% Debug
15% Read source / docs
10% Write notes
5% Interview / explain
```

### 一个理想学习日

```text
09:00 — 阅读官方文档
10:00 — 实现 feature
11:30 — 写 tests
14:00 — 故意制造 failure
15:00 — trace/debug
16:00 — benchmark
17:00 — 写 README / architecture note
```

---

# 31. 12 个月建议路线图

## Month 1

- Python
- Git
- FastAPI
- PostgreSQL
- Docker
- pytest

**项目：Async Job API**

## Month 2

- LLM APIs
- structured output
- tool calling
- streaming

**项目：Tool Agent**

## Month 3

- embeddings
- vector DB
- RAG
- retrieval evaluation

**项目：Enterprise RAG Agent**

## Month 4

- agent loop
- planner
- executor
- workflow
- LangGraph

**项目：Research Agent**

## Month 5

- state
- persistence
- retry
- idempotency
- human approval

**项目：MiniAgent Runtime**

## Month 6

- MCP
- tool registry
- policy engine
- guardrails

**项目：MCP Enterprise Agent**

## Month 7

- eval harness
- regression testing
- tracing
- OpenTelemetry

**项目：Agent Eval Platform**

## Month 8

- Docker
- Kubernetes
- cloud
- CI/CD

## Month 9

- LLM gateway
- routing
- cost control
- model fallback
- security

## Month 10

- coding agent
- sandbox
- repository agent

**项目：Repo Repair Agent**

## Month 11

- system design
- distributed systems
- performance
- architecture reviews

## Month 12

- portfolio polish
- Germany CV
- English interviews
- German interviews
- applications

---

# 32. 你什么时候可以开始投德国岗位？

不要等“全部学完”。

建议设置三个投递门槛：

## Gate A — Junior / Internship / Working Student / Entry-ish

达到：

- Python
- API
- LLM API
- Tool calling
- RAG
- 一个 agent project
- Docker

就可以开始观察岗位。

## Gate B — Mid-level AI/Agent Engineer

达到：

- strong Python
- production backend
- RAG
- agent orchestration
- evaluation
- observability
- Docker
- cloud
- 至少 2 个完整 portfolio projects

## Gate C — Agentic / Harness / AI Platform 强岗位

达到：

- agent runtime / harness
- eval harness
- tracing
- sandbox
- MCP
- state persistence
- Kubernetes
- security
- CI/CD
- architecture design

并有一个“production-like”项目。

---

# 33. 最终 GitHub Portfolio 应该长什么样？

建议只做 3–4 个精品项目。

## Project A — `enterprise-rag-agent`

证明：

- RAG
- retrieval
- citations
- eval

## Project B — `mini-agent-runtime`

证明：

- harness
- state
- memory
- tools
- retry
- HITL

## Project C — `agent-eval-platform`

证明：

- benchmark
- evaluation
- tracing
- regression

## Project D — `repo-repair-agent`

证明：

- coding agent
- sandbox
- long-horizon tasks
- recovery

### 这四个项目组合起来

几乎可以把：

```text
LLM
RAG
Agent
Harness
MCP
Eval
Observability
Security
Platform
Coding Agent
```

串成一条完整职业故事。

---

# 34. 最终能力图

```text
                       AI Product
                           │
                    Agent Architecture
                           │
                 ┌─────────┴─────────┐
                 │                   │
             LLM Apps             Agents
                 │                   │
             RAG/Context       Planning/Tools
                 │                   │
                 └─────────┬─────────┘
                           │
                    Harness Runtime
                           │
       ┌──────────┬───────┼────────┬──────────┐
       ▼          ▼       ▼        ▼          ▼
     State      Tools   Memory    Safety    Execution
       │          │       │        │          │
       └──────────┴───────┼────────┴──────────┘
                           │
                 Eval + Observability
                           │
                  Production Platform
                           │
        ┌──────────┬───────┼────────┬──────────┐
        ▼          ▼       ▼        ▼          ▼
      Docker     K8s    Cloud     CI/CD    Security
                           │
                        Germany
                           │
                  AI / Agent / Platform
                         Engineer
```

---

# 35. 最重要的学习原则

### 原则 1

**少学“框架 API”，多学“系统行为”。**

### 原则 2

**每学一个 agent pattern，都必须制造 failure。**

### 原则 3

**每个项目必须有 evaluation。**

### 原则 4

**每个项目必须有 observability。**

### 原则 5

**每个 tool 都应该考虑 permission / side effect / idempotency。**

### 原则 6

**Agent 越强，Harness 越重要。**

### 原则 7

**不要为了 multi-agent 而 multi-agent。**

### 原则 8

**生产系统优先于 demo。**

### 原则 9

**GitHub 是你的第二份简历。**

### 原则 10

**面试时要能解释 trade-off，而不是背术语。**

---

# 36. 一句话定义你的目标画像

> **A software engineer who builds reliable, observable, evaluable and secure LLM-powered agent systems — including the runtime/harness, tools, context, state, orchestration and production infrastructure around the model.**

这比“我会 LangChain、会 Prompt、会 RAG”更接近德国当前 Agentic / AI Platform 工程岗位真正需要的能力组合。

---

# 37. 当前岗位与官方技术资料参考

## 德国岗位参考

1. Next Ventures — Agentic AI Architect / AI-Native Builder, Berlin
   - https://de.linkedin.com/jobs/view/agentic-ai-architect-ai-native-builder-berlin-or-remote-%E2%82%AC120k-at-next-ventures-4462220988
2. Accenture DACH — AI Native Engineer (Agentic / Applied), Berlin
   - https://de.linkedin.com/jobs/view/ai-native-engineer-agentic-applied-at-accenture-dach-4453253711
3. NEURA Robotics — Agentic AI Engineer, Riederich
   - https://de.linkedin.com/jobs/view/agentic-ai-engineer-mensch-at-neura-robotics-4422958388
4. E.ON — Senior Agentic AI Platform Engineer
   - https://de.linkedin.com/jobs/view/senior-agentic-ai-platform-engineer-m-w-d-at-e-on-deutschland-4456597538
5. Hellmann — AI Platform Engineer
   - https://hellmann.wd103.myworkdayjobs.com/en-US/hellmannexternaljobs/job/AI-Platform-Engineer--m-w-d-_R20265462
6. Capgemini — AI Platform Engineer
   - https://careers.capgemini.com/job/Berlin-AI-Platform-Engineer-%28wmd%29/1281361001/
7. HENSOLDT — AI Platform Engineer MLOps & LLMOps
   - https://jobs.hensoldt.net/job/Ulm-AI-Platform-Engineer-MLOps-%26-LLMOps-%28wmd%29-89077/1355701755/
8. Jedox — AI Platform Engineer
   - https://jedox.jobs.personio.de/job/2700696

## 官方技术资料

1. Anthropic — Building effective agents
   - https://www.anthropic.com/engineering/building-effective-agents
2. OpenAI — The next evolution of the Agents SDK
   - https://openai.com/index/the-next-evolution-of-the-agents-sdk/
3. OpenAI Agents SDK — Tracing
   - https://openai.github.io/openai-agents-python/tracing/
4. LangGraph — Overview
   - https://docs.langchain.com/oss/python/langgraph/overview
5. LangGraph — Interrupts / Human-in-the-loop
   - https://docs.langchain.com/oss/python/langgraph/interrupts
6. Model Context Protocol — Tools
   - https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/server/tools.mdx

> 招聘职位会变化；以上岗位链接用于说明当前岗位能力趋势，而不是保证职位持续开放。学习路线以“能力组合”而非某一家公司的 JD 为准。
