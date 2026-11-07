# AI Infrastructure Digest 2026-11-07

> Generated: 2026-11-07 02:20 UTC | Projects covered: 6

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## Cross-Project Comparison

⚠️ Comparative analysis generation failed.

---

## Per-Project Reports

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM Digest — 2026-11-07

## 1. Today's Highlights

No new vLLM releases landed in the last 24 hours; activity is concentrated in open PRs and long-running design threads. The Rust frontend parity roadmap continues to attract the most discussion ([#44280](https://github.com/vllm-project/vllm/issues/44280)), while the Q4 2026 roadmap was published ([#61024](https://github.com/vllm-project/vllm/issues/61024)). Performance work spans ROCm DSpark/MLA paths, FP8 prefill, MoE DP padding, and multimodal encoder CUDA graphs; stability attention remains on disaggregated KV transfer and hybrid-model correctness.

## 3. New Model & Hardware Support

- **Kimi-K3 FP8 MLA on ROCm gfx1250** — open PR adds AITER FP8 MHA plus MLA prefill/decode paths, scoped to Kimi-K3 with FP8 KV cache; other models/arches retain existing paths. [PR #66208](https://github.com/vllm-project/vllm/pull/66208)
- **DeepSeek-V4/V4.1 DSpark on ROCm** — PR stacks context WKV projections, replacing three per-layer GEMMs with one combined GEMM. [PR #66212](https://github.com/vllm-project/vllm/pull/66212)
- **GPT-OSS MXFP4 on ROCm AITER fused MoE** — fix forwards `quant_config.gemm1_clamp_limit` (GPT-OSS uses 7.0) into AITER, which previously only read `moe_config.swiglu_limit`. [PR #66210](https://github.com/vllm-project/vllm/pull/66210)
- **Nemotron 3.5 ASR streaming** — feature request for `nemotron-3.5-asr-streaming-0.6b`, a 600M multilingual streaming ASR model. [Issue #47455](https://github.com/vllm-project/vllm/issues/47455)
- **GLM-5.3 optimization** — follow-up to prior GLM work; same `glm_moe_dsa` architecture as GLM-5.2, with remaining CUDA and kernel opportunities tracked. [Issue #57406](https://github.com/vllm-project/vllm/issues/57406)
- **Rust frontend parity** — experimental Rust API-server replacement via `VLLM_USE_RUST_FRONTEND=1`; roadmap tracks missing features relative to the Python frontend. [Issue #44280](https://github.com/vllm-project/vllm/issues/44280)
- **ReplaySSM for SSM/linear-attention decode** — RFC proposes caching SSM inputs rather than state for Mamba2 and Gated DeltaNet, targeting decode memory bandwidth. [Issue #47572](https://github.com/vllm-project/vllm/issues/47572)
- **Large-page KV cache allocator** — RFC for one KV cache group per spec, motivated by hybrid models requiring different page sizes. [Issue #64401](https://github.com/vllm-project/vllm/issues/64401)
- **Engine-neutral sharded weight transfer** — “load plan” RFC for NCCL/M2N and sharded RDMA/NIXL engines to know which tensor slices each rank needs and where they land. [Issue #64418](https://github.com/vllm-project/vllm/issues/64418)
- **MiMo-V2 omni video timestamps** — bugfix labels video grids with sampled frames’ actual media time instead of fixed 1-second spacing. [PR #66201](https://github.com/vllm-project/vllm/pull/66201)

## 4. Performance & Optimization

- **FP8 prefill page expansion on SM120** — bounded conversion of referenced FP8 KV pages into BF16 scratch; recorded paired cold-prefill tests showed up to **10.1% input-token throughput improvement**, while retaining FP8 storage. [PR #64942](https://github.com/vllm-project/vllm/pull/64942)
- **MoE DP padding skip** — avoids processing padded rows in FlashInfer TRT-LLM monolithic MoE kernels under data parallelism on Blackwell block-FP8/MXFP8 checkpoints. [PR #65972](https://github.com/vllm-project/vllm/pull/65972)
- **Multimodal encoder CUDA graphs** — moves from greedy splitting into multiple padded replays toward one replay per batch, with a finer grid up to the max token budget. [PR #65227](https://github.com/vllm-project/vllm/pull/65227)
- **DSpark windowed draft context** — computes context K/V precompute and `main_proj` only for trailing rows when all KV groups are sliding-window; targets long-context drafting (16K-token example cited). [PR #60320](https://github.com/vllm-project/vllm/pull/60320)
- **DeepSeek-V4/V4.1 MegaMoE locality placement** — opt-in placement of expert weights in the GPU memory locality domain of the SMs that read them via DeepGEMM’s locality allocator. [PR #64835](https://github.com/vllm-project/vllm/pull/64835)
- **Direct weight reload rollout** — models must opt in; known-silent reload paths are refused, preparing `VLLM_RELOAD_MODE=direct` to become default. [PR #66002](https://github.com/vllm-project/vllm/pull/66002)
- **Speculative-decoding oracle benchmark** — reference-replay benchmark compares real draft methods (EAGLE, n-gram, etc.) against saved target-model output on the same prompts. [PR #62258](https://github.com/vllm-project/vllm/pull/62258)
- **Rust preprocessing benchmark** — community comparison of Rust vs. PyTorch preprocessing performance. [Issue #47601](https://github.com/vllm-project/vllm/issues/47601)
- **Regression watch:** MoE decode reported **~15% slower on SM12x** since DeepGEMM contiguous-layout alignment changed from 64 to 128. [Issue #58624](https://github.com/vllm-project/vllm/issues/58624)
- **P/D descriptor overhead:** GLM-5.3 P/D on GB200 with NIXL sees **91k–120k KV descriptors per TP-rank transfer**, making descriptor submission, not fabric bandwidth, the bottleneck. [Issue #55434](https://github.com/vllm-project/vllm/issues/55434)

## 5. Stability & Regressions

Ranked by severity:

1. **Silent KV corruption in NixlConnector P/D** — producer releases KV blocks on lease expiry while a remote READ is still in flight; decode can consume overwritten KV with no error. [Issue #61229](https://github.com/vllm-project/vllm/issues/61229)
2. **Illegal memory access / gibberish on Qwen3.8-27B GDN hybrid with PP=3** — Xid 31 with prefix caching, incorrect output without it; regression since v0.28.0 across v0.31.0/v0.32.0 and both model runners. [Issue #63505](https://github.com/vllm-project/vllm/issues/63505)
3. **TP worker hang → `EngineDeadError`** — RPC `sample_tokens` timeout with DeepSeek-V4-Pro, TP=8, MTP speculative decoding; high community attention (8 👍). [Issue #41530](https://github.com/vllm-project/vllm/issues/41530)
4. **FP4 TP splits cutting compressed-tensors scale groups** — NVFP4/MXFP4 row-parallel and MoE `w2` layers could silently multiply FP4 codes by other groups’ scales. Fix PR refuses invalid splits. [PR #66213](https://github.com/vllm-project/vllm/pull/66213)
5. **DeepSeek DSv4.1 v0.32.0 block-size failure** — neither 64 nor 128 works on the release tag; issue closed with fix tracked in #60762. [Issue #59203](https://github.com/vllm-project/vllm/issues/59203)
6. **Marlin MoE batch invariance** — output depends on equivalent within-expert token ordering, affecting deterministic/batch-invariant serving. [Issue #52525](https://github.com/vllm-project/vllm/issues/52525)
7. **Hybrid models with ≥3 attention groups + EAGLE/MTP + prefix caching untested** — block-drop behavior not validated for DeepSeek-V4-Flash/DSpark-class models. [Issue #51771](https://github.com/vllm-project/vllm/issues/51771)
8. **Qwen3.5 hybrid GDN + dflash speculative decoding + PP** — missing `supports_aux_hidden_states_over_pp` opt-in blocks the combination. [Issue #60765](https://github.com/vllm-project/vllm/issues/60765)
9. **Responses API drops tool-call items** — `previous_response_id` loses `function_call`/`function_call_output` from stored context. [Issue #43244](https://github.com/vllm-project/vllm/issues/43244)
10. **Responses API HTTP 500 on empty/refusal content** — replaying a prior assistant turn with empty or refusal content crashes `_construct_message_from_response_item`; fix PR open. [Issue #49130](https://github.com/vllm-project/vllm/pull/49130)
11. **Packed weight-transfer misalignment** — odd-sized packed NCCL/IPC entries shift later entries; fix copies misaligned slices on unpack. [PR #65487](https://github.com/vllm-project/vllm/pull/65487)
12. **Dequant identity dtype mismatch** — generic fallback builds identity in `out_dtype` but feeds it as an activation; fix builds it in the layer’s activation dtype. [PR #66209](https://github.com/vllm-project/vllm/pull/66209)
13. **CI/eval fixes** — ROCm `test_block_fp8.py` skip condition corrected ([PR #66205](https://github.com/vllm-project/vllm/pull/66205)); B200 nightly gsm8k eval failures fixed via config-only changes for ReplaySSM, Kimi-K3 DSpark, and DeepEP LL ([PR #66207](https://github.com/vllm-project/vllm/pull/66207)).

Also closed in this window: Qwen1.5-MoE `torch.compile` degenerate output ([#56900](https://github.com/vllm-project/vllm/issues/56900)), GLM-5.2 MTP 0% draft acceptance on MI355X ([#52833](https://github.com/vllm-project/vllm/issues/52833)), and int8 per-token head scale overwriting Gated DeltaNet state ([#63944](https://github.com/vllm-project/vllm/issues/63944)).

## 6. What This Means for Application Developers

- **No shipped release to adopt today.** The performance items above are open PRs; treat them as “coming soon,” not available in a stable version.
- **Be cautious with disaggregated P/D and prefix caching.** The NixlConnector lease-expiry bug can silently serve corrupted KV; validate outputs or avoid aggressive lease expiry until fixed.
- **Pin carefully for DeepSeek DSv4.1.** v0.32.0 reportedly has no working block size on the release tag; wait for the #60762 fix or use a known-good build.
- **FP4 quantization requires TP alignment.** If your TP split cuts a 16/32-element scale group, the new PR will refuse the load rather than silently produce wrong results — size TP ranks accordingly.
- **Responses API tool-calling has known gaps.** `previous_response_id` can drop function-call history, and empty/refusal assistant turns can return HTTP 500. Gate agent workflows that replay prior turns until fixes land.
- **Hybrid models + speculative decoding remain a sharp edge.** PP combinations and ≥3 attention groups with prefix caching are under-tested; prefer validated configurations for production.
- **Rust frontend is still experimental.** `VLLM_USE_RUST_FRONTEND=1` is a drop-in alternative but not feature-complete; do not assume parity with the Python API server.
- **Multimodal and long-context serving should benefit first** from encoder CUDA graph and DSpark windowed-context work once merged.

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang Digest — 2026-11-07

*Source: github.com/sgl-project/sglang — issues and PRs updated in the last 24h (64 issues, 500 PRs; top 30 / top 20 by comment count reviewed).*

---

## 1. Today's Highlights

The dominant thread is **Kimi-K3.1 Day-0 enablement**: PR #48414 lands the full serving stack (DSA with decode context parallelism, PD disaggregation, DFlash speculative decoding, PP prefill, HiSparse, fused kernels), with a TP-sharding follow-up already merged. In parallel, a large batch of **AMD/ROCm and Intel XPU kernel work** moved through review, and two **silent-correctness bugs** surfaced that matter to anyone running reasoning models or prefix caching through the gateway.

---

## 2. Releases & Breaking Changes

**No new releases in the last 24h.** No API-breaking changes landed.

Config surface additions in flight (not yet merged):
- `--hicache-mamba-size-gb` to pin the HiCache Mamba host pool size — [PR #47867](https://github.com/sgl-project/sglang/pull/47867)
- `--layernorm-sp-min-tokens` to run small extend passes replicated instead of sequence-parallel — [PR #48947](https://github.com/sgl-project/sglang/pull/48947)
- `--hicache-swa-host-ratio` for hybrid sliding-window/full-attention HiCache pools — [PR #48950](https://github.com/sgl-project/sglang/pull/48950)

---

## 3. New Model & Hardware Support

- **Kimi-K3.1** (`KimiK31ForConditionalGeneration`) — Day-0 serving stack: DSA + decode context parallelism, PD disaggregation, DFlash spec decode, PP prefill, HiSparse, fused kernels. Basic model support already on main via #48415 — [PR #48414](https://github.com/sgl-project/sglang/pull/48414)
- **AMD Q4 2026 roadmap** — Helios rack-scale, MI45x (gfx1250), Ryzen AI Halo (gfx1151/1152), Monokernel work — [Issue #46462](https://github.com/sgl-project/sglang/issues/46462)
- **Intel XPU (Xe2 / Battlemage)** — FP8 dense layers can now run weight-only (W8A16) via `sgl_kernel.fp8_linear`; the W8A8 path is slower than bf16 on parts without an FP8 matrix engine — [PR #48787](https://github.com/sgl-project/sglang/pull/48787)
- **Apple Silicon** — RFC for a Torch-owned SRT path with an exported whole-model MLX region; implementation at #36164 — [Issue #32321](https://github.com/sgl-project/sglang/issues/32321)
- **Ascend** — RFC to integrate MindIE-SD as an optional acceleration library for SGLang Diffusion (dense quantized + sparse attention) — [Issue #48582](https://github.com/sgl-project/sglang/issues/48582)
- **Weight transfer** — native ModelExpress backend via `POST /update_weights_from_modelexpress`, aimed at trainer→server weight sync loops — [PR #42103](https://github.com/sgl-project/sglang/pull/42103)

---

## 4. Performance & Optimization

**Speculative decoding / DFlash**
- Kimi-K3.1 decode MoE front + latent-up tail TP-sharded (merged): each TP rank previously read the full 12.9 MB router and 51.4 MB latent down-proj per layer, plus 51.4 MB up_proj — [PR #48584](https://github.com/sgl-project/sglang/pull/48584)
- DFlash verify: side-stream context projection and a single-kernel verify commit — [PR #48948](https://github.com/sgl-project/sglang/pull/48948)
- dspark: build the compact verify window and stage its padded layout in one launch each — [PR #48833](https://github.com/sgl-project/sglang/pull/48833)
- `kda-replayssm`: reuse verify-fused commit step indices, removing ~17 eager kernel launches per verify step — [PR #44396](https://github.com/sgl-project/sglang/pull/44396)

**AMD / ROCm**
- Kimi-K3 on MI355X: emit AITER `moe_sorting` directly from the radix-4 router launch, cutting one of three pre-GEMM launches at M ≤ 64 tokens/MoE call — [PR #43126](https://github.com/sgl-project/sglang/pull/43126)
- Quark FP8 KDA in-proj and MLA absorb routed onto FP8 kernels (MI355X decode is latency-bound) — [PR #44362](https://github.com/sgl-project/sglang/pull/44362)
- Segmented split-K bf16 matmul for gfx950 in deterministic/batch-invariant mode — [PR #45599](https://github.com/sgl-project/sglang/pull/45599)
- Qwen3-Next: fuse Q/K GemmaRMSNorm + partial RoPE into one Triton kernel on gfx950 — [PR #48044](https://github.com/sgl-project/sglang/pull/48044)

**Kernels & sampling**
- `reject_sampling`: vocabulary-tiled final-token pass for small-batch spec sampling — [PR #48949](https://github.com/sgl-project/sglang/pull/48949)
- Unified FP32 activation library in `sgl_kernel/activation.cuh`, incl. `silu_fast` for latency-bound fused quantization — [PR #48942](https://github.com/sgl-project/sglang/pull/48942)
- DeepSeek-V4.1: compact-verify ratio-1/2 compressors moved off the general torch path onto fused kernels — [PR #48832](https://github.com/sgl-project/sglang/pull/48832)
- DSV4.1 indexer consumers score only candidate positions rather than all visible K — [PR #46003](https://github.com/sgl-project/sglang/pull/46003)

**Roadmap-level**
- Weight Cache Daemon: post-quantized weights served over CUDA IPC, weight load **~306–327 s → <1 s** on Qwen3-235B FP8 — [Issue #33522](https://github.com/sgl-project/sglang/issues/33522)
- GLM-5.3-Flash Hopper (SM90) optimization tracker — [Issue #45373](https://github.com/sgl-project/sglang/issues/45373)

---

## 5. Stability & Regressions

Ranked by severity (silent correctness first):

1. **FP8 KV-cache scales silently ignored** — attention built without `quant_config` on GLM-4.5, Llama 4, Qwen3-MoE, GPT-OSS, HunYuan, HY3, K-EXAONE, LFM2. Silent quality degradation, no error. — [Issue #48749](https://github.com/sgl-project/sglang/issues/48749)
2. **Reasoning parser truncates on quoted `</think>`** — first-occurrence split cuts `reasoning_content` and leaks the remainder of the CoT into `content`. — [Issue #48791](https://github.com/sgl-project/sglang/issues/48791)
3. **GLM-5.3-Flash thinking degenerates into repeated `!`** under complex system prompt + multiple tools + `tool_choice=auto`. — [Issue #36669](https://github.com/sgl-project/sglang/issues/36669)
4. **Model gateway drops `cache_salt`** on `/v1/chat/completions` before forwarding — same request behaves differently direct vs. via gateway. — [Issue #48793](https://github.com/sgl-project/sglang/issues/48793)
5. **CI regression on main**: `test_kandinsky6_sr_decode_parallel` spatial-2 fails since #48366 (max abs diff 9); fix PR open — [Issue #48892](https://github.com/sgl-project/sglang/issues/48892) / [PR #48903](https://github.com/sgl-project/sglang/pull/48903)
6. **HiCache + Mooncake cache hit-rate dip** under L2/L3 capacity pressure (87.4% → sharp drop mid-benchmark). — [Issue #47481](https://github.com/sgl-project/sglang/issues/47481)
7. **Closed/resolved today**: `fast_topk_v2` returning wrong top-k sets when a radix bucket exceeds its 4096-entry buffer at k=2048 ([#36807](https://github.com/sgl-project/sglang/issues/36807)); PP-disaggregated prefill deadlock under abort storms, RCA + fix series ([#34572](https://github.com/sgl-project/sglang/issues/34572)); spec-decode OOM mid-traffic with large draft windows and no upfront capacity check ([#38341](https://github.com/sgl-project/sglang/issues/38341)).
8. **CI health**: tracker reports **1 broken, 4 flaky, 1164 recently fixed** — [Issue #17050](https://github.com/sgl-project/sglang/issues/17050); flaky/CI-infra tracker at [Issue #42752](https://github.com/sgl-project/sglang/issues/42752); CUDA coredump auto-tracker at [Issue #26340](https://github.com/sgl-project/sglang/issues/26340).

---

## 6. What This Means for Application Developers

- **Kimi-K3.1 is imminent.** If you're planning a K3.1 rollout, budget for PD disaggregation and DFlash rather than single-node TP — the Day-0 stack assumes both.
- **Audit reasoning-tag handling now.** Issue #48791 affects any model whose CoT can quote `</think>` (tool-use traces, tag-processing tasks). Strip or re-parse `reasoning_content` defensively until fixed.
- **Don't trust gateway-transparent params.** `cache_salt` is dropped on chat completions via `sgl-model-gateway` (#48793), which silently breaks per-tenant prefix-cache isolation. Verify routing if you depend on cache salting for multi-tenant privacy.
- **Check FP8 KV-cache quality on long context.** #48749 means the checkpoint's FP8 KV scales may be ignored for several popular model families — measure accuracy before assuming quantized KV is active.
- **Agentic GLM-5.3-Flash needs a workaround** for multi-tool prompts with `tool_choice=auto`; prefer explicit tool choice or reduce tool count until #36669 is resolved.
- **Concurrency introspection is coming.** #38846 proposes exposing the *effective* `max_running_requests` (post mamba/linear-attention state-cache cap) in `/get_server_info` — useful for autoscalers that currently over-provision on hybrid models.
- **Hardware options are widening.** XPU W8A16 and AMD FP8 paths are maturing; if you're cost-optimizing non-Hopper fleets, the MI355X and Xe2 kernel PRs above are the ones to track for benchmark validation.

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp Digest — 2026-11-07

## 1. Today's Highlights

llama.cpp shipped build **b12180** with first-class support for **Kimi-K3.1-Pro**, a hybrid architecture that swaps the full-attention MLA layers for DeepSeek-style sparse attention (DSA) while keeping KDA linear-attention layers. Alongside the model work, the Vulkan backend now detects ReBAR and avoids BAR allocations when unsupported, and the MoE expert cache was rewritten as an LRFU/MQ-style hybrid (`ClassLRU`). The day's issue traffic was dominated by Intel Vulkan correctness problems and a server-side prompt-cache isolation bug that deserves attention from anyone running multi-tenant `llama-server`.

## 2. Releases & Breaking Changes

Ten builds landed (b12170–b12180). No breaking API or config changes; all user-facing changes are additive.

- **b12180** — `model: add Kimi-K3.1-Pro text model` ([#31779](https://github.com/ggml-org/llama.cpp/pull/31779))
- **b12179** — `hexagon: conv_2d optimizations and support for conv_3d` ([#31814](https://github.com/ggml-org/llama.cpp/pull/31814))
- **b12177** — `cli: fix crash on invalid UTF-8 in prompt or files` ([#31563](https://github.com/ggml-org/llama.cpp/pull/31563)) — upgrade if you feed untrusted or non-UTF-8 text through `llama-cli`
- **b12176** — `tests: qualify isnan and signbit with std::` ([#31791](https://github.com/ggml-org/llama.cpp/pull/31791)) — NetBSD/gcc 14.4 build fix
- **b12175** — `tests: override hardware_concurrency in CI` ([#31797](https://github.com/ggml-org/llama.cpp/pull/31797))
- **b12174** — `cmake: generate RC file directly without win-app.rc.in` ([#31718](https://github.com/ggml-org/llama.cpp/pull/31718))
- **b12173** — `opencl: support repeat for f16, i32, i16, bf16` ([#31782](https://github.com/ggml-org/llama.cpp/pull/31782))
- **b12172** — `webgpu: add batched tensor API (get_tensor_n / set_tensor_n)` ([#31473](https://github.com/ggml-org/llama.cpp/pull/31473)) — additive WebGPU binding surface
- **b12171** — `vulkan: detect ReBAR support, avoid BAR allocations if unavailable` ([#31658](https://github.com/ggml-org/llama.cpp/pull/31658))
- **b12170** — `moe-cache: LRFU/MQ-like hybrid cache, ClassLRU` ([#31233](https://github.com/ggml-org/llama.cpp/pull/31233))

## 3. New Model & Hardware Support

- **Kimi-K3.1-Pro** — hybrid K3 variant: 24 of 93 layers use a lightning indexer (32 heads × 128, `top_k` 2048, no RoPE) in place of full-attention MLA; the top-k mask implementation was extracted from `build_attn` for reuse. ([#31779](https://github.com/ggml-org/llama.cpp/pull/31779))
- **Hexagon: CONV_3D** — new `HTP_OP_CONV_3D` wired through `htp-ops.h`, `main.c` and the op remap in `htp-kparams.cpp`, running on the existing HMX tiling path. ([#31814](https://github.com/ggml-org/llama.cpp/pull/31814))
- **Vulkan: ReBAR-aware allocation** — BAR allocations are skipped on hardware/drivers without resizable BAR, which directly targets the slow-token-generation reports on AMD dGPUs with ReBAR disabled ([#27097](https://github.com/ggml-org/llama.cpp/issues/27097), now closed). ([#31658](https://github.com/ggml-org/llama.cpp/pull/31658))
- **OpenCL** — `repeat` extended to f16, i32, i16 and bf16, closing another op-coverage gap for non-f32 paths. ([#31782](https://github.com/ggml-org/llama.cpp/pull/31782))
- **HIP/RDNA4 documentation** — gfx1200/gfx1201 added to the supported `GPU_TARGETS` list. ([#26745](https://github.com/ggml-org/llama.cpp/pull/26745))
- **External PLE (engram) loading** — new `--shard` argument plus conversion support for PLE-less and PLE-only GGUFs; still in review. ([#31185](https://github.com/ggml-org/llama.cpp/pull/31185))
- **Server UI** — Discover now surfaces audio capability for models whose mmproj carries an audio encoder (Gemma 4, LFM 3 Omni). ([#31837](https://github.com/ggml-org/llama.cpp/pull/31837))

## 4. Performance & Optimization

- **Gemma 4 E2B/E4B graph optimizations** — unused prompt tokens are dropped before KV-shared layers, eliminating compute for layers that do not contribute to the KV cache. Author reports "massive gains on pp." ([#31356](https://github.com/ggml-org/llama.cpp/pull/31356))
- **MoE cache redesign (`ClassLRU`)** — LRFU/MQ-inspired hybrid eviction replacing the prior policy, with the clock advanced by real token counts. ([#31233](https://github.com/ggml-org/llama.cpp/pull/31233))
- **Hexagon: post-norm residual fusion** — `ADD(MUL(RMS_NORM(x), w), r)` fused into `RMS_NORM_MUL`, removing one op launch and one DDR round trip twice per layer. ([#31822](https://github.com/ggml-org/llama.cpp/pull/31822))
- **HIP: fixed 32 MB BLAS workspace** — avoids rocBLAS allocating scratch per call; `test-backend-ops -o SOLVE_TRI` went from `HSA_STATUS_ERROR_MEMORY_FAULT` to 24/24 passing across 5 consecutive runs. ([#31838](https://github.com/ggml-org/llama.cpp/pull/31838))
- **CUDA NVFP4 quantization** — Blackwell-native FP4 MMA at Q4_0 size with Q3_K-tier accuracy; measured **+40% PP** (≈20% on longer prompts). ([#23572](https://github.com/ggml-org/llama.cpp/pull/23572))
- **Vulkan: `readonly` on decodeBuf** — preparation for a driver-side perf win. ([#31833](https://github.com/ggml-org/llama.cpp/pull/31833))
- **KleidiAI SME2 for CPU flash attention** — routes eligible `FLASH_ATTN_EXT` QK/AV tile products through SME2 F32 kernels. ([#29394](https://github.com/ggml-org/llama.cpp/pull/29394))
- **Intel Vulkan FA prefill kernel** — dedicated prefill shader for Intel platforms. ([#29357](https://github.com/ggml-org/llama.cpp/pull/29357))
- **`--prefill-budget-busy`** — new server flag to bound prefill work while the server is under load. ([#30992](https://github.com/ggml-org/llama.cpp/pull/30992))
- **Checkpoint copy elimination** — existing checkpoints are reused instead of re-copied. ([#30683](https://github.com/ggml-org/llama.cpp/pull/30683))

## 5. Stability & Regressions

Ranked by severity:

1. **Prompt cache cross-conversation contamination (server, correctness/privacy).** `server_prompt_cache` behind `--cache-ram` and `--cache-idle-slots` can restore KV state from an unrelated, already-finished conversation into a slot serving a new request under concurrent load. Open, no fix PR. ([#27148](https://github.com/ggml-org/llama.cpp/issues/27148))
2. **Vulkan `KHR_coopmat` nondeterministic wrong results on Intel Arc Pro B70 (BMG G31, Mesa 26.2.5).** Same input yields a different answer on every call for a BERT-style model, diverging substantially from the CPU reference. Open. ([#31560](https://github.com/ggml-org/llama.cpp/issues/31560))
3. **Tokenizer SIGSEGV — stack overflow in `unicode_regex_split_stl`.** The o200k/GPT-4o pre-tokenizer regex recurses once per character, so a ~26,000-character unbroken run of one character class kills `llama-server`. Remotely triggerable DoS if prompts are user-controlled. Open. ([#27783](https://github.com/ggml-org/llama.cpp/issues/27783))
4. **`draft-dflash` (DFlash2) abort in `post_decode` `seq_rm`** on hybrid Qwen3.8-27B with `--parallel 8`. Open. ([#31656](https://github.com/ggml-org/llama.cpp/issues/31656))
5. **CUDA illegal memory access on GLM-5.3-Flash long prefill** at `-ub 2048` on Blackwell `sm_120`; reproduced only on the unmerged PR branch. ([#28282](https://github.com/ggml-org/llama.cpp/issues/28282))
6. **Vulkan/ANV: FA falling back to the SCALAR path** causes O(N²) prompt-processing degradation and device loss on Intel Arc B580. Open. ([#27638](https://github.com/ggml-org/llama.cpp/issues/27638))
7. **gfx1201 FA dispatch threshold regression** — head-size-256 WMMA threshold too low, ~20% speculative-decoding regression since #28102. Open. ([#28867](https://github.com/ggml-org/llama.cpp/issues/28867))
8. **SYCL dual Arc Pro B60 stall** — `--cpu-moe -sm layer` with a 40,000 MiB MoE cache is ~100–1000× slower than single-GPU; 15,000 MiB completes. Open. ([#31510](https://github.com/ggml-org/llama.cpp/issues/31510))
9. **Memory leak** reported on aarch64/CUDA builds. Open. ([#27725](https://github.com/ggml-org/llama.cpp/issues/27725))
10. **iGPU: CPU-resident layers allocate through the Vulkan host buffer**, consuming device memory. Open. ([#31538](https://github.com/ggml-org/llama.cpp/issues/31538))
11. **MTP performance drop** with `n-max > 1` and `-np > 1` under spec decoding + Vulkan. Open. ([#27544](https://github.com/ggml-org/llama.cpp/issues/27544))

Fixed / closed today: ReBAR-disabled Vulkan slowdown ([#27097](https://github.com/ggml-org/llama.cpp/issues/27097), addressed by b12171); `draft-mtp` determinism change on Qwen3.6 MTP ([#23335](https://github.com/ggml-org/llama.cpp/issues/23335)); CUDA `-cmoe` illegal memory access on Turing sm_75 at 94 tokens ([#28241](https://github.com/ggml-org/llama.cpp/issues/28241)); Qwen3-VL vision-embedding precision-floor noise ([#29251](https://github.com/ggml-org/llama.cpp/issues/29251)); `--cache-disk` context offloading request closed ([#20697](https://github.com/ggml-org/llama.cpp/issues/20697)).

In-review fixes worth tracking: JSON-schema `allOf` over plain types producing an empty-object grammar ([#31834](https://github.com/ggml-org/llama.cpp/pull/31834), fixes [#31805](https://github.com/ggml-org/llama.cpp/issues/31805)); per-prompt-cache LoRA setting tracking to avoid restoring KV incompatible with the new task ([#30275](https://github.com/ggml-org/llama.cpp/pull/30275)); `--cache-reuse` restoring stale checkpoints after a mid-prompt chunk deletion ([#30683](https://github.com/ggml-org/llama.cpp/pull/30683)).

## 6. What This Means for Application Developers

- **New frontier model, same binary.** Kimi-K3.1-Pro runs on stock `llama.cpp` as of b12180 — but its hybrid DSA/KDA layout is new code, so validate output quality against a reference implementation before putting it in a production route.
- **Pin your build if you serve multiple tenants.** The `--cache-ram` / `--cache-idle-slots` isolation bug ([#27148](https://github.com/ggml-org/llama.cpp/issues/27148)) can leak prior conversation context into a fresh slot. Until it's fixed, either disable those flags or run one process per tenant.
- **Sanitize long prompts.** The o200k tokenizer stack overflow ([#27783](https://github.com/ggml-org/llama.cpp/issues/27783)) is reachable from a single API call; cap unbroken character runs or add a request-size guard at the gateway.
- **Vulkan on Intel is not production-safe yet.** Nondeterministic coopmat results ([#31560](https://github.com/ggml-org/llama.cpp/issues/31560)) and the SCALAR FA fallback cliff ([#27638](https://github.com/ggml-org/llama.cpp/issues/27638)) argue for `-DGGML_VULKAN_COOPMAT=OFF` or a non-Vulkan backend on Arc hardware.
- **ReBAR detection removes a silent perf trap.** b12171 means AMD Vulkan users no longer need to know their BAR configuration to get sane token-generation speed.
- **Structured output gets more reliable.** The `allOf` grammar fix ([#31834](https://github.com/ggml-org/llama.cpp/pull/31834)) addresses schemas that silently degraded to `{}` — relevant for tool-calling and agent frameworks relying on JSON-schema-constrained decoding.
- **Server roadmap is moving toward serving-grade features.** Disaggregated prefill/decode ([#21266](https://github.com/ggml-org/llama.cpp/issues/21266), 35 comments), `--prefill-budget-busy` ([#30992](https://github.com/ggml-org/llama.cpp/pull/30992)), per-cache-entry LoRA tracking ([#30275](https://github.com/ggml-org/llama.cpp/pull/30275)) and a file upload API ([#23948](https://github.com/ggml-org/llama.cpp/issues/23948)) all point to `llama-server` becoming a viable multi-tenant gateway rather than a single-user demo.

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama Digest — 2026-11-07

## 1. Today's Highlights

No new releases landed in the last 24 hours, but activity was heavy on the runtime side: the MLX backend picked up a long-awaited concurrency PR (#19197) that lets MLX models serve parallel generations via `OLLAMA_NUM_PARALLEL`, and a scheduler fix (#19581) targets a VRAM-leak path where `ollama serve` fails to reap an idle `llama-server` runner. The dominant theme, however, is **Vulkan backend instability** across AMD and Intel iGPUs on the 0.40.x `llama-server`-based builds, with three separate crash reports (AMD Renoir, Intel Lunar Lake, AMD Navi 14) all updated today.

---

## 2. Releases & Breaking Changes

**None.** No releases in the last 24 hours.

Context worth flagging for anyone pinning versions: multiple issues updated today attribute behavior changes to the **0.40.x transition to a `llama-server`-based runner** — #19158 explicitly notes `gemma4:12b` "worked on 0.34.4" and broke on 0.40.x, and #19558 reports crashes on both 0.40.14 stable and 0.40.15-rc2. Treat 0.40.x as a migration boundary for GPU backend selection, not a routine bump.

- [Issue #19158](https://github.com/ollama/ollama/issues/19158) — regression introduced by 0.40.x
- [Issue #19558](https://github.com/ollama/ollama/issues/19558) — affected versions enumerated

---

## 3. New Model & Hardware Support

| Item | Status | Notes |
|---|---|---|
| [PR #19580](https://github.com/ollama/ollama/pull/19580) — `windows: add CUDA 13.5 and SM 103 for ARM64` | **CLOSED/merged** | Adds CUDA 13.5 + SM 103 for Windows ARM64; retains SM 121 support; other platforms unchanged. Expands usable NVIDIA hardware on ARM64 Windows. |
| [PR #18078](https://github.com/ollama/ollama/pull/18078) — `MLX: make Qwen3.8 Flash Next memory efficient` | **CLOSED** | Replaces blanket NVFP4 quantization with a mixed scheme: QSA projections and the non-expert MTP path kept in BF16, MXFP8 for other eligible dense/control weights. Published as `qwen3.8-flash-next:125b-a6b-nvfp4` and `...:125b-mlx`. |
| [Issue #19566](https://github.com/ollama/ollama/issues/19566) — MLX support for dense Nemotron / Luciole Safetensors | **OPEN, feature request** | v0.40.15 rejects direct Safetensors→MLX conversions with `unsupported architecture: NemotronForCausalLM`. The MLX runner registers **Nemotron-H but not dense Nemotron** (affects Luciole-1B and 23B). Reporter offers to contribute the patch. |

**Hardware gaps surfaced today (all Vulkan):** AMD Renoir `gfx90c` (#19558), Intel Arc 130V / Lunar Lake (#19158), AMD RX 5500 / Navi 14 `gfx1010` (#19589 — `--list-devices` fails with SIGBUS).

---

## 4. Performance & Optimization

- **[PR #19197](https://github.com/ollama/ollama/pull/19197) — `mlx: support concurrent generation`** *(OPEN, updated today)*. Lets MLX models serve multiple concurrent generations through `OLLAMA_NUM_PARALLEL`, cutting time-to-first-token under load. Ready requests share projection and feed-forward compute; attention is kept per-request. Default remains 1 request — parallelism costs memory per active request. **This is the most significant perf change in flight.**
- **[PR #19585](https://github.com/ollama/ollama/pull/19585) — `llm: handle split memory log lines`** *(OPEN, fixes #19204)*. `memoryParsingWriter.Write()` parsed each write independently, so log lines split across writes failed regex matching, producing **inaccurate memory and VRAM accounting**. Buffers incomplete lines between writes. Directly affects scheduler load decisions.
- **[PR #19581](https://github.com/ollama/ollama/pull/19581) — `sched: keep a runner we couldn't stop in the loaded set`** *(OPEN, fixes #19577)*. Correctness fix with a perf consequence: a leaked runner's VRAM was treated as free, so the next load under-predicted available memory, **tilted toward CPU offload, and spawned a second `llama-server`**. Fixes the accounting chain that degrades throughput after a failed unload.
- **[PR #18078](https://github.com/ollama/ollama/pull/18078)** (see above) — targeted quantization to avoid the long-generation quality degradation seen with blanket NVFP4.

---

## 5. Stability & Regressions

Ranked by severity:

1. **GPU falls off the bus (Xid 79) under grammar-constrained generation** — [Issue #17026](https://github.com/ollama/ollama/issues/17026) *(OPEN)*. `cuda_v13` backend, RTX 4070 Ti SUPER, Ollama 0.30.10: long `format`-constrained generations crash with `CUDA error: unspecified launch failure` escalating to Xid 79. Same requests are fine on `cuda_v12`. No fix PR visible. **Highest severity — requires host/GPU reset.**
2. **Vulkan crash at model load on AMD Renoir iGPU** — [Issue #19558](https://github.com/ollama/ollama/issues/19558) *(OPEN, filed today)*. `llama-server` exits with `0xe06d7363` (unhandled C++ exception) on Ryzen 4700U / gfx90c, **for every model including ~380 MB ones**, on 0.40.14 stable and 0.40.15-rc2. Vulkan backend is effectively unusable on this class of hardware.
3. **Vulkan `device lost` on Intel Arc 130V since 0.40.x** — [Issue #19158](https://github.com/ollama/ollama/issues/19158) *(OPEN)*. `gemma4:12b` dies with `ggml_vulkan: device lost on Vulkan0` immediately after tensor upload; regression from 0.34.4 with any GPU offload.
4. **Idle runner never reaped → VRAM leak** — [Issue #19577](https://github.com/ollama/ollama/issues/19577) *(OPEN, filed today)*. At `keep_alive` expiry, `ollama serve` ignores the failed `Close`, marks the model unloaded, drops the runner from the scheduler, and **logs nothing**. The runner keeps all its VRAM; the next load sees too little free memory. **Fix PR exists: [#19581](https://github.com/ollama/ollama/pull/19581).**
5. **AMD RX 5500 (Navi 14) undetected; SIGBUS on Vulkan** — [Issue #19589](https://github.com/ollama/ollama/issues/19589) *(OPEN)*. `llama-server --list-devices` fails with `signal: bus error` (exit 135) under `GGML_BACKEND` Vulkan. Report includes a proposed fix.
6. **Intermittent 500 on `/api/chat` after a long streamed reply** — [Issue #19293](https://github.com/ollama/ollama/issues/19293) *(OPEN)*. `llama-server apply-template error … write: broken pipe`, raised 2–3 s after the previous reply completes; runner stays healthy and an immediate retry succeeds. Affects 0.40.8 and 0.40.11 on macOS.
7. **`ornith:35b` renderer/parser leaks raw special tokens and tool-call XML** — [Issue #16992](https://github.com/ollama/ollama/issues/16992) *(OPEN)*. Unparsed tool-call XML and special tokens reach OpenAI-/Anthropic-compatible clients. Related parser fixes in flight: [#19574](https://github.com/ollama/ollama/pull/19574) (stray `<channel|>` close tags in Gemma4Parser), [#19590](https://github.com/ollama/ollama/pull/19590) (whitespace after `</think>` in deepseek3), [#17021](https://github.com/ollama/ollama/pull/17021) (nesting depth for qwen3-coder tool-call close tags), [#19588](https://github.com/ollama/ollama/pull/19588) (`tool_result` blocks missing `ToolName` on `/v1/messages`).
8. **`ollama pull` TLS handshake timeout on post-quantum-hostile networks** — [Issue #19430](https://github.com/ollama/ollama/issues/19430) *(OPEN)*. Networks that silently drop TLS 1.3 ClientHellos carrying `X25519MLKEM768` cause `net/http: TLS handshake timeout` against `registry.ollama.ai`. Relevant for locked-down corporate/edge egress.
9. **`OLLAMA_NUM_PARALLEL` + multimodal → `failed processing images`** — [Issue #4165](https://github.com/ollama/ollama/issues/4165) *(CLOSED today after 24 comments)*. Long-standing interaction between parallel slots and vision models (`llava`, `moondream`) is now resolved/closed. Verify against your pinned version before relying on parallel multimodal serving.

**Non-technical:** [Issue #19591](https://github.com/ollama/ollama/issues/19591) reports a duplicate $20 Stripe renewal on a cancelled account. Out of scope for infrastructure, but worth escalating to billing/support rather than the issue tracker.

---

## 6. What This Means for Application Developers

- **Do not assume Vulkan works on iGPUs.** Three independent crash classes (AMD Renoir, Intel Arc, AMD Navi 14) are open against 0.40.x. If you ship to consumer laptops or home-lab boxes, gate on backend detection and provide a CPU fallback; the Renoir case fails even on ~380 MB models, so "small model" is not a mitigation.
- **Pin to a known-good version and backend, not just a version.** `cuda_v13` and `cuda_v12` diverge on the same hardware under constrained decoding (#17026). If you use `format`/JSON-schema-constrained generation heavily, prefer `cuda_v12` until #17026 is addressed.
- **Make `/api/chat` retries idempotent-safe.** The post-reply 500 in #19293 is transient and retry-succeeds — a single retry with backoff is a cheap, effective mitigation. Don't surface it to users as a hard failure.
- **Treat tool-call parsing as untrusted output.** #16992 plus the four parser PRs landing today show the renderer/parser layer still leaks raw control tokens and malformed tool-call XML into client-visible streams. Validate tool-call payloads before executing them, and don't assume OpenAI/Anthropic compatibility is byte-exact for structured output.
- **Memory accounting is currently unreliable — don't trust reported VRAM.** The combination of #19577 (silent leaked runner) and #19585 (split log lines break memory parsing) means `/api/ps` and scheduler decisions can be wrong. If you run multi-model workloads or long `keep_alive`, monitor GPU memory out-of-band.
- **Parallelism is expanding beyond the llama.cpp backend.** MLX models gain `OLLAMA_NUM_PARALLEL` support via #19197 (default still 1). Budget VRAM per concurrent request before raising it, and note that the multimodal + parallel path was only just closed (#4165) — retest rather than assume.
- **Watch CUDA 13.5 / SM 103 on Windows ARM64** (#19580) if you're targeting Snapdragon-class dev machines or ARM64 CI runners with discrete NVIDIA GPUs.

---

*Generated from github.com/ollama/ollama — 14 issues and 50 PRs updated in the last 24h; 0 releases.*

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM Digest — 2026-11-07

Source: [github.com/BerriAI/litellm](https://github.com/BerriAI/litellm)

## 1. Today's Highlights

The 1.108.x LTS line got a patch ([v1.108.2](https://github.com/BerriAI/litellm/releases)) and an encrypted-reasoning round-trip backport ([#50456](https://github.com/BerriAI/litellm/pull/50456)) aimed at `/v1/messages` sessions on `chatgpt/` models that were restarting reasoning on every tool turn. The bulk of activity is a sustained cleanup of **streaming tool-call correctness** — argument fragments, per-call indices, and server-side tool blocks — alongside router cooldown/retry accounting fixes. On the routing/ops side, prompt-storage opt-outs per key/team ([#50135](https://github.com/BerriAI/litellm/pull/50135)) and a long-standing Gemini pass-through key leak ([#46844](https://github.com/BerriAI/litellm/pull/46844)) are in review.

## 2. Releases & Breaking Changes

- **[v1.108.2 (LTS)](https://github.com/BerriAI/litellm/releases)** — new LTS patch. The `lts` image tag tracks the newest LTS patch, so `ghcr.io/berriai/litellm:lts` picks this up automatically. Release artifacts are cosign-signed and verifiable against the repo's `cosign.pub` at commit `e82a3c6`.
- **[#47762 — build(deps)!: move uv.lock and the Docker image to OpenAI SDK 3](https://github.com/BerriAI/litellm/pull/47762)** (open, `risk:low` label but flagged breaking) — bumps the SDK from 2.33.0 to 3 and carries the transport fix from #44993. This is the item to watch before your next image rebuild; pin your image tag if you cannot absorb an SDK major.
- **[#50135 — spend-logs: turn off prompt storage per key or team](https://github.com/BerriAI/litellm/pull/50135)** (open) — new config surface. Key- or team-level opt-out overrides the global setting; either level can disable storage, and a key/client header can further suppress it. Behavior change for anyone relying on global-only semantics.

## 3. New Model & Hardware Support

- **[#50439 — feat(nvidia_nim): refresh the catalog and discover upstream models](https://github.com/BerriAI/litellm/pull/50439)** (open) — loads model choices from the selected NIM connection and refreshes the bundled catalog so fallback resolution finds newer NIM models that were missing from Add Model.
- **[#50491 — feat(cost-map): move `-latest` model aliases into the cost map](https://github.com/BerriAI/litellm/pull/50491)** (open) — consolidates aliases from the separate `litellm/model_latest_aliases.json` into the cost map so alias targets and prices are maintained together.
- **[#33921 — native support for Kimi K3 / Inkling / Tinker](https://github.com/BerriAI/litellm/issues/33921)** closed as stale-adjacent after ~4 months. No landed implementation is visible in this window; treat these as still requiring a custom `openai`-compatible provider entry.
- No new accelerator backend, quantization format, or architecture work appeared in the last 24h.

## 4. Performance & Optimization

- **[#47096 — SAP GenAI Hub `deployment_url` re-fetched on every LLM call](https://github.com/BerriAI/litellm/issues/47096)** (open) — each `acompletion()` with a `sap/` model makes two blocking HTTP GETs (`/v2/lm/deployments`, `/v2/lm/configurations/{id}`) to AICORE, measured at **~870ms overhead per call**. Highest-impact latency bug in the current queue for SAP users.
- **[#50493 — feat(mcp): opt-in per-server memory of upstream tool output schemas](https://github.com/BerriAI/litellm/pull/50493)** (open) — a call with no session id currently forces a full upstream tool listing just to check one result; caching output schemas per server removes that round trip.
- **[#50492 — refactor(bedrock): select native structured output from the by-default flag only](https://github.com/BerriAI/litellm/pull/50492)** (open) — collapses the opt-in switching rules so Bedrock Claude uses native structured output on every structured request, falling back to the JSON tool only on schema rejection. Fewer conversion paths, less per-request branching.
- **[#47822 — `httpx.Timeout` connect timeout ignored for non-OpenAI providers](https://github.com/BerriAI/litellm/issues/47822)** (open) — `supports_httpx_timeout()` returns `False` for everything but OpenAI/Azure/Bedrock, so `connect=1.0` collapses into a flat 10s. This silently inflates tail latency on dead connections to Mistral and similar providers.

## 5. Stability & Regressions

Ranked by severity:

1. **[#48145 — JSON-defined openai-like providers lose tools when model is not in catalog](https://github.com/BerriAI/litellm/issues/48145)** (OPEN, `bug`/`llm translation`) — **regression** introduced between 1.81 and 1.106: tool definitions are silently dropped for models like `nano-gpt/z-ai/glm-5.3-flash` unless declared as `openai`. Silent capability loss for agents. No fix PR linked yet.
2. **[#47046 — Vertex/Gemini context caching reorders the conversation](https://github.com/BerriAI/litellm/issues/47046)** (OPEN) — a `cache_control` block that is not a prefix gets moved ahead of history and `modify_params` drops `system_instruction`, so the model answers the *previous* user turn. Correctness bug with no visible error.
3. **[#47924 — `reasoning_effort="none"` omits thinking instead of disabling it](https://github.com/BerriAI/litellm/issues/47924)** (OPEN, 1 👍) — on default-on Claude models, removing the thinking config leaves the upstream default in effect, so "none" costs more than asked.
4. **[#47577 — Gemini `_build_vertex_schema` + streaming drops `native_finish_reason` on mid-stream `MALFORMED_FUNCTION_CALL`](https://github.com/BerriAI/litellm/issues/47577)** (OPEN) — trailing tool calls silently dropped on both `/v1/messages` and `/v1/chat/completions`.
5. **[#50248 — `COHERE_API_BASE` ignored for cohere embeddings](https://github.com/BerriAI/litellm/issues/50248)** (OPEN) — always hits hardcoded `api.cohere.ai/v2/embed`; the rerank path honors the same variable, so private-endpoint deployments break only on embeddings.
6. **[#50317 — daily tag spend lost after Redis dequeue if writer exits before DB commit](https://github.com/BerriAI/litellm/issues/50317)** (OPEN) — billing data loss window between Redis buffer and PostgreSQL commit; reproduced with two writer processes.
7. **[#45584 — Langfuse callback drops traces for `/v1/systemone` and `/v1/decisions`](https://github.com/BerriAI/litellm/issues/45584)** (OPEN) — `DecisionsUsage` has no `.get`, trace silently discarded.
8. **[#36280 — `pass_through_endpoints` with `include_subpath` rejected for virtual keys](https://github.com/BerriAI/litellm/issues/36280)** (OPEN) — wildcard route is registered but classified as a management route, returning 401 to all non-admin keys.

**Fixes landed or in review:**
- **[#50470](https://github.com/BerriAI/litellm/pull/50470)** — preserves streamed tool argument fragments verbatim; fixes corrupted structured output when a fragment like `5e7650…` is parsed as standalone JSON.
- **[#49183](https://github.com/BerriAI/litellm/pull/49183)** — per-tool-call `index` for `ollama_chat/`, which previously glued multiple calls' arguments into one.
- **[#50445](https://github.com/BerriAI/litellm/pull/50445)** — records retries and fallback hops after a request's first logged failure, so `allowed_fails`/cooldown/RPM actually see repeated 429s.
- **[#50432](https://github.com/BerriAI/litellm/pull/50432)** — skips cooldown on 404 for single-deployment groups; one bad request shape no longer locks everyone out.
- **[#50489](https://github.com/BerriAI/litellm/pull/50489)** — releases *redacted* buffered stream content; previously masked text could leak in responses and chat deltas.
- **[#50488](https://github.com/BerriAI/litellm/pull/50488)** — preserves routing tags through pre-call guardrails, which were hiding them and silently selecting the default deployment.
- **[#46844](https://github.com/BerriAI/litellm/pull/46844)** — Gemini pass-through sends the key in `x-goog-api-key` instead of `?key=`, closing a URL-logging credential exposure.

Closed this window: [#25869](https://github.com/BerriAI/litellm/issues/25869) (Gemini `stream_chunk_builder` corrupting `server_side_tool_invocations`/`thought_signatures`), [#48384](https://github.com/BerriAI/litellm/issues/48384) (Anthropic `server_tool_use` returned as a pending `tool_call`, causing agent loops), [#47396](https://github.com/BerriAI/litellm/issues/47396) (budget reservation priced at `max_input_tokens` on `/v1/messages` with Anthropic `tool_choice`), [#35570](https://github.com/BerriAI/litellm/issues/35570), [#46999](https://github.com/BerriAI/litellm/issues/46999), [#29638](https://github.com/BerriAI/litellm/issues/29638), [#49038](https://github.com/BerriAI/litellm/issues/49038).

## 6. What This Means for Application Developers

- **Audit any model you declare as a JSON-defined `openai`-like provider.** [#48145](https://github.com/BerriAI/litellm/issues/48145) means tools can be dropped without an error if the model string isn't in the catalog — verify `tool_calls` actually arrive before shipping an agent on a custom provider entry.
- **Streaming + tools is the riskiest surface right now.** The highest-value fixes ([#50470](https://github.com/BerriAI/litellm/pull/50470), [#49183](https://github.com/BerriAI/litellm/pull/49183)) are still open; if you stream structured output or multi-tool responses through Anthropic/Vertex/Bedrock/Ollama, pin your version and test argument reassembly explicitly.
- **Don't rely on `reasoning_effort: "none"` to cut cost on Claude.** [#47924](https://github.com/BerriAI/litellm/issues/47924) leaves upstream thinking enabled — set the provider-native thinking-disable parameter instead until it lands.
- **If you use `chatgpt/` behind `/v1/messages`,** the encrypted-reasoning backport ([#50456](https://github.com/BerriAI/litellm/pull/50456)) is the one to track on the LTS branch; without it every tool turn re-reasons from scratch, inflating both latency and token spend.
- **For SAP AI Core deployments,** budget ~870ms of per-call overhead ([#47096](https://github.com/BerriAI/litellm/issues/47096)) until `deployment_url` is cached — relevant for any tight agent loop.
- **Plan for the OpenAI SDK 3 bump** ([#47762](https://github.com/BerriAI/litellm/pull/47762)) in your image build pipeline; it carries a transport fix but is a dependency major.

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth Digest — 2026-11-07

**Scope note:** the activity in this window is dominated by **Unsloth Studio / Desktop** (the local inference + fine-tuning app), not the core `unsloth` training library. Release notes for v0.1.916-beta were truncated in the source data.

---

## 1. Today's Highlights

Unsloth shipped **v0.1.916-beta ("Large Performance Fixes")**, targeting long-chat typing/search latency, image and video load times, and adding **two-GPU image/video generation** plus per-chat browsers — the release is a client-side responsiveness push on top of continued Glass-theme work. In parallel, `@danielhanchen` landed/opened a cluster of diffusion-path fixes: returning freed heap to the OS after image loads ([#16934](https://github.com/unslothai/unsloth/pull/16934)) and eliminating redundant Triton autotuning on warm starts, worth **~9.5 s of a 22 s first SDXL render on an L4** ([#16981](https://github.com/unslothai/unsloth/pull/16981)). The counterweight is a set of still-open Windows/GPU regressions: a reproducible **Krea 2 Turbo load crash** ([#16892](https://github.com/unslothai/unsloth/issues/16892)), **DiffusionGemma multi-GPU abort above ~1792 tokens** ([#16925](https://github.com/unslothai/unsloth/issues/16925)), and image generation silently falling back to CPU on an RTX 3060 ([#16803](https://github.com/unslothai/unsloth/issues/16803)).

Repo velocity: 42 issues and 213 PRs updated in the last 24 h.

---

## 2. Releases & Breaking Changes

- **v0.1.916-beta — "Large Performance Fixes"**
  Faster typing and search in long chats; faster image/video loads; two-GPU image/video gen; separate browser per chat; floating browser window; additional Glass theme optimizations. No documented API/config breakage.
  https://github.com/unslothai/unsloth/releases

- **`transformers` 5.23 checkpointing rename (tracked, issue closed)** — `gradient_checkpointing` → `activation_checkpointing`. Old names persist as aliases until v6, but `Trainer` now reads only the new names, changing behavior in **4 call sites** in this repo (audited at `f5adb36642`). Anyone pinning `transformers` should pin `<5.23` or patch.
  https://github.com/unslothai/unsloth/issues/16667

- **MCP SDK 2.3.0 incompatibility (resolved)** — Blender MCP failed to start under v0.1.915-beta with MCP SDK 2.3.0 / FastMCP 4.1.6; closed during this window.
  https://github.com/unslothai/unsloth/issues/16878

---

## 3. New Model & Hardware Support

- **LFM3-Audio-2B** — now trainable in Studio, chattable post-training, and exportable as LoRA, merged 16-bit, and GGUF. Fixes a `TokenizersBackend has no attribute ...` failure at first-batch build.
  https://github.com/unslothai/unsloth/pull/16973
- **Cosmos3 (Edge + Nano)** — 256p generation, NVIDIA's published frame ranges, and a per-model frame-rate picker; **Nano video continuation** (extend a clip from its last or first frames) stacked on top.
  https://github.com/unslothai/unsloth/pull/16737 · https://github.com/unslothai/unsloth/pull/16741
- **Two-GPU image/video generation** — shipped in v0.1.916-beta (see caveat in §5 for multi-GPU text/diffusion models).
- **Unified-memory quant fit badges** — request to make fit badges visible/accurate on unified-memory laptops (Apple Silicon class); open.
  https://github.com/unslothai/unsloth/issues/13947
- **Transformer block swap** (layer offload to RAM) — long-standing feature request closed this window.
  https://github.com/unslothai/unsloth/issues/3997
- **Per-model extended parameters** (YaRN context extension without a separate `unsloth run` launch) and **download speed limiting** — both closed.
  https://github.com/unslothai/unsloth/issues/7589 · https://github.com/unslothai/unsloth/issues/15603

---

## 4. Performance & Optimization

**Landed / in review:**

- **Diffusion warm-start autotune caching** — a compiled diffusion load with a warm compile cache re-benchmarked every multi-config Triton reduction on each Studio reopen. On **SDXL / L4: ~40 kernels, ~9.5 s of a 22 s first render**, repeated every time. PR reads back the workers' autotune results instead.
  https://github.com/unslothai/unsloth/pull/16981
- **Heap return after image load** — glibc keeps freed chunks below the mmap threshold in arenas, so staging copies stayed counted as used host RAM after weights were pinned/uploaded. PR returns that heap to the OS.
  https://github.com/unslothai/unsloth/pull/16934
- **WebKit restyle cost** — closing any dialog or menu on long chats took **5–7 s** on macOS WebKit and WebKitGTK; opening the browser pane on Linux WebKit took **>20 s**. Root cause: WebKit restyles every element on certain document mutations. Closed; CI gate allowances tightened afterward.
  https://github.com/unslothai/unsloth/pull/16961 · https://github.com/unslothai/unsloth/pull/16978
- **Reduce-motion theme switch** — light/dark flip took **~2.5 s** vs **~230 ms** with Reduce motion off (Windows Desktop), because a transition was started on every element. Fix in review.
  https://github.com/unslothai/unsloth/pull/16977
- **Long-conversation UI lag regression** (web + desktop) — closed.
  https://github.com/unslothai/unsloth/issues/16603

**Open, with measurements:**

- **RAG ingest is batch-of-1 across documents** — `_embed_all` (`core/rag/ingestion.py:166`) is called per document; `_EMBED_BATCH` only batches chunks *within* a document. Measured **~99 ms/chunk** on the batch-of-1 path vs **2.07 ms** batched — a ~48× gap for corpora of small files (notes, docs, wikis).
  https://github.com/unslothai/unsloth/issues/16715

---

## 5. Stability & Regressions

**Critical — crashes / hard aborts**

1. **DiffusionGemma multi-GPU: prompts over ~1792 tokens abort the visual server** — `GGML_ASSERT n_ubatch >= n_tokens`. Reproduced on 2× CUDA (RTX 3060 12 GB sm 8.6 + RTX 4060 Ti 16 GB sm 8.9), Windows 11, Studio API mode. No fix PR yet; effectively caps multi-GPU diffusion prompt length.
   https://github.com/unslothai/unsloth/issues/16925
2. **Krea 2 Turbo fails to load on Windows — `0xc0000005`** access violation in the Studio backend, reproducible via Images → select `unsloth/Krea-2-Turbo`. No fix PR yet.
   https://github.com/unslothai/unsloth/issues/16892
3. **Desktop crash on v0.1.915-beta** (Win 11, RTX 4060 Ti) — closed this window.
   https://github.com/unslothai/unsloth/issues/16816

**High — functional breakage**

4. **`web_search` always returns "No results found."** — the `ddgs` request lacks an `Accept` header (DuckDuckGo returns 202), and `yandex` is missing from the engine allow-list. Any agent relying on Studio's search tool is silently non-functional.
   https://github.com/unslothai/unsloth/issues/16664
5. **`search_conversation` tool disappeared — third occurrence.** Recurring tool-registration regression; no fix PR.
   https://github.com/unslothai/unsloth/issues/16706
6. **Image generation falls back to CPU/RAM on Windows 11 + RTX 3060** while text generation correctly uses the GPU; not reproducible on an M1 Pro. No fix PR.
   https://github.com/unslothai/unsloth/issues/16803
7. **Beta update does not complete** on Desktop.
   https://github.com/unslothai/unsloth/issues/16897

**Medium — correctness**

8. **CLI treats a truncated stream as success** — if Studio's HTTP response ends after a text delta but before a finish reason or `[DONE]`, `unsloth inference` exits 0 with a partial answer and `unsloth chat` stores it as assistant history. Closed; relevant to anyone parsing these streams.
   https://github.com/unslothai/unsloth/issues/16867
9. **S3 dataset filename collisions** — distinct object keys mapped to one local filename, silently pairing transcripts with the wrong recording; manifest shards could overwrite each other. Fix PR closed.
   https://github.com/unslothai/unsloth/pull/16830
10. **S3 audio JSONL prep fails on U+2028/U+2029** inside transcripts — one bad row blocks training. Closed.
    https://github.com/unslothai/unsloth/issues/16721
11. **RAG indexing during embedding-model download fails every file** with `EmbeddingModelDownloadRequiredError`; nothing queues. Closed.
    https://github.com/unslothai/unsloth/issues/16714
12. **`gemma-4-thinking` chat template strips reasoning** — closed.
    https://github.com/unslothai/unsloth/issues/16699

**Low — UI / cosmetic (all closed):** Glass setting not applied consistently ([#16770](https://github.com/unslothai/unsloth/issues/16770)), Glass theme blurs entire background ([#16820](https://github.com/unslothai/unsloth/issues/16820)), wallpaper hidden in active chats by CSS specificity ([#16736](https://github.com/unslothai/unsloth/issues/16736)), model-selection resets across multi-model conversations ([#14506](https://github.com/unslothai/unsloth/issues/14506)).

**Flaky CI:** `Inline images (windows-latest)` failed twice today on unrelated PRs, both Firefox-only, on the same Japanese-filename check; PR adds a bounded grace period and timeout diagnostics.
https://github.com/unslothai/unsloth/pull/16923

---

## 6. What This Means for Application Developers

- **Don't trust a stream that ends without a terminal marker.** The CLI previously treated a truncated response as a successful reply and persisted it as history ([#16867](https://github.com/unslothai/unsloth/issues/16867)). If you proxy Studio's HTTP API, validate for `finish_reason` or `[DONE]` yourself — the same failure mode will surface in your own client code.
- **Search-backed agents are degraded right now.** `web_search` returns empty results unconditionally on current builds ([#16664](https://github.com/unslothai/unsloth/issues/16664)), and `search_conversation` has vanished for the third time ([#16706](https://github.com/unslothai/unsloth/issues/16706)). Pin a known-good version or stub the tool rather than assuming tool-call availability at startup.
- **Multi-GPU diffusion has a hard prompt-length ceiling.** The `n_ubatch >= n_tokens` abort above ~1792 tokens ([#16925](https://github.com/unslothai/unsloth/issues/16925)) means long-prompt image workflows should stay single-GPU or chunk prompts until fixed.
- **RAG ingest cost is dominated by per-document embedding calls, not chunk count** ([#16715](https://github.com/unslothai/unsloth/issues/16715)). For many-small-files corpora, batching at the ingestion layer is the highest-leverage workaround (~48× measured gap).
- **State is moving server-side.** Preferences previously in `localStorage` are being synced per account ([#16912](https://github.com/unslothai/unsloth/issues/16912), [#16958](https://github.com/unslothai/unsloth/pull/16958)); idle-unloaded GGUFs now reload with their original launch settings instead of defaults ([#16927](https://github.com/unslothai/unsloth/pull/16927)). If you depend on deterministic reload behavior, that's a fix — but verify context/KV-cache settings survive idle unload on your build.
- **Claude Pro/Max plans become usable without an API key** via a `claude` CLI-backed Agent connection through Anthropic's Agent SDK ([#16963](https://github.com/unslothai/unsloth/pull/16963)) — worth watching if you're consolidating agent backends behind Studio.
- **Audio fine-tuning is broadening** with LFM3-Audio-2B train → chat → export (LoRA / merged 16-bit / GGUF) in one flow ([#16973](https://github.com/unslothai/unsloth/pull/16973)).
- **Pin `transformers < 5.23`** until the checkpointing rename is fully absorbed ([#16667](https://github.com/unslothai/unsloth/issues/16667)).

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/SnowEagle-Kn/agents-radar).*