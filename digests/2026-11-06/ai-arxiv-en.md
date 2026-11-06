# ArXiv AI Research Digest 2026-11-06

> Source: [ArXiv](https://arxiv.org/) (cs.AI, cs.CL, cs.LG) | 50 papers | Generated: 2026-11-06 02:48 UTC

---

## ArXiv AI Research Digest — 2026-11-06

### 1. Today’s Highlights

Today’s submissions show a strong push on the operational layer of AI: long-context LLM inference efficiency, agent-environment optimization, and runtime security for tool-using agents. Notable work includes verifier-free test-time self-play, sparse/mixed-precision attention, and recursive sparse attention for million-token contexts. Alignment and data work is becoming more diagnostic, with DPO constraint drift and coverage-based data selection challenging standard recipes. Domain foundation models also continue to consolidate, exemplified by IMUDINO for inertial sensing and WeLM-Image for unified multimodal generation. Overall, the emphasis is shifting from raw scale toward reliability, deployability, and provable/resource-aware learning.

---

### 2. Key Papers

#### 🧠 Large Language Models (architecture, training, alignment, evaluation)

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [CacheGraft: Enhancing Retrieval-Augmented Generation via Latent-Space Document Enrichment](http://arxiv.org/abs/2611.07393v1) | V. Lakshman, P. B. Chen, Z. Gu et al. | Proposes cache-to-cache translation and latent-space document enrichment to improve retrieval-augmented generation beyond text-level retrieval. It matters because KV-cache communication can make RAG more efficient and expressive for transformer agents. |
| [When Scores Conflict with Preferences: Pre-Alignment Diagnosis and Adaptive Correction of Constraint Drift in DPO](http://arxiv.org/abs/2611.07375v1) | P. Liu, Y. Yan | Diagnoses constraint drift when pairwise preferences conflict with pointwise criteria such as length, correctness, or safety, and proposes adaptive correction for DPO. This matters because standard DPO can amplify proxies that disagree with actual quality or safety scores. |
| [Rethinking Data Selection: The Importance of Coverage over Difficulty in LLM Fine-Tuning](http://arxiv.org/abs/2611.07323v1) | L. Pandia, K. Misra, A. Ettinger | Finds that difficulty-based data selection, effective for smaller classifiers, is less reliable for LLM fine-tuning; coverage of diverse phenomena is more important. This shifts data-selection practice toward representativeness rather than hard-example mining. |
| [TriAttention: Unified Sparse Mixed-Precision Attention for Long-Context LLM Inference](http://arxiv.org/abs/2611.07256v1) | H. Zhang, Q. Zhang, Q. Cao et al. | Introduces unified sparse mixed-precision attention that treats some token interactions as removable and others as precision-sensitive. It matters for long-context LLM inference because it targets both sparsity and quantization without a one-size-fits-all policy. |
| [Recursive Sparse Attention: Million-Token Length Extrapolation at Logarithmic Cost](http://arxiv.org/abs/2611.07232v1) | R. Wang, X. Yang, M. Zhang et al. | Proposes recursive sparse attention for million-token length extrapolation at logarithmic cost, avoiding scoring all preceding blocks or tokens per query. This matters for scaling long-context prefill and decoding beyond document-length limits. |

#### 🤖 Agents & Reasoning (planning, tool use, multi-agent, chain-of-thought)

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [Test-Time Self-Play: Scaling without a Verifier](http://arxiv.org/abs/2611.07367v1) | A. Yoshiyama, A. Ashirmatov, J. Hübotter et al. | Studies test-time scaling without an external verifier by using self-play to generate and select or refine candidates. It matters because many deployment regimes lack reliable correctness signals, limiting most test-time scaling methods. |
| [EnvOpt: Automated Optimization for Agent Environments](http://arxiv.org/abs/2611.07372v1) | M. Li, H. Mao, Q. Mang et al. | Formulates automated optimization over stationary agent environments as a third axis alongside model tuning and harness optimization. It matters because environment design can strongly affect agent cost and success in deployment. |
| [How Should Agents See Code? Rethinking Code Representations for Agentic Software Engineering](http://arxiv.org/abs/2611.07286v1) | M. Sepidband, J. Shin, S. Wang et al. | Investigates map-based code-representation workflows as a design variable for repository-level software-engineering agents. It matters because most agents still treat code as raw text plus lexical search, which may limit navigation and reasoning. |
| [SPILLWAY: Separating Target Admission from Sanitization to Defend Tool-Using Agents against Prompt Injection](http://arxiv.org/abs/2611.07222v1) | W. Lim, Y. Chang, J. Lee | Separates target admission from sanitization to defend tool-using agents against prompt injection hidden in web pages or documents. It matters because runtime defenses that rely solely on an LLM decision remain vulnerable to model compromise. |
| [PSGAgent: Enhancing MLLMs' Dynamic Visual Perception for Panoptic Scene Graph Generation via Agentic Reasoning](http://arxiv.org/abs/2611.07408v1) | Q. Liu, Y. Liu, J. Feng et al. | Uses agentic reasoning to give MLLMs dynamic visual perception for panoptic scene graph generation, addressing fixed-resolution static-observer bottlenecks. It matters because fine-grained relational scene understanding requires active, multi-view perception. |

#### 🔧 Methods & Frameworks (new techniques, benchmarks, efficiency improvements)

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [IMUDINO: Self-distilled Foundation Model for Inertial Sensing](http://arxiv.org/abs/2611.07437v1) | Z. Ye, C. Zhang, Z. Wang et al. | Presents a self-distilled foundation model for inertial sensing that aims at competitive performance across human activity recognition, health, motion capture, and odometry. It matters because prior inertial foundation models are often subfield-specific, limiting transfer. |
| [MAC-v2: Long-horizon model-based value expansion from pixels](http://arxiv.org/abs/2611.07245v1) | K. Park, S. Levine | Extends model-based value expansion to long-horizon, pixel-based offline RL, where both representation and dynamics must be learned. It matters because long-horizon value expansion could improve sample efficiency and credit assignment in visual control. |
| [Provably Efficient Representation Learning for Low-Rank CMDPs](http://arxiv.org/abs/2611.07295v1) | K. Liu, G. Xiong, S. Tang et al. | Studies representation learning in low-rank constrained MDPs with unknown representation vectors and low-rank transition dynamics.

---
*This digest is auto-generated by [agents-radar](https://github.com/SnowEagle-Kn/agents-radar).*