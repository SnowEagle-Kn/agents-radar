# AI 基础设施日报 2026-11-06

> 生成时间: 2026-11-06 02:48 UTC | 覆盖项目: 6 个

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## 横向对比

# 跨项目 AI 基础设施对比 — 2026-11-06

## 1. 生态概览

活动集中在两个领域：**服务栈加固**和**内存/量化效率**。vLLM 正在处理高严重性的 v0.32.0 回归问题，同时推进分布式服务、Rust 前端对齐和 RL 权重重载工作流。llama.cpp 和 Ollama 继续在本地运行时内核、后端和内存方面的工作，而 LiteLLM 正在修复网关级流式传输、工具调用和速率限制的正确性问题。Unsloth 正在推进微调和扩散/视频内存效率。SGLang 的摘要生成失败，因此被排除在定性比较之外，但在活动表中有所记录。

## 2. 活动对比

仅在来源摘要提供数据时报告计数；`n/r` = 未报告。

| 项目 | Issue 数量 | PR 数量 | 发布状态 |
|---|---:|---:|---|
| **vLLM** | n/r；关联大量活跃 issue | n/r；关联大量活跃 PR | 过去 24 小时无发布；v0.32.0 已确认存在回归 |
| **SGLang** | n/a | n/a | 摘要生成失败 |
| **llama.cpp** | n/r | n/r | 滚动构建已发布：b12131–b12143 |
| **Ollama** | n/r | n/r | 无发布；稳定版 0.40.14，0.40.15-rc2 测试中 |
| **LiteLLM** | 95 个已更新 | 500 个已更新 | v1.108.1 已发布，含 cosign Docker 签名验证 |
| **Unsloth** | n/r | n/r | 无发布；活跃 PR 队列涉及 Studio、扩散、视频、RAG |

## 3. 模型支持竞赛

**vLLM 在前沿 LLM 服务覆盖和分布式执行方面领先。** 它正在推进 DeepSeek-V4.1 任意流水线阶段分区、MiniMax-M3 用于 prefill/decode 的上下文并行，以及 GB10/DGX Spark SM121 作为新兴目标。它还在跟踪 Nemotron 3.5 ASR Streaming，但这仍是一个请求。

**llama.cpp 在本地/边缘架构和后端广度方面领先。** 它新增了 `xing4_0` 架构支持、Blackwell MXFP4 W4A8 matmul、WebGPU `MEAN`、Vulkan IQ4_NL、Metal bf16 matmul 以及 Hexagon 后端工作。这是广泛的硬件/量化覆盖，而非数据中心服务。

**Unsloth 在扩散/视频和微调硬件驻留方面领先。** 它正在为 B200/GB200/B300 级显卡添加 NVFP4 步进保护、Lumina-Image-2.0 激活尺寸调整、面向 16 GiB L4 的 MiniMax-H3 slab 压缩、CUDA 13/cuDNN 选择、集成 GPU GGUF 加载以及 CPU 上下文并行。

**LiteLLM 在提供商/API 规范化方面领先，而非模型内核。** 它正在为 Opus 5.5/Fable 5.1/Sonnet 5/Haiku 5.5 添加 Bedrock 原生结构化输出、为 Seedance/MiniMax 添加 OpenRouter 视频字段、Vertex AI Cloud TTS 区域端点以及 Codex 特定版本的模型目录。

**Ollama 目前在新区模型/硬件支持方面相对落后。** 它有 Hindsight `hs-decide-1-flash` 请求/MLX PR、Qwen3.5 0.8B 量化请求以及 qwen35/qwen35moe 并行服务重新启用，但 Intel Arc 和 Arrow Lake Vulkan 支持仍是未解决的问题。

## 4. 性能前沿

优化工作按层次划分：

- **vLLM — KV 缓存、注意力、MoE、分布式服务。** 工作包括 GDN prefill 原地 SSM 状态、Blackwell 上 TRT-LLM 单体 MoE 填充移除、通过 CUDA 复制引擎的 PP 交接、ROCm MXFP8 GEMM 重新调优、RoPE+KV 缓存融合、tokenizer 池浅拷贝以及 RL 的直接权重重载。
- **llama.cpp — 本地内核、内存映射、MoE 缓存。** Checkpoint 保存/恢复现在使用引用计数共享状态而非深拷贝；MoE 专家缓存报告 **QFN 上解码 +6%、GLM 上 +7%、专家上传减少 8%**；Vulkan/Metal/WebGPU 内核持续扩展。
- **Ollama — 本地运行时内存与调度。** MLX Qwen3.8 内存调优、Strix Halo/gfx1151 的统一内存 APU 调度以及应用启动的惰性语法高亮。一个 25–30% t/s 回归报告因需要更多信息而被关闭。
- **LiteLLM — 网关开销与路由效率。** 流开销指标工作、调用方超时约束的重试、rpm/tpm 计数修复以避免过早 429、提示缓存亲和性用于

---

## 各项目详细报告

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM 文摘 — 2026-11-06

## 1. 今日亮点

过去 24 小时内没有新版本发布，但项目在 Rust 前端对齐路线图、Q4 SIG 路线图以及一系列 Blackwell/ROCm 问题上有大量活动。今天最具影响力的信号是 **v0.32.0 中影响 DeepSeek-V4.1 块大小选择的回归**（修复跟踪于 #60762），以及 **Qwen4Exp/Qwen3.8 在 262K 预填充边界处的崩溃**，该问题出现在 v0.31.1 与 v0.32.0 之间。**RL 训练直接权重重载**（#54477 及后续 PR）的工作也明显在加速推进，有望成为默认方式。

---

## 2. 发布与破坏性变更

