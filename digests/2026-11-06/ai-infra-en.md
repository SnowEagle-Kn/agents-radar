# AI Infrastructure Digest 2026-11-06

> Generated: 2026-11-06 02:48 UTC | Projects covered: 6

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## Cross-Project Comparison

# Cross-Project AI Infrastructure Comparison — 2026-11-06

## 1. Ecosystem Overview

Activity is concentrated in two areas: **serving-stack hardening** and **memory/quantization efficiency**. vLLM is dealing with high-severity v0.32.0 regressions while advancing distributed serving, Rust-frontend parity, and RL weight-reload workflows. llama.cpp and Ollama continue local-runtime kernel, backend, and memory work, while LiteLLM is fixing gateway-level streaming, tool-call, and rate-limit correctness. Unsloth is pushing fine-tuning and diffusion/video memory efficiency. SGLang’s digest failed, so it is excluded from qualitative comparisons but noted in the activity table.

## 2. Activity Comparison

Counts are only reported where the source digest provides them; `n/r` = not reported.

| Project | Issues count | PR count | Release status |
|---|---:|---:|---|
| **vLLM** | n/r; many active issues linked | n/r; many active PRs linked | No release in last 24h; v0.32.0 has confirmed regressions |
| **SGLang** | n/a | n/a | Summary generation failed |
| **llama.cpp** | n/r | n/r | Rolling builds landed: b12131–b12143 |
| **Ollama** | n/r | n/r | No release; stable 0.40.14, 0.40.15-rc2 in testing |
| **LiteLLM** | 95 updated | 500 updated | v1.108.1 released with cosign Docker signature verification |
| **Unsloth** | n/r | n/r | No release; active PR queue on Studio, diffusion, video, RAG |

## 3. Model Support Race

**vLLM leads on frontier LLM serving coverage and distributed execution.** It has DeepSeek-V4.1 arbitrary pipeline-stage partitions in flight, MiniMax-M3 context parallelism for prefill/decode, and GB10/DGX Spark SM121 as an emerging target. It is also tracking Nemotron 3.5 ASR Streaming, but that is still a request.

**llama.cpp leads on local/edge architecture and backend breadth.** It added `xing4_0` architecture support, Blackwell MXFP4 W4A8 matmul, WebGPU `MEAN`, Vulkan IQ4_NL, Metal bf16 matmul, and Hexagon backend work. This is broad hardware/quantization coverage rather than datacenter serving.

**Unsloth leads on diffusion/video and fine-tuning hardware residency.** It is adding NVFP4 step protection for B200/GB200/B300-class cards, Lumina-Image-2.0 activation sizing, MiniMax-H3 slab compaction for 16 GiB L4, CUDA 13/cuDNN selection, integrated-GPU GGUF loading, and CPU context parallelism.

**LiteLLM leads on provider/API normalization, not model kernels.** It is adding Bedrock native structured output for Opus 5.5/Fable 5.1/Sonnet 5/Haiku 5.5, OpenRouter video fields for Seedance/MiniMax, Vertex AI Cloud TTS regional endpoints, and Codex release-specific model catalogs.

**Ollama is comparatively behind on new model/hardware support today.** It has Hindsight `hs-decide-1-flash` requests/MLX PR, a Qwen3.5 0.8B quantization request, and qwen35/qwen35moe parallel-serving re-enablement, but Intel Arc and Arrow Lake Vulkan support remain open problems.

## 4. Performance Frontier

Optimization effort is split by layer:

