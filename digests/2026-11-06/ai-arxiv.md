# ArXiv AI 研究日报 2026-11-06

> 数据来源: [ArXiv](https://arxiv.org/) (cs.AI, cs.CL, cs.LG) | 共 50 篇论文 | 生成时间: 2026-11-06 02:48 UTC

---

## ArXiv AI 研究文摘 — 2026-11-06

### 1. 今日亮点

今日提交的论文显示，AI 的运行层正受到强劲推动：长上下文 LLM 推理效率、智能体-环境优化，以及工具使用型智能体的运行时安全。值得关注的工作包括无验证器的测试时自我博弈、稀疏/混合精度注意力，以及面向百万 token 上下文的递归稀疏注意力。对齐与数据工作正变得更具诊断性，DPO 约束漂移和基于覆盖度的数据选择正在挑战标准配方。领域基础模型也在持续整合，例如面向惯性感知的 IMUDINO 和面向统一多模态生成的 WeLM-Image。总体而言，重点正从原始规模转向可靠性、可部署性，以及可证明/资源感知的学习。

---

### 2. 重点论文

#### 🧠 大语言模型（架构、训练、对齐、评估）

| 论文 | 作者 | 摘要 |
| :--- | :--- | :--- |
| [CacheGraft: Enhancing Retrieval-Augmented Generation via Latent-Space Document Enrichment](http://arxiv.org/abs/2611.07393v1) | V. Lakshman, P. B. Chen, Z. Gu et al. | 提出缓存到缓存的转换与潜在空间文档增强，以改进超越文本级检索的检索增强生成。其意义在于，KV 缓存通信可使 RAG 对 transformer 智能体更高效、更具表现力。 |
| [When Scores Conflict with Preferences: Pre-Alignment Diagnosis and Adaptive Correction of Constraint Drift in DPO](http://arxiv.org/abs/2611.07375v1) | P. Liu, Y. Yan | 诊断成对偏好与逐点标准（如长度、正确性或安全性）冲突时的约束漂移，并为 DPO 提出自适应校正。其意义在于，标准 DPO 可能放大与实际质量或安全分数不一致的代理指标。 |
| [Rethinking Data Selection: The Importance of Coverage over Difficulty in LLM Fine-Tuning](http://arxiv.org/abs/2611.07323v1) | L. Pandia, K. Misra, A. Ettinger | 发现基于难度的数据选择虽对较小分类器有效，但对 LLM 微调不太可靠；多样化现象的覆盖度更为重要。这将数据选择实践从困难样本挖掘转向代表性。 |
| [TriAttention: Unified Sparse Mixed-Precision Attention for Long-Context LLM Inference](http://arxiv.org/abs/2611.07256v1) | H. Zhang, Q. Zhang, Q. Cao et al. | 引入统一的稀疏混合精度注意力，将部分 token 交互视为可移除，另一些视为精度敏感。其对长上下文 LLM 推理的意义在于，同时针对稀疏性和量化，而非采用一刀切策略。 |
| [Recursive Sparse Attention: Million-Token Length Extrapolation at Logarithmic Cost](http://arxiv.org/abs/2611.07232v1) | R. Wang, X. Yang, M. Zhang et al. | 提出递归稀疏注意力，以对数成本实现百万 token 长度外推，避免为每个查询对所有前序块或 token 打分。其对将长上下文预填充和解码扩展至超出文档长度限制的意义重大。 |

#### 🤖 智能体与推理（规划、工具使用、多智能体、思维链）

| 论文 | 作者 | 摘要 |
| :--- | :--- | :--- |
| [Test-Time Self-Play: Scaling without a Verifier](http://arxiv.org/abs/2611.07367v1) | A. Yoshiyama, A. Ashirmatov, J. Hübotter et al. | 研究在没有外部验证器的情况下进行测试时扩展，利用自我博弈生成并选择或精炼候选。其意义在于，许多部署场景缺乏可靠的正确性信号，限制了大多数测试时扩展方法。 |
| [EnvOpt: Automated Optimization for Agent Environments](http://arxiv.org/abs/2611.07372v1) | M. Li, H. Mao, Q. Mang et al. | 将对静态智能体环境的自动优化表述为与模型调优和 harness 优化并列的第三条轴。其意义在于，环境设计可强烈影响部署中智能体的成本和成功率。 |
| [How Should Agents See Code? Rethinking Code Representations for Agentic Software Engineering](http://arxiv.org/abs/2611.07286v1) | M. Sepidband, J. Shin, S. Wang et al. | 研究基于地图的代码表示工作流作为仓库级软件工程智能体的设计变量。其意义在于，大多数智能体仍将代码视为原始文本加词法搜索，这可能限制导航和推理。 |
| [SPILLWAY: Separating Target Admission from Sanitization to Defend Tool-Using Agents against Prompt Injection](http://arxiv.org/abs/2611.07222v1) | W. Lim, Y. Chang, J. Lee | 将目标准入与净化分离，以防御工具使用型智能体免受隐藏在网页或文档中的提示注入。其意义在于，仅依赖 LLM 决策的运行时防御仍易受模型攻陷影响。 |
| [PSGAgent: Enhancing MLLMs' Dynamic Visual Perception for Panoptic Scene Graph Generation via Agentic Reasoning](http://arxiv.org/abs/2611.07408v1) | Q. Liu, Y. Liu, J. Feng et al. | 利用智能体推理为 MLLM 提供动态视觉感知以进行全景场景图生成，解决固定分辨率静态观察者的瓶颈。其意义在于，细粒度关系场景理解需要主动的多视角感知。 |

#### 🔧 方法与框架（新技术、基准、效率改进）

| 论文 | 作者 | 摘要 |
| :--- | :--- | :--- |
| [IMUDINO: Self-distilled Foundation Model for Inertial Sensing](http://arxiv.org/abs/2611.07437v1) | Z. Ye, C. Zhang, Z. Wang et al. | 提出面向惯性感知的自蒸馏基础模型，旨在在人类活动识别、健康、动作捕捉和里程计方面取得有竞争力的性能。其意义在于，此前的惯性基础模型往往局限于特定子领域，限制了迁移能力。 |
| [MAC-v2: Long-horizon model-based value expansion from pixels](http://arxiv.org/abs/2611.07245v1) | K. Park, S. Levine | 将基于模型的价值扩展扩展到长视野、基于像素的离线 RL，其中表示和动力学均需学习。其意义在于，长视野价值扩展可改善视觉控制中的样本效率和信用分配。 |
| [Provably Efficient Representation Learning for Low-Rank CMDPs](http://arxiv.org/abs/2611.07295v1) | K. Liu, G. Xiong, S. Tang et al. | 研究具有未知表示向量和低秩转移动力学的低秩约束 MDP 中的表示学习。

---
*本日报由 [agents-radar](https://github.com/SnowEagle-Kn/agents-radar) 自动生成。*