# OpenClaw 生态日报 2026-11-04

> Issues: 403 | PRs: 500 | 覆盖项目: 5 个 | 生成时间: 2026-11-04 02:55 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw 项目深度报告

⚠️ 摘要生成失败。

---

## 横向生态对比

# Cross-Project Comparison — Personal AI Assistant / Agent OSS Ecosystem
**Snapshot date:** 2026-11-04 · **Projects:** OpenClaw, Hermes Agent, IronClaw, QwenPaw, ZeroClaw

> **Data caveat:** OpenClaw's digest generation failed for this window. All OpenClaw quantitative fields are marked `N/A`; its treatment below is structural/positional only and is **not** backed by 24-hour telemetry. Health scores are analyst-derived composites (activity, closure throughput, release cadence, risk/backlog), not vendor-reported.

---

## 1. Ecosystem Overview

The five projects are converging on the same product thesis — a long-running, tool-using agent with persistent sessions, pluggable skills, and multi-channel delivery — while differentiating almost entirely on **deployment surface** (consumer desktop vs. self-hosted gateway vs. research harness). The dominant work this cycle is not feature expansion but **reliability hardening**: packaging correctness, session durability, approval/authority enforcement, and config validation. Three of four reporting projects are mid-release-cycle (Hermes patch rollup, QwenPaw 2.3.0 beta, ZeroClaw pre-v0.9.0 gate), which suppresses net-new feature signals and inflates bug/regression chatter. Activity concentration is extreme: Hermes and ZeroClaw together account for ~84 of ~102 PRs touched, while IronClaw recorded a single dependency PR. The market is separating into **high-velocity beta projects** and **quiet maintenance projects**, with little middle ground.

---

## 2. Activity Comparison

| Project | Issues (24h touched / open) | PRs (24h touched / open) | Release status | Health score |
|---|---|---|---|---|
| **OpenClaw** | N/A | N/A | N/A — **digest generation failed** | N/A |
| **Hermes Agent** | 50 / 46 (4 closed) | 50 / 45 (5 merged-closed); **~174 PRs merged since v0.22.6** | ✅ **v0.22.7 shipped** (patch rollup) | **8.5 / 10** |
| **IronClaw** | 0 / 0 | 1 / 1 (Dependabot only) | ⏸ None | **5.0 / 10** (stable-but-dormant) |
| **QwenPaw** | 20 / 15 (5 closed, 25%) | 34 / 26 (8 merged-closed, 24%) | 🔄 `2.3.0-beta.1/.2/3`; stable `2.2.2.post1` | **7.6 / 10** |
| **ZeroClaw** | 18 / 15 (3 closed) | 50 / 40 (10 merged-closed) | 🔄 No release; items gated on **v0.9.0** | **6.8 / 10** (strained) |

**Read:** QwenPaw posts the best closure ratio (~25% both queues) and thus the cleanest triage hygiene. ZeroClaw has the largest *absolute* queue imbalance — 40 of 50 touched PRs remain open, many `size/XL` — indicating review bandwidth, not contributor interest, is the constraint. Hermes' 174-PR consolidation window is an outlier in merge throughput and is the only project to ship.

---

## 3. OpenClaw's Position

**Data status:** no telemetry for this window; the following is positional inference and should be re-validated before decision use.

- **Advantages vs. peers:** OpenClaw's differentiation is *categorical*, not quantitative — it is designated the **core reference implementation**. Peers re-litigate the same surface area (session lifecycle, elicitation/approval, channel adapters, skill provisioning), which implies OpenClaw's abstractions are the de facto schema the ecosystem converges on. That gives it an ecosystem-tax and interoperability advantage no single peer can replicate by shipping features.
- **Technical approach differences:** peers are specializing where OpenClaw must generalize. Hermes optimizes for model-research iteration; QwenPaw for a packaged consumer desktop binary; ZeroClaw for multi-tenant gateway/channel routing. OpenClaw, as reference, carries the burden of representing all three, which typically manifests as slower cadence and broader but shallower coverage per area.
- **Community size comparison:** unmeasurable from this snapshot. Indirect proxies: Hermes (50/50) and ZeroClaw (50 PRs) show materially higher raw engagement than QwenPaw (34/20) and IronClaw (~0). No proxy supports a numeric ranking involving OpenClaw.
- **Operational note:** the failed digest is itself a signal worth flagging — the reference project is the only one in the set lacking automated release-note/community observability this cycle. For downstream consumers who track OpenClaw as the upstream contract, that is a **low-severity but real transparency gap**, not evidence of project inactivity.

