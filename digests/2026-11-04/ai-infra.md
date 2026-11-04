# AI 基础设施日报 2026-11-04

> 生成时间: 2026-11-04 02:55 UTC | 覆盖项目: 6 个

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## 横向对比

# AI Infrastructure Cross-Project Comparison — 2026-11-04

## 1. Ecosystem Overview

Activity across the six projects is bifurcated by layer: serving engines (vLLM, SGLang) are in a stabilization phase where **correctness regressions dominate the signal** — cross-request KV contamination, PD/PP send races, and silent-garbage output under Confidential Computing — while local runtimes (llama.cpp, Ollama) and tooling (LiteLLM, Unsloth) shipped releases or build trains. Hardware breadth expanded faster than hardware quality: ROCm, Ascend NPU, Intel XPU, Iluvatar, Hexagon, and SM120/Blackwell all gained support, yet each also produced load failures, HBM exhaustion, or throughput gaps. Speculative/MTP decoding is the single most common *correctness* risk surface today, appearing as a top-severity item in vLLM, SGLang, and llama.cpp simultaneously. Meanwhile the gateway layer is quietly re-platforming: LiteLLM's MCP permission stack signals tool access is becoming a control-plane primitive rather than an application concern.

---

## 2. Activity Comparison

Counts reflect items *referenced in today's digest*, not total tracker volume; treat them as a proxy for editorial salience, not project size.

