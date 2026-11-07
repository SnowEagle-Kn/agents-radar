# AI 开源趋势日报 2026-11-07

> 数据来源: GitHub Trending + GitHub Search API | 生成时间: 2026-11-07 02:20 UTC

---

# AI Open Source Trends Report — 2026-11-07

## 1. Today's Highlights

Agent **"skills"** packaging is the single loudest signal today: `cloudflare/security-audit-skill` (+2,548), `mattpocock/skills` (+2,070) and `google/skills` (+534) pulled in over 5,100 new stars combined, turning portable capability bundles for coding agents into a first-class distribution format. NVIDIA's `auto-ontology` (+2,015) marks a major vendor betting on semantic graph layers over raw vector retrieval, promising deterministic query generation and explainable context for agent workflows. Meanwhile the context/memory tier keeps compounding — `graphify` (143,582), `claude-mem` (114,050), `headroom` (79,031) and `mem0` (68,531) all sit in the top tier of the topic search, evidence that "cheaper, structured context" has replaced "bigger context window" as the engineering battleground. Coding-agent harnesses and terminals (`ECC` at 298,619, `Codewhale`, `DeepSeek-Reasonix`, `oh-my-pi`) continue to expand, and vertical agents in finance and career search are producing some of the fastest-growing application repos.

---

## 2. Top Projects by Category