过去 24 小时内无新版本发布。

**值得关注的迁移/配置说明：**
- **v0.32.0 在 DeepSeek-V4.1 上的回归**：发布标签上没有有效的块大小（64 和 128 均失败）。修复跟踪于 [#60762](https://github.com/vllm-project/vllm/issues/60762)；原始报告 [#59203](https://github.com/vllm-project/vllm/issues/59203)（已关闭）。
- **Rust 前端**（`VLLM_USE_RUST_FRONTEND=1`）仍为实验性，且相对于 Python API 服务器明确**功能不完整**。参见路线图 [#44280](https://github.com/vllm-project/vllm/issues/44280)。
- **直接权重重载**（`VLLM_RELOAD_MODE=direct`）正在向默认方式过渡；PR [#66002](https://github.com/vllm-project/vllm/pull/66002) 增加了模型选择加入机制并拒绝已知的静默路径，因此运维人员应预期在其变为隐式默认之前会有配置层面的门控变更。
- **XPU 分布式后端**正在从 `xccl` → `xccl2` 迁移，旧版已弃用：[#64818](https://github.com/vllm-project/vllm/pull/64818)。

---

## 3. 新模型与硬件支持

- **DeepSeek-V4.1**：任意流水线阶段分区支持正在推进（[#63106](https://github.com/vllm-project/vllm/pull/63106)）；允许在 KV 共享层组内部进行 PP 切分，而当前 NVIDIA 路径会拒绝此操作。
- **MiniMax-M3**：单节点 NVLink TP2/TP4 上使用 FlashInfer CAKE 的预填充和解码索引器上下文并行（CP）（[#61929](https://github.com/vllm-project/vllm/pull/61929)）。
- **NVIDIA Nemotron 3.5 ASR Streaming**（0.6B 多语言流式 ASR）已请求支持：[#47455](https://github.com/vllm-project/vllm/issues/47455)。
- **Rust 前端**即插即用 API 服务器持续向对齐推进 — 跟踪：[#44280](https://github.com/vllm-project/vllm/issues/44280)。
- **GB10 / DGX Spark（SM121）** 正在成为受支持的目标，但存在已知的逐张量 H2D 权重加载开销（[#58726](https://github.com/vllm-project/vllm/issues/58726)）以及 FLASHMLA_SPARSE_DSV4 元数据崩溃（[#50774](https://github.com/vllm-project/vllm/issues/50774)）。

---

## 4. 性能与优化

**已合入 / 审查中：**
- **Qwen3.5/3.8 和 Qwen3-Next 线性注意力的 GDN 预填充原地 SSM 状态** — 当预填充行是全新的或完全缓存时，每层减少 4–5 个 gather/mask/cast/scatter 内核（[#64498](https://github.com/vllm-project/vllm/pull/64498)）。
- **TRT-LLM 单体 MoE**：在 Blackwell 上为 block-FP8/MXFP8 将 DP 填充行排除在专家计算之外（[#65972](https://github.com/vllm-project/vllm/pull/65972)）。
- **通过 CUDA 复制引擎进行 PP 交接（仅限 NVLink）** — 0-SM 复制以减少流水线并行步骤中的 SM 争用（[#65896](https://github.com/vllm-project/vllm/pull/65896)）。
- **Tokenizer 池**现在仅对 Rust 后端进行浅拷贝，而非对整个 tokenizer 执行 `deepcopy`（[#65674](https://github.com/vllm-project/vllm/pull/65674)）。
- **ROCm MXFP8 `dot_scaled` GEMM 重新调优**，针对 gfx950 上 MiniMax-M3.1 TP4 线性层；M=17–256 瓦片重塑（[#65530](https://github.com/vllm-project/vllm/pull/65530)）。
- **RoPE + KV-cache 融合**现在可通过显式请求或文档化的选择加入在 ROCm/AITER 上使用（[#57900](https://github.com/vllm-project/vllm/pull/57900)）。

**回归 / 未解决的性能问题：**
- **MiniMax-M2.7 TP4+EP 吞吐量回归**，跨越 0.29、0.31、0.32：[#63705](https://github.com/vllm-project/vllm/issues/63705)。
- **Qwen3.5 397B NVFP4 DEP8**：在 8×B200 上混合预填充-解码步骤比仅解码慢约 6 倍：[#44806](https://github.com/vllm-project/vllm/issues/44806)。
- **ROCm / MI355X 性能跟踪**，针对 Qwen3.8-2.4T-A95B（[#57149](https://github.com/vllm-project/vllm/issues/57149)）和 Qwen3.8-Flash-Next（[#59575](https://github.com/vllm-project/vllm/issues/59575)）。
- **GB10 权重加载**因从 safetensors mmap 视图进行逐张量 H2D 而缓慢（[#58726](https://github.com/vllm-project/vllm/issues/58726)）。
- **异步 KV 拉取在完成拉取后出现 1.0–1.8 秒的重新排队停顿**；RFC 提议进行队首重新准入（[#62532](https://github.com/vllm-project/vllm/issues/62532)）。

---

## 5. 稳定性与回归

按严重程度排序：

1. **v0.32.0 在 DeepSeek-V4.1 上的块大小回归** — 发布标签上没有可用的块大小（64 和 128 均失败）。修复跟踪于 [#60762](https://github.com/vllm-project/vllm/issues/60762)；原始：[#59203](https://github.com/vllm-project/vllm/issues/59203)。
2. **Qwen4Exp / Qwen3.8-Flash-Next GPU 崩溃** — 当单次预填充跨越 262,144 原生边界时出现 CUDA 非法内存访问。在 v0.31.1 上正常，在 v0.32.0 上损坏：[#62383](https://github.com/vllm-project/vllm/issues/62383)。
3. **NixlConnector KV 损坏** — 远程 READ 正在进行时，KV 块在租约到期时被释放；解码可能静默消费被覆盖的 KV。正确性 bug，未引用可见修复 PR：[#61229](https://github.com/vllm-project/vllm/issues/61229)。
4. **DeepSeek-V4.1 MoE 置换索引 bug** — 存储在权重池中的置换索引在二级睡眠下被清零，导致重载时每一行都变成第 0 行的副本。修复：[#66010](https://github.com/vllm-project/vllm/pull/66010)。
5. **二级睡眠 + 排队请求** — 如果在 `reload_weights` 完成之前触发 `wake_up()`，在 L2 睡眠期间排队的请求可能在已丢弃的权重上运行。修复：[#65326](https://github.com/vllm-project/vllm/pull/65326)。
6. **FlashInfer 采样器 JIT** 在默认 wheel 安装中无法发现 `nvcc` 时导致引擎启动崩溃（无原生采样器回退）：[#49497](https://github.com/vllm-project/vllm/issues/49497)。
7. **SM120 自适应验证门控** — `enable_adaptive_verification` 在 SM120 上被拒绝（设备查询透镜门控为 SM90/SM100）：[#63343](https://github.com/vllm-project/vllm/issues/63343)。
8. **Qwen3.5-9B 混合 GDN + 动态 LoRA** 在长序列上出现 NaN（punica Triton 内核）：[#52568](https://github.com/vllm-project/vllm/issues/52568)。
9. **int8_per_token_head KV 正确性** — 开启前缀缓存时损坏输出（池被钉在 100%）：[#50702](https://github.com/vllm-project/vllm/issues/50702)；禁用前缀缓存时损坏首批生成的 token：[#50749](https://github.com/vllm-project/vllm/issues/50749)。
10. **ROCm GLM-5.3-Flash "Workspace is locked"** 在高解码并发下 `rocm_fp8_paged_mqa_logits` 中出现 AssertionError：[#64874](https://github.com/vllm-project/vllm/issues/64874)。
11. **EngineCore 断言** 在 `_update_from_kv_xfer_finished` 中，当异步 KV 连接器报告已中止/已释放请求的传输完成时：[#43226](https://github.com/vllm-project/vllm/issues/43226)。
12. **ROCm AITER FA 跳过 1 token bug** — CK 路径上的 `min_seqlen_q=1` 丢弃单 token 提示。修复 PR（已关闭/已合并跟踪）：[#65722](https://github.com/vllm-project/vllm/pull/65722)。

---

## 6. 对应用开发者的意义

- **谨慎固定版本。** v0.32.0 有两个已确认的回归（DeepSeek-V4.1 块大小、Qwen3.8/4Exp 262K 预填充崩溃）。如果你使用这些模型，请留在 v0.31.1，直到 #60762 和 #62383 解决。在生产中采用 v0.32.x 之前请跟踪这两个问题。
- **KV 连接器用户（P/D 分离）：** NixlConnector 租约到期 bug 是静默的 — 你可能看到看似合理但错误的输出，而非崩溃。如果你依赖去中心化服务，请关注 #61229，并考虑固定到已知良好的连接器版本，直到修复落地。
- **共享集群上的睡眠/唤醒（L2）：** L2 睡眠 + 排队请求路径可能在过期权重上提供服务。#65326 中的修复尚未合并；如果你使用睡眠模式进行 RL/评估交换，请在编排中将 `wake_up()` 门控在权重重载完成之后。
- **混合 + 量化 KV 用户：** 如果你在 Gemma-4 或类似模型上运行 int8-per-token head KV 并开启前缀缓存，请注意两个正确性 bug（#50702、#50749）。作为安全网，在解决之前请禁用前缀缓存。
- **Linux/ROCm 用户：** 若干 AITER 和 workspace-lock 问题（#64874、#65722）影响多租户解码工作负载；如果你在 ROCm 上服务 GLM-5.3-Flash 或单 token 提示，请在高负载下显式测试。#65530 中的 M=17–256 MXFP8 重新调优可能会显著改善 MiniMax-M3.1 TP4 吞吐量（>仅瓦片选择变更，但值得基准测试）。
- **跨越 262K 的长上下文智能体：** Qwen4Exp 崩溃在预填充边界处可精确复现 — 如果你构建具有超大工具/系统提示的智能体，要么在单次预填充中保持在 262,144 token 以下，要么固定到 v0.31.1。
- **RL / 微调团队：** 直接权重重载工作（#54477、#64352、#66002）正在快速收敛；预计近期会切换为默认，现在就开始审计自定义加载器 — 新的选择加入模型将拒绝之前静默不正确的路径（参见 #66010 了解正在捕获的此类 bug）。
- **Rust 前端：** 尚未准备好用于生产 — 在 #44280 的对齐检查清单关闭之前，将 `VLLM_USE_RUST_FRONTEND=1` 视为实验性。

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

⚠️ 摘要生成失败。

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp 摘要 — 2026-11-06

## 今日亮点

llama-server 落地了一项以内存为重点的变更，用引用计数的共享状态替代深拷贝的检查点（[b12141](https://github.com/ggml-org/llama.cpp/releases/tag/b12141)），同时还伴有 Vulkan 和 Metal 内核方面的工作（[b12143](https://github.com/ggml-org/llama.cpp/releases/tag/b12143)、[b12138](https://github.com/ggml-org/llama.cpp/releases/tag/b12138)）。在 PR 方面，Blackwell 借助 `mxf8f6f4` MMA 获得了 MXFP4 W4A8 路径（[#31747](https://github.com/ggml-org/llama.cpp/pull/31747)），而一种混合 MoE 专家缓存报告了可测量的解码收益（[#31233](https://github.com/ggml-org/llama.cpp/pull/31233)）。稳定性仍是主导主题：量化目标上的投机解码正确性（[#25618](https://github.com/ggml-org/llama.cpp/issues/25618)）以及与 MTP 相关的崩溃（[#31077](https://github.com/ggml-org/llama.cpp/issues/31077)、[#30698](https://github.com/ggml-org/llama.cpp/issues/30698)）仍处于开启状态。

## 发布与破坏性变更

无公开 API 破坏。值得注意的行为变更：

- **[b12137](https://github.com/ggml-org/llama.cpp/releases/tag/b12137) — 服务器拒绝非对象的实时会话更新。** 此前格式错误的负载会抛出未捕获异常并终止服务器；现在发送无效会话 JSON 的客户端将收到错误响应，而不再是崩溃。请审查任何自定义的 Realtime 兼容客户端。
- **[b12140](https://github.com/ggml-org/llama.cpp/releases/tag/b12140) — YaRN 的 `truncate=false`。** 长上下文 RoPE 缩放现在可以避免将校正间隔边界舍入为整数；需主动启用，且向后兼容。
- **[b12131](https://github.com/ggml-org/llama.cpp/releases/tag/b12131) / [b12132](https://github.com/ggml-org/llama.cpp/releases/tag/b12132) — server/common 中移除 `u8path()`**，改用 `generic_string()`。属于内部清理，但值得在 Windows 上对非 ASCII 模型路径做回归测试。

## 新模型与硬件支持

- **Blackwell MXFP4 W4A8 matmul** — 使用 `kind::mxf8f6f4`（`m16n8k32`、`scale_vec::1X`）的 `mul_mat` 路径，激活量化为 e4m3，每 32 个值使用 ue8m0 缩放，对应 e2m1 权重（[#31747](https://github.com/ggml-org/llama.cpp/pull/31747)）。
- **xing4_0 架构** — 完整注册、张量加载、HF→GGUF 转换，以及 mHC/MLA/MoE/MTP 的计算图；无新增 ggml 算子（[#31589](https://github.com/ggml-org/llama.cpp/pull/31589)）。
- **WebGPU**：`MEAN` 算子已落地（[b12135](https://github.com/ggml-org/llama.cpp/releases/tag/b12135)）；`COUNT_EQUAL` 正在评审中，用于 `ggml-opt` 的 token 准确率统计，目前回退到 CPU（[#31578](https://github.com/ggml-org/llama.cpp/pull/31578)）。
- **Vulkan IQ4_NL 标量 MMQ** 整数点积着色器，面向不支持 coopmat 的 GPU，沿用 IQ4_XS 的先例（[#31764](https://github.com/ggml-org/llama.cpp/pull/31764)）。
- **Metal bf16×bf16 matmul 内核**已添加，同时将 `mul_mm` 扩展到 `32 <= K < 64`（[b12138](https://github.com/ggml-org/llama.cpp/releases/tag/b12138)）。
- **Hexagon**：`set_tensor_n` 后端 API，以及重打包/分块稳健性修复（[#31744](https://github.com/ggml-org/llama.cpp/pull/31744)）。
- **OpenAI 兼容的 `/v1/audio/speech` 端点**正在评审中，基于已关闭的 mtmd 音频输出设计 issue 构建（[#31763](https://github.com/ggml-org/llama.cpp/pull/31763)、[#21956](https://github.com/ggml-org/llama.cpp/issues/21956)）。

## 性能与优化

- **检查点内存开销降低** — 在槽位保存/恢复中，用引用计数的 `shared_ptr` 检查点列表和 `common_state_data` 缓冲区替代深拷贝（[b12141](https://github.com/ggml-org/llama.cpp/releases/tag/b12141)）。
- **MoE 专家缓存（ClassLRU）** — 类 LRFU/MQ 的混合方案，报告称在热缓存下相比 SLRU 达到 **QFN 上解码 +6%、GLM 上 +7%、专家上传减少 8%**（[#31233](https://github.com/ggml-org/llama.cpp/pull/31233)）。
- **Vulkan FWHT 内核**扩展到块宽度 > 512，且现在可直接接受 F16 源，覆盖子组和共享内存两条路径（[b12143](https://github.com/ggml-org/llama.cpp/releases/tag/b12143)）。
- **Vulkan flash-attention 行分块**将验证 token 和 GQA 头打包进一个 tile，以共享 K/V 读取（WIP/RFC）（[#30190](https://github.com/ggml-org/llama.cpp/pull/30190)）。
- **Linux mmap**：提议移除 `MAP_POPULATE`，该选项目前会阻塞直到整个模型被换入（[#28055](https://github.com/ggml-org/llama.cpp/pull/28055)）。
- **WASM relaxed SIMD** 点积实现，用于浏览器构建（[#19590](https://github.com/ggml-org/llama.cpp/pull/19590)）。
- **Qualcomm Adreno Vulkan 变通方案**正在进行中：为 `rms_norm` 添加运行时循环次数，以避免展开变体被错误编译（[#30824](https://github

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

 

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

We need respond with translation. Need understand user request: They provided long English technical report (actually a generated digest?) and ask "Translate the following English technical report into Simplified Chinese." Then rules. We need output only translation, preserve Markdown structure exactly. We need translate the English technical report into Simplified Chinese. Need include all content. We need not add preamble. Must preserve Markdown structure exactly. But the source text is not fully formatted? It is a long instruction plus digest. Wait the "following English technical report" is the text after that? Let's parse.

User message:
"Translate the following English technical report into Simplified Chinese.

We need answer user asks generate structured English digest for LiteLLM based on GitHub data for 2026-11-06. Need sections specified. Need use provided data. Need include GitHub links for each item. Need be concise professional infrastructure engineer. We can analyze data.

Need produce digest. We need infer. Data includes releases last 24h: v1.108.1 with Docker image signature verification. Latest issues updated last 24h total 95 top 30. PRs total 500 top 20.

Need structured sections:
1 Today's Highlights 2-3 sentences summarizing most important updates
2 Releases & Breaking Changes - new versions API/config changes migration notes omit if none
3 New Model & Hardware Support - newly supported models architectures backends CUDA/ROCm/Metal/CPU quantization formats
4 Performance & Optimization - throughput latency memory kernel work landed/in progress, concrete numbers when available
5 Stability & Regressions - crashes correctness bugs regressions reported today ranked severity, note fix PRs
6 What This Means for Application Developers - practical takeaways

Need include GitHub links for each item. We can link issue/PR/release. Need not cover all. We should synthesize.

Need be accurate to data. Date 2026-11-06 but data includes issues created earlier updated 2026-11-05/06. "Today" maybe updates. We need mention v1.108.1 release. Highlights maybe Docker cosign signature and active fixes around routing, streaming overhead, Bedrock structured output. Also key regressions: OpenRouter streaming empty post-finish choice in v1.108.0, COHERE_API_BASE ignored for embeddings, MCP is_error dropped, token_counter image detail original, etc. PR fixes: #49920 overhead latency metric streams, #50316 retry timeout, #50321 responses prompt caching affinity, #50320 rpm/tpm double count, #50318 Codex catalog, #50221 Bedrock native structured output, #50319 OpenRouter video fields, #49905 guardrails post_call rewrites buffered streams, #40188 token_counter input_audio, #37291 routing group names, #50322 terraform tag budgets, #47496 S3 bucket restriction breaking change, #47601 closed Bedrock S3 bucket. Need mention breaking change in PR #47496: clients cannot pick S3 buckets unless admin allowed; breaking. Also release v1.108.1 no details besides signature. PRs marked breaking: #47496 feat(proxy)! only let clients pick S3 buckets admin allowed. #47601 fix(bedrock)! use S3 bucket passed to SDK file calls instead of requiring proxy config (closed). Could mention potential breaking. Also PR #50318 stacked on #49825. Not necessarily landed.

New Model & Hardware Support: PR #50221 Bedrock default native structured output on Opus 5.5, Fable 5.1, Sonnet 5, Haiku 5.5. PR #50319 OpenRouter video request fields first/last frames, reference images, duration, aspect ratio, audio, seed for Seedance and MiniMax. Issue #26444 Claude Opus 4.7 rejects temperature (closed bug). Issue #47924 reasoning_effort none for default-on Claude. PR #50237 Vertex AI Cloud TTS regional endpoint. Maybe no hardware. Issue #48238 Vertex Model Garden streaming drops reasoning. No CUDA/ROCm.

Performance & Optimization: PR #49920 prometheus overhead latency for streams and per-chunk work. PR #50316 bound retries and Retry-After sleeps by caller timeout. PR #50320 count deployment rpm/tpm once for usage-based-routing-v2 and enforce_model_rate_limits, prevents premature 429 on 6th request with rpm:10. PR #50221 native structured output 20-40% cheaper (from PR summary). Issue #47096 SAP GenAI Hub deployment_url re-fetched every call ~870ms overhead. Issue #50320. PR #50145 model hub full prices. Issue #47837 S3 presigned URL query re-parse 403 maybe correctness not perf. Issue #50156 OpenRouter streaming empty post-finish choice. Issue #47332 stream cut after finish_reason before include_usage logged success cached_tokens lost billed full input rate. Issue #33167 Prisma binaries downloads at startup (closed stale) maybe perf. PR #49920 concrete: overhead metric for streams. PR #50221: 20-40% cheaper native output.

Stability & Regressions: Need rank severity. Issues updated today. We can group:
- Critical streaming correctness: #50156 OpenRouter streaming emits empty post-finish choice without usage before final usage chunk (v1.108.0). #47332 stream cut after finish_reason before include_usage chunk logged success with locally counted usage (cached_tokens lost, billed at full input rate). #49711 SSE keepalive opens stream then Anthropic client never told upstream failed. #48238 Vertex Model Garden streaming drops reasoning_content, extra tool calls, usage details. #49905 PR fixes guardrails post_call rewrites into buffered streams.
- Translation/tool correctness: #48145 JSON-defined openai-like providers lose tools by default if model not in catalog. #39736 Mistral drops named tool_choice (closed). #49359 MCP tool execution failures drop is_error flag and not propagate to Anthropic tool_result. #47531 /v1/messages to non-Anthropic model drops Claude Code mid-conversation tool_addition blocks. #36557 PR preserve tool reference results. #37291 routing group names resolve to member deployment ids.
- Provider/API bugs: #50248 COHERE_API_BASE ignored for cohere embeddings (rerank honors it). #47046 Vertex/Gemini context caching reorders conversation and drops system_instruction. #46866 Azure Responses API sends gpt5_series routing prefix as deployment name. #47563 Vertex AI claude-opus-5-5 rejects response_format json_schema sends deprecated output_format (closed). #47837 HTTPHandler query re-parse breaks S3/SigV4 presigned image URLs 403. #50173 /v1/images/edits on vertex_ai Gemini no image returns 500 not 400.
- Auth/security: #35383 MCP stateful session cap bucketed by API key — shared keys collapse all users into one 100-session limit. #47496 S3 bucket security breaking change.
- Token counting/context: #47327 token_counter rejects image detail "original", pre-call checks log ERROR and skip context window check. #40188 PR count OpenAI input_audio blocks instead of raising. #23345 Model Info manually added field not persisted. #28994 Feature reading secrets from files.
- Rate limiting: #50320 PR counts rpm/tpm once; bug currently double counts? Summary says with enforce_model_rate_limits on and routing group on usage-based-routing-v2, deployment rpm:10 429s on 6th request. This is a regression/bug with fix PR.
- Release signature: v1.108.1 verify Docker image signature.

Need include links. Use markdown links. Since user wants GitHub links for each item. We can link text to URL. Use https://github.com/BerriAI/litellm/releases/tag/v1.108.1 etc. Issues: https://github.com/BerriAI/litellm/issues/50156. PRs: https://github.com/BerriAI/litellm/pull/50316. Need not link all? "for each item" yes.

Need generate structured digest. Should avoid saying "today" if data includes updated. But can say "As of 2026-11-06". We need maybe mention total counts: 95 issues updated, 500 PRs updated. But not required.

Need maybe note v1.108.1 release only signature. We can mention no functional release notes in provided data. Release and breaking changes: v1.108.1 signed with cosign key from commit e82a3c6. Breaking change in PR #47496 (OPEN) and #47601 (CLOSED) around Bedrock S3 buckets: admin allowlist for client-specified S3 bucket; SDK uses call bucket/env var. Potential migration: clients sending s3_bucket_name, s3_output_bucket_name etc must be allowlisted or use admin-configured bucket. Also #50221 changes default structured output for Bedrock models—could alter behavior/cost but not breaking. #50316 changes timeout semantics: caller timeout bounds retries and Retry-After sleeps; may alter long retry behavior. Could mention as behavior change.

New Model & Hardware Support: #50221 Bedrock models Opus 5.5, Fable 5.1, Sonnet 5, Haiku 5.5 native structured output all thinking modes. #50319 OpenRouter video fields for Seedance and MiniMax. #50237 Vertex Cloud TTS regional endpoint (eu/us) for Chirp. #50318 Codex model catalog fetched from GitHub for exact release. Maybe issue #26444 Claude Opus 4.7 temperature not supported. #47924 reasoning_effort none on Claude default-on. #48238 SGLang backend GLM-5.3 via Vertex Model Garden. #48558 ModelScope async image generation polling. #50319 video. No hardware specifics.

Performance & Optimization: #49920 Prometheus overhead latency for streams including per-chunk work. #50316 retry/timeout bounding. #50320 rpm/tpm counting once to avoid premature 429. #50221 native structured output 20-40% cheaper, as fast/faster. #47096 SAP GenAI Hub deployment URL discovery ~870ms overhead per call. #50145 model hub full prices. #49905 guardrails post_call rewrites. Maybe #50308 spend row race. #50312 i18n.

What this means for app devs: Upgrade to v1.108.1 for signed images if supply chain matters. Watch streaming usage and error handling: OpenRouter usage trailer, SSE keepalive, stream cuts; do not rely on locally counted usage. Provider-specific params and base URLs: cohere embeddings need api_base explicit; Anthropic Opus 4.7 no temperature; reasoning_effort none may not disable thinking; Gemini cache_control not prefix can reorder. Tools/MCP: tool definitions may be dropped for non-catalog JSON providers; MCP is_error lost; Claude Code mid-conversation tool additions dropped. Rate limits/retries: caller timeout now bounds retries; rpm/tpm double count fix helps. Bedrock structured output native cheaper but behavior change. S3 bucket security: clients must use allowlisted buckets. Prompt caching affinity for /v1/responses helps repeated prompts. Codex model catalog per release. Video fields for OpenRouter. Secrets from files feature open. Terraform tag budgets refresh.

Need be concise. Desired oververbosity 5. We can produce maybe 800-1200 words? User wants structured digest. We can use bullets. Need maybe not overlong. We should include links.

Let's plan structure:

# LiteLLM Digest — 2026-11-06
Data source: github.com/BerriAI/litellm. 95 issues updated, 500 PRs updated in last 24h (top items). Maybe mention.

## 1. Today's Highlights
- v1.108.1 released with cosign Docker signature verification [release link].
- Routing/streaming optimizations and fixes dominate: PRs for stream overhead metric, retry timeout bounding, prompt caching affinity for /v1/responses, rpm/tpm counting.
- Several provider translation/tool-call regressions surfaced/updated: OpenRouter streaming empty post-finish choice, Cohere embedding base URL ignored, MCP is_error dropped, Claude Code tool additions dropped.
- Bedrock native structured output default for newer Anthropic models, 20-40% cheaper.

## 2. Releases & Breaking Changes
- v1.108.1: Docker images signed with cosign; verify with key from commit e82a3c6. No functional notes in provided data. Link.
- Breaking/behavior: PR #47496 [OPEN] proxy only lets clients pick admin-allowed S3 buckets for Bedrock batch; clients sending s3_bucket_name etc will be refused unless allowlisted. Link. PR #47601 [CLOSED] Bedrock SDK uses S3 bucket passed to SDK call/env var instead of requiring proxy config; safe only on top of #47496. Link.
- Behavior: PR #50316 [OPEN] caller timeout bounds whole routed request including retries and Retry-After sleeps; reads OpenAI SDK timeout header as deadline. May change retry behavior. Link.
- Behavior: PR #50221 [OPEN] default to native structured output for Bedrock Opus 5.5/Fable 5.1/Sonnet 5/Haiku 5.5 in all thinking modes; native 20-40% cheaper. Link.

## 3. New Model & Hardware Support
- Bedrock: native JSON schema structured output for Opus 5.5, Fable 5.1, Sonnet 5, Haiku 5.5 via PR #50221. Link.
- OpenRouter video: first/last frames, reference images, duration, aspect ratio, audio, seed fields for Seedance and MiniMax via PR #50319. Link.
- Vertex AI Cloud TTS: uses deployment regional endpoint (eu-/us-) for Chirp TTS when vertex_location set; global/unknown stay global via PR #50237. Link.
- Codex: proxy serves Codex its release-specific model catalog fetched from public Codex GitHub repo via PR #50318. Link.
- Provider quirks: Claude Opus 4.7 rejects temperature; get_supported_openai_params still lists it (closed issue #26444). Link. reasoning_effort="none" doesn't disable thinking on default-on Claude models (#47924). Link. No CUDA/ROCm/Metal/CPU/quantization updates in provided data.

## 4. Performance & Optimization
- Stream overhead: PR #49920 records litellm_overhead_latency_metric for streams and counts proxy per-chunk work. Link.
- Rate limit/routing: PR #50320 counts deployment rpm/tpm once for usage-based-routing-v2 + enforce_model_rate_limits; fixes rpm:10 429 on 6th request. Link.
- Retry/timeout: PR #50316 bounds retries/Retry-After sleeps by caller timeout and reads SDK timeout header. Link.
- Structured output: PR #50221 native Bedrock structured output 20-40% cheaper and as fast/faster. Link.
- SAP GenAI Hub: issue #47096 deployment URL re-fetched every LLM call, ~870ms overhead; no fix PR listed. Link.
- Prompt caching: PR #50321 pins /v1/responses requests for prompt caching affinity. Link.
- Model hub: PR #50145 shows full per-deployment prices. Link.
- Spend/race: PR #50308 avoids response reads claiming creation spend rows. Link.

## 5. Stability & Regressions
Ranked. Use "High", "Medium", "Low". Include links and fix PRs if present.

High:
- Streaming usage/billing: #50156 OpenRouter streaming emits empty post-finish choice without usage before final usage chunk (v1.108.0). Link. #47332 stream cut after finish_reason before include_usage logged success with locally counted usage, cached_tokens lost, billed full input rate (closed). Link.
- SSE keepalive: #49711 after SSE keepalive opens stream, Anthropic client never told upstream failed. Link.
- Tool/MCP correctness: #49359 MCP tool execution failures drop is_error and don't propagate to Anthropic tool_result. Link. #48145 JSON-defined openai-like providers lose tools if model not in catalog (1.106 regression from 1.81). Link. #47531 /v1/messages to non-Anthropic model drops Claude Code mid-conversation tool_addition blocks. Link.
- Rate limits: #50320 describes rpm:10 429 on 6th request with enforce_model_rate_limits + usage-based-routing-v2; fix PR #50320. Link.

Medium:
- Token/context: #47327 token_counter rejects image detail "original", pre-call checks log ERROR and skip context window check. Link. Fix PR #40188 for input_audio token counting. Link.
- Provider config: #50248 COHERE_API_BASE ignored for cohere embeddings while rerank honors it. Link.
- Gemini/Vertex: #47046 Vertex/Gemini context caching reorders conversation when cache_control not prefix and drops system_instruction. Link. #46866 Azure Responses API sends gpt5_series routing prefix as deployment name. Link. #50173 /v1/images/edits on vertex_ai Gemini with no image returns 500 not 400. Link.
- S3/SigV4: #47837 HTTPHandler query re-parse breaks S3/SigV4 presigned image URLs (403). Link.
- Model info persistence: #23345 manually added Model Info field not persisted. Link.
- MCP session cap: #35383 MCP stateful session cap bucketed by API key; shared keys collapse users into one 100-session limit. Link.
- Guardrails: PR #49905 delivers post_call rewrites into buffered streams on guardrails config path. Link.
- Routing groups: PR #37291 resolves routing group names to member deployment ids. Link.
- Mistral tool choice: #39736 closed, Mistral drops named tool_choice. Link.
- Vertex AI Claude response_format: #47563 closed, sends deprecated output_format instead of output_config.format. Link.
- GitHub Copilot system messages: #48568 list-form content passed unflattened. Link.
- SAP overhead as above.
- Prisma startup downloads: #33167 closed stale. Link.
- docs CLA link: #49585 closed. Low.

Need maybe not include all 30. But "Stability & Regressions" can include top. We can group. Need include links for each item. We can list many but concise.

## 6. What This Means for Application Developers
- Upgrade to v1.108.1 if you need cosign-verified Docker images. Verify signature before deploy.
- Do not trust locally counted usage on streams: handle missing usage/final chunks, empty post-finish choices, SSE keepalive failures; check provider usage trailer. Billing correctness fixes in flight.
- If

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth Digest — 2026-11-06

## 1. Today's Highlights
No releases landed in the last 24h, but the PR queue is very active on two fronts: Studio desktop/chat UI polish and lag fixes (glass theme, long-chat scrolling, floating browser), and serious perf/memory work on the diffusion and video paths (Lumina-Image-2.0, MiniMax-H3, NVFP4 step protection). A notable correctness/performance item surfaced in RAG ingestion: embeddings run at batch-of-1 per document (~99 ms/chunk vs 2.07 ms batched).

## 2. Releases & Breaking Changes
None in the last 24h. However, a forward-compat break is flagged:
- **transformers 5.23 renames gradient checkpointing → activation checkpointing**, with 4 behavior-changing spots in Unsloth. Old names remain as aliases until v6, but Trainer reads only new names. ([#16667](https://github.com/unslothai/unsloth/issues/16667))

## 3. New Model & Hardware Support
- **NVFP4 diffusion on sm_100-class cards (B200/GB200/B300)**: W4A16 step-protect schedule `auto` now default, protecting first/last NVFP4 steps. ([#16759](https://github.com/unslothai/unsloth/pull/16759))
- **Lumina-Image-2.0**: added measured activation sizes, improving int8 residency on 16–24 GB cards. ([#16808](https://github.com/unslothai/unsloth/pull/16808))
- **MiniMax-H3**: host-reserve-aware slab compaction to avoid OOM on 16 GiB L4. ([#16849](https://github.com/unslothai/unsloth/pull/16849))
- **CUDA 13 / cuDNN selection fix** for stable-diffusion.cpp builds (reads CUDA major from `DT_NEEDED`). ([#16789](https://github.com/unslothai/unsloth/pull/16789))
- **Integrated GPU GGUF loading**: memory-map oversized models and read lookup tables from disk. ([#16857](https://github.com/unslothai/unsloth/pull/16857))
- **CPU backend**: context parallelism now engages (or refuses explicitly) instead of silently falling back to DDP. ([#16802](https://github.com/unslothai/unsloth/pull/16802))

## 4. Performance & Optimization
- **RAG ingest batching gap**: `_embed_all` is called per document; small-file corpora hit the batch-of-1 path at **~99 ms/chunk vs 2.07 ms batched (~48×)**. ([#16715](https://github.com/unslothai/unsloth/issues/16715))
- **Diffusion LoRA training**: removed per-micro-step host syncs, single decode per cached image, streamed int8 base load — bit-identical numerics. ([#16763](https://github.com/unslothai/unsloth/pull/16763))
- **Video memory planning**: forward headroom now counted before keeping whole-module offload, preventing first-forward OOMs. ([#16805](https://github.com/unslothai/unsloth/pull/16805))
- **Long-chat UI lag on Linux**: offscreen message bodies parked behind measured placeholders to unblock WebKitGTK style/layout. ([#16854](https://github.com/unslothai/unsloth/pull/16854))
- **Pipeline parallel on CPU**: chunked cross entropy now runs on last stage instead of silently falling back to full `[rows, vocab]` logits. ([#16846](https://github.com/unslothai/unsloth/pull/16846))
- **sd.cpp installer**: retries transient downloads before stepping down to CPU build. ([#16788](https://github.com/unslothai/unsloth/pull/16788))

## 5. Stability & Regressions
Ranked by severity:
- **High — RAG throughput regression**: ~48× slower ingest for multi-document corpora. ([#16715](https://github.com/unslothai/unsloth/issues/16715))
- **High — Image generation falls back to CPU/RAM on Windows 11 / RTX 3060** while text gen correctly uses GPU. ([#16803](https://github.com/unslothai/unsloth/issues/16803))
- **Medium — Studio fine-tuning job list drops cancelled queued jobs** when a newer job starts (multi-LoRA interaction); CLOSED. ([#16632](https://github.com/unslothai/unsloth/issues/16632))
- **Medium — Fine-tuning pagination can skip a job** when Multi-LoRA batches start out of order; open follow-up. ([#16720](https://github.com/unslothai/unsloth/issues/16720))
- **Medium — Cursor invalidation race** when Multi-LoRA batch starts during a list request. ([#16719](https://github.com/unslothai/unsloth/issues/16719))
- **Medium — Download never resumes** on Windows (v0.1.915-beta). ([#16810](https://github.com/unslothai/unsloth/issues/16810))
- **Low/Medium — Long-conversation UI lag** on web + desktop; fix PR exists. ([#16603](https://github.com/unslothai/unsloth/issues/16603), [#16854](https://github.com/unslothai/unsloth/pull/16854))
- **Low — Glass theme inconsistencies and full-background blur**; several reports. ([#16770](https://github.com/unslothai/unsloth/issues/16770), [#16820](https://github.com/unslothai/unsloth/issues/16820))
- **Low — `search_conversation` tool disappearance (3rd recurrence)**. ([#16706](https://github.com/unslothai/unsloth/issues/16706))
- **Low — AppImage startup failure on Slackware64** (GLib/AppIndicator symbol); CLOSED. ([#16605](https://github.com/unslothai/unsloth/issues/16605))
- **Low — Homebrew cask `unsloth` unavailable despite docs**; CLOSED. ([#16672](https://github.com/unslothai/unsloth/issues/16672))
- **Tool-calling correctness**: GLM/MiniMax/Kimi/DeepSeek/Seed-OSS in-process routes return tool calls as plain text (`finish_reason: "stop"`) — fix PR open. ([#16824](https://github.com/unslothai/unsloth/pull/16824))

## 6. What This Means for Application Developers
- **RAG builders**: until batching is fixed, chunk aggregation across documents is your only lever — the per-document embed call dominates ingest cost.
- **Tool-using agents**: validate `finish_reason` and parse embedded `<tool_call>` blocks if you run GLM/MiniMax/Kimi/DeepSeek/Seed-OSS in-process; the fix is in flight but not merged.
- **Windows + NVIDIA users**: verify image-gen actually lands on GPU — silent CPU fallback is reported on RTX 3060.
- **Multi-LoRA fine-tuning pipelines**: pagination and job-list consistency issues exist; treat queue listings as eventually-consistent and reconcile by job ID.
- **Frontier stack (transformers 5.x)**: pin or prepare for the checkpointing API rename; behavior changes in 4 spots even with aliases present.
- **Serverless/small-GPU diffusion**: NVFP4 step protection and measured activation tables materially change residency on B200-class and 16–24 GB cards — good time to re-run memory plans.

</details>

---
*本日报由 [agents-radar](https://github.com/SnowEagle-Kn/agents-radar) 自动生成。*