- **vLLM — KV cache, attention, MoE, distributed serving.** Work includes GDN prefill in-place SSM state, TRT-LLM monolithic MoE padding removal on Blackwell, PP handoff over CUDA copy engines, ROCm MXFP8 GEMM retuning, RoPE+KV-cache fusion, tokenizer-pool shallow copies, and direct weight reload for RL.
- **llama.cpp — local kernels, memory mapping, MoE caching.** Checkpoint save/restore now uses ref-counted shared state instead of deep copies; MoE expert cache reports **+6% decode on QFN, +7% on GLM, 8% fewer expert uploads**; Vulkan/Metal/WebGPU kernels continue expanding.
- **Ollama — local runtime memory and scheduling.** MLX Qwen3.8 memory tuning, unified-memory APU scheduling for Strix Halo/gfx1151, and lazy syntax highlighting for app startup. A 25–30% t/s regression report was closed as needs-more-info.
- **LiteLLM — gateway overhead and routing efficiency.** Stream overhead metric work, caller-timeout-bounded retries, rpm/tpm counting fix to avoid premature 429s, prompt-caching affinity for

---

## Per-Project Reports

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM Digest — 2026-11-06

## 1. Today's Highlights

No new releases landed in the last 24h, but the project saw heavy activity across the Rust frontend parity roadmap, Q4 SIG roadmaps, and a cluster of Blackwell/ROCm issues. The most impactful signals today are a **regression in v0.32.0** affecting block-size selection for DeepSeek-V4.1 (fix tracked in #60762) and a **Qwen4Exp/Qwen3.8 crash at the 262K prefill boundary** that appeared between v0.31.1 and v0.32.0. Work on **direct weight reload for RL training** (#54477 and follow-up PRs) is also clearly accelerating toward becoming the default.

---

## 2. Releases & Breaking Changes

No new releases in the last 24h.

**Migration / config notes worth flagging:**
- **v0.32.0 regression on DeepSeek-V4.1**: no valid block size on the release tag (both 64 and 128 fail). Fix is being tracked in [#60762](https://github.com/vllm-project/vllm/issues/60762); original report [#59203](https://github.com/vllm-project/vllm/issues/59203) (CLOSED).
- **Rust frontend** (`VLLM_USE_RUST_FRONTEND=1`) remains experimental and explicitly **not feature-complete** relative to the Python API server. See roadmap [#44280](https://github.com/vllm-project/vllm/issues/44280).
- **Direct weight reload** (`VLLM_RELOAD_MODE=direct`) is transitioning toward default; PR [#66002](https://github.com/vllm-project/vllm/pull/66002) adds model opt-in and refuses known-silent paths, so operators should expect config-level gating changes before it becomes implicit.
- **XPU distributed backend** migrating from `xccl` → `xccl2` as legacy is deprecated: [#64818](https://github.com/vllm-project/vllm/pull/64818).

---

## 3. New Model & Hardware Support

- **DeepSeek-V4.1**: arbitrary pipeline-stage partitions support in flight ([#63106](https://github.com/vllm-project/vllm/pull/63106)); allows PP cuts inside KV-sharing layer groups, which the current NVIDIA path rejects.
- **MiniMax-M3**: indexer context parallelism (CP) for prefill and decode on single-node NVLink TP2/TP4 with FlashInfer CAKE ([#61929](https://github.com/vllm-project/vllm/pull/61929)).
- **NVIDIA Nemotron 3.5 ASR Streaming** (0.6B multilingual streaming ASR) requested: [#47455](https://github.com/vllm-project/vllm/issues/47455).
- **Rust frontend** drop-in API server continues toward parity — tracking: [#44280](https://github.com/vllm-project/vllm/issues/44280).
- **GB10 / DGX Spark (SM121)** is emerging as a supported target but with known per-tensor H2D weight-load overhead ([#58726](https://github.com/vllm-project/vllm/issues/58726)) and FLASHMLA_SPARSE_DSV4 metadata crashes ([#50774](https://github.com/vllm-project/vllm/issues/50774)).

---

## 4. Performance & Optimization

**Landing / in review:**
- **GDN prefill in-place SSM state** for Qwen3.5/3.8 and Qwen3-Next linear attention — removes 4–5 gather/mask/cast/scatter kernels per layer when prefill rows are fresh or fully cached ([#64498](https://github.com/vllm-project/vllm/pull/64498)).
- **TRT-LLM monolithic MoE**: keep DP padding rows out of expert compute on Blackwell for block-FP8/MXFP8 ([#65972](https://github.com/vllm-project/vllm/pull/65972)).
- **PP handoff over CUDA copy engines (NVLink only)** — 0-SM copy to reduce SM contention on pipeline-parallel steps ([#65896](https://github.com/vllm-project/vllm/pull/65896)).
- **Tokenizer pool** now shallow-copies only the Rust backend instead of `deepcopy` of the whole tokenizer ([#65674](https://github.com/vllm-project/vllm/pull/65674)).
- **ROCm MXFP8 `dot_scaled` GEMM retune** for MiniMax-M3.1 TP4 linears on gfx950; M=17–256 tiles reshaped ([#65530](https://github.com/vllm-project/vllm/pull/65530)).
- **RoPE + KV-cache fusion** now reachable on ROCm/AITER via explicit request or documented opt-ins ([#57900](https://github.com/vllm-project/vllm/pull/57900)).

**Regressions / open perf issues:**
- **MiniMax-M2.7 TP4+EP throughput regression** across 0.29, 0.31, 0.32: [#63705](https://github.com/vllm-project/vllm/issues/63705).
- **Qwen3.5 397B NVFP4 DEP8**: mixed prefill-decode steps ~6× slower than decode-only on 8×B200: [#44806](https://github.com/vllm-project/vllm/issues/44806).
- **ROCm / MI355X perf trackers** for Qwen3.8-2.4T-A95B ([#57149](https://github.com/vllm-project/vllm/issues/57149)) and Qwen3.8-Flash-Next ([#59575](https://github.com/vllm-project/vllm/issues/59575)).
- **GB10 weight loading** slow due to per-tensor H2D from safetensors mmap views ([#58726](https://github.com/vllm-project/vllm/issues/58726)).
- **Async KV pull re-queue stall of 1.0–1.8s** on completed pulls; RFC proposes front-of-queue re-admission ([#62532](https://github.com/vllm-project/vllm/issues/62532)).

---

## 5. Stability & Regressions

Ranked by severity:

1. **v0.32.0 block-size regression on DeepSeek-V4.1** — no working block size (64 and 128 both fail) on the release tag. Fix tracked in [#60762](https://github.com/vllm-project/vllm/issues/60762); original: [#59203](https://github.com/vllm-project/vllm/issues/59203).
2. **Qwen4Exp / Qwen3.8-Flash-Next GPU crash** — CUDA illegal memory access when a single prefill crosses the 262,144 native boundary. Works on v0.31.1, broken on v0.32.0: [#62383](https://github.com/vllm-project/vllm/issues/62383).
3. **NixlConnector KV corruption** — KV blocks released on lease expiry while a remote READ is in flight; decode can silently consume overwritten KV. Correctness bug, no visible fix PR referenced: [#61229](https://github.com/vllm-project/vllm/issues/61229).
4. **DeepSeek-V4.1 MoE permutation-index bug** — permutation indices stored in the weights pool get zeroed under level-2 sleep, causing every row to become a copy of row 0 on reload. Fix: [#66010](https://github.com/vllm-project/vllm/pull/66010).
5. **Level-2 sleep + queued requests** — requests queued during L2 sleep can run on discarded weights if `wake_up()` fires before `reload_weights` completes. Fix: [#65326](https://github.com/vllm-project/vllm/pull/65326).
6. **FlashInfer sampler JIT** crashes engine startup when `nvcc` isn't discoverable on default wheel installs (no fallback to native sampler): [#49497](https://github.com/vllm-project/vllm/issues/49497).
7. **SM120 adaptive verification gating** — `enable_adaptive_verification` rejected on SM120 (device-query-lens gated to SM90/SM100): [#63343](https://github.com/vllm-project/vllm/issues/63343).
8. **Qwen3.5-9B hybrid-GDN + dynamic LoRA** NaNs on long sequences (punica Triton kernel): [#52568](https://github.com/vllm-project/vllm/issues/52568).
9. **int8_per_token_head KV correctness** — corrupts output with prefix caching on (pool pinned 100%): [#50702](https://github.com/vllm-project/vllm/issues/50702); and corrupts first generated tokens with prefix caching disabled: [#50749](https://github.com/vllm-project/vllm/issues/50749).
10. **ROCm GLM-5.3-Flash "Workspace is locked"** AssertionError under high decode concurrency in `rocm_fp8_paged_mqa_logits`: [#64874](https://github.com/vllm-project/vllm/issues/64874).
11. **EngineCore assert** in `_update_from_kv_xfer_finished` when an async KV connector reports a finished transfer for an aborted/freed request: [#43226](https://github.com/vllm-project/vllm/issues/43226).
12. **ROCm AITER FA skip-1-token bug** — `min_seqlen_q=1` on CK path drops single-token prompts. Fix PR (CLOSED/merged track): [#65722](https://github.com/vllm-project/vllm/pull/65722).

---

## 6. What This Means for Application Developers

- **Pin versions carefully.** v0.32.0 has two confirmed regressions (DeepSeek-V4.1 block-size, Qwen3.8/4Exp 262K prefill crash). If you're on those models, stay on v0.31.1 until #60762 and #62383 resolve. Track both before adopting v0.32.x in production.
- **KV-connector users (P/D disaggregation):** the NixlConnector lease-expiry bug is silent — you may see plausible but wrong output rather than a crash. If you rely on decentralized serving, watch #61229 and consider pinning to a known-good connector version until a fix lands.
- **Sleep/wake (L2) on shared clusters:** the L2-sleep + queued-request path can serve on stale weights. The fix in #65326 is not yet merged; if you use sleep modes for RL/eval swapping, gate `wake_up()` behind weight-reload completion in your orchestration.
- **Hybrid + quantized KV users:** if you run int8-per-token head KV with prefix caching on Gemma-4 or similar, be aware of the two correctness bugs (#50702, #50749). Disable prefix caching as a safety net until resolved.
- **Linux/ROCm shops:** several AITER and workspace-lock issues (#64874, #65722) affect multi-tenant decode workloads; if you serve GLM-5.3-Flash or single-token prompts on ROCm, test explicitly under load. The M=17–256 MXFP8 retune in #65530 may improve MiniMax-M3.1 TP4 throughput measurably (>tile selection changes only, but worth benchmarking).
- **Long-context agents crossing 262K:** the Qwen4Exp crash is reproducible at exactly the prefill boundary — if you build agents with very large tools/system prompts, either stay below 262,144 tokens in a single prefill or pin to v0.31.1.
- **RL / fine-tuning teams:** the direct weight reload work (#54477, #64352, #66002) is converging fast; expect a near-term default switch, and start auditing custom loaders now — the new opt-in model will refuse paths that were previously silently incorrect (see #66010 for the class of bug being caught).
- **Rust frontend:** not yet ready for production — treat `VLLM_USE_RUST_FRONTEND=1` as experimental until #44280's parity checklist closes.

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

⚠️ Summary generation failed.

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

 # llama.cpp Digest — 2026-11-06

## Today's Highlights

llama-server landed a memory-focused change replacing deep-copied checkpoints with reference-counted shared state ([b12141](https://github.com/ggml-org/llama.cpp/releases/tag/b12141)), alongside Vulkan and Metal kernel work ([b12143](https://github.com/ggml-org/llama.cpp/releases/tag/b12143), [b12138](https://github.com/ggml-org/llama.cpp/releases/tag/b12138)). On the PR side, Blackwell gets an MXFP4 W4A8 path using the `mxf8f6f4` MMA ([#31747](https://github.com/ggml-org/llama.cpp/pull/31747)), while a hybrid MoE expert cache reports measurable decode gains ([#31233](https://github.com/ggml-org/llama.cpp/pull/31233)). Stability remains the dominant theme: speculative decoding correctness on quantized targets ([#25618](https://github.com/ggml-org/llama.cpp/issues/25618)) and MTP-related crashes ([#31077](https://github.com/ggml-org/llama.cpp/issues/31077), [#30698](https://github.com/ggml-org/llama.cpp/issues/30698)) are still open.

## Releases & Breaking Changes

No public API breakage. Notable behavior changes:

- **[b12137](https://github.com/ggml-org/llama.cpp/releases/tag/b12137) — server rejects non-object realtime session updates.** Malformed payloads previously threw an uncaught exception and terminated the server; clients sending invalid session JSON will now get an error response instead of a crash. Audit any custom Realtime-compatible clients.
- **[b12140](https://github.com/ggml-org/llama.cpp/releases/tag/b12140) — `truncate=false` for YaRN.** Long-context RoPE scaling can now avoid rounding correction-interval boundaries to integers; opt-in and backward compatible.
- **[b12131](https://github.com/ggml-org/llama.cpp/releases/tag/b12131) / [b12132](https://github.com/ggml-org/llama.cpp/releases/tag/b12132) — `u8path()` removed in server/common** in favor of `generic_string()`. Internal cleanup, but worth regression-testing non-ASCII model paths on Windows.

## New Model & Hardware Support

- **Blackwell MXFP4 W4A8 matmul** — `mul_mat` path using `kind::mxf8f6f4` (`m16n8k32`, `scale_vec::1X`), with activations quantized to e4m3 and ue8m0 scales per 32 values against e2m1 weights ([#31747](https://github.com/ggml-org/llama.cpp/pull/31747)).
- **xing4_0 architecture** — full registration, tensor loading, HF→GGUF conversion, and graphs for mHC/MLA/MoE/MTP; no new ggml ops ([#31589](https://github.com/ggml-org/llama.cpp/pull/31589)).
- **WebGPU**: `MEAN` op landed ([b12135](https://github.com/ggml-org/llama.cpp/releases/tag/b12135)); `COUNT_EQUAL` in review for `ggml-opt` token-accuracy accounting, currently CPU-fallback ([#31578](https://github.com/ggml-org/llama.cpp/pull/31578)).
- **Vulkan IQ4_NL scalar MMQ** integer-dot shader for GPUs without coopmat, following the IQ4_XS precedent ([#31764](https://github.com/ggml-org/llama.cpp/pull/31764)).
- **Metal bf16×bf16 matmul kernel** added while extending `mul_mm` to `32 <= K < 64` ([b12138](https://github.com/ggml-org/llama.cpp/releases/tag/b12138)).
- **Hexagon**: `set_tensor_n` backend API plus repacking/tiling robustness fixes ([#31744](https://github.com/ggml-org/llama.cpp/pull/31744)).
- **OpenAI-compatible `/v1/audio/speech` endpoint** in review, building on the closed mtmd audio-output design issue ([#31763](https://github.com/ggml-org/llama.cpp/pull/31763), [#21956](https://github.com/ggml-org/llama.cpp/issues/21956)).

## Performance & Optimization

- **Checkpoint memory overhead reduced** — ref-counted `shared_ptr` checkpoint lists and `common_state_data` buffers replace deep copies in slot save/restore ([b12141](https://github.com/ggml-org/llama.cpp/releases/tag/b12141)).
- **MoE expert cache (ClassLRU)** — LRFU/MQ-like hybrid reported at **+6% decode on QFN, +7% on GLM, 8% fewer expert uploads** vs SLRU with a warm cache ([#31233](https://github.com/ggml-org/llama.cpp/pull/31233)).
- **Vulkan FWHT kernels** extended to block widths > 512 and now accept F16 source directly, covering both subgroup and shared-memory paths ([b12143](https://github.com/ggml-org/llama.cpp/releases/tag/b12143)).
- **Vulkan flash-attention row tiling** packs verify tokens and GQA heads into one tile to share K/V reads (WIP/RFC) ([#30190](https://github.com/ggml-org/llama.cpp/pull/30190)).
- **Linux mmap**: proposal to drop `MAP_POPULATE`, which currently blocks until the full model is paged in ([#28055](https://github.com/ggml-org/llama.cpp/pull/28055)).
- **WASM relaxed SIMD** dot-product implementation for browser builds ([#19590](https://github.com/ggml-org/llama.cpp/pull/19590)).
- **Qualcomm Adreno Vulkan workarounds** in progress: runtime loop count for `rms_norm` to avoid miscompiled unrolled variants ([#30824](https://github

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama Digest — 2026-11-06

## Today's Highlights

No releases in the last 24 hours, but a busy day of stability work: a new Vulkan crash on AMD Renoir iGPUs (gfx90c) surfaced in 0.40.14/0.40.15-rc2, and the MLX runner was found to ignore request stop sequences (fix PR already open). API-compat correctness also got attention — silent truncation in the Responses API and `data: null` from `/v1/models` both have fixes in flight, while the Anthropic `/v1/messages` shim was found to drop all but the last thinking block.

## Releases & Breaking Changes

None in the last 24h. Current stable line referenced across issues is **0.40.14** (with **0.40.15-rc2** in testing).

## New Model & Hardware Support

- **Hindsight `hs-decide-1-flash`** — feature request ([#19554](https://github.com/ollama/ollama/issues/19554)) plus companion MLX PR adding a `hindsight-decision-v1` Qwen decision format that reads a request in one causal pass and scores option codes from the LM head ([#19555](https://github.com/ollama/ollama/pull/19555)).
- **Qwen3.5 0.8B `q4_K_M`** — request to publish the missing quantization; ~2x faster than `q8_*` on CPU ([#19436](https://github.com/ollama/ollama/issues/19436)).
- **qwen35 / qwen35moe parallel serving** — PR removes the hardcoded `numParallel = 1` blocklist now that the upstream llama.cpp crash was fixed (2026-03-08) ([#17144](https://github.com/ollama/ollama/pull/17144)).
- **Intel Arc / Arrow Lake** — long-standing Arc support request still the most-upvoted item (76 comments, 96 👍, [#1590](https://github.com/ollama/ollama/issues/1590)); Arrow Lake Vulkan still produces garbage on 3B+ models ([#13964](https://github.com/ollama/ollama/issues/13964)).
- **KailA architecture** proposal closed without support ([#18922](https://github.com/ollama/ollama/issues/18922)); GPT-OSS 20B empty-output on AMD Radeon Pro 5700 XT Vulkan closed (CPU path works) ([#19060](https://github.com/ollama/ollama/issues/19060)).

## Performance & Optimization

- **MLX Qwen3.8 Flash Next memory efficiency** — replaces blanket NVFP4 with BF16 for QSA projections/non-expert MTP and MXFP8 elsewhere, since NVFP4 degraded long generations; affects `qwen3.8-flash-next:125b-a6b-nvfp4` and `:125b-mlx` ([#18078](https://github.com/ollama/ollama/pull/18078)).
- **Unified-memory APU scheduling** — scheduler currently caps GPU budget by *host* free RAM, which mis-sizes Strix Halo / Ryzen AI Max+ 395 (gfx1151); PR trusts GPU-reported free memory ([#16720](https://github.com/ollama/ollama/pull/16720)).
- **App startup latency** — syntax highlighting now lazy-initialized, removing a multi-second Settings-open delay found in profiling ([#19559](https://github.com/ollama/ollama/pull/19559)).
- **Open regression report** — 25–30% lower t/s across all models vs v0.40.1, closed as needs-more-info ([#18968](https://github.com/ollama/ollama/issues/18968)).

## Stability & Regressions

Ranked by severity:

1. **Vulkan crash at model load on AMD Renoir iGPU (gfx90c)** — `llama-server` exits with NTSTATUS `0xe06d7363` on *every* model, including ~380MB, in 0.40.14 and 0.40.15-rc2. No fix PR yet. ([#19558](https://github.com/ollama/ollama/issues/19558))
2. **MLX runner ignores stop sequences** — `Options.Stop` never checked in `mlxrunner/pipeline.go`; generation runs to EOS/`num_predict`. Fix PR buffers streamed chunks and truncates at earliest match ([#19551](https://github.com/ollama/ollama/issues/19551) / [#19552](https://github.com/ollama/ollama/pull/19552)).
3. **Anthropic `/v1/messages` drops thinking blocks** — only the last `thinking` block survives conversion, breaking clients that replay reasoning. ([#19415](https://github.com/ollama/ollama/issues/19415))
4. **Responses API truncates despite `truncation: disabled`** — nil `Truncate` defaults to `true`. Fix maps the option through ([#19492](https://github.com/ollama/ollama/issues/19492) / [#19501](https://github.com/ollama/ollama/pull/19501), closed).
5. **`/v1/models` returns `"data": null`** with zero models, breaking typed OpenAI SDKs. Fixed by initializing the slice ([#19381](https://github.com/ollama/ollama/issues/19381) / [#19539](https://github.com/ollama/ollama/pull/19539), closed).
6. **Native tool calling failures** on `qwen2.5-coder:7b` and Qwen3 under 0.40.13 — valid-looking calls not executed ([#19524](https://github.com/ollama/ollama/issues/19524)).
7. **Gemma 4 tool-call loss** — a string arg missing its opening `<|"|>` fails all repair candidates; `parseGemma4ToolCall` error only logged, client gets empty response. Fix PR reuses the model's trailing delimiter ([#19549](https://github.com/ollama/ollama/issues/19549) / [#19556](https://github.com/ollama/ollama/pull/19556)).
8. **`OLLAMA_MODELS` path treated as glob** — paths containing `[`, `*`, `?` silently yield an empty model list ([#19548](https://github.com/ollama/ollama/pull/19548)).
9. **Qwen3.8 27B MLX emits `tool_calls` only when content is empty** ([#19353](https://github.com/ollama/ollama/issues/19353)).
10. **Modelfile parser correctness** — CRLF counted as two line endings ([#17734](https://github.com/ollama/ollama/pull/17734)); zero-width/non-breaking runes stripped from directives ([#17809](https://github.com/ollama/ollama/pull/17809)); explicit `SYSTEM ""` ignored ([#18990](https://github.com/ollama/ollama/pull/18990)).

## What This Means for Application Developers

- **Pin your version if you're on an AMD iGPU with Vulkan** — 0.40.14 and 0.40.15-rc2 can fail to load any model on Renoir/gfx90c. Stay on 0.40.13 or fall back to CPU until #19558 is resolved.
- **MLX on macOS: don't rely on stop sequences yet.** If you're using `-mlx` tags in an agent loop, enforce termination client-side with `num_predict` bounds until #19552 merges.
- **Thinking-block round-tripping is unsafe via `/v1/messages`.** If you replay assistant messages with multiple reasoning blocks, expect context loss — flatten or replay a single block.
- **Always set `truncation` explicitly** for oversized inputs; the default currently overrides your intent until the fix lands.
- **Tool calling is the weak spot this week** across Gemma 4, Qwen3.8 MLX, and Qwen2.5-Coder. Validate tool-call output before executing, and avoid mixed free-text-plus-tool-call patterns where issues cluster.

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

We need answer user asks generate structured English digest for LiteLLM based on GitHub data for 2026-11-06. Need sections specified. We need use provided data. Need include GitHub links for each item. We need be concise professional infrastructure engineer. We can analyze data.

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
- If using OpenRouter, Cohere embeddings, Gemini/Vertex, MCP, Claude Code tool additions, test streaming/tool flows; some issues are open. Pin versions or apply PRs.
- Rate limit/retry: caller timeout now bounds retries; rpm/tpm double-count fix prevents premature 429. Set timeouts with retries in mind.
- Bedrock structured output: newer Anthropic models get native JSON schema, 20-40% cheaper; validate behavior change.
- S3/Bedrock batch: clients must use admin-allowlisted buckets; SDK users can pass own bucket/env var once #47496

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
*This digest is auto-generated by [agents-radar](https://github.com/SnowEagle-Kn/agents-radar).*