| Project | Layer | Issues cited | PRs cited | Release status | Top-severity signal |
|---|---|---|---|---|---|
| **vLLM** | Datacenter serving engine | 16 | 14 | No release; `v0.32.0` tag regression on SM120 | Cross-request KV cache contamination in PD disaggregation w/ prefix caching (#61160) — multi-tenant data leak |
| **SGLang** | Datacenter serving engine | 21 | 16 | No release | Speculative sampling rejects valid drafts (`q > 1.0` rounding) since #37134 (#42568) — silent acceptance-rate loss |
| **llama.cpp** | Local/edge runtime + GGUF reference | 3 | 21 | 14-build train **b12081–b12094** | Draft-MTP greedy divergence on quantized targets (#25618) + CUDA IMA with draft-MTP |
| **Ollama** | Local runtime + model distribution | 13 | 17 | **v0.40.15-rc0** | Scheduler deadlock in eviction path; wedges all subsequent loads (#17408) |
| **LiteLLM** | LLM gateway / control plane | 16 | 12 | **v1.109.0-rc.1** (cosign-signed images) | Redis circuit breaker stuck `HALF_OPEN` after failover (#49884) |
| **Unsloth** | Fine-tuning + Studio | 17 | 15 | **v0.1.914-beta** | Silent training-data corruption on S3 audio manifests (#16528) |

**Read-out:** llama.cpp has the most skewed ratio (21 PRs vs. 3 issues) — a healthy merge cadence. vLLM and SGLang carry the heaviest *severity* load (4 "critical"-class items combined) despite zero releases. Unsloth and Ollama both shipped releases with *known-broken updater paths*, which is the riskiest profile for downstream consumers.

---

## 3. Model Support Race

**Tier 1 — Frontier datacenter models (vLLM, SGLang).** Both are converging on the same 2026 model cohort: DeepSeek-V4.1, GLM-5.3-Flash, Qwen3.8-Flash-Next, Kimi-K3. The differentiator is *which hardware path lands first*:
- **SGLang** is ahead on DeepSeek-V4.1 hardware breadth — SM90 packed-FP4 KV attention (#43852) plus gfx950 Gluon MLA kernels (#46486).
- **vLLM** is ahead on architecture plumbing — GLM-4.5→4.7 MTP head post-final-hidden alignment (#62234), DeepSeek-V4.1 `latency_specialized` decode (#63921) — but is currently *broken* on SM120 for DeepSeek-V4.1 (no valid block size, #59203) and on ROCm for Qwen3.8-Flash-Next-FP8 (#58688).
- Neither has shipped Nemotron 3.5 ASR Streaming or Phi4Flash-V1 — both are open requests in vLLM.

**Tier 2 — Architecture breadth (llama.cpp).** Clear leader in long-tail coverage: Metis-1.6 (b12089), NVFP4 LFM3 (b12088), Nemotron 3.5 Transcribe (b12087), and **xing4_0** in review (#31589) with mHC/MLA/MoE/MTP. This is the only project adding genuinely novel architectures this week.

**Tier 3 — Consumer/multimodal surface (Ollama).** MiMo V2.6 (#19400) and **MLX video input** for LFM3-VL, LFM3-Omni, Qwen3.5/3.6 (#19348) — but the capability is macOS/Linux-MLX-only, with a documented GGUF/Windows gap (#19503). Video parity is currently Ollama's weakest claim.

**Tier 4 — Training-side coverage (Unsloth).** Not model *support* per se, but FastModel coverage is now near-universal for decode CUDA graphs: Gemma 3, Qwen3-VL, Llama 4, Mistral 3, Granite, Phi-4, OLMo 2, Cohere 2, GLM-4, Qwen3-MoE (#16587). This list is effectively a second vote on which architectures matter in production.

**Gateway (LiteLLM).** No

---

## 各项目详细报告

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM Digest — 2026-11-04

## Today's Highlights

No new release landed in the last 24 hours, but the Marlin quantization kernels are being gutted and reshaped across three coordinated PRs (atomic-add deprecation, bf16/sm90 build removal, 8-bit activation removal in favor of Humming). The day's most serious signal is correctness, not performance: a reproducible **cross-request KV cache contamination** in prefill/decode disaggregated serving with prefix caching (#61160), a **silent-garbage output** bug in Confidential Computing mode (#57224), and a release-tag blocker for DeepSeek-V4.1 on Blackwell SM120 (#59203). A cluster of ROCm issues (GLM-5.3-Flash workspace assertions, MI350P MonoKernel CUDA-count assumptions, Qwen3.8 FP8 load failures) continues to accumulate.

---

## Releases & Breaking Changes

No new versions published. Two forward-looking config/support removals are in flight:

- [#65558](https://github.com/vllm-project/vllm/pull/65558) — `VLLM_MARLIN_USE_ATOMIC_ADD` becomes a **no-op** and the bf16 Marlin kernels for `9.0+PTX` stop building. Anyone relying on that env var for SM90 bf16 throughput should re-benchmark.
- [#65542](https://github.com/vllm-project/vllm/pull/65542) — **INT8/FP8 activation support (W4A8) removed from dense and MoE Marlin**; Humming becomes the supported path for 8-bit activations. Marlin is now a 16-bit-activation kernel family.
- Release-tag regression: [#59203](https://github.com/vllm-project/vllm/issues/59203) reports **no working block size** (both 64 and 128 fail) for DeepSeek-V4.1 on the `v0.32.0` tag with SM120 + FlashInfer 0.7.0. Fix tracked in #60762.

---

## New Model & Hardware Support

- [#65557](https://github.com/vllm-project/vllm/pull/65557) — Iluvatar CoreX GPUs added to the supported hardware list (follow-up to #62500).
- [#38175](https://github.com/vllm-project/vllm/issues/38175) — RFC tracker for **full ViT CUDA Graph** capture, targeting Qwen3-VL, Qwen3.5, GLM-V and Kimi K2.5 vision encoders (49 comments; still open).
- [#47455](https://github.com/vllm-project/vllm/issues/47455) — request for NVIDIA **Nemotron 3.5 ASR Streaming** (`nemotron-3.5-asr-streaming-0.6b`), a 600M multilingual streaming ASR model.
- [#23957](https://github.com/vllm-project/vllm/issues/23957) — `Phi4Flash` still needs a V1 port (custom Mamba layer state management) before V0 removal.
- [#62234](https://github.com/vllm-project/vllm/pull/62234) (closed) — GLM-4.5/4.5-Air/4.6, GLM-4.7-Flash and GLM-OCR MTP heads now return post-final-hidden state, aligning with GLM5Next speculator behavior.
- [#63348](https://github.com/vllm-project/vllm/pull/63348) (closed) — `Qwen/Qwen3-4B` added to the batch-invariance validated model list.
- [#58688](https://github.com/vllm-project/vllm/issues/58688) — **Qwen3.8-Flash-Next-FP8 cannot load on ROCm**: the Qwen4ExpNGramEmbedding path has no FP8 `weight_scale` (MI325X, v0.30.0).

---

## Performance & Optimization

- [#63921](https://github.com/vllm-project/vllm/pull/63921) — DeepSeek-V4.1 `latency_specialized` decode extended on NVIDIA: larger batches stay on low-latency kernel paths, more per-step overlap, split indexers.
- [#62113](https://github.com/vllm-project/vllm/pull/62113) — `TRITON_ATTN` packs int8/fp8 per-token-head KV as `[K|k_scale|V|v_scale]`, producing 520 B (head 256) and 264 B (head 128) slots that are **not 16-byte multiples**, forcing byte-at-a-time loads. PR pads each half to 16 B for vectorized loads (~4 files, +93/−23).
- [#65559](https://github.com/vllm-project/vllm/pull/65559) — ROCm KV offload batches host↔device copies as a single SDMA blit via `hipMemcpyFlagExtPreferCE` (HIP 7.16+), instead of running the copy as a kernel on the CUs.
- [#65561](https://github.com/vllm-project/vllm/pull/65561) — `SimpleCPUOffloadConnector`: requeue a finished request's CPU prefix and free CPU blocks **tail first**, so eviction stops eating the head of a prefix and breaking lookups.
- [#58352](https://github.com/vllm-project/vllm/pull/58352) — ROCm sparse-MLA decode packs ragged indices **directly into graph buffers**, removing the scratch→graph copy per decode step.
- [#62532](https://github.com/vllm-project/vllm/issues/62532) — RFC to eliminate a **1.0–1.8 s re-queueing stall** for completed async KV pulls via front-of-queue re-admission.
- [#64460](https://github.com/vllm-project/vllm/issues/64460) — KV-pool-saturated admission retries inflate decode **TPOT ~2.3×**; proposes automatic admission backoff.
- [#57406](https://github.com/vllm-project/vllm/issues/57406) — GLM-5.3 optimization tracker (same `glm_moe_dsa` base as 5.2).
- [#54477](https://github.com/vllm-project/vllm/issues/54477) — Direct Weight Reload for RL, Rev 2 after validating 17 model families; Rev 1's "skip post-load processing" caused stale MLA absorbed weights. Supporting PRs: [#64717](https://github.com/vllm-project/vllm/pull/64717), [#65261](https://github.com/vllm-project/vllm/pull/65261), [#64352](https://github.com/vllm-project/vllm/pull/64352).

---

## Stability & Regressions

**Critical**

1. [#61160](https://github.com/vllm-project/vllm/issues/61160) — **Cross-request context contamination** ("crosstalk") in NIXL prefill/decode disaggregation with automatic prefix caching serving DeepSeek-V4.1-Flash: responses intermittently contain content from other requests. Reproducer on `/v1/...`; clean with `--no-enable-prefix-caching`. Multi-tenant data-leak severity.
2. [#57224](https://github.com/vllm-project/vllm/issues/57224) — **Silent garbage output** on H100 in Confidential Computing (TDX guest): V2 model runner's UVA views of pinned host memory are stale under CC. Workaround: `VLLM_USE_V2_MODEL_RUNNER=0`.
3. [#59203](https://github.com/vllm-project/vllm/issues/59203) — DeepSeek-V4.1 + SM120 (RTX PRO 6000 Blackwell, TP8) has no usable block size on `v0.32.0`; fix in #60762.
4. [#62851](https://github.com/vllm-project/vllm/issues/62851) — Qwen3.6 GDN greedy DFlash2 divergence from batch-dependent numerics and preemption/recompute (v0.31.0), a batch-invariance/accuracy break.

**High**

5. [#63343](https://github.com/vllm-project/vllm/issues/63343) — `enable_adaptive_verification` rejected on SM120; device-query-lens gated to SM90/SM100 only.
6. [#64874](https://github.com/vllm-project/vllm/issues/64874) — ROCm GLM-5.3-Flash: `"Workspace is locked"` AssertionError in `rocm_fp8_paged_mqa_logits` under high decode concurrency (indexer allocates decode logits with `torch.empty` instead of the workspace).
7. [#64814](https://github.com/vllm-project/vllm/issues/64814) — `MooncakeStoreConnector` on Kimi-K3: one TP rank stalls (GPU idle, peers spinning) under agentic load, B300 + EFA.
8. [#63712](https://github.com/vllm-project/vllm/issues/63712) — Muse Spark AMD MonoKernel decode hard-wired to

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang 摘要 — 2026-11-04

## 1. 今日亮点

过去 24 小时没有新版本发布，但活动主要集中在 **prefill-decode 分离与 HiCache 加固**：Q4 PD 路线图已更新（[#43699](https://github.com/sgl-project/sglang/issues/43699)），同时有多个针对 PP prefill 同步和混合缓存 decode 复用的 PD 相关修复。内核方面，AMD/ROCm 与 NPU 优化 PR 正在与面向 DeepSeek-V4.1 的 SM90 FP4 attention 工作并行推进，而长期跟踪的 CUDA coredump 与 CI 不稳定测试追踪器仍在持续累积（分别有 354 条和 174 条评论）。

## 2. 版本发布与破坏性变更

过去 24 小时没有新版本发布。有两个 API 层面的变更值得关注：

- **RFC：全面迁移至共享的 `api.v1` 后端**（[#45816](https://github.com/sgl-project/sglang/issues/45816)）——将生成能力统一到 Rust 服务端的 `api.v1.Generate` API 之下，子任务分别迁移 `sgl-processor`、`sgl-router` 以及 router 路由决策。对于任何在 OpenAI 兼容 HTTP 层之下做集成的用户来说，这是目前待落地的最大接口变更。
- **跨 API 的 prompt/cache 计量对齐**（[#46091](https://github.com/sgl-project/sglang/pull/46091)）——对齐 Responses、Messages 和 Chat 三条路径上的 prompt token 边界，以及缓存未命中与实测写入的计量方式。对于计费/遥测消费方而言，token 计数语义可能发生变化。
- **依赖替换：`openai-harmony` → `oss-harmony==0.0.12`**（[#48267](https://github.com/sgl-project/sglang/pull/48267)）——支持离线加载 GPT-OSS 服务词表；工具历史渲染现在使用原生 oss-harmony。

## 3. 新模型与硬件支持

- **面向打包 FP4 Main KV 的 SM90 attention（DeepSeek-V4.1）**——[PR #43852](https://github.com/sgl-project/sglang/pull/43852) 为打包 FP4 KV 布局新增 Hopper attention 支持，属于 dsv4.1 NVIDIA 路径的第 2/N 部分。
- **Intel XPU paged-experts RFC**——[Issue #46520](https://github.com/sgl-project/sglang/issues/46520) 提出 Lookahead Expert Scheduling（LES）以及用于 iGPU 单卡 MoE offload 的 SSD 层级，延续了 #42557 中的 Paged Experts 工作。
- **NPU 多模态 `mm_feature_transport=npu_ipc`**——[PR #46208](https://github.com/sgl-project/sglang/pull/46208) 在 Ascend 上为多模态特征新增 NPU-IPC 传输。
- **VLA 模型（Pi0.5）的 CPU RoPE 修复**——[PR #40139](https://github.com/sgl-project/sglang/pull/40139) 将 `apply_rotary_pos_emb_cpu` 扩展到支持带 `unsqueeze_dim` 的 4D 输入，修复了 Pi0.5 的 CPU 服务。
- **通用推测解码 provider 边界**——[PR #47201](https://github.com/sgl-project/sglang/pull/47201) 引入与模型无关的策略接口，使可选的 SD provider 可以接入，而无需 fork 面向上层图调用的调用点。

## 4. 性能与优化

- **SM120 prefill 吞吐差距调查中**——[Issue #33422](https://github.com/sgl-project/sglang/issues/33422)：4× RTX PRO 6000（TP=4，PCIe）上的 DeepSeek-V4-Flash 报告 prefill 约 **2–7K tok/s，而同硬件上 vLLM/marlin 约为 12.5K**；根本原因尚未确定，怀疑是 SM120 上的内核覆盖缺口。
- **Hopper 上的 GLM-5.3-Flash 跟踪**——[Issue #45373](https://github.com/sgl-project/sglang/issues/45373) 在 Q4 路线图（#42473）下跟踪 SM90 性能工作。
- **gfx950/MI355X 上的 Qwen3.8-Flash-Next**——[Issue #44304](https://github.com/sgl-project/sglang/issues/44304) 涵盖 ROCm 上的 GatedDeltaNet + QSA + MXFP4 MoE + NEXTN MTP 优化。
- **gfx950 上将 GLM-5 decode MLA 实现为 Gluon 内核**——[PR #46486](https://github.com/sgl-project/sglang/pull/46486) 将每层的三个内核（q-absorb+RoPE+FP8 KV 写入、split-K sparse MLA、FP8 value-attend）合并为一条 Gluon 链。
- **AMD：在 MTP verify 中跳过全词表排序**——[PR #48090](https://github.com/sgl-project/sglang/pull/48090) 在 `--speculative-eagle-topk 1` 下移除了对 `bs × num_draft_tokens` 行的稠密 softmax + `top_k_renorm_prob` + `top_p_renorm_prob` 路径。
- **AMD：融合 prefill QK-norm + RoPE**——[PR #47052](https://github.com/sgl-project/sglang/pull/47052) 用单个 JIT `qknorm_rope` 内核替代每层五次启动；现有的融合路径仅支持 CUDA。
- **避免推测解码中 MRoPE delta 的阻塞式 H2D**——[PR #46781](https://github.com/sgl-project/sglang/pull/46781) 移除了多模态推测请求每步中可页宿主内存 `torch.stack` → `.to(device)` 的停顿。
- **在非发送 rank 上跳过 routed-expert 捕获**——[PR #47553](https://github.com/sgl-project/sglang/pull/47553) 在 DeepEP 关闭时，消除了那些从不读取 id 的 TP rank 上每个 MoE 层的 id 拷贝。
- **MambaPool COW OOM 峰值修复**——[PR #47984](https://github.com/sgl-project/sglang/pull/47984) 对 `temporal[:, dst] = temporal[:, src]` 进行分块，以避免 radix COW 期间产生 `n × slot_bytes` 的临时内存。
- **NPU 词表并行 embedding 融合**——[PR #48050](https://github.com/sgl-project/sglang/pull/48050) 将 `get_masked_input_and_mask` 中的 13 次逐元素 ATen 调用 / 16 个内核替换为一个融合内核。
- **延迟/吞吐里程碑：** 面向 DeepSeek-V4 的 HiSparse 配合 MTP + DSpark（[PR #44684](https://github.com/sgl-project/sglang/pull/44684)）报告了在 DeepSeek-V4-Flash-FP8 上使用 6.6–6.8K token prompt 的 GSM8K 验证结果。

## 5. 稳定性与回归

按严重程度排序：

1. **推测采样正确性——拒绝有效 draft**（[#42568](https://github.com/sgl-project/sglang/issues/42568)）：自 #37134 起，`chain_speculative_sampling_triton` 将 `q > 1.0`（由舍入导致）视为无效，从而丢弃每一个此类 draft。造成静默的质量/接受率下降，尚无修复 PR。
2. **ROCm `--dcp-size > 1` 在首次前向时使所有 rank 崩溃**（[#38709](https://github.com/sgl-project/sglang/issues/38709)）：`forward_absorb_rocm_core` 从仅返回 1 个值的返回值中解包 2 个值；在 8× MI300X 上使用 GLM-5.3 FP8 复现，main 分支上仍然存在。
3. **EAGLE/MTP 同 checkpoint draft 使 KV 池估算膨胀 → 启动 OOM**（[#42510](https://github.com/sgl-project/sglang/issues/42510)）：在池估算期间，冗余的 `embed_tokens`/`lm_head` 副本始终驻留内存。
4. **Mooncake HiCache 备份对 Qwen3.8-Flash-Next-FP8 失败**（[#48145](https://github.com/sgl-project/sglang/issues/48145)）：生成成功，但备份在 Mamba 零拷贝 slot 侧状态上抛出 `NotImplementedError`。**已有修复 PR**：[#48293](https://github.com/sgl-project/sglang/pull/48293)。
5. **ROCm 上 DeepSeek-V4.1 FP4 indexer 在长上下文 AgentX 下仍耗尽 HBM**，尽管已有 #37660（[#39441](https://github.com/sgl-project/sglang/issues/39441)）。
6. **PD + PP 的 KV 发送与 prefill 前向存在竞态**（[PR #45524](https://github.com/sgl-project/sglang/pull/45524)）：`process_prefill_chunk` 在 `forward_stream` 上前向尚未确保完成时，就发送了非最终 chunk 的 KV。修复正在评审中。
7. **CI：`5090-sm*` runner 上 Wan VAE 解码慢 2–10 倍**（[#48196](https://github.com/sgl-project/sglang/issues/48196)），自 2026-10-17 runner 迁移以来一直存在；`multimodal-gen-test-1-5090` 约有一半尝试失败。
8. **后台追踪器**：CUDA coredump 收集（[#26340](https://github.com/sgl-project/sglang/issues/26340)，354 条评论）和不稳定/CI 基础设施故障（[#42752](https://github.com/sgl-project/sglang/issues/42752)，174 条评论）仍然开放，是构建健康状况的主要信号。
9. **陈旧 issue 清理**：今天关闭了一大批 `[inactive]` bug，包括 PD 下的 DFLASH（[#36140](https://github.com/sgl-project/sglang/issues/36140)）、HiCache prefetch all_reduce 死锁（[#30760](https://github.com/sgl-project/sglang/issues/30760)）、radix `evictable_size()` 重复计数（[#35270](https://github.com/sgl-project/sglang/issues/35270)）、畸形多模态引擎崩溃（[#38022](https://github.com/sgl-project/sglang/issues/38022)）以及 EAGLE `vectorized_gather_kernel` 越界（[#37936](https://github.com/sgl-project/sglang/issues/37936)）。除非有链接的 PR 已落地，否则应将它们视为**延期处理，而非已修复**。

## 6. 对应用开发者的意义

- **PD 分离已成为默认部署形态**，也是问题最集中之处：如果你在 prefill 侧运行 PP，请关注 [PR #45524](https://github.com/sgl-project/sglang/pull/45524)；如果你使用 DFLASH + PD，它仍不受支持（issue 以 inactive 关闭，并非已修复）。
- **使用混合/Mamba 模型的 HiCache + Mooncake 用户**应跟踪 [#48293](https://github.com/sgl-project/sglang/pull/48293)——备份失败对生成而言是非致命的，因此你可能在请求路径上没有任何报错的情况下，静默失去缓存分层。请显式监控备份错误。
- **在 [#42568](https://github.com/sgl-project/sglang/issues/42568) 解决之前，Triton 采样路径上的推测解码接受率可能被低估**；在断定调优回归之前，请先关闭拒绝采样进行基准测试。
- **Token 计量变更即将到来**（[#46091](https://github.com/sgl-project/sglang/pull/46091)）——如果你基于 `count_tokens` 计费或做缓存，请锁定当前的边界语义，并在合并后重新验证。
- **离线/隔离环境下的 GPT-OSS 服务**将因 [PR #48267](https://github.com/sgl-project/sglang/pull/48267) 而变得可行；此前的 `openai-harmony` 固定版本需要网络访问才能加载词表。
- **Router 级会话亲和性**（[#34513](https://github.com/sgl-project/sglang/issues/34513)）以 inactive 关闭，因此对话前缀不断增长的 agent 工作负载仍需要外部粘性路由，才能受益于 RadixAttention。

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp 摘要 — 2026-11-04

## 1. 今日亮点
llama.cpp 连续发布了一系列构建版本（b12081–b12094），聚焦于投机解码、模型覆盖与后端 kernel 效率。其中最具影响的工作围绕 MTP/投机解码展开：隐藏状态保存/恢复已落地，LFM3 现在会记录层输入以支持投机解码，但多个未关闭的问题报告显示 draft-MTP 在量化目标与 CUDA 上存在正确性偏差与崩溃问题。模型支持扩展至 Metis-1.6、NVFP4 LFM3 与 Nemotron 3.5 Transcribe，同时一个大型 xing4_0 架构 PR 正在评审中。

## 2. 发布与破坏性变更
过去 24 小时内未发现明确的 API 或配置破坏性变更。值得关注的构建版本：

- **b12094** — Hexagon：通过 tile skipping + block scan 实现更快的 `TOP_K` 后端采样器（[#31279](https://github.com/ggml-org/llama.cpp/pull/31279)）。
- **b12091** — 投机解码：保存/恢复 MTP 隐藏状态；将 checkpoint 状态简化为边界隐藏行（[#30141](https://github.com/ggml-org/llama.cpp/pull/30141)）。
- **b12090** — ggml-zdnn：修复 FATTN 测试失败（[#31583](https://github.com/ggml-org/llama.cpp/pull/31583)）。
- **b12089** — 新模型：支持 Metis-1.6（[#31134](https://github.com/ggml-org/llama.cpp/pull/31134)）。
- **b12088** — 启用 NVFP4 LFM3 GGUF 文件（[#31608](https://github.com/ggml-org/llama.cpp/pull/31608)）。
- **b12087** — mtmd：支持 Nemotron 3.5 Transcribe，并修复 Nemotron-H draft 模型的词表，以及仅 BF16 的 Transcribe checkpoint 处理（[#31471](https://github.com/ggml-org/llama.cpp/pull/31471)）。
- **b12086** — qwen4exp：写入 conv-state 尾部时不再做中间连续拷贝（[#31443](https://github.com/ggml-org/llama.cpp/pull/31443)）。
- **b12084** — LFM3：为投机解码记录层输入（[#31598](https://github.com/ggml-org/llama.cpp/pull/31598)）。
- **b12083** — ggml：修复 `ggml_roll` 边界检查中的有符号溢出 / UB（[#30375](https://github.com/ggml-org/llama.cpp/pull/30375)）。
- **b12081** — CUDA：Q8_1 校正求和的潜在修复，包括 `quantize_mmq_q8_1` 流程（[#31247](https://github.com/ggml-org/llama.cpp/pull/31247)）。

发布产物：https://github.com/ggml-org/llama.cpp/releases

## 3. 新模型与硬件支持

- **Metis-1.6** — 模型架构支持已在 b12089 落地（[#31134](https://github.com/ggml-org/llama.cpp/pull/31134)）。
- **LFM3 NVFP4** — 启用了 NVFP4 量化的 LFM3 GGUF 文件（[#31608](https://github.com/ggml-org/llama.cpp/pull/31608)）；LFM3 同时新增了面向投机解码的层输入记录（[#31598](https://github.com/ggml-org/llama.cpp/pull/31598)）。
- **Nemotron 3.5 Transcribe** — 新增多模态/音频支持，并对非 BF16 checkpoint 采用更严格的转换处理（[#31471](https://github.com/ggml-org/llama.cpp/pull/31471)）。
- **xing4_0（评审中）** — 完整架构支持，包括 mHC/MLA/MoE/MTP、HF→GGUF 转换与推理图；复用现有的 ggml 算子（[#31589](https://github.com/ggml-org/llama.cpp/pull/31589)）。
- **Hexagon LIGHTNING_INDEXER（评审中）** — 将该算子加入 Hexagon 后端，支持 Q4_0/Q8_0/F16/F32；其他量化暂缓（[#31289](https://github.com/ggml-org/llama.cpp/pull/31289)）。
- **OpenCL/Adreno** — 改进 `q2_0` GEMV，每线程产出 4 或 8 行，并新增 f32f32 MV 变体（[#31577](https://github.com/ggml-org/llama.cpp/pull/31577)）。
- **WebGPU** — 新增批处理张量 API（`get_tensor_n`/`set_tensor_n`）（[#31473](https://github.com/ggml-org/llama.cpp/pull/31473)）；`get_max_size` 现在返回 `maxBufferSize`，并收窄了 memset/clear 绑定（[#31348](https://github.com/ggml-org/llama.cpp/pull/31348)）。
- **Vulkan** — Intel FA prefill kernel 正在评审中，统一了 prefill/decode 的 push constant（[#29357](https://github.com/ggml-org/llama.cpp/pull/29357)）。
- **待处理请求** — Zamba2-2.7B 支持请求仍未关闭，获 20 👍（[#8795](https://github.com/ggml-org/llama.cpp/issues/8795)）。

## 4. 性能与优化

- **Hexagon 采样器** — 新的 `TOP_K` 路径对最大值无法达到当前第 k 佳值的 tile 跳过双调排序，其余部分使用 `top_k_scan()`（[#31279](https://github.com/ggml-org/llama.cpp/pull/31279)）。
- **Qwen4exp 循环状态** — 去掉 conv 输入尾部的中间连续拷贝，使每个生成 token 的两次设备拷贝降为每个 GDN 层一次（[#31443](https://github.com/ggml-org/llama.cpp/pull/31443)）。
- **SYCL 张量并行** — 在双 Arc Pro B70 上使用 `-sm tensor`，快速的 `ggml_sycl_comm_exchange()` 报告 **prompt-eval t/s 提升 +16.7% 至 +20.6%（均值 ≈ +19%）**，与 `-sm layer` 持平且 PPL 无变化；PR 已关闭/合并（[#30956](https://github.com/ggml-org/llama.cpp/pull/30956)）。
- **CUDA GDN 排序** — 后续 PR 为 4 列 GDN 实现新增 `GGML_CUDA_GDN_ORDERED`，此前上游 #30087 导致 MTP 解码性能回退（[#31634](https://github.com/ggml-org/llama.cpp/pull/31634)；先前的 [#31630](https://github.com/ggml-org/llama.cpp/pull/31630) 已关闭）。
- **主机内存** — 重构了调度器上下文尺寸计算与图元数据分配，实现惰性分配/减少过度分配，报告称可大幅节省主机内存（[#31274](https://github.com/ggml-org/llama.cpp/pull/31274)）。
- **HIP fast-math** — PR 在 HIP 上启用 fast-math 且无回退，目标是了结 ROCm fast-math 这一议题（[#31625](https://github.com/ggml-org/llama.cpp/pull/31625)）。
- **RTX 5090 上的 CUDA FA 回退** — 在 170-SM 的 RTX 5090 上，stream-k 的 block 数不是 `ntiles_dst` 的整数倍，导致**长上下文 prefill 相比 RTX 4090 慢 10–13%**，修复后报告**提升 +37–40%**（[#30784](https://github.com/ggml-org/llama.cpp/issues/30784)）。

## 5. 稳定性与回退

按严重程度排序：

1. **投机解码正确性偏差** — 在贪心采样（`temperature=0`、`top_k=1`）下，当目标模型为量化版本（如 Q4_K_M）时，draft-MTP/draft-dspark 投机解码可能产生与常规解码不同的输出；BF16 目标则一致。对依赖确定性或可复现贪心输出的用户影响很大。未关闭，37 条评论（[#25618](https://github.com/ggml-org/llama.cpp/issues/25618)）。
2. **draft-MTP 触发 CUDA 非法内存访问** — Gemma 4 31B + draft

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama Digest — 2026-11-04

## 1. Today's Highlights

Ollama shipped **v0.40.15-rc0**, headlined by **MiMo V2.6 model support** and a fix that loads bundled CUDA libraries in-process for MLX on Linux. The MLX backend is seeing the heaviest development activity — video input, concurrent generation, AutoTrust decision models, and oversized-request handling — while the issue tracker shows a cluster of correctness regressions around truncation, tool calls, and auth error handling, several of which already have fix PRs. A long-standing **scheduler deadlock** in the eviction path remains open and is the most severe item in the queue.

## 2. Releases & Breaking Changes

**v0.40.15-rc0** ([release](https://github.com/ollama/ollama/releases))
- [PR #19433](https://github.com/ollama/ollama/pull/19433) — `mlx: load bundled CUDA libraries in-process on Linux`. Removes the need for external CUDA library resolution for MLX workloads on Linux.
- [PR #19400](https://github.com/ollama/ollama/pull/19400) — `model: add MiMo V2.6 model support`.

No explicit API breaking changes in this release. However, two behavioral bugs are worth flagging before upgrading: the Responses API currently **ignores `truncation: disabled`** ([#19492](https://github.com/ollama/ollama/issues/19492)), and tool calls cut off by `num_predict`/`max_tokens` return HTTP 500 instead of a partial result ([#19493](https://github.com/ollama/ollama/issues/19493)). Both have open fix PRs.

## 3. New Model & Hardware Support

- **MiMo V2.6** — candidate MLX and GGUF builds available via `ollama pull dhiltgen/mimo-v2.6` ([PR #19400](https://github.com/ollama/ollama/pull/19400), merged into the RC).
- **AutoTrust decision models on MLX** — text and vision support through creation, prompt encoding, and API routing; test models at `dhiltgen/autotrust-jev` ([PR #19399](https://github.com/ollama/ollama/pull/19399)).
- **MLX video input** — LFM3-VL, LFM3-Omni, and Qwen3.5/3.6 MLX models accept MP4/MOV in the existing `images` field; models expose a new `"video"` capability ([PR #19348](https://github.com/ollama/ollama/pull/19348)). Note the gap on GGUF/Linux/Windows tracked in [#19503](https://github.com/ollama/ollama/issues/19503).
- **Kimi Code CLI support** — `ollama launch kimi` now drives the new `~/.kimi-code/config.toml` flow in addition to legacy `kimi-cli` ([PR #19506](https://github.com/ollama/ollama/pull/19506)).
- **New tokenization APIs** — `/api/tokenize` and `/api/detokenize` convert text ↔ token IDs without loading the model, with `add_special_tokens` matching `prompt_eval_count` ([PR #19498](https://github.com/ollama/ollama/pull/19498)); input-token counting endpoints for Anthropic- and OpenAI-compatible routes ([PR #17478](https://github.com/ollama/ollama/pull/17478)).
- **BSD build fix** — `Bavail` cast to `uint64` for FreeBSD/BSD-derived platforms ([PR #19502](https://github.com/ollama/ollama/pull/19502)).

## 4. Performance & Optimization

- **MLX concurrent generation** — `OLLAMA_NUM_PARALLEL` now applies to MLX models, with requests sharing projection and feed-forward compute while attention and sampling stay per-request. Default remains 1 ([PR #19197](https://github.com/ollama/ollama/pull/19197)).
- **Pull-time hashing** — GGUF blob parts are hashed from page cache during parallel Range downloads, eliminating the redundant post-download `verifyBlob` re-read pass ([PR #19505](https://github.com/ollama/ollama/pull/19505) / [#19504](https://github.com/ollama/ollama/issues/19504)).
- **MLX request bounding** — long System One decision-model requests are bounded before runner load, returning 413 instead of thrashing ([PR #19408](https://github.com/ollama/ollama/pull/19408)).
- **Open regressions to watch:** model loading slowdown since 0.23.4 with GPT-OSS:120b ([#18373](https://github.com/ollama/ollama/issues/18373)) and VRAM under-estimation with large context windows ([#10128](https://github.com/ollama/ollama/issues/10128)).
- **Memory pressure:** MLX prefix cache's hard-coded 8 GiB budget pushes 32 GB Apple Silicon into heavy swap during agent workloads — characterized as expected cache growth, not a leak ([#18131](https://github.com/ollama/ollama/issues/18131)).

## 5. Stability & Regressions

Ranked by severity:

1. **Scheduler deadlock (critical, no fix yet)** — when a load takes the eviction path and the eviction target is resurrected by a concurrent request, the pending loop blocks forever; all subsequent loads hang. Reproduced on 0.31.1, logic unchanged on main ([#17408](https://github.com/ollama/ollama/issues/17408)).
2. **Tool calls truncated by token limit return HTTP 500** — cut-off JSON arguments fail the whole request instead of being dropped ([#19493](https://github.com/ollama/ollama/issues/19493)); fix in [PR #19494](https://github.com/ollama/ollama/pull/19494) drops invalid calls only when `finish_reason: "length"`.
3. **Responses API silently truncates input with truncation disabled** — `Truncation` was never mapped, so the handler defaulted `truncate: true` (0.40.13/0.40.14, Bonsai 27B) ([#19492](https://github.com/ollama/ollama/issues/19492)); fix in [PR #19501](https://github.com/ollama/ollama/pull/19501).
4. **Streaming 401 loses `AuthorizationError`/`SigninURL`** when the body isn't valid JSON ([#19489](https://github.com/ollama/ollama/issues/19489)); fix classifies all streaming 401s as auth errors ([PR #19491](https://github.com/ollama/ollama/pull/19491)).
5. **Vision inference HTTP 500 on NVIDIA GPU** — `qwen2.5vl:3b` aborts with "token repeat limit reached" only when the vision encoder runs on GPU; CPU path works ([#19483](https://github.com/ollama/ollama/issues/19483)).
6. **Windows `cmake -G Ninja` produces a broken nested llama-server build** — Visual Studio generator used internally, CUDA install fails, CPU payload lacks `llama-server.exe` ([#19495](https://github.com/ollama/ollama/issues/19495)); fix in [PR #19496](https://github.com/ollama/ollama/pull/19496).
7. **`HumanNumber` rounding past unit boundaries** — `999500` renders as `"1000K"` instead of `"1M"` ([#19490](https://github.com/ollama/ollama/issues/19490)); two candidate fixes ([PR #19499](https://github.com/ollama/ollama/pull/19499), [PR #19500](https://github.com/ollama/ollama/pull/19500)).
8. **Resolved:** Pascal CUDA breakage in 0.40.13 ([#19485](https://github.com/ollama/ollama/issues/19485), closed); Qwen3.5:9b ROCm `hipErrorInvalidDeviceFunction` on RX 9060 XT ([#15343](https://github.com/ollama/ollama/issues/15343), closed).

## 6. What This Means for Application Developers

- **Guard your tool-calling loops.** Until [PR #19494](https://github.com/ollama/ollama/pull/19494) lands, a `max_tokens` cut mid-tool-call yields a 500 rather than a partial response. Set generous `num_predict` budgets, or retry on that specific error and treat it as a truncation signal.
- **Don't rely on `truncation: disabled` via the Responses API** on 0.40.13/0.40.14 — oversized prompts may be silently shortened. Validate prompt lengths client-side or pin to a build containing [PR #19501](https://github.com/ollama/ollama/pull/19501).
- **Token budget planning gets easier.** The new `/api/tokenize`, `count_tokens`, and `input_tokens` routes let you compute prompt costs without loading a model — useful for routing and pre-flight validation in gateways.
- **MLX on Apple Silicon is maturing but memory-hungry.** Concurrent generation ([#19197](https://github.com/ollama/ollama/pull/19197)) improves first-token latency, but the fixed 8 GiB prefix cache can push 32 GB machines into swap under agent workloads ([#18131](https://github.com/ollama/ollama/issues/18131)) — budget headroom or serialize long agent sessions.
- **Multi-modal parity is uneven.** Video works through MLX on macOS/Linux containers but not for GGUF models or native Linux/Windows llama-server paths ([#19503](https://github.com/ollama/ollama/issues/19503)). Design ingestion pipelines with a format/backend fallback.
- **If you run at scale, track #17408.** The eviction-path deadlock can wedge a shared inference host under concurrent load; health-check model loads and be prepared to restart the server until a fix lands.

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM Digest — 2026-11-04

## 1. Today's Highlights

LiteLLM published release candidate **v1.109.0-rc.1** with cosign-signed Docker images, while the bulk of merged/in-review activity centers on a five-PR stack adding **provider-native MCP server registration and per-key/team tool permissions** ([#49891](https://github.com/BerriAI/litellm/pull/49891), [#49894](https://github.com/BerriAI/litellm/pull/49894), [#49897](https://github.com/BerriAI/litellm/pull/49897), [#49900](https://github.com/BerriAI/litellm/pull/49900)). On the reliability side, several high-impact proxy issues surfaced around Redis failover handling, TPM accounting under upstream 429s, and mid-stream fallback classification for reasoning models. A fix PR is already open for incorrect OpenAI 403 exception mapping ([#49936](https://github.com/BerriAI/litellm/issues/49936) → [#49997](https://github.com/BerriAI/litellm/pull/49997)).

## 2. Releases & Breaking Changes

- **v1.109.0-rc.1** — Release candidate. All Docker images are signed with cosign using the key introduced in commit [`e82a3c6`](https://github.com/BerriAI/litellm/commit/e82a3c646908ba0a6ee0beddc4310d7a6cac4065); verification instructions are included in the release notes. No explicit breaking changes or migration steps are documented in the supplied data. → [Release](https://github.com/BerriAI/litellm/releases/tag/v1.109.0-rc.1)
- **Watch item — pass-through response ID hardening**: PR [#46424](https://github.com/BerriAI/litellm/pull/46424) (`fix(proxy)!`) would refuse `resp_*` IDs never issued by the managed OpenAI/Azure pass-through, closing a cross-key response-continuation exposure. Marked breaking with `!`; review before adopting. → [PR #46424](https://github.com/BerriAI/litellm/pull/46424)
- **Config surface — callback settings by key reference**: PR [#49995](https://github.com/BerriAI/litellm/pull/49995) lets a virtual key reference a named `callback_settings` entry in `config.yaml`, with credentials resolved from `os.environ/` secret-manager references per request. → [PR #49995](https://github.com/BerriAI/litellm/pull/49995)

## 3. New Model & Hardware Support

- **Provider-native MCP registrations** — Admins can register remote MCP servers and explicit tool lists for *provider-executed* tools without LiteLLM connecting to those servers; registrations are kept separate from gateway execution mode. → [PR #49891](https://github.com/BerriAI/litellm/pull/49891), [PR #49886](https://github.com/BerriAI/litellm/pull/49886)
- **Custom-provider model discovery** — Open feature request for wildcard-based discovery (`model_name: my-custom-service/*`) in `model_list`. Not landed. → [Issue #20064](https://github.com/BerriAI/litellm/issues/20064)
- **Cloudflare Workers AI image/audio** — Image generation and speech-to-text/TTS support requests were closed as stale/duplicate; do not assume availability. → [Issue #35055](https://github.com/BerriAI/litellm/issues/35055), [Issue #35056](https://github.com/BerriAI/litellm/issues/35056)
- **Bedrock non-Anthropic models** — `zai.glm-5.3` appears in an active Bedrock Converse routing bug, indicating support is in use but currently mishandles replayed Anthropic thinking signatures. → [Issue #49969](https://github.com/BerriAI/litellm/issues/49969)
- **OCI Gemini translation** — Issue where only the last `system` message reached `oci/google.gemini-*` models was closed. → [Issue #48813](https://github.com/BerriAI/litellm/issues/48813)

No new CUDA/ROCm/Metal kernels or quantization formats were reported in this window.

## 4. Performance & Optimization

- **Spend accounting refactor** — PR [#49843](https://github.com/BerriAI/litellm/pull/49843) removes the legacy `UsageMeter`/`CostCompiler` scaffolding and replaces it with a `SpendLogs`-shaped projection deriving rollup table updates. Primarily an architectural/perf cleanup for accounting writes. → [PR #49843](https://github.com/BerriAI/litellm/pull/49843)
- **Spend log read transaction** — Test adjustment for session pages indicates spend log reads now run on a read transaction with a statement timeout; query-count expectations were doubled. → [PR #49996](https://github.com/BerriAI/litellm/pull/49996)
- **Streaming TTS** — PR [#46222](https://github.com/BerriAI/litellm/pull/46222) streams `/v1/audio/speech` audio as the provider emits it; the description cites a long ElevenLabs clip starting playback after ~0.65s instead of waiting for the full clip. → [PR #46222](https://github.com/BerriAI/litellm/pull/46222)
- **No benchmark numbers** (tokens/sec, TTFT, memory) were included in today's dataset.

## 5. Stability & Regressions

Ranked by operational severity:

1. **Redis circuit breaker stuck HALF_OPEN after failover** — After a managed Redis/Valkey primary failover, pods remain permanently HALF_OPEN when the probe fails with a non-connectivity error such as `READONLY`; all guarded Redis calls fast-fail while traffic continues. High severity for HA deployments. No fix PR referenced. → [Issue #49884](https://github.com/BerriAI/litellm/issues/49884)
2. **TPM counter outlives its window under upstream 429s** — With v3 limiter and `LITELLM_TPM_TOKEN_RESERVATION_ENABLED=true`, keys/teams with both RPM and TPM limits get 429s well below `tpm_limit`. High severity for shared multi-tenant gateways. → [Issue #49675](https://github.com/BerriAI/litellm/issues/49675)
3. **Proxy startup crash with `TZ=Asia/Calcutta`** — On stock Ubuntu 24.04/26.04, `tzlocal` cannot resolve the legacy alias because `tzdata-legacy` is not installed; process exits at startup. High severity for affected images/hosts. → [Issue #49813](https://github.com/BerriAI/litellm/issues/49813)
4. **Lost streaming fallback on reasoning-only prefix** — `sent_first_chunk` flips on the first reasoning delta, so a mid-stream failure before any content is reported as a post-first-chunk failure and fallback is suppressed. High severity for high-effort reasoning models. → [Issue #49795](https://github.com/BerriAI/litellm/issues/49795)
5. **OpenAI 403 mapped to wrong exception type** — Returns generic `APIError`/`BadRequestError` instead of `PermissionDeniedError`, breaking error-handling contracts. Fix PR open. → [Issue #49936](https://github.com/BerriAI/litellm/issues/49936), [PR #49997](https://github.com/BerriAI/litellm/pull/49997)
6. **`responses()` ignores `drop_params` for o-series temperature** — `temperature` is still forwarded to `openai/o3-mini`, causing upstream rejection; `completion()` behaves correctly. Medium severity. → [Issue #49927](https://github.com/BerriAI/litellm/issues/49927)
7. **Bedrock Converse replays `reasoningText.signature` to non-Anthropic models** — Auto-router transitions from Claude Haiku 5.5 to `zai.glm-5.3` keep the thinking signature and trigger `Bad Request`. Medium severity for mixed-model routing. → [Issue #49969](https://github.com/BerriAI/litellm/issues/49969)
8. **`langfuse_otel` missing `/v1/traces` suffix** — Self-hosted Langfuse receives 404 and silently drops spans. Medium severity for observability. → [Issue #43032](https://github.com/BerriAI/litellm/issues/43032)
9. **Weekly budget reset leaves fractional negative spend** — Post-reset `spend` can be e.g. `-2.7e-17` until the next write; cosmetic but affects strict accounting checks. Low severity. → [Issue #49842](https://github.com/BerriAI/litellm/issues/49842)
10. **Azure-style routes return provider model name** — Non-streaming `/engines/{model}/chat/completions` and `/openai/deployments/{model}/chat/completions` return the upstream model rather than the requested alias. Low severity, client-compat issue. → [Issue #49932](https://github.com/BerriAI/litellm/issues/49932)
11. **Mistral drops named `tool_choice`** — Object-form `tool_choice` is lost, so the pinned tool is never invoked. Medium severity for agent tooling. → [Issue #39736](https://github.com/BerriAI/litellm/issues/39736)
12. **`vllm` `/ui/chat` response ID lookup failure** — Closed; playground worked while `/ui/chat` failed from the second turn. → [Issue #26147](https://github.com/BerriAI/litellm/issues/26147)
13. **FIPS-mode UI password hashing** — With FIPS OpenSSL, scrypt is refused and password login fails; PR [#49874](https://github.com/BerriAI/litellm/pull/49874) switches to PBKDF2 when `general_settings.fips_mode` is enabled. → [PR #49874](https://github.com/BerriAI/litellm/pull/49874)

## 6. What This Means for Application Developers

- **MCP permissions are becoming first-class.** The provider-native MCP stack will let platform teams grant tool access per key, team, alias, or toolset without LiteLLM proxying the server. If you build agents on LiteLLM, plan for scoped tool inventories rather than broad gateway-level MCP access. → [PR #49894](https://github.com/BerriAI/litellm/pull/49894)
- **Don't rely on streaming fallbacks with reasoning models yet.** Until [#49795](https://github.com/BerriAI/litellm/issues/49795) is fixed, implement your own retry/idempotency layer for high-effort reasoning requests, since a mid-stream provider failure may surface as non-retryable.
- **Error handling should not assume upstream parity.** OpenAI-compatible 403s may arrive as `APIError`; branch on `status_code` rather than exception class until [#49997](https://github.com/BerriAI/litellm/pull/49997) lands.
- **Pin images and verify signatures.** The RC ships cosign-signed images; for production, gate on `v1.109.0` final or verify the RC signature before rollout.
- **Audit Redis HA and TPM configuration.** If you run Redis with automated failover or enforce both RPM and TPM per key/team, validate behavior against [#49884](https://github.com/BerriAI/litellm/issues/49884) and [#49675](https://github.com/BerriAI/litellm/issues/49675) before upgrading.
- **Set `TZ` to a canonical zone in containers.** Avoid legacy aliases like `Asia/Calcutta` on minimal Ubuntu images or install `tzdata-legacy` to prevent startup failure ([#49813](https://github.com/BerriAI/litellm/issues/49813)).

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth 文摘 — 2026-11-04

## 今日亮点

Unsloth 发布了 **v0.1.914-beta**（“Glass 主题、个性化与更快的生成”），新增界面主题功能，并带来更快的生成速度、扩展的多 GPU 与 CPU 训练路径。在引擎侧，最重要的技术项是 PR #16587，它将解码 CUDA 图扩展到几乎所有 FastModel 架构，据报告在 T4 上可实现 **3–7 倍加速**，且输出与 eager 模式逐位一致。Studio 的稳定性问题占据了今日 issue 跟踪器的大部分内容，包括一组更新器/AppImage 启动失败问题，以及若干正在推进的工具调用延迟与 RAG 索引性能补丁。

---

## 发布与破坏性变更

- **v0.1.914-beta** — Glass 主题、可自定义背景/问候语/聊天、更快的生成、额外的多 GPU 训练选项，以及 CPU 训练支持。[发布说明](https://github.com/unslothai/unsloth/releases)
- **此版本的更新器故障** — 多方报告称，更新*至* v0.1.914-beta 在 Windows 11（[#16573](https://github.com/unslothai/unsloth/issues/16573)）和 macOS/Apple MLX（[#16561](https://github.com/unslothai/unsloth/issues/16561)）上无法完成。尚无修复 PR 合并；受影响的用户应全新安装，而非就地更新。
- **PyPI extras 滞后** — `cu130-2130` / `cu130-2140` torch extras 已存在于 `pyproject.toml` 中，但未出现在已发布的 2026.10.11 wheel 中（[#16520](https://github.com/unslothai/unsloth/issues/16520)）。从 PyPI 固定 torch 2.13/2.14 无法通过 Unsloth extras 解析。
- **新的 CLI/打包面：** CPU 训练现已成为一等路径，这改变了此前假定仅使用 GPU 的用户的默认设备放置行为。

---

## 新模型与硬件支持

- **为所有 FastModel 架构启用解码 CUDA 图** — Gemma 3、Qwen3-VL（文本）、Llama 4（文本）、Mistral 3、Granite、Phi-4、OLMo 2、Cohere 2、GLM-4 以及 batch 1 下的 Qwen3-MoE 现在均可重放图；此前仅 Llama attention 和 Qwen3 支持。[PR #16587](https://github.com/unslothai/unsloth/pull/16587)
- **CPU 训练** — 根据 v0.1.914-beta 的说明，Unsloth 包已新支持该功能。
- **扩展的多 GPU 训练选项** — 在桌面/CLI 训练路径中暴露了更多并行配置。
- **OpenRouter 模型目录现由 API 驱动** — 取代了硬编码列表，其中 18 个条目中有 8 个已在上游不复存在（`anthropic/claude-sonnet-4-5`、`deepseek/deepseek-r1`、`mistralai/mistral-large-2411`），并为 OpenRouter 启用了“重新加载模型”。[PR #16562](https://github.com/unslothai/unsloth/pull/16562)
- **ROCm 测试覆盖加强** — 扩散测试现在固定 NVIDIA 主机伪装，并断言 ROCm 在 gfx1151 / Strix Halo 上保持原生（非优化）路径。[PR #16609](https://github.com/unslothai/unsloth/pull/16609)
- **未解决的缺口：** Vulkan 后端在 Windows 10 22H2 下的 RX 5700 XT 上仍然损坏（[#16395](https://github.com/unslothai/unsloth/issues/16395)）；DGX Spark 用户在询问已发布的博客指南对于 Studio 时代的容器是否仍然适用（[#16342](https://github.com/unslothai/unsloth/issues/16342)）。

---

## 性能与优化

- **解码 CUDA 图，所有 FastModel** — 在 T4 上 batch 1 时吞吐量提升 3–7 倍，与 eager 逐位相等。这是本窗口期内落地的最大单项性能变更。[PR #16587](https://github.com/unslothai/unsloth/pull/16587)
- **长 `generate` 调用的 LoRA 折叠** — 在调用期间将 16 位 LoRA 折叠进基础权重，之后逐位恢复，通过消除每步的 A/B matmul 实现 **1.3–1.4 倍解码**。[PR #16576](https://github.com/unslothai/unsloth/pull/16576)
- **FP8 LoRA 前向 + 输入梯度 GEMM**，作用于冻结的 16 位基础层，保留精确 master 用于保存/合并/生成；合并路径仍受最终头验证门控。[PR #16423](https://github.com/unslothai/unsloth/pull/16423)
- **FP8 逐行 `scaled_mm` 正确性/性能修复** — 避免了 torch < 2.14 上 fp16 输出溢出至 `inf`（值接近 65450/65504），并在 compile 下对始终反量化的 dtype 移除了行保护。[PR #16598](https://github.com/unslothai/unsloth/pull/16598)
- **GGUF 聊天中的工具调用延迟** — Studio 在工具返回与下一次模型传递之间的间隙（上下文适配/压缩）中，**每次工具调用耗费 26–40 秒**，且不显示状态。PR #16608 添加了中间状态报告。[PR #16608](https://github.com/unslothai/unsloth/pull/16608)
- **混合/循环模型上的提示缓存失效** — 动态 MCP 加载会使提示缓存失效，并导致报告的 **17 倍减速**；该 issue 已关闭。[#16538](https://github.com/unslothai/unsloth/issues/16538)
- **RAG 索引开销** — `rag.db` 连接现在在链接文件夹同步过程中按线程复用，而非每次簿记调用都打开一个。[PR #16607](https://github.com/unslothai/unsloth/pull/16607)
- **移除冗余的 Hub 读取** — 远程代码扫描配置每请求读取一次（`config.json`、`tokenizer_config.json`、`preprocessor_config.json`），`adapter_config.json` 在每次推理 worker 加载时读取一次而非两次。[PR #16606](https://github.com/unslothai/unsloth/pull/16606), [PR #16604](https://github.com/unslothai/unsloth/pull/16604)
- **提议按工具 MCP 开关** — 以减少 23 工具 MCP 服务器在任何工作开始前注入的约 18k tokens 的工具定义。[PR #15629](https://github.com/unslothai/unsloth/pull/15629)

---

## 稳定性与回归

按严重程度排序：

1. **静默的训练数据损坏（最高）** — 带有清单相对路径（`clips/a.wav`）的 S3 音频数据集，当另一个对象在前缀根共享同名时，会静默解析到*不同的*文件，保留原始转录并产生错误的训练对。尚无修复 PR。[#16528](https://github.com/unslothai/unsloth/issues/16528)
2. **AppImage 在 Slackware64-current 上无法启动** — GLib/AppIndicator 符号查找错误（`libappindicator-sys` 在捆绑的旧版 lib 之前 dlopen `libayatana-appindicator3.so.1`）。**修复 PR 已开放：** [#16613](https://github.com/unslothai/unsloth/pull/16613) 关闭 [#16605](https://github.com/unslothai/unsloth/issues/16605)。
3. **更新器在 v0.1.914-beta 上卡住** — Windows（[#16573](https://github.com/unslothai/unsloth/issues/16573)）和 macOS（[#16561](https://github.com/unslothai/unsloth/issues/16561)）。无修复 PR。
4. **加载模型会静默取消正在运行的生成** — 与卸载不对称，后者正确返回 409 并要求 `force_cancel_active`。已关闭。[#16479](https://github.com/unslothai/unsloth/issues/16479)
5. **`chat_template_kwargs` 过滤破坏非思考模板** — `studio/backend/core/inference/llama_cpp.py` 中的 `_request_reasoning_kwargs()` 仅将 `enable_thinking`、`reasoning_effort`、`preserve_thinking` 列入白名单，丢弃了例如 Sage-A1.5 代理模板的 `verification: true`。[#16545](https://github.com/unslothai/unsloth/issues/16545)
6. **多 LoRA 专家适配器不可移植** — 通过 `adapter_state_dict` 导出的适配器无法由原生 transformers + PEFT 加载，因为专家因子未转换为 PEFT 的布局。**修复 PR 已开放：** [#16568](https://github.com/unslothai/unsloth/pull/16568)
7. **UI 回归** — AppImage UI“非常卡顿”（[#15999](https://github.com/unslothai/unsloth/issues/15999)）；长对话导致 web 和桌面 UI 卡顿（[#16603](https://github.com/unslothai/unsloth/issues/16603)）；聊天开始后背景图片消失（[#16574](https://github.com/unslothai/unsloth/issues/16574)）。
8. **RAG 解析器泄漏隐藏 DOM 内容** — 原生 `hidden` 子树被索引，导致过时的隐藏策略污染检索文本。**修复 PR 已开放：** [#16612](https://github.com/unslothai/unsloth/pull/16612) 关闭 [#16611](https://github.com/unslothai/unsloth/issues/16611)。
9. **macOS 托管账户沙箱中止工具** — 终端和 Python 工具以 `-6` 退出且输出为空，因为 dyld 在进程启动时读取 `/`，而配置文件拒绝了该操作。已关闭并修复。[#16551](https://github.com/unslothai/unsloth/issues/16551), [PR #16579](https://github.com/unslothai/unsloth/pull/16579)
10. **`search_conversation` 工具第二次消失**（[#16602](https://github.com/unslothai/unsloth/issues/16602)）；空闲自动卸载损坏（[#16484](https://github.com/unslothai/unsloth/issues/16484)，已关闭）。

---

## 这对应用开发者意味着什么

- **Batch-1 解码变得显著更便宜。** CUDA 图现在覆盖了大多数团队实际服务的模型（Gemma 3、Qwen3/Qwen3-MoE、Llama 4、Mistral 3、Phi-4、GLM-4）。逐位相等的声明意味着你可以直接启用它而无需重新验证输出分布——但在假定 3–7 倍之前，请先基准测试你自己的 T4/A100 数字。
- **LoRA 密集型服务可通过权重折叠在长 `generate` 调用中获得免费的 1.3–1.4 倍解码**，基础权重逐位恢复。由于每次调用都会恢复状态，对于多租户 LoRA 交换是安全的——如果你共享模型实例，请在并发 generate 下验证。
- **当前升级有风险。** 两个独立的更新器 bug 加上一个 AppImage 加载器 bug，意味着就地更新到 v0.1.914-beta 可能让你停留在损坏或半更新的安装上。优先使用干净安装，并在 CI 镜像中固定你已知良好的版本。
- **如果你在 S3 音频数据集上训练，请审计你的清单**，检查前缀根名称冲突——这是静默的标签损坏，而非崩溃，且不会在损失曲线中显现。
- **使用自定义聊天模板的代理部署**受 3 键 `chat_template_kwargs` 白名单影响；你的模板依赖的除思考/推理标志之外的任何 kwargs 都会在 llama.cpp 后端边界被静默丢弃。
- **工具定义 token 开销是真实存在的：** 23 个 MCP 工具在第一个用户轮次之前约 18k tokens。如果你将 Studio 作为代理主机运行，按工具开关（[PR #15629](https://github.com/unslothai/unsloth/pull/15629)）和工具轮次状态修复（[PR #16608](https://github.com/unslothai/unsloth/pull/16608)）是两个值得跟踪的 PR——一个削减预填充成本，另一个削减慢速 CPU/GGUF 路径上的感知延迟。

</details>

---
*本日报由 [agents-radar](https://github.com/SnowEagle-Kn/agents-radar) 自动生成。*