# AI Infrastructure Digest 2026-11-04

> Generated: 2026-11-04 02:55 UTC | Projects covered: 6

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## Cross-Project Comparison

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

## Per-Project Reports

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

# SGLang Digest — 2026-11-04

## 1. Today's Highlights

No new releases landed in the last 24 hours, but activity is dominated by **prefill-decode disaggregation and HiCache hardening**: the Q4 PD roadmap was refreshed ([#43699](https://github.com/sgl-project/sglang/issues/43699)) alongside multiple PD-related fixes for PP prefill synchronization and hybrid-cache decode reuse. On the kernel side, AMD/ROCm and NPU optimization PRs are moving in parallel with SM90 FP4 attention work for DeepSeek-V4.1, while the long-running CUDA coredump and CI flaky-test trackers continue to accumulate volume (354 and 174 comments respectively).

## 2. Releases & Breaking Changes

No releases in the last 24h. Two API-surface items worth tracking:

- **RFC: complete migration to shared `api.v1` backend** ([#45816](https://github.com/sgl-project/sglang/issues/45816)) — unifies generation behind the Rust server's `api.v1.Generate` API, with sub-tasks migrating `sgl-processor`, `sgl-router`, and router routing decisions. This is the largest pending interface change for anyone integrating below the OpenAI-compatible HTTP layer.
- **Prompt/cache accounting alignment across APIs** ([#46091](https://github.com/sgl-project/sglang/pull/46091)) — aligns prompt token boundaries and cache-miss vs. measured-write accounting across Responses, Messages, and Chat paths. Token-count semantics may shift for billing/telemetry consumers.
- **Dependency swap: `openai-harmony` → `oss-harmony==0.0.12`** ([#48267](https://github.com/sgl-project/sglang/pull/48267)) — enables offline GPT-OSS serving vocabulary loading; tool-history rendering now uses native oss-harmony.

## 3. New Model & Hardware Support

- **SM90 attention for packed FP4 Main KV (DeepSeek-V4.1)** — [PR #43852](https://github.com/sgl-project/sglang/pull/43852) adds Hopper attention support for the packed-FP4 KV layout, part 2/N of the dsv4.1 NVIDIA path.
- **Intel XPU paged-experts RFC** — [Issue #46520](https://github.com/sgl-project/sglang/issues/46520) proposes Lookahead Expert Scheduling (LES) plus an SSD tier for iGPU single-GPU MoE offload, extending the Paged Experts work from #42557.
- **NPU multimodal `mm_feature_transport=npu_ipc`** — [PR #46208](https://github.com/sgl-project/sglang/pull/46208) adds NPU-IPC transport for multimodal features on Ascend.
- **CPU RoPE fix for VLA models (Pi0.5)** — [PR #40139](https://github.com/sgl-project/sglang/pull/40139) extends `apply_rotary_pos_emb_cpu` to 4D inputs with `unsqueeze_dim`, fixing Pi0.5 CPU serving.
- **Generic speculative-decoding provider boundary** — [PR #47201](https://github.com/sgl-project/sglang/pull/47201) introduces a model-neutral strategy interface so optional SD providers can plug in without forking graph-facing call sites.

## 4. Performance & Optimization

- **SM120 prefill throughput gap under investigation** — [Issue #33422](https://github.com/sgl-project/sglang/issues/33422): DeepSeek-V4-Flash on 4× RTX PRO 6000 (TP=4, PCIe) reports **~2–7K tok/s prefill vs. ~12.5K** with vLLM/marlin on identical hardware; no root cause yet, kernel-coverage gaps on SM120 suspected.
- **GLM-5.3-Flash on Hopper tracking** — [Issue #45373](https://github.com/sgl-project/sglang/issues/45373) tracks SM90 perf work under the Q4 roadmap (#42473).
- **Qwen3.8-Flash-Next on gfx950/MI355X** — [Issue #44304](https://github.com/sgl-project/sglang/issues/44304) covers GatedDeltaNet + QSA + MXFP4 MoE + NEXTN MTP optimization on ROCm.
- **GLM-5 decode MLA as Gluon kernels on gfx950** — [PR #46486](https://github.com/sgl-project/sglang/pull/46486) collapses three kernels per layer (q-absorb+RoPE+FP8 KV write, split-K sparse MLA, FP8 value-attend) into a Gluon chain.
- **AMD: skip vocabulary-wide sort in MTP verify** — [PR #48090](https://github.com/sgl-project/sglang/pull/48090) removes the dense softmax + `top_k_renorm_prob` + `top_p_renorm_prob` path over `bs × num_draft_tokens` rows under `--speculative-eagle-topk 1`.
- **AMD: fused prefill QK-norm + RoPE** — [PR #47052](https://github.com/sgl-project/sglang/pull/47052) replaces five launches per layer with a single JIT `qknorm_rope` kernel; the existing fused path was CUDA-only.
- **Avoid blocking H2D for MRoPE delta in spec decode** — [PR #46781](https://github.com/sgl-project/sglang/pull/46781) removes a pageable-host `torch.stack` → `.to(device)` stall per step for multimodal speculative requests.
- **Skip routed-expert capture on non-sending ranks** — [PR #47553](https://github.com/sgl-project/sglang/pull/47553) eliminates per-MoE-layer id copies on TP ranks that never read them when DeepEP is off.
- **MambaPool COW OOM spike fix** — [PR #47984](https://github.com/sgl-project/sglang/pull/47984) chunks `temporal[:, dst] = temporal[:, src]` to avoid an `n × slot_bytes` temporary during radix COW.
- **NPU vocabulary-parallel embedding fusion** — [PR #48050](https://github.com/sgl-project/sglang/pull/48050) replaces 13 elementwise ATen calls / 16 kernels in `get_masked_input_and_mask` with one fused kernel.
- **Latency/throughput milestone:** HiSparse for DeepSeek-V4 with MTP + DSpark ([PR #44684](https://github.com/sgl-project/sglang/pull/44684)) reports GSM8K validation on DeepSeek-V4-Flash-FP8 with 6.6–6.8K-token prompts.

## 5. Stability & Regressions

Ranked by severity:

1. **Speculative sampling correctness — rejects valid drafts** ([#42568](https://github.com/sgl-project/sglang/issues/42568)): `chain_speculative_sampling_triton` treats `q > 1.0` (from rounding) as invalid since #37134, discarding every such draft. Silent quality/acceptance-rate degradation, no fix PR yet.
2. **ROCm `--dcp-size > 1` crashes all ranks on first forward** ([#38709](https://github.com/sgl-project/sglang/issues/38709)): `forward_absorb_rocm_core` unpacks 2 values from a 1-value return; reproduced on 8× MI300X with GLM-5.3 FP8, still present on main.
3. **EAGLE/MTP same-checkpoint draft inflates KV pool sizing → startup OOM** ([#42510](https://github.com/sgl-project/sglang/issues/42510)): redundant `embed_tokens`/`lm_head` copies remain resident during pool sizing.
4. **Mooncake HiCache backup fails for Qwen3.8-Flash-Next-FP8** ([#48145](https://github.com/sgl-project/sglang/issues/48145)): generation succeeds but backup raises `NotImplementedError` on Mamba zero-copy slot side states. **Fix PR exists**: [#48293](https://github.com/sgl-project/sglang/pull/48293).
5. **ROCm DeepSeek-V4.1 FP4 indexer still exhausts HBM** under long-context AgentX despite #37660 ([#39441](https://github.com/sgl-project/sglang/issues/39441)).
6. **PD + PP KV send races prefill forward** ([PR #45524](https://github.com/sgl-project/sglang/pull/45524)): `process_prefill_chunk` transmits a non-final chunk's KV before the forward is guaranteed complete on `forward_stream`. Fix in review.
7. **CI: Wan VAE decode 2–10× slowdown on `5090-sm*` runners** ([#48196](https://github.com/sgl-project/sglang/issues/48196)) since the 2026-10-17 runner migration; roughly half of `multimodal-gen-test-1-5090` attempts fail.
8. **Background trackers**: CUDA coredump collection ([#26340](https://github.com/sgl-project/sglang/issues/26340), 354 comments) and flaky/CI-infra failures ([#42752](https://github.com/sgl-project/sglang/issues/42752), 174 comments) remain open and are the main signal for build health.
9. **Stale-issue sweep**: a large batch of `[inactive]` bugs were closed today, including DFLASH under PD ([#36140](https://github.com/sgl-project/sglang/issues/36140)), HiCache prefetch all_reduce deadlock ([#30760](https://github.com/sgl-project/sglang/issues/30760)), radix `evictable_size()` double-count ([#35270](https://github.com/sgl-project/sglang/issues/35270)), malformed-multimodal engine crash ([#38022](https://github.com/sgl-project/sglang/issues/38022)), and EAGLE `vectorized_gather_kernel` OOB ([#37936](https://github.com/sgl-project/sglang/issues/37936)). Treat these as **deferred, not fixed** unless a linked PR landed.

## 6. What This Means for Application Developers

- **PD disaggregation is the default deployment shape** and is where the sharp edges are: if you run PP on the prefill side, watch [PR #45524](https://github.com/sgl-project/sglang/pull/45524); if you use DFLASH + PD, it remains unsupported (issue closed as inactive, not fixed).
- **HiCache + Mooncake users on hybrid/Mamba models** should track [#48293](https://github.com/sgl-project/sglang/pull/48293) — backup failure is non-fatal to generation, so you can silently lose cache tiering without an error on the request path. Monitor backup errors explicitly.
- **Speculative decoding acceptance rates may be understated** on Triton sampling paths until [#42568](https://github.com/sgl-project/sglang/issues/42568) is resolved; benchmark with rejection sampling disabled before concluding a tuning regression.
- **Token accounting changes are coming** ([#46091](https://github.com/sgl-project/sglang/pull/46091)) — if you bill or cache on `count_tokens`, pin against the current boundary semantics and re-validate after merge.
- **Offline/air-gapped GPT-OSS serving** becomes viable via [PR #48267](https://github.com/sgl-project/sglang/pull/48267); the previous `openai-harmony` pin required network access for vocabulary loading.
- **Router-level session affinity** ([#34513](https://github.com/sgl-project/sglang/issues/34513)) was closed inactive, so agent workloads with growing conversation prefixes still need external sticky routing to benefit from RadixAttention.

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp Digest — 2026-11-04

## 1. Today's Highlights
llama.cpp shipped a dense run of builds (b12081–b12094) focused on speculative decoding, model coverage, and backend kernel efficiency. The most consequential work is around MTP/speculative decoding: hidden-state save/restore landed, LFM3 now records layer inputs for spec decode, but multiple open reports show draft-MTP correctness and crash issues on quantized targets and CUDA. Model support expanded to Metis-1.6, NVFP4 LFM3, and Nemotron 3.5 Transcribe, while a large xing4_0 architecture PR is in review.

## 2. Releases & Breaking Changes
No explicit API or config breaking changes were identified in the last 24 hours. Notable builds:

- **b12094** — Hexagon: faster `TOP_K` backend sampler via tile skipping + block scan ([#31279](https://github.com/ggml-org/llama.cpp/pull/31279)).
- **b12091** — Speculative: save/restore MTP hidden state; simplifies checkpoint state to boundary hidden row ([#30141](https://github.com/ggml-org/llama.cpp/pull/30141)).
- **b12090** — ggml-zdnn: fix FATTN test failure ([#31583](https://github.com/ggml-org/llama.cpp/pull/31583)).
- **b12089** — New model: Metis-1.6 support ([#31134](https://github.com/ggml-org/llama.cpp/pull/31134)).
- **b12088** — Enable NVFP4 LFM3 GGUF files ([#31608](https://github.com/ggml-org/llama.cpp/pull/31608)).
- **b12087** — mtmd: Nemotron 3.5 Transcribe support, plus vocab fixes for Nemotron-H draft models and BF16-only Transcribe checkpoints ([#31471](https://github.com/ggml-org/llama.cpp/pull/31471)).
- **b12086** — qwen4exp: write conv-state tail without intermediate contiguous copy ([#31443](https://github.com/ggml-org/llama.cpp/pull/31443)).
- **b12084** — LFM3: record layer inputs for speculative decoding ([#31598](https://github.com/ggml-org/llama.cpp/pull/31598)).
- **b12083** — ggml: fix signed overflow / UB in `ggml_roll` bounds check ([#30375](https://github.com/ggml-org/llama.cpp/pull/30375)).
- **b12081** — CUDA: potential fix for Q8_1 correction sum, including `quantize_mmq_q8_1` flow ([#31247](https://github.com/ggml-org/llama.cpp/pull/31247)).

Release artifacts: https://github.com/ggml-org/llama.cpp/releases

## 3. New Model & Hardware Support

- **Metis-1.6** — model architecture support landed in b12089 ([#31134](https://github.com/ggml-org/llama.cpp/pull/31134)).
- **LFM3 NVFP4** — NVFP4-quantized LFM3 GGUF files enabled ([#31608](https://github.com/ggml-org/llama.cpp/pull/31608)); LFM3 also gained layer-input recording for spec decode ([#31598](https://github.com/ggml-org/llama.cpp/pull/31598)).
- **Nemotron 3.5 Transcribe** — multimodal/audio support added, with stricter conversion handling for non-BF16 checkpoints ([#31471](https://github.com/ggml-org/llama.cpp/pull/31471)).
- **xing4_0 (in review)** — Full architecture support including mHC/MLA/MoE/MTP, HF→GGUF conversion, and inference graphs; reuses existing ggml ops ([#31589](https://github.com/ggml-org/llama.cpp/pull/31589)).
- **Hexagon LIGHTNING_INDEXER (in review)** — Adds the op to the Hexagon backend with Q4_0/Q8_0/F16/F32; other quants deferred ([#31289](https://github.com/ggml-org/llama.cpp/pull/31289)).
- **OpenCL/Adreno** — Improved `q2_0` GEMV producing 4 or 8 rows per thread, plus an f32f32 MV variant ([#31577](https://github.com/ggml-org/llama.cpp/pull/31577)).
- **WebGPU** — Batched tensor API (`get_tensor_n`/`set_tensor_n`) added ([#31473](https://github.com/ggml-org/llama.cpp/pull/31473)); `get_max_size` now returns `maxBufferSize`, with narrower memset/clear bindings ([#31348](https://github.com/ggml-org/llama.cpp/pull/31348)).
- **Vulkan** — Intel FA prefill kernel under review, consolidating prefill/decode push constants ([#29357](https://github.com/ggml-org/llama.cpp/pull/29357)).
- **Open request** — Zamba2-2.7B support still open with 20 👍 ([#8795](https://github.com/ggml-org/llama.cpp/issues/8795)).

## 4. Performance & Optimization

- **Hexagon sampler** — New `TOP_K` path skips bitonic sort for tiles whose max cannot reach the running k-th best, then uses `top_k_scan()` for the remainder ([#31279](https://github.com/ggml-org/llama.cpp/pull/31279)).
- **Qwen4exp recurrent state** — Removes an intermediate contiguous copy of the conv-input tail, cutting two device copies per generated token to one per GDN layer ([#31443](https://github.com/ggml-org/llama.cpp/pull/31443)).
- **SYCL tensor parallel** — Fast `ggml_sycl_comm_exchange()` reported **+16.7% to +20.6% prompt-eval t/s (mean ≈ +19%)** with `-sm tensor` on dual Arc Pro B70s, matching `-sm layer` with zero PPL change; PR is closed/merged ([#30956](https://github.com/ggml-org/llama.cpp/pull/30956)).
- **CUDA GDN ordering** — Follow-up PR adds `GGML_CUDA_GDN_ORDERED` for the 4-column GDN implementation after upstream #30087 caused MTP decode performance regressions ([#31634](https://github.com/ggml-org/llama.cpp/pull/31634); prior [#31630](https://github.com/ggml-org/llama.cpp/pull/31630) closed).
- **Host memory** — Scheduler context sizing and graph metadata allocation reworked for lazy/less over-allocation, with substantial host-memory savings reported ([#31274](https://github.com/ggml-org/llama.cpp/pull/31274)).
- **HIP fast-math** — PR enables fast-math on HIP without regressions, aiming to close the ROCm fast-math chapter ([#31625](https://github.com/ggml-org/llama.cpp/pull/31625)).
- **CUDA FA regression on RTX 5090** — Stream-k block count not a multiple of `ntiles_dst` on 170-SM RTX 5090 yields **10–13% slower long-context prefill vs RTX 4090**, with a reported **+37–40%** improvement when fixed ([#30784](https://github.com/ggml-org/llama.cpp/issues/30784)).

## 5. Stability & Regressions

Ranked by severity:

1. **Speculative decoding correctness divergence** — Under greedy sampling (`temperature=0`, `top_k=1`), draft-MTP/draft-dspark speculation can produce different output than vanilla decoding when the target is quantized (e.g. Q4_K_M); BF16 targets match. High-impact for anyone relying on deterministic or reproducible greedy output. Open, 37 comments ([#25618](https://github.com/ggml-org/llama.cpp/issues/25618)).
2. **CUDA illegal memory access with draft-MTP** — Gemma 4 31B + draft

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

# Unsloth Digest — 2026-11-04

## Today's Highlights

Unsloth shipped **v0.1.914-beta** ("Glass Theme, Personalize & Faster Generation"), adding UI theming plus faster generation and expanded multi-GPU and CPU training paths. On the engine side, the biggest technical item is PR #16587, which extends decode CUDA graphs to essentially every FastModel architecture with reported **3–7x speedups on T4** and bitwise-identical output to eager mode. Studio stability consumed most of the issue tracker today, with a cluster of updater/AppImage launch failures alongside several in-flight performance patches for tool-call latency and RAG indexing.

---

## Releases & Breaking Changes

- **v0.1.914-beta** — Glass themes, customizable backgrounds/greetings/chats, faster generation, additional multi-GPU training options, and CPU training support. [Release notes](https://github.com/unslothai/unsloth/releases)
- **Updater failures on this release** — Multiple reports that updating *to* v0.1.914-beta does not complete on Windows 11 ([#16573](https://github.com/unslothai/unsloth/issues/16573)) and macOS/Apple MLX ([#16561](https://github.com/unslothai/unsloth/issues/16561)). No fix PR merged yet; affected users should install fresh rather than in-place update.
- **PyPI extras lag** — `cu130-2130` / `cu130-2140` torch extras are present in `pyproject.toml` but absent from the published 2026.10.11 wheel ([#16520](https://github.com/unslothai/unsloth/issues/16520)). Pinning torch 2.13/2.14 from PyPI will not resolve through Unsloth extras.
- **New CLI/packaging surface:** CPU training is now a first-class path, which changes default device-placement behavior for users who previously assumed GPU-only.

---

## New Model & Hardware Support

- **Decode CUDA graphs for all FastModel architectures** — Gemma 3, Qwen3-VL (text), Llama 4 (text), Mistral 3, Granite, Phi-4, OLMo 2, Cohere 2, GLM-4, and Qwen3-MoE at batch 1 now replay graphs; previously only Llama attention and Qwen3 did. [PR #16587](https://github.com/unslothai/unsloth/pull/16587)
- **CPU training** — newly supported in the Unsloth package per the v0.1.914-beta notes.
- **Expanded multi-GPU training options** — additional parallelism configurations exposed in the desktop/CLI training path.
- **OpenRouter model catalog is now API-driven** — replaces a hardcoded list where 8 of 18 entries no longer existed upstream (`anthropic/claude-sonnet-4-5`, `deepseek/deepseek-r1`, `mistralai/mistral-large-2411`), and enables "Reload models" for OpenRouter. [PR #16562](https://github.com/unslothai/unsloth/pull/16562)
- **ROCm test coverage hardened** — diffusion tests now pin NVIDIA-host spoofing and assert ROCm keeps the stock (non-optimized) path on gfx1151 / Strix Halo. [PR #16609](https://github.com/unslothai/unsloth/pull/16609)
- **Open gaps:** Vulkan backend still broken on RX 5700 XT under Windows 10 22H2 ([#16395](https://github.com/unslothai/unsloth/issues/16395)); DGX Spark users are asking whether the published blog guidance is still current for the Studio-era container ([#16342](https://github.com/unslothai/unsloth/issues/16342)).

---

## Performance & Optimization

- **Decode CUDA graphs, all FastModels** — 3–7x throughput on T4 at batch 1, bitwise equal to eager. This is the single largest landed perf change in the window. [PR #16587](https://github.com/unslothai/unsloth/pull/16587)
- **LoRA folding for long `generate` calls** — folds 16-bit LoRA into base weights for the duration of the call and restores bitwise afterward, yielding **1.3–1.4x decode** by eliminating per-step A/B matmuls. [PR #16576](https://github.com/unslothai/unsloth/pull/16576)
- **FP8 LoRA forward + input-gradient GEMMs** on frozen 16-bit base layers, retaining exact masters for save/merge/generate; merge path still gated on final-head validation. [PR #16423](https://github.com/unslothai/unsloth/pull/16423)
- **FP8 rowwise `scaled_mm` correctness/perf fixes** — avoids fp16 output overflow to `inf` on torch < 2.14 (values near 65450/65504) and drops the row guard for always-dequant dtypes under compile. [PR #16598](https://github.com/unslothai/unsloth/pull/16598)
- **Tool-call latency in GGUF chat** — Studio spends **26–40s per tool call** in the gap between tool return and the next model pass (context fitting/compaction), with no status shown. PR #16608 adds intermediate status reporting. [PR #16608](https://github.com/unslothai/unsloth/pull/16608)
- **Prompt-cache invalidation on hybrid/recurrent models** — dynamic MCP loading was invalidating the prompt cache and causing a reported **17x slowdown**; issue closed. [#16538](https://github.com/unslothai/unsloth/issues/16538)
- **RAG indexing overhead** — `rag.db` connections are now reused per thread within a linked-folder sync pass instead of opening one per bookkeeping call. [PR #16607](https://github.com/unslothai/unsloth/pull/16607)
- **Redundant Hub reads removed** — remote-code scan configs read once per request (`config.json`, `tokenizer_config.json`, `preprocessor_config.json`) and `adapter_config.json` read once per inference worker load instead of twice. [PR #16606](https://github.com/unslothai/unsloth/pull/16606), [PR #16604](https://github.com/unslothai/unsloth/pull/16604)
- **Per-tool MCP switches** proposed to cut the ~18k tokens of tool definitions the 23-tool MCP server injects before any work happens. [PR #15629](https://github.com/unslothai/unsloth/pull/15629)

---

## Stability & Regressions

Ranked by severity:

1. **Silent training-data corruption (highest)** — S3 audio datasets with manifest-relative paths (`clips/a.wav`) silently resolve to a *different* file when another object shares the name at the prefix root, keeping the original transcript and producing a wrong training pair. No fix PR yet. [#16528](https://github.com/unslothai/unsloth/issues/16528)
2. **AppImage fails to start on Slackware64-current** — GLib/AppIndicator symbol lookup error (`libappindicator-sys` dlopens `libayatana-appindicator3.so.1` before the bundled legacy lib). **Fix PR open:** [#16613](https://github.com/unslothai/unsloth/pull/16613) closes [#16605](https://github.com/unslothai/unsloth/issues/16605).
3. **Updater wedged on v0.1.914-beta** — Windows ([#16573](https://github.com/unslothai/unsloth/issues/16573)) and macOS ([#16561](https://github.com/unslothai/unsloth/issues/16561)). No fix PR.
4. **Loading a model silently cancels running generations** — asymmetric with unload, which correctly returns 409 and requires `force_cancel_active`. Closed. [#16479](https://github.com/unslothai/unsloth/issues/16479)
5. **`chat_template_kwargs` filtering breaks non-thinking templates** — `_request_reasoning_kwargs()` in `studio/backend/core/inference/llama_cpp.py` whitelists only `enable_thinking`, `reasoning_effort`, `preserve_thinking`, dropping e.g. `verification: true` for Sage-A1.5 agentic templates. [#16545](https://github.com/unslothai/unsloth/issues/16545)
6. **Multi-LoRA expert adapters not portable** — adapters exported via `adapter_state_dict` cannot be loaded by vanilla transformers + PEFT because expert factors aren't converted to PEFT's layout. **Fix PR open:** [#16568](https://github.com/unslothai/unsloth/pull/16568)
7. **UI regressions** — AppImage UI "very laggy" ([#15999](https://github.com/unslothai/unsloth/issues/15999)); long conversations cause UI lag in web and desktop ([#16603](https://github.com/unslothai/unsloth/issues/16603)); background images disappear once a chat starts ([#16574](https://github.com/unslothai/unsloth/issues/16574)).
8. **RAG parser leaks hidden DOM content** — native `hidden` subtrees are indexed, so stale hidden policies contaminate retrieved text. **Fix PR open:** [#16612](https://github.com/unslothai/unsloth/pull/16612) closes [#16611](https://github.com/unslothai/unsloth/issues/16611).
9. **macOS managed-account sandbox aborts tools** — terminal and Python tools exited `-6` with empty output because dyld reads `/` at process start and the profile denied it. Closed with fix. [#16551](https://github.com/unslothai/unsloth/issues/16551), [PR #16579](https://github.com/unslothai/unsloth/pull/16579)
10. **`search_conversation` tool vanished** for the second time ([#16602](https://github.com/unslothai/unsloth/issues/16602)); auto-unload-on-idle broken ([#16484](https://github.com/unslothai/unsloth/issues/16484), closed).

---

## What This Means for Application Developers

- **Batch-1 decode got materially cheaper.** CUDA graphs now cover the models most teams actually serve (Gemma 3, Qwen3/Qwen3-MoE, Llama 4, Mistral 3, Phi-4, GLM-4). The bitwise-equality claim means you can enable it without re-validating output distributions — but benchmark your own T4/A100 numbers before assuming the 3–7x.
- **LoRA-heavy serving gets a free 1.3–1.4x decode** via weight folding on long `generate` calls, with base weights restored bitwise. Safe for multi-tenant LoRA swaps since state is restored per call — verify under concurrent generate if you share a model instance.
- **Upgrades are risky right now.** Two independent updater bugs plus an AppImage loader bug mean in-place update to v0.1.914-beta can leave you on a broken or half-updated install. Prefer clean installs and pin your known-good version in CI images.
- **If you train on S3 audio datasets, audit your manifests** for prefix-root name collisions — this is silent label corruption, not a crash, and it will not surface in loss curves.
- **Agentic deployments with custom chat templates are affected** by the 3-key `chat_template_kwargs` whitelist; any kwargs your template depends on beyond thinking/reasoning flags are silently dropped at the llama.cpp backend boundary.
- **Tool-definition token overhead is real:** 23 MCP tools ≈ 18k tokens before the first user turn. If you run Studio as an agent host, per-tool switches ([PR #15629](https://github.com/unslothai/unsloth/pull/15629)) and the tool-round status fix ([PR #16608](https://github.com/unslothai/unsloth/pull/16608)) are the two PRs to track — one cuts prefill cost, the other cuts perceived latency on slow CPU/GGUF paths.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/SnowEagle-Kn/agents-radar).*