# ArXiv AI Research Digest 2026-11-04

> Source: [ArXiv](https://arxiv.org/) (cs.AI, cs.CL, cs.LG) | 50 papers | Generated: 2026-11-04 02:55 UTC

---

# ArXiv AI Research Digest — 2026-11-04

## 1. Today's Highlights

Today's submissions show a field increasingly preoccupied with the *reliability of composed systems* rather than isolated model capability. Two safety papers stand out: multi-agent debate — widely adopted to reconcile noisy web evidence — is shown to amplify latent social bias rather than neutralize it, while RAG retrieval is systematically stress-tested against query-relevant corpus poisoning at the admission step. A second cluster interrogates **evaluation methodology itself**: work on tokenizer-invariant metrics, cross-provider judge replication, rubric phrasing effects for Korean support dialogues, and "evaluation contracts" collectively argue that many reported capability gaps are artifacts of measurement rather than model behavior. Efficiency work targets the inference stack directly — certified parallel decoding for diffusion LMs, loop-aware KV cache compression for looped transformers, and loss-informed rank allocation for LoRA. Finally, agent infrastructure is maturing from single-threaded harnesses toward durable, multi-agent coordination substrates with explicit human oversight surfaces.

---

## 2. Key Papers

### 🧠 Large Language Models (architecture, training, alignment, evaluation)

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [Tokenizer Myths and Where To Find Them](http://arxiv.org/abs/2611.04215v1) | Rylan Schaeffer, Sanmi Koyejo | Identifies and challenges three pervasive myths in tokenization research, including the assumption that Bits-Per-Byte is tokenizer-agnostic. The paper forces a re-examination of a large body of cross-model comparisons that may be measuring vocabulary choice rather than model quality. |
| [Neologism Inoculation Prompting](http://arxiv.org/abs/2611.04260v1) | David Demitri Africa, Cameron Holmes | Extends inoculation prompting — training on harmful examples under an explicit undesired-behavior prompt — to reduce reward hacking in frontier models, and probes where the intervention generalizes. It matters because inoculation is one of the few cheap, training-time safeguards that survives deployment prompting. |
| [Where Do Distilled Students Store Thinking?](http://arxiv.org/abs/2611.04141v1) | Taeyeong Kim, Jaemin Jung, Ahhyun Kim et al. | Compares LoRA adapters of students distilled from long-reasoning teachers to ask what is actually retained when the teacher "stops thinking." The analysis clarifies why small students degrade on long solutions and where the lost capability is localized. |
| [Swapping the Judge Changes the Ranking](http://arxiv.org/abs/2611.04090v1) | Sangdo Han, Moonsoo Kim | A preregistered study of five judge models testing whether cross-provider judge replication is a trustworthy validation of LLM-judged system comparisons. It finds the check is far weaker than practitioners assume, with direct consequences for leaderboard and A/B practice. |

### 🤖 Agents & Reasoning (planning, tool use, multi-agent, chain-of-thought)

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [When Debate Amplifies Risk: Social-Bias Vulnerabilities in Multi-Agent Safety Deliberation](http://arxiv.org/abs/2611.04289v1) | Qinghua Mao, Xi Lin, Yuxin Qi et al. | Shows that multi-agent debate over retrieved web content is a social process susceptible to interpersonal dynamics, and that latent hazards in sources can be amplified rather than filtered by deliberation. This undermines a common safety assumption behind retrieval-augmented web applications. |
| [Harness-S: Learning from Sibling Harnesses for Generalizable Agent Training](http://arxiv.org/abs/2611.04277v1) | Kangning Zhang, Wenxiang Jiao, Bowen Fang et al. | Characterizes harness evolution through Locality, Complementarity, and Nonstationarity, then selectively rescues base-executor failures using sibling harness variants instead of searching for one universally superior harness. The framing matches how agent scaffolds actually evolve in practice. |
| [Agentic Path Maps: Exposing an Agent's Traversal Through the Garden of Forking Paths](http://arxiv.org/abs/2611.04183v1) | Madeleine Grunde-McLaughlin, Hafsah Usman, Daniel Mitroshkov et al. | Proposes a post-hoc visualization of impactful but unexplored decision paths an agent passed over during an underspecified task. It offers a middle ground between interrupting users for constant clarification and silently committing to unstated assumptions. |
| [The Agent Board: Kanban for Machines](http://arxiv.org/abs/2611.04100v1) | Tyler Hunt, Donald Nguyen, Gurbinder Gill | Argues the dominant single-threaded harness shape (one agent, one history, one machine, one sitting) fails work spanning many agents or long horizons, and introduces a durable coordination substrate. It reframes agent infrastructure as a distributed-systems problem rather than a prompting problem. |

### 🔧 Methods & Frameworks (new techniques, benchmarks, efficiency improvements)

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [PACT: Certifying What Is Written in Parallel Decoding of Diffusion Language Models](http://arxiv.org/abs/2611.04152v1) | Yanchao Li, Yusu Zhang, Jiaqing Xie et al. | Reveals that published commit rules for parallel decoding test predicted distributions but never inspect the tokens actually sampled, causing quality to follow an unseen quantity. The proposed certification makes multi-token-per-step decoding auditable under sampling rather than only greedy decoding. |
| [LIRA: A More Interpretable Dynamic Loss-Informed Rank Allocation for Low-Rank Adaptation](http://arxiv.org/abs/2611.04239v1) | Sihao Wang, Bozheng Li, Xuemiao Xu et al. | Replaces LoRA's fixed rank profile with a dynamic allocation driven by loss signals and made interpretable, avoiding the heuristics and fixed schedules of prior adaptive-rank variants. It targets the well-known mismatch between uniform rank budgets and layer-/stage-dependent adaptation needs. |
| [LoopKV: Loop-Aware KV Cache Compression for Serving Looped Transformers](http://arxiv.org/abs/2611.04137v1) | Tianle Zhou, Yang Zhang, Tianyu Wu et al. | Addresses models that keep a separate KV cache per loop, where cache memory caps the batch size that amortizes repeated weight reads, by compressing with loop awareness instead of uniform-bit quantization. It is a prerequisite for making looped architectures practical at serving scale. |
| [Evaluation Contracts and Evaluation Leverage](http://arxiv.org/abs/2611.04099v1) | Hao Lu, Weixing Zhu, Ruiyang Li | Demonstrates that sliced performance gaps — notably the intra-inter gap in document-level relation extraction — are joint properties of models *and* evaluation contracts (scoring function, gold version, threshold). Many headline capability differences can therefore move without any model change. |

### 📊 Applications (domain-specific, multimodal, code generation)

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [FlowTranslator: Training-Free In-Image Machine Translation with Flow-Based Editing](http://arxiv.org/abs/2611.04303v1) | Zhixian Li, Xingsong Ye, Yongkun Du et al. | Uses flow-based editing with text-to-image models to replace in-image text with translations while preserving surrounding visual context, eliminating the need for massive paired before/after image corpora. It shifts IIMT from task-specific supervised models to training-free generative editing. |
| [Predicting protein structures at any folding stage from sequence](http://arxiv.org/abs/2611.04287v1) | Lisheng Zhang, Qingsong Li, Zinan Xie et al. | Introduces FoldScape, a sequence-conditioned framework generating Cα conformational ensembles at a specified folding fraction rather than only the native state. It extends structure prediction from a single endpoint to the folding continuum, with implications for mechanistic biology. |
| [AdSteer: Native Advertising in Large Language Model Generation](http://arxiv.org/abs/2611.04147v1) | Shuhang Lin, Chuhao Zhou, Zhenyu Wang et al. | Proposes aligning user intent with merchant offerings to inject native advertising into LLM generation as a monetization mechanism for rising serving costs. It raises immediate questions about disclosure, manipulation, and the boundary between recommendation and persuasion. |

---

## 3. Research Trend Signal

Three converging directions dominate today's batch. First, **evaluation is becoming the object of study**: tokenizer-invariant metrics, evaluation contracts, judge-swap replication, and rubric-language effects all suggest the community is treating benchmark scores as measurements with confounds rather than ground truth. Expect a wave of meta-evaluation papers and, eventually, reporting standards. Second, **safety is migrating from single models to pipelines**: RAG poisoning at the retrieval-admission step and bias amplification inside multi-agent debate both indicate that deliberation and retrieval — sold as robustness mechanisms — can be attack surfaces. Third, **inference-time efficiency is getting structurally aware**: loop-aware KV compression, certification of parallel decoding under sampling, and FP8/FP4 expert-parallel dispatch all exploit architecture-specific structure rather than generic quantization. A quieter fourth thread is **agent infrastructure as systems work** — durable coordination, harness evolution, and post-hoc oversight of agent trajectories — signaling that agent research is moving from capability demos toward operability and accountability.

---

## 4. Worth Deep Reading

**1. [Tokenizer Myths and Where To Find Them](http://arxiv.org/abs/2611.04215v1)** — Read this if you ever cite a Bits-Per-Byte number or compare models with different vocabularies. It is a compact, high-leverage critique that potentially invalidates comparison practices across a large slice of the efficiency and compression literature, and it is the kind of paper that changes how you design an experiment rather than what you believe.

**2. [When Debate Amplifies Risk: Social-Bias Vulnerabilities in Multi-Agent Safety Deliberation](http://arxiv.org/abs/2611.04289v1)** — Multi-agent debate is being deployed in production retrieval-augmented systems precisely because it is assumed to average out noise and bias. A demonstrated failure mode where deliberation *amplifies* latent hazards is a direct threat model for those deployments, and the social-dynamics framing opens a productive bridge to prior work on group polarization.

**3. [PACT: Certifying What Is Written in Parallel Decoding of Diffusion Language Models](http://arxiv.org/abs/2611.04152v1)** — A precise diagnosis of a subtle but consequential gap: commit heuristics validated on predicted distributions do not constrain sampled tokens. Since parallel decoding is the main efficiency argument for diffusion LMs, closing this gap is a precondition for the architecture's practical adoption, and the certification framing generalizes to other speculative and multi-token decoding schemes.

---
*This digest is auto-generated by [agents-radar](https://github.com/SnowEagle-Kn/agents-radar).*