### 🔧 AI Infrastructure

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [ollama/ollama](https://github.com/ollama/ollama) | Go | 186,153 | Local model runtime serving Kimi, GLM, MiniMax, DeepSeek, gpt-oss, Qwen and Gemma behind one CLI/API. It remains the default on-ramp for self-hosted inference, which keeps it central every time a new open-weight family ships. |
| [huggingface/transformers](https://github.com/huggingface/transformers) | Python | 168,607 | The reference model-definition framework for text, vision, audio and multimodal models across inference and training. Its breadth makes it the layer most new open-weight releases target first. |
| [pytorch/pytorch](https://github.com/pytorch/pytorch) | Python | 105,219 | Tensors and dynamic neural networks with strong GPU acceleration. Still the substrate underneath nearly every training and fine-tuning stack in this report. |
| [tensorflow/tensorflow](https://github.com/tensorflow/tensorflow) | C++ | 201,367 | The long-standing ML framework, now primarily a production-serving and legacy-ecosystem anchor. Its star count keeps it atop the topic search even as new development concentrates in PyTorch and Rust tooling. |
| [langchain4j/langchain4j](https://github.com/langchain4j/langchain4j) | Java | 13,367 | Idiomatic Java library for LLM apps on the JVM with a unified provider API, tool calling/MCP, agents and RAG. It matters because Spring Boot and Quarkus backends are where a large share of enterprise production still lives. |
| [0xPlaygrounds/rig](https://github.com/0xPlaygrounds/rig) | Rust | 9,081 | Modular Rust toolkit for building scalable LLM applications. It represents the steady Rust migration of agent and inference plumbing where latency and memory footprint dominate. |
| [skyzh/tiny-llm](https://github.com/skyzh/tiny-llm) | Python | 5,680 | A build-your-own tiny vLLM + Qwen inference system for systems engineers, running on Apple Silicon. Educational in framing but directly useful for understanding KV cache, batching and serving internals. |
| [FonaTech/Project_Chronos](https://github.com/FonaTech/Project_Chronos) | Python | 207 | Zero-stall MoE inference via lookahead prediction and async DMA prefetching, tuned for SSD I/O with hybrid MLA + sliding-window attention. Small project, but a concrete signal that MoE serving and storage-bound inference are the new optimization frontier. |

### 🤖 AI Agents / Workflows

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [affaan-m/ECC](https://github.com/affaan-m/ECC) | JavaScript | 298,619 | An agent-harness performance optimization system spanning skills, instincts, memory, security and research-first development across Claude Code, Codex, Opencode and Cursor. At nearly 300k stars it is the clearest evidence that harness-level tuning, not just model choice, is where agent quality is now decided. |
| [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) | Python | 261,224 | "The agent that grows with you" — a persistent, self-extending personal agent. Its scale signals strong appetite for long-running agents that accumulate capability rather than reset each session. |
| [cloudflare/security-audit-skill](https://github.com/cloudflare/security-audit-skill) | JavaScript | 0 (+2,548) | A coding-agent skill for multi-phase security audits with independently verified, machine-readable findings. It is today's top trending repo overall, showing vendors shipping official agent skills as a product surface. |
| [mattpocock/skills](https://github.com/mattpocock/skills) | Shell | 0 (+2,070) | "Skills for Real Engineers," published straight from an `.agents` directory. Its second-place trending finish confirms skills are now a personal-brand and knowledge-sharing format, not just vendor tooling. |
| [google/skills](https://github.com/google/skills) | Python | 0 (+534) | Official Agent Skills for Google products and technologies. A hyperscaler formally packaging its APIs for coding agents is a notable shift in how developer platforms are being distributed. |
| [langgenius/dify](https://github.com/langgenius/dify) | Python | 160,579 | Collaborative workspace for building agentic workflows and RAG pipelines with broad model and tool support, deployable cloud, VPC or self-hosted. It is the de-facto open alternative to managed agent platforms for teams moving from prototype to production. |
| [browser-use/browser-use](https://github.com/browser-use/browser-use) | Python | 120,218 | Python library for AI browser agents plus a hosted cloud with stealth browsers and an agent API. Browser control remains the most commercially legible agent capability, and this repo owns that niche. |
| [Panniantong/Agent-Reach](https://github.com/Panniantong/Agent-Reach) | Python | 109,705 | One CLI giving agents read/search access to Twitter, Reddit, YouTube, GitHub, Bilibili and XiaoHongShu with zero API fees. It addresses the practical bottleneck of agent data access without per-source integration work. |

### 📦 AI Applications

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [open-webui/open-webui](https://github.com/open-webui/open-webui) | Svelte | 157,955 | User-friendly self-hosted AI interface supporting Ollama and OpenAI-compatible APIs. It remains the most-starred front end in the ecosystem and the standard UI layer for local deployments. |
| [f/prompts.chat](https://github.com/f/prompts.chat) | MDX | 176,079 | The community prompt library formerly known as Awesome ChatGPT Prompts, self-hostable for private organizational use. It shows prompt assets still command enormous attention as reusable organizational IP. |
| [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) | Python | 144,049 | A collection of 100+ AI agents, agent skills and RAG apps, all free and open source. It functions as the practical reference catalogue developers copy from when starting new agent products. |
| [harry0703/MoneyPrinterTurbo](https://github.com/harry0703/MoneyPrinterTurbo) | Python | 140,278 | Generates HD short videos from a topic or keyword via an automated LLM workflow. It is a durable example of a single-vertical generative pipeline reaching massive consumer demand. |
| [hugohe3/ppt-master](https://github.com/hugohe3/ppt-master) | Python | 74,278 | Turns documents or topics into native PowerPoint decks with real shapes, transitions, charts and narration from speaker notes. Native-format output rather than image slides is what distinguishes it from earlier deck generators. |
| [openbq-org/OpenBB](https://github.com/openbq-org/OpenBB) | Python | 75,171 | Open data platform for analysts, quants and AI agents. It is becoming the standard data layer that financial agent projects build on top of. |
| [HKUDS/DeepTutor](https://github.com/HKUDS/DeepTutor) | Python | 55,972 | Lifelong personalized tutoring system from the HKU data science lab. Education remains one of the strongest product-market fits for persistent-memory agents. |
| [Mintplex-Labs/anything-llm](https://github.com/Mintplex-Labs/anything-llm) | JavaScript | 68,015 | Local-first agent workspace bundling document chat, agents and model management in one owned deployment. Its "stop renting your intelligence" positioning captures the self-hosting mood of the moment. |

### 🧠 LLMs / Training

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [rasbt/LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch) | Jupyter Notebook | 107,935 | Step-by-step PyTorch implementation of a ChatGPT-like LLM. It is the most-cited hands-on path from transformer theory to working training loop. |
| [rohitg00/ai-engineering-from-scratch](https://github.com/rohitg00/ai-engineering-from-scratch) | Python | 77,166 | A "learn it, build it, ship it" curriculum for AI engineering. Its 77k stars show demand shifting from ML theory toward end-to-end production skills. |
| [labmlai/annotated_deep_learning_paper_implementations](https://github.com/labmlai/annotated_deep_learning_paper_implementations) | Python | 67,716 | 60+ deep-learning paper implementations with side-by-side notes, covering transformer variants, optimizers, GANs, RL and distillation. It stays relevant because it explains the mechanisms that newer architectures recombine. |
| [open-compass/opencompass](https://github.com/open-compass/opencompass) | Python | 7,570 | LLM evaluation platform spanning 100+ datasets across knowledge, reasoning, coding, science, long-context and safety, with support for OpenAI, Anthropic, Gemini, Qwen, GLM and DeepSeek. Evaluation breadth is increasingly the real moat when choosing models. |
| [EgoAlpha/prompt-in-context-learning](https://github.com/EgoAlpha/prompt-in-context-learning) | Jupyter Notebook | 2,250 | Curated resources for in-context learning and prompt engineering across ChatGPT-era and later models. It tracks a technique layer that survives model churn. |
| [AkaliKong/MiniOneRec](https://github.com/AkaliKong/MiniOneRec) | Python | 1,955 | A minimal reproduction of OneRec, the generative recommendation approach. Small but indicative of recommender systems converging on LLM-style generative formulations. |
| [thinkwee/AgentsMeetRL](https://github.com/thinkwee/AgentsMeetRL) | HTML | 1,877 | An awesome list for agentic reinforcement learning. It marks RL-for-agents as a distinct, rapidly consolidating research area. |
| [datamllab/OpenEBFM](https://github.com/datamllab/OpenEBFM) | Python | 282 | Open-source code for training and scaling energy-based foundation models. A niche but notable non-autoregressive alternative worth watching. |

### 🔍 RAG / Knowledge

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) | Python | 143,582 | Turns a codebase with docs, SQL schemas, configs and PDFs into a queryable knowledge graph via local deterministic AST parsing, with no vector store. Its 143k stars make it the flagship of the "graph over embeddings" wave. |
| [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) | TypeScript | 114,050 | Captures everything an agent does per session, compresses it with AI and reinjects relevant context into future sessions across Claude Code, Codex, Gemini, Copilot and more. Cross-agent portability is what makes it strategically interesting. |
| [infiniflow/ragflow](https://github.com/infiniflow/ragflow) | Go | 93,699 | A RAG engine that has grown into agent-native context infrastructure and a database in its own right. It is one of the few RAG projects that has successfully repositioned rather than stagnated. |
| [NVIDIA/auto-ontology](https://github.com/NVIDIA/auto-ontology) | Python | 0 (+2,015) | A Python framework for building semantic, graph-based reasoning layers over data, enabling deterministic query generation, better context retrieval and more explainable AI for LLM and agent workflows. Its +2,015 star day makes it the strongest single-day signal in the retrieval space today. |
| [unclecode/crawl4ai](https://github.com/unclecode/crawl4ai) | Python | 87,017 | Apache-2.0 crawler powering a hosted scraping, extraction and search API for AI agents, with a free tier of 1,000 credits monthly. It is the practical ingestion layer most RAG stacks still start from. |
| [mem0ai/mem0](https://github.com/mem0ai/mem0) | Python | 68,531 | Drop-in memory infrastructure giving agents persistent, production-grade context. Memory has become a separate purchasable layer rather than a feature inside agent frameworks. |
| [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) | Python | 79,031 | Compresses tool outputs, logs, files and RAG chunks before they reach the LLM — 20% fewer tokens for coding agents, 60–95% fewer for JSON — as a library, proxy and MCP server. It quantifies the token-economics turn in retrieval design. |
| [milvus-io/milvus](https://github.com/milvus-io/milvus) | Go | 46,608 | Cloud-native vector database built for scalable ANN search. It remains the reference production-grade option even as vectorless and hybrid approaches gain mindshare. |

---

## 3. Trend Signal Analysis

Three signals stand out. First, **agent skills are becoming a distribution format**. Cloudflare, Google and an individual engineer all shipped skills repos that trended simultaneously — a pattern that looks like the npm-ification of agent capability. Skills are small, composable and provider-agnostic, and they let vendors expose APIs to agents without building a full framework.

Second, **context engineering is displacing context expansion**. The retrieval tier is splitting into four distinct bets: knowledge graphs (`graphify`, NVIDIA `auto-ontology`), session memory (`claude-mem`, `mem0`), token compression (`headroom`, `caveman` at 65% reduction), and vectorless reasoning-based indexing (`PageIndex`, `LEANN`). Vector databases like `milvus`, `qdrant`, `weaviate` and Alibaba's in-process `zvec` still matter, but they are no longer the default answer. Determinism and explainability — every edge explained, deterministic query generation — are now explicit selling points, which suggests enterprises are hitting the reliability ceiling of pure embedding similarity.

Third, **the agent harness is the new IDE**. `ECC` at 298k stars, `Codewhale` in Rust, `DeepSeek-Reasonix`, `oh-my-pi` and `openclaude` all compete on harness quality, multi-provider routing and terminal ergonomics rather than model weights. Notably, Rust appears repeatedly (`rig`, `Codewhale`, `zvec`, `qdrant`, `caveman`), and `Project_Chronos` points at MoE serving and SSD-bound inference as the next performance frontier. Verticals — finance (`TradingAgents`, `Vibe-Trading`, `daily_stock_analysis`, `OpenBB`), career search, tutoring, slide generation — are consolidating fast, which is exactly what you expect once the harness layer stabilizes.

---

## 4. Community Hot Spots

- **Agent skills as a package format** — `cloudflare/security-audit-skill` (+2,548), `mattpocock/skills` (+2,070), `google/skills` (+534). Worth studying the file layout and invocation conventions now; this is likely how agent capabilities get distributed over the next year.
- **Graph and ontology layers over RAG** — `NVIDIA/auto-ontology` (+2,015) and `graphify` (143,582) both argue that structured semantics beat similarity search for code and enterprise data. High-value area for anyone building retrieval for agents.
- **Token-economics tooling** — `headroom` (79,031) and `caveman` (118,024) sell measurable token reduction rather than capability. As agent loops lengthen, cost compression becomes a first-class infrastructure category.
- **Agent harness optimization** — `affaan-m/ECC` at 298,619 stars shows harness-level work (memory, instincts, security, research loops) draws more attention than any single model release.
- **Vertical agent products with native output** — `ppt-master` (74,278, real `.pptx` shapes and animations), `career-ops` (80,736), `TradingAgents` (121,029). Native-format output and human-in-the-loop submission are the differentiators separating these from demo-grade wrappers.

---
*本日报由 [agents-radar](https://github.com/SnowEagle-Kn/agents-radar) 自动生成。*