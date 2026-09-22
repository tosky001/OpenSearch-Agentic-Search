# AgenticSearch

**English** | [简体中文](README.zh-CN.md)

**AgenticSearch is an enterprise AI agent product developed by the Alibaba Cloud OpenSearch team.** It combines information retrieval, reasoning, planning, and tool execution for complex business scenarios such as **deep research, operations diagnostics, data insights, and shopping assistance**. Users describe their goals, and AgenticSearch brings together enterprise knowledge and web information to break down tasks, invoke tools, and adapt its approach based on execution feedback, delivering research reports, data insights, or actionable results.

[**Try AgenticSearch →**](https://opensearch.console.aliyun.com/cn-shanghai/agentic-search#/agent/session) · [**Product Documentation →**](https://help.aliyun.com/zh/open-search/search-platform/product-overview/agentic-search-ai-driven-next-generation-enterprise-search)

> This repository introduces the technical architecture behind AgenticSearch and our Self-Evolving Agents approach. Implementation code will be released progressively.

## Technical Architecture

AgenticSearch centers on the **AgentLoop execution cycle**, coordinating task orchestration, long- and short-term memory, skill accumulation, multimodal knowledge retrieval, and sandboxed tool execution to support the complete path from task intake to result delivery.

![AgenticSearch architecture: execution feedback loop, self-updating long- and short-term memory, AutoSkill, multimodal enterprise knowledge integration, and sandboxed execution](agenticsearch-framework-zh.png)

Tasks enter through the console, IM, API, or Skill interfaces. AgentManager orchestrates tasks and persists their state, while ControlPlane manages prompts and tool configuration. AgentLoop loads relevant memory, skills, and context on demand, then plans, makes decisions, and invokes tools. Execution results inform the next round of decisions, while progress is streamed back through SSE. Task traces also drive memory updates and skill refinement, allowing experience to benefit future tasks.

### Technical Strengths

- **Feedback-driven complex task solving:** AgentLoop connects context loading, reasoning, and execution, adjusting subsequent steps based on tool outputs and intermediate results to support tasks that span multiple steps and tools.
- **Self-updating long- and short-term memory:** AgenticMemory combines long-term user profiles with short-term conversational context. It loads relevant memory on demand and continually updates and consolidates it from task traces, enabling context reuse across conversations and tasks.
- **Experience-driven skill evolution:** AutoSkill follows a cycle of skill extraction, evaluation and admission, publication and reuse, and execution feedback. It turns task experience into reusable skills so that future tasks can apply validated methods.
- **Multimodal enterprise knowledge integration:** PageIndex indexes both document structure and content across text, tables, images, and code. Context expansion and rich-text reconstruction preserve the context surrounding evidence. AgenticWiki captures knowledge, methodologies, and task experience; retrieval across source documents and Wiki entries provides traceable citations.
- **Controlled tool execution:** Sandbox hosts web search, multimodal knowledge retrieval, browser, code, terminal, and file tools, with isolated execution and access control. SandboxManager handles lifecycle management, supporting coordinated tool use in complex tasks.
- **On-demand enterprise integration:** Connectors to OpenSearch, Elasticsearch, DingTalk and Feishu documents, and external capabilities such as DataWorks and DMS Data Agent bring existing enterprise data and tools into the task execution workflow.

## No. 1 on the GAIA Leaderboard

**Our agent solution built on AgenticSearch achieved the No. 1 position on the GAIA leaderboard.**

GAIA evaluates general-purpose AI assistants on real-world tasks that require a combination of multi-step reasoning, information retrieval, multimodal understanding, and tool use. To address these challenges, we built our Self-Evolving Agents approach on the AgenticSearch execution framework, combining recursive improvement within a task, experience reuse across tasks, and verification and ensemble across agents to improve task-solving capabilities.

> **Leaderboard screenshot: to be added.**

<!--
Placeholder for the GAIA leaderboard screenshot.
Once the actual screenshot is available, replace the placeholder above with an image reference, for example:
![AgenticSearch ranked No. 1 on the GAIA leaderboard](gaia-leaderboard.png)
Also add the leaderboard date, evaluation split or track, submission name, score, and leaderboard link.
-->

## Self-Evolving Agents

Self-evolution uses **execution feedback to improve the current task, task experience to strengthen future tasks, and verification across agents to improve the reliability of the final answer**. The approach connects execution, answer verification, and experience accumulation at three levels: within a trajectory, across tasks, and across agents.

![Self-Evolving Agents: recursive self-improvement, experience-driven AutoSkill augmentation, and cross-agent verification and ensemble](architecture.png)

### A. Recursive Self-Improvement

**Recursive Self-Improvement · Intra-Trajectory**

For a given task, multiple agents each run a recursive self-improvement loop. An execution agent produces an execution trace and a candidate answer, while an evaluation agent performs **trajectory diagnosis and answer verification**. If verification fails, the causes of the errors are summarized and incorporated into the execution context to guide the next attempt.

The loop terminates when the answer passes verification or the maximum number of execution attempts is reached. Verified candidates proceed to the subsequent verification and ensemble stage; reaching the attempt limit does not mean an answer has been verified. This mechanism turns evaluation feedback into concrete context improvements, helping each retry address previously identified problems.

### B. Experience-Driven AutoSkill Augmentation

**Experience-Driven AutoSkill Augmentation · Cross-Task**

AutoSkill transforms execution traces from multiple tasks into reusable skills and tools, allowing experience to accumulate and transfer across tasks.

**Trace pooling and clustering → Recurring pattern mining → Skill / tool distillation → Cross-task validation → Skill library**

Runtime Feedback continuously supplies execution steps, tool calls, errors, and verification outcomes. Skills that pass cross-task validation are stored in the skill library and applied to subsequent tasks through Skill Reuse. New execution feedback drives further refinement, creating a loop from experience distillation to validation in use.

### C. Cross-Agent Verification and Heterogeneous Ensemble

**Cross-Agent · Verification & Heterogeneous Ensemble**

Candidate answers from multiple agents are collected in a shared pool and grouped into different verification views. Each view independently verifies the candidates, and a second-stage heterogeneous ensemble combines the results into the final answer, reducing reliance on a single solution path.

Together, these three levels support continuous improvement: **correct errors within the current task, reuse experience in future tasks, and combine results through verification from multiple perspectives.**

## Open-Source Progress

This repository currently provides the product's technical architecture and self-evolution approach. Implementation code, usage guides, and examples will be added progressively.

We welcome discussions, questions, and use cases through Issues.