---

## 4. Shared Technical Focus Areas

| Requirement | Projects | Specific need |
|---|---|---|
| **Transactional context control** | QwenPaw (#7997), ZeroClaw (#11921, #7759) | Message retraction/editing with auto-truncation *and* workspace rollback via snapshots; append-only session event history as the substrate for replay. Users treat "undo" as a trust primitive, not a UI nicety. |
| **Action-layer approval enforcement** | QwenPaw (#8513), ZeroClaw (#11126, #11623/#11624, #11225/#11230/#11220) | Approval must gate *outbound shell/`ask_user` actions* at `AUTO`/`SMART` policy levels, not just the UI. Elicitations must be answered and recorded, not silently dropped. |
| **Authority evaluated at execution, not enqueue** | ZeroClaw (#11126, #9394) | Revoked-admin bypass in queued session ops; pairing codes that never expire despite `gateway.pairing_dashboard` being "accepted." Dead config = latent privilege escalation. |
| **Install-time correctness** | QwenPaw (#7816, #8488, #8265, #8013), IronClaw (#8167) | PyInstaller cannot follow dynamic `importlib.import_module` → console channel silently dropped; missing `visual_compression` assets; `invalidate_caches()` crashing on distro-patched Python 3.11; 80 MB / 12,994-file skill pools timing out. "Works from source" ≠ "works installed." |
| **Provider capability negotiation** | ZeroClaw (#12014), QwenPaw (provider/model config validation) | Silent 32,000-token default for OpenAI-compatible providers causes valid turns to be rejected locally. Context windows must be explicit/negotiated, not assumed. |
| **Session durability independent of transport** | ZeroClaw (#7759) | Agent turns must survive client disconnect and resume on reconnect — WebSocket lifetime is currently coupled to turn lifecycle. |
| **Config hot-reload coverage** | QwenPaw (#4312) | Watcher scope extended beyond `channels`/`heartbeat` to MCP and Skills — long-standing (open since May). |
| **Channel rendering fidelity** | ZeroClaw (#11052, #9562), QwenPaw (#8528) | WhatsApp setext headings/thematic breaks; WebChat auto-scroll overriding manual scroll during streaming; markdown preview rewriting relative paths → 404. Per-channel renderers are now a correctness surface. |
| **Dependency hygiene** | IronClaw (#8167) | Zod 4.x major bump isolated to docs tooling — no runtime exposure, but illustrative of ecosystem-wide Zod/Remotion churn. |

---

## 5. Differentiation Analysis

| Project | Feature focus | Target user | Architecture signals |
|---|---|---|---|
| **OpenClaw** | Canonical reference surface; interoperability contract | Framework builders, downstream implementers | Broad, generalizing; upstream-of-forks |
| **Hermes Agent** (NousResearch) | Model-centric agent behavior; research-to-product velocity | Researchers, ML engineers | Batch-merge workflow implied by ~174-PR patch rollup; `0.22.x` = longest release lineage in set |
| **IronClaw** (nearai) | Minimal footprint; docs/internal tooling maturity | NEAR-ecosystem / infra-oriented | Near-zero community surface; documentation pipeline is the only active artifact |
| **QwenPaw** (AgentScope) | **Consumer desktop**: T

---

## 同赛道项目详细报告

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — 项目摘要

**日期：** 2026-11-04 · **仓库：** [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)

---

## 1. 今日概览

Hermes Agent 正处于高速稳定化阶段：过去 24 小时内涉及 50 个 issue 和 50 个 PR（其中 46 个 issue 开启，45 个 PR 开启），并且 v0.22.7 已作为自 v0.22.6 以来约 **174 个已合并 PR** 的补丁汇总版本发布——这是一个异常庞大的合并窗口

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

# IronClaw Project Digest — 2026-11-04

## 1. Today's Overview

IronClaw recorded no issue activity and no new releases in the last 24 hours. The only update in the snapshot is a single open Dependabot pull request (#8167) modifying JavaScript dependencies used by internal architecture-video documentation. No pull requests were merged or closed, and no bugs, regressions, or feature discussions were reported. Overall activity is **very low**, consistent with a quiet maintenance period rather than active development. Project health appears stable from this snapshot, but the limited data volume makes deeper assessment difficult.

## 2. Releases

No new releases were published in the last 24 hours.

## 3. Project Progress

No merged or closed pull requests and no closed issues were recorded today. Consequently, no features advanced and no fixes landed.

The only open work item is:

- **[PR #8167](https://github.com/nearai/ironclaw/pull/8167)** — `chore(deps): bump zod, @remotion/cli and @remotion/tailwind-v4 in /docs/internal/architecture-video`  
  Author: `dependabot[bot]` | Created: 2026-11-03 | Updated: 2026-11-03 | Status: Open

This is an automated dependency-maintenance change scoped to internal documentation tooling, not the core IronClaw agent/runtime code.

## 4. Community Hot Topics

Only one item is present in the 24-hour window:

- **[PR #8167](https://github.com/nearai/ironclaw/pull/8167)** — Dependabot dependency bump for `zod` (to 4.6.7), `@remotion/cli`, and `@remotion/tailwind-v4`.  
  Comments/reactions: none recorded in the provided data.

**Underlying need:** routine dependency hygiene for the internal architecture-video documentation pipeline. This is not a community discussion topic; it reflects automated maintenance rather than user-driven demand. There are no hot Issues or high-engagement PRs to analyze today.

## 5. Bugs & Stability

No bugs, crashes, or regressions were reported in the last 24 hours. There are no known fix PRs pending for stability issues.

**Potential risk note:** PR #8167 bumps `zod` to `4.6.7` alongside Remotion packages. A Zod major-version update can carry breaking API changes for dependent tooling. However, the change is isolated to `/docs/internal/architecture-video`, so any impact should be limited to documentation build tooling rather than IronClaw’s product runtime. No evidence of breakage is present in the data.

## 6. Feature Requests & Roadmap Signals

No feature requests or roadmap-related issues were opened or updated today. There are **no user-driven roadmap signals** in this snapshot.

The only weak signal is continued maintenance of internal architecture-video documentation dependencies via PR #8167, which suggests ongoing housekeeping of documentation assets rather than product feature investment.

## 7. User Feedback Summary

No user feedback was captured today: zero Issues were updated and the sole PR has no recorded comments or reactions. As a result, there is insufficient data to identify user pain points, use cases, or satisfaction/dissatisfaction trends. The absence of reports may indicate stability, but it may also reflect low community engagement during this window; the data cannot distinguish between the two.

## 8. Backlog Watch

No long-unanswered Issues are present in the snapshot, and no aging PRs beyond today’s item were reported.

- **[PR #8167](https://github.com/nearai/ironclaw/pull/8167)** has been open since 2026-11-03 — only about one day, so it is not yet a backlog concern. It should still be reviewed and merged promptly to prevent dependency drift in the documentation toolchain.

**Overall assessment:** IronClaw is in a low-activity, no-incident state for 2026-11-04. The project shows no regressions or release activity; the only pending action is routine dependency maintenance in internal docs tooling.

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw Project Digest — 2026-11-04

## 1. Today's Overview

QwenPaw is in an active 2.3.0 beta cycle (users report on `2.3.0-beta.1`, `beta.2`, and `2.3.0b3`, with `2.2.2.post1` as the latest stable), and no new release shipped in the last 24 hours. Maintenance throughput is high: 34 PRs were touched (26 open, 8 merged/closed) against 20 issues (15 open, 5 closed), giving a roughly 25% closure rate across both queues — a healthy sign of triage, though the open PR queue is heavily weighted toward very large changes (`size/L`–`size/XXXL`). Activity is concentrated in four areas: Windows desktop stability, plugin/dependency provisioning, provider/model configuration validation, and chat-channel reliability. The most notable risk today is a security-governance gap (#8513) where outbound shell actions bypass approval at `AUTO`/`SMART` levels, alongside a cluster of Windows-specific regressions that suggest the desktop build remains the weakest surface in beta. Overall: **high-velocity, beta-quality project with strong community contribution and a growing review backlog.**

## 2. Releases

No new releases in the last 24 hours. The project remains between stable `2.2.2.post1` (published 2026-10-22) and the `2.3.0` beta line.

## 3. Project Progress

**Closed Issues (5):**

| Issue | Title | Impact |
|---|---|---|
| [#8265](https://github.com/agentscope-ai/QwenPaw/issues/8265) | Plugin hot-install aborts when `importlib.invalidate_caches()` raises on distro-patched Python 3.11 | Plugin robustness restored on patched Python builds |
| [#8488](https://github.com/agentscope-ai/QwenPaw/issues/8488) | Desktop build omits `visual_compression` assets → `FileNotFoundError: atlas-gray-native.ts` | Visual Compact no longer silently no-ops on PyInstaller/Windows builds |
| [#4312](https://github.com/agentscope-ai/QwenPaw/issues/4312) | `agent.json` hot-reload scope should cover MCP, Skills, etc. | Config watcher extended beyond `channels`/`heartbeat` — long-standing enhancement (open since May) landed |
| [#8013](https://github.com/agentscope-ai/QwenPaw/issues/8013) | Large skill pool download timeouts (80 MB / 12,994 files) | Skill distribution path hardened |
| [#8528](https://github.com/agentscope-ai/QwenPaw/issues/8528) | Markdown preview rewrites relative image/link paths → 404 in subdirectories | File preview correctness fixed |

**Merged/Closed PRs:** The dataset shows 8 merged/closed PRs, of which [#7816](https://github.com/agentscope-ai/QwenPaw/pull/7816) (`fix(pack): force-include console channel in PyInstaller bundle`, first-time contributor) is explicitly listed as closed. This is a meaningful packaging fix: the console channel was being silently dropped from the Tauri sidecar bundle because PyInstaller could not follow the dynamic `importlib.import_module(".console", ...)` call, causing packaged Desktop builds to fail startup.

**Net read:** The day's progress was mostly *reliability hardening* — packaging, hot-reload coverage, plugin install, and asset shipping — rather than new feature delivery.

## 4. Community Hot Topics

1. **[#7997](https://github.com/agentscope-ai/QwenPaw/issues/7997) — Message retraction/editing + workspace rollback in WebUI** (9 comments, open since 2026-09-27)
   The most-discussed item in the window. Users want to edit or retract a sent message, auto-truncate subsequent conversation history, and optionally roll back file changes via snapshots. Underlying need: **deterministic context control**. Once an agent has modified workspace files, users have no clean "undo" path — this is fundamentally a trust-and-recoverability request, not a UI nicety.

2. **[#8265](https://github.com/agentscope-ai/QwenPaw/issues/8265) — Plugin hot-install crash on distro-patched Python** (5 comments, now closed)
   A concrete blocker ("我的插件被这个卡了" — "my plugin is stuck on this"). The loader called `importlib.invalidate_caches()` unconditionally at three sites. Reflects how brittle the plugin lifecycle is on non-standard Python distributions.

3. **[#8505](https://github.com/agentscope-ai/QwenPaw/issues/8505) / [#8488](https://github.com/agentscope-ai/QwenPaw/issues/8488)** (3 comments each)
   #8505:

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw 项目摘要 — 2026-11-04

## 1. 今日概览

ZeroClaw 在过去 24 小时内开发活动活跃：18 个 issue 有更新（15 个打开/活跃，3 个已关闭），50 个 pull request 有更新（40 个打开，10 个已合并/关闭），无新版本发布。吞吐量健康，但打开队列主要由 P1/高风险的安全、运行时和 CI issue 占据，其中若干被标记为即将到来的 `v0.9.0` 发布门禁。项目正在积极关闭安全敏感的会话所有权和 ZeroCode elicitation 缺陷，而围绕持久会话历史与网关轮次生命周期的长期架构工作仍在评审中。整体项目健康度活跃，但受明显的高严重性事项积压以及大型、推进缓慢的 PR 拖累。

## 2. 发布

本时间窗口内没有发布新版本。

## 3. 

</details>

---
*本日报由 [agents-radar](https://github.com/SnowEagle-Kn/agents-radar) 自动生成。*