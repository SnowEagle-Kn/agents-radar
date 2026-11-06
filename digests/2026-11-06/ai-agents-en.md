# OpenClaw Ecosystem Digest 2026-11-06

> Issues: 500 | PRs: 500 | Projects covered: 5 | Generated: 2026-11-06 02:48 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw Deep Dive

# OpenClaw Project Digest — 2026-11-06

## 1. Today's Overview

OpenClaw remains in a high-velocity, high-pressure operating state: 500 issues and 500 PRs were touched in the last 24h, with 186 PRs merged/closed against 314 still open and 253 of 500 updated issues closed. Activity is dominated by maintainer-driven cleanup (steipete, vincentkoc, obviyus), security-adjacent work around access-policy and staged-commit authority, and a stream of CI/test hardening PRs. However, the issue tracker shows a persistent cluster of P0/P1 crash-loop and data-loss regressions tracking back to the 2026.9.x–2026.10.x line, several with 15–75 comments and no merged fix. One release shipped (v2026.11.1), but it is a small hardening patch rather than a regression-closing update. Overall: **very active, but stability debt on long-lived regressions is accumulating and needs maintainer triage.**

## 2. Releases

**v2026.11.1** — small, safety-focused patch:
- **Safer Mac settings:** failed config saves no longer overwrite secrets; disabling Tailscale exposure preserves Gateway authentication (#187734, #187664; fixes #187600, #187597; thanks @anmol0b).
- **More reliable channel replies:** Telegram, Discord, and webhook delivery robustness improvements (release note truncated in source data — full changelog should be verified on the release page).

No breaking changes or migration notes are stated in the provided excerpt. Notably, **none of the top P0 regressions below are fixed in this release** (e.g., #160959, #166017, #191847), and #141474's fix is explicitly noted as shipping in `v2026.11.5-beta.1`, *not* stable v2026.11.1.

## 3. Project Progress

Merged/closed PRs today show steady incremental hardening rather than headline features:

- **MCP App content durability:** #193812 preserves saved MCP App display content after view expiry via session-owned custom-entry lane.
- **Recovery model selection:** #193719 keeps recovery models selectable across providers when a personal-account pin is missing (closes #193770).
- **Memory-wiki correctness:** #193469 (closed) stops `wiki lint` treating commented/escaped `[[wikilinks]]` as live links.
- **UI performance:** #193479 (closed) reduces long-session streaming onset work by retaining prepared history in the bounded projection cache.
- **CI/test hygiene:** #193887 (closed) retires a redundant Swift portability adapter; #193870, #193904 rework CI admission for historical runs and PostgreSQL conformance.
- **QA/test reliability:** #193908 awaits Workshop enable before reload.

Feature work advancing in open PRs: access-policy revision publishing (#182383), staged PostgreSQL authority at physical commit (#193842), OpenClaw ID Gateway membership authority behind Labs flag (#192335), and an opt-in "portable research" tool (#173964).

## 4. Community Hot Topics

| Item | Comments | Theme |
|---|---|---|
| [#160959](https://github.com/openclaw/openclaw/issues/160959) — Gateway blocks for minutes capturing large external plugins (P0) | 75 | Plugin capture regression from 2026.9.6 stalls the event loop |
| [#177656](https://github.com/openclaw/openclaw/issues/177656) — Native host exec shared-state integrity-admission error (P1) | 29 | Authority/admission model breaking native exec |
| [#170956](https://github.com/openclaw/openclaw/issues/170956) — Windows slow startup, unclean shutdown, schtasks EPERM (P0) | 29 | Windows managed-service reliability |
| [#178359](https://github.com/openclaw/openclaw/issues/178359) — `doctor --fix` idles 15+ min (P1) | 25 | Doctor/repair-path hangs |
| [#166634](https://github.com/openclaw/openclaw/issues/166634) — Late reclamation checkpoint failure leaves agent unavailable | 24 | SQLite recovery leaves agents permanently UNAVAILABLE |
| [#191944](https://github.com/openclaw/openclaw/issues/191944) — `gateway stop` fails: "Process ancestry could not be fully inspected" (P2) | 19 | Process-census/service-inspection reliability |
| [#40001](https://github.com/openclaw/openclaw/issues/40001) — `write` tool lacks append mode; cron destroys shared files (P0) | 17 | Silent data loss in isolated sessions |

**Underlying need:** the community is converging on a single meta-theme — **the shared-state/integrity-admission and service-child lifecycle model introduced around 2026.9.x is producing a long tail of hangs, permanent-unavailable states, and recovery dead-ends**. Users are not asking for features; they are asking for deterministic recovery after crashes and restarts.

## 5. Bugs & Stability

**P0 / crash-loop & data-loss (no fix PR confirmed merged):**
- [#160959](https://github.com/openclaw/openclaw/issues/160959) — Gateway event-loop block for minutes on large plugin capture (75 comments, diamond-lobster rating). **Highest-impact open issue.**
- [#166017](https://github.com/openclaw/openclaw/issues/166017) — `mcp.sessionIdleTtlMs` default not applied; MCP runtimes never evicted, spawn broker hits 256-child cap, all exec/read/MCP start fails. *(linked PR open.)*
- [#170956](https://github.com/openclaw/openclaw/issues/170956) — Windows restart/update unreliability (schtasks permission + unclean plugin shutdown).
- [#40001](https://github.com/openclaw/openclaw/issues/40001) — `write` overwrites instead of appending; isolated cron causes silent shared-file data loss.
- [#161921](https://github.com/openclaw/openclaw/issues/161921) — post-`state-migrated-no-rollback` deadlock between `doctor --fix` and `plugins update`.
- [#70903](https://github.com/openclaw/openclaw/issues/70903) — persistent provider cooldown blocks users for hours after billing recovery.
- [#142821](https://github.com/openclaw/openclaw/issues/142821) — default-on transcript redaction poisons replayed agent context (security + data-loss).
- [#158912](https://github.com/openclaw/openclaw/issues/158912) — `MEMORY.md` no longer injected at bootstrap on 2026.9.6 (docs mismatch, session-state/security).

**P1 regressions:**
- [#191847](https://github.com/openclaw/openclaw/issues/191847) — 2026.10.10: 512 MB SQLite worker OOM kills every turn in large sessions ("admission is closed").
- [#155563](https://github.com/openclaw/openclaw/issues/155563) — service-child relay/anchor and MCP groups retained after successful turns (2026.9.5 regression).
- [#134993](https://github.com/openclaw/openclaw/issues/134993) — Gateway pegs one CPU core in filesystem discovery with large skill/agent fleets.
- [#162637](https://github.com/openclaw/openclaw/issues/162637) — native Codex completion handoff fails with `SESSION_WORK_START_CHANGED`.
- [#182647](https://github.com/openclaw/openclaw/issues/182647) — cloud-worker turns fail with "Session capability metadata requires worker preparation" (labeled queueable-fix).
- [#118793](https://github.com/openclaw/openclaw/issues/118793) — Claude CLI session-limit error bypasses model fallback chain.

**Exceptionally long-lived:** #40001 (#40001) has been open since **2026-03-08** — ~8 months — despite being P0 data-loss. #70903 since 2026-04-24.

## 6. Feature Requests & Roadmap Signals

- **Progress streaming for Feishu/Lark** ([#131457](https://github.com/openclaw/openclaw/issues/131457)) — parity with Slack/Discord/Telegram/Matrix/Mattermost/Teams; likely candidate once channel-delivery hardening in v2026.11.1 settles.
- **Append mode for `write` tool** ([#40001](https://github.com/openclaw/openclaw/issues/40001)) — arguably a bug fix, but explicitly a missing capability; high candidate for near-term.
- **`--prompt-file` for `openclaw infer model run`** ([#140244](https://github.com/openclaw/openclaw/issues/140244)) — small, low-risk automation ergonomics.
- **Disable hosted plugin catalog feed** ([#164620](https://github.com/openclaw/openclaw/issues/164620)) — privacy/air-gap demand.
- **Control UI: browse/fork prior channel-chat segments after `/new`** ([#158800](https://github.com/openclaw/openclaw/issues/158800)) — UX continuity.
- **Failure-only `notifyOnExit` mode** ([#141973](https://github.com/openclaw/openclaw/issues/141973), closed stale) — noise reduction.
- **Session tool-set explainer** (PR [#192713](https://github.com/openclaw/openclaw/pull/192713), closes #192712) — strong debuggability signal; likely to land soon.
- **First-run provider→chat flow** (PR [#193892](https://github.com/openclaw/openclaw/pull/193892)) — onboarding friction fix.

**Prediction for v2026.11.x:** expect the #141474 recovery fix (already in `v2026.11.5-beta.1`) to reach stable, plus CI/authority-model hardening and the Control UI onboarding/model-catalog fixes. Feishu streaming and `write` append mode are less certain for the immediate next tag.

## 7. User Feedback Summary

**Pain points (dissatisfaction):**
- **Recovery is not trustworthy.** Multiple users report that after a crash, restart, or update, the system lands in a state that requires the *other* subsystem to fix it first (`doctor` ↔ `plugins update` deadlock, permanent `UNAVAILABLE` agents, "admission is closed"). This is the dominant emotional theme in high-comment threads.
- **Windows is a second-class platform.** schtasks EPERM, slow startup, unclean shutdown, and the fact that Windows CI covers only ~0.6% of test files ([#126874](https://github.com/openclaw/openclaw/issues/126874), closed stale) are repeatedly cited.
- **Silent data loss is the most feared failure mode** — cron overwriting shared memory files, transcript redaction propagating into commands/files/replies, and stale async results replaying into the wrong Discord channel ([#135117](https://github.com/openclaw/openclaw/issues/135117)).
- **Cost/caching surprises** — Anthropic prompt cache stuck at ~46k prefix ([#140129](https://github.com/openclaw/openclaw/issues/140129)), cache never reused ([#182891](https://github.com/openclaw/openclaw/issues/182891)) — direct billing anxiety.

**Satisfaction signals:** users are actively filing highly detailed, reproducible reports with version pins and logs; several explicitly credit the team's responsiveness (e.g., #187468 confirmed update-failure report). The maintainers' `clawsweeper` triage labels (diamond lobster / gold shrimp ratings) show a maturing triage process that contributors respect.

## 8. Backlog Watch

Items needing maintainer attention — high severity, low or stalled momentum:

1. **[#40001](https://github.com/openclaw/openclaw/issues/40001)** — P0 data-loss, open since 2026-03-08, no fix PR, needs product decision. *Most overdue P0.*
2. **[#70903](https://github.com/openclaw/openclaw/issues/70903)** — persistent provider cooldown blocks paying users post-billing-recovery; open since 2026-04-24, stale.
3. **[#160959](https://github.com/openclaw/openclaw/issues/160959)** — highest-comment P0 (75), needs maintainer review, no fix PR.
4. **[#142821](https://github.com/openclaw/openclaw/issues/142821)** — redaction poisoning agent context; security review requested, linked PR open since Sept.
5. **[#134993](https://github.com/openclaw/openclaw/issues/134993)** — CPU pegging with large fleets; open since 2026-09-01.
6. **[#126874](https://github.com/openclaw/openclaw/issues/126874)** — Windows CI coverage gap; closed stale but the underlying risk (unverified Windows path) remains and correlates with #170956.
7. **Stale-but-unresolved product decisions:** [#150743](https://github.com/openclaw/openclaw/issues/150743) (QQ channel long-term support after Tencent handoff stalled), [#164620](https://github.com/openclaw/openclaw/issues/164620) (disable hosted catalog feed), [#124306](https://github.com/openclaw/openclaw/issues/124306) (exec approval card doesn't flag mount-point targets before `rm -rf` — a safety gap worth prioritizing).
8. **PR risk:** [#173964](https://github.com/openclaw/openclaw/pull/173964) (portable research, XL, security-boundary, "needs proof") and [#192335](https://github.com/openclaw/openclaw/pull/192335) (OpenClaw ID membership authority) are large security-boundary changes that need explicit review bandwidth; [#191509](https://github.com/openclaw/openclaw/pull/191509) is an accidental empty placeholder that should be closed.

---
*Data source: github.com/openclaw/openclaw, snapshot 2026-11-06. Comment/reaction counts and issue states are as reported in the provided dataset.*

---

## Cross-Ecosystem Comparison

# Cross-Project Comparison Report — Personal AI Agent / Assistant OSS Ecosystem
**Snapshot date:** 2026-11-06 · **Projects:** OpenClaw, Hermes Agent, QwenPaw, ZeroClaw, IronClaw
**Method note:** Figures are single-day (24h) community digests. OpenClaw's "500/500" counts appear to be a dataset cap and should be read as *≥500*, not a true ceiling. Counts measure *items touched*, not net-new work.

---

## 1. Ecosystem Overview

The ecosystem is converging from "chatbot with tools" to **durable, multi-surface agent runtimes**, and the current fault line is no longer model capability but *session authority, migration integrity, and plugin isolation*. Four of the five projects are simultaneously rebuilding their state layer — append-only session history, deterministic replay, and gateway-mediated session ownership appear independently in OpenClaw, Hermes, QwenPaw, and ZeroClaw. Release posture splits cleanly: Hermes is shipping from a large release train, OpenClaw is patching a mature but debt-laden core, and QwenPaw/ZeroClaw are pre-release, accumulating review-queue backlogs. The strongest cross-project user signal is not feature demand but **trust in recovery and failure reporting** — silent data loss and "success that did nothing" outrank missing features in every tracker.

---

## 2. Activity Comparison

| Project | Issues (24h) | PRs (24h) | Merge/Close Throughput | Release Status | Health Score |
|---|---|---|---|---|---|
| **OpenClaw** | ~500 touched / 253 closed | ~500 touched / 314 open | 186 merged/closed (~37%) | v2026.11.1 — small hardening patch; top P0s unfixed | **6.4 / 10** |
| **Hermes Agent** | 50 (47 open / 3 closed) | 50 (44 open / 6 merged) | 6 merged (~12%) | **v0.23.0 "The Hearth"** shipped Nov 5 (~1,950 PRs, 666 contributors) | **8.0 / 10** |
| **QwenPaw** | 14 | 32 (20 open / 12 closed) | ~12 closed/merged (~38%) | None; public 2.2.2.post1 vs. beta 2.3.0b3 skew | **5.5 / 10** |
| **ZeroClaw** | 30 (25 open / 5 closed) | 50 (48 open / 2 merged) | 2 merged (~4%) | None; v0.9.0 staged but blocked | **5.9 / 10** |
| **IronClaw** | 0 | 0 | — | None | **n/a (dormant)** |

**Scoring rubric (composite, 0–10):** activity/throughput 25% · release cadence 20% · stability & regression debt 35% · review throughput/maturity 20%. Scores are analyst judgment, not vendor metrics.

**Reading the table:** OpenClaw operates at roughly **10× the daily item volume** of any peer. Hermes has the healthiest *ratio* (one large, coherent release with spin-out tracking issues). ZeroClaw's 2-of-50 merge rate is not low contribution — it is a **review bottleneck** with substantial PRs stacked behind `do-not-merge`/`status:blocked` dependencies (#12244 → #11964/#12232; #11126 → #10412).

---

## 3. OpenClaw's Position

**Advantages vs. peers**
- **Breadth of integration surface** — the only project in the set with mature parity across Feishu/Lark, QQ, Telegram, Discord, Slack, Matrix, Mattermost, Teams, and webhooks, plus MCP app content durability and a Control UI.
- **Operational depth** — `doctor --fix`, managed Windows services (schtasks), macOS Tailscale/Gateway auth handling, process-census service inspection. Peers are still building desktop/serve entry points; OpenClaw already has repair tooling (that occasionally deadlocks).
- **Triage maturity** — `clawsweeper` severity ratings (diamond lobster / gold shrimp) and reproducible, version-pinned reports. This is the most *institutionally* mature tracker in the set.

**Technical approach differences**
- **Monolithic gateway with an integrity-admission / shared-state authority model** introduced around 2026.9.x. Where Hermes is consolidating *into* a unified gateway, OpenClaw is absorbing the cost of having done so earlier — the entire P0 cluster (#160959 event-loop stalls, #177656 admission errors, #166634 permanent `UNAVAILABLE`, #161921 migration deadlock) is downstream of this design.
- Staged PostgreSQL commit authority (#193842) and access-policy revision publishing (#182383) show OpenClaw pushing further into **transactional session authority** than any peer.

**Community size comparison**
- OpenClaw's top thread carries **75 comments**; Hermes' **227**; ZeroClaw's **16**; QwenPaw's **8**. OpenClaw dominates *volume of distinct reporters*, while Hermes dominates *per-thread consensus-building*.
- Decisive gap: **~500 items/day vs. 30–50** for peers. OpenClaw is the de facto reference implementation by traffic, but that scale is precisely what exposes its stability debt — **#40001 (P0 data loss) has been open since 2026-03-08, ~8 months, with no fix PR.**

**Positioning verdict:** OpenClaw is the ecosystem's *substrate and cautionary tale* — highest capability and adoption, lowest marginal stability per shipped feature. Its competitive risk is not a feature gap but **trust erosion on recovery paths** while Hermes closes the integration gap at a healthier ratio.

---

## 4. Shared Technical Focus Areas

| Requirement | Projects | Specific Needs Observed |
|---|---|---|
| **Durable, append-only session state + deterministic replay** | **All four active** | OpenClaw: session-owned custom-entry lanes (#193812), SQLite recovery dead-ends (#166634). Hermes: unified-gateway session authority (#145171), cron state → `cron/runtime.db` (#75833). QwenPaw: append-only per-session SQLite + paginated history + turn forks (#8532). ZeroClaw

---

## Peer Project Reports

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — Project Digest

**Date:** 2026-11-06
**Repository:** [github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)

---

## 1. Today's Overview

Hermes Agent shipped **v0.23.0 "The Hearth Release"** on November 5, 2026 — a massive cycle since v0.22.0 (~10,080 commits, ~1,950 merged PRs, ~850 issues closed, 666 contributors). Activity remains extremely high: **50 issues updated in 24h** (47 open/active, 3 closed) and **50 PRs updated** (44 open, 6 merged/closed), consistent with a large-scale, high-velocity open-source project in active maintenance. The dominant theme of ongoing work is stabilization of the **unified gateway (#106742)**, which merged Oct 14 and continues to generate follow-up defects in session state, Desktop connection, and profile scoping. Community engagement is substantive — the top tracking issues carry 200+ and 40+ comments respectively — suggesting a mature user base running multi-profile, multi-surface deployments. Overall project health looks strong on throughput, with moderate risk in post-merge regressions around sessions and gateway routing.

---

## 2. Releases

### v0.23.0 — "The Hearth Release" (released 2026-11-05)

**Scope since v0.22.0:**
- ~10,080 commits · ~1,950 merged PRs
- ~8,720 files changed (~725,200 insertions, ~201,400 deletions)
- **~850 issues closed** · 666 contributors

**Notes:**
- The release framing ("Hermes kept the threshold…") suggests a **gateway/entry-point consolidation theme**, consistent with the unified gateway work landing in this cycle.
- **Breaking changes / migration notes:** Not explicitly stated in the provided excerpt. Given the unified-gateway rollout (#106742), users with **standalone `hermes serve`, Desktop URL/Cloud/SSH modes, and standalone profiles** should expect migration behavior changes — see the migration command (`hermes gateway migrate --multiplex`) referenced in [#149682](https://github.com/NousResearch/hermes-agent/issues/149682). Treat this release as a **protocol/plumbing transition release**; verify Desktop remote connections and Group Chats after upgrade.
- Tracked known post-release issues include profile-scoped auth ([PR #149813](https://github.com/NousResearch/hermes-agent/pull/149813)) and local image upload regressions on Desktop v0.23.0 ([#149770](https://github.com/NousResearch/hermes-agent/issues/149770)).

---

## 3. Project Progress

**Merged/closed today (6 PRs / 3 issues), highlights via closed items and fix PRs in flight:**

- **[#134889](https://github.com/NousResearch/hermes-agent/issues/134889) [CLOSED]** — Tracking issue for unified gateway post-merge fixes (227 comments). Closure signals the **primary stabilization wave for one-gateway-per-local-session is complete**; remaining work has been spun out.
- **[#139818](https://github.com/NousResearch/hermes-agent/issues/139818) [CLOSED]** — Remote-backend Desktop sessions unresumable (`session.resume` → `not_found`) is resolved; a notable **Desktop remote-backend reliability win**.
- **[#149787](https://github.com/NousResearch/hermes-agent/issues/149787) [CLOSED]** — Desktop Scheduled view: script-only (`no_agent`) run rows now openable with real output preview.
- **[PR #137942 [CLOSED]](https://github.com/NousResearch/hermes-agent/pull/137942)** — Bot-mode turn subprocess now bounded by the 600s per-attempt deadline (was an unbounded `subprocess.run` holding the per-profile delivery lock). Prevents wedged-child deadlocks.
- **Follow-on work formalized:** [#143547](https://github.com/NousResearch/hermes-agent/issues/143547) (open, 50 comments) now carries remaining unified-gateway problems; [#145171](https://github.com/NousResearch/hermes-agent/issues/145171) tracks the **entry-point cutover** of `serve`, remote Desktop, and web onto gateway session authority.

**Feature work advancing (open PRs):**
- **[#149630](https://github.com/NousResearch/hermes-agent/pull/149630)** — `/undo` now cross-references `/rollback` for file restoration (checkpoint awareness).
- **[#149786](https://github.com/NousResearch/hermes-agent/pull/149786)** / **[#149746](https://github.com/NousResearch/hermes-agent/pull/149746)** — CI cost reduction (same-runner job merging) and npm → **pnpm 12.15.0** workspace migration.
- **[#75833](https://github.com/NousResearch/hermes-agent/pull/75833)** — Cron scheduler state moved out of `jobs.json` into per-profile `cron/runtime.db` (long-running infra refactor).

---

## 4. Community Hot Topics

| Rank | Item | Comments | Signal |
|---|---|---|---|
| 1 | [#134889](https://github.com/NousResearch/hermes-agent/issues/134889) — Unified gateway post-merge tracking (CLOSED) | **227** | The defining workstream of this release cycle; massive coordination surface. |
| 2 | [#143547](https://github.com/NousResearch/hermes-agent/issues/143547) — Remaining unified-gateway problems | **50** | Continuation issue; still active the same day #134889 closed. |
| 3 | [#139818](https://github.com/NousResearch/hermes-agent/issues/139818) — Remote desktop session resume fails | **41** (👍3) | High user impact, now closed. |
| 4 | [#97681](https://github.com/NousResearch/hermes-agent/issues/97681) — Cross-gateway bot collaboration | **40** (👍4) | Strongest feature appetite signal: multi-agent, cross-machine, federated bots. |
| 5 | [#105758](https://github.com/NousResearch/hermes-agent/issues/105758) — Lifecycle guard false-positive on Node CLIs | **25** | Developer-workflow friction; scanner heuristics too broad. |

**Underlying needs:** Users are running **multi-profile, multi-surface** setups (CLI + TUI + Desktop + messaging bots + cron) and want them to behave as one coherent system. The unified gateway pain is the cost of that consolidation. Separately, **federation/collaboration between personal agents** (#97681) is the clearest "next platform" demand.

---

## 5. Bugs & Stability

**P0 / High severity (recent, tracked):**
- **[#149682](https://github.com/NousResearch/hermes-agent/issues/149682)** — Group Chats imported via `hermes gateway migrate --multiplex` fail **first turn for every member** with `runtime_draining` (room session title collides with standalone-era `Group: <room>` row). **Migration-day blocker**; no linked fix PR yet in provided data.
- **[#149784](https://github.com/NousResearch/hermes-agent/issues/149784)** — Desktop sessions become **permanently unrecoverable** (`admission_conflict`) after 90-day pruning; routing entries never refreshed, restore won't self-heal. **Data-availability risk**; no fix PR shown.
- **[#148742](https://github.com/NousResearch/hermes-agent/issues/148742)** — Legacy `state.db` without `INTEGER PRIMARY KEY id` accumulates **NULL-id rows** post-upgrade → compression fails with `int(None)`, `attempts_exhausted`. **Upgrade-path corruption risk**; no fix PR shown.

**P1 / P2 (open):**
- **[#149770](https://github.com/NousResearch/hermes-agent/issues/149770)** — Desktop local image uploads fail ("session not found") when a **legacy remote profile** overrides the negotiated wire. Regression in v0.23.0.
- **[#148531](https://github.com/NousResearch/hermes-agent/issues/148531)** — `owner-backfill` 409s "Exclusive maintenance refused" on every desktop boot in split topology.
- **[#147387](https://github.com/NousResearch/hermes-agent/issues/147387)** — Auth-gated dashboard **rejects Desktop token-mode credentials** (WebSocket upgrade always rejected).
- **[#148389](https://github.com/NousResearch/hermes-agent/issues/148389)** — Kanban worker **hangs at startup on Windows** (never issues model request, zero TCP connections).
- **[#132645](https://github.com/NousResearch/hermes-agent/issues/132645)** — Windows: `hermes update` leaves host gateway **down** when a named profile is sticky.
- **[#135171](https://github.com/NousResearch/hermes-agent/issues/135171)** — macOS: `update` SIGTERMs launchd **wrapper/helper processes** as "manual gateways"; warning never clears.
- **[#110374](https://github.com/NousResearch/hermes-agent/issues/110374)** (👍16, highest-reaction bug) — Slack status indicator **silently broken** with `slack-sdk >= 3.44` (`agents.sessions.setStatus` rejects free-text/empty).
- **[#148457](https://github.com/NousResearch/hermes-agent/issues/148457)** — `browser.executable_path` to a sandbox-capable Chromium still launched with `--no-sandbox` on AppArmor-userns hosts (**security-relevant**).

**Fix PRs in flight:**
- [#137562](https://github.com/NousResearch/hermes-agent/pull/137562) — Desktop clarify-lock expiry.
- [#148265](https://github.com/NousResearch/hermes-agent/pull/148265) — Matrix prose sanitizer deleting text resembling attributes.
- [#149802](https://github.com/NousResearch/hermes-agent/pull/149802) — `/stop`-cancelled delegation incorrectly waking the parent session.
- [#149813](https://github.com/NousResearch/hermes-agent/pull/149813) — Remote Config Nous token resolved under wrong (production) secret scope.
- [#117769](https://github.com/NousResearch/hermes-agent/pull/117769) — Delayed process notifications conflicting with newer user instructions.

**Pattern:** Post-unified-gateway, the **split-topology / legacy-DB / profile-scoping** seams are the dominant defect class. Migration from standalone backends is the highest-risk path right now.

---

## 6. Feature Requests & Roadmap Signals

- **[#97681](https://github.com/NousResearch/hermes-agent/issues/97681)** (40 comments, 👍4) — **Bots collaborate across gateways** (across machines now, across owners next). Strong candidate for the next platform milestone.
- **[#102269](https://github.com/NousResearch/hermes-agent/issues/102269)** — **Bot Marketplace**: discover/review/install "bot packs" via profiles + `distribution.yaml`. Natural commercialization/ecosystem bet.
- **[#98004](https://github.com/NousResearch/hermes-agent/issues/98004)** — **Per-group auto-continue budget** for Bot Mode rooms (item 4 of #89545) — safety-cap tuning.
- **[#96906](https://github.com/NousResearch/hermes-agent/issues/96906)** — Delegation closeout: hold one terminal response until task-scoped background work finishes (default-off, awaiting maintainer decision; prior PR deferred for gateway reliability).
- **[#145171](https://github.com/NousResearch/hermes-agent/issues/145171)** — **Entry-point cutover** of `serve`/remote Desktop/web onto gateway authority (security-tagged tracking issue).
- Plugin ecosystem is visibly growing: new catalog submissions for **PrintWatch** (3D printers, LAN mode) [#149803](https://github.com/NousResearch/hermes-agent/pull/149803), **qbo-ops** (QuickBooks Online) [#149801](https://github.com/NousResearch/hermes-agent/pull/149801), and **proofops** (completion-claim verification) [#149625](https://github.com/NousResearch/hermes-agent/pull/149625).

**Prediction for next release (v0.24.x):** entry-point cutover completion, remote/Cloud/SSH Desktop onto gateway sessions, cross-gateway bot messaging foundation, and continued migration hardening (Group Chat imports, legacy `state.db`). Bot Marketplace likely remains a larger future milestone.

---

## 7. User Feedback Summary

**Pain points:**
- **Migration/upgrade fragility** dominates: legacy `state.db` corruption (#148742), unrecoverable pruned Desktop sessions (#149784), Group Chat import failures (#149682), split-topology 409s (#148531). Users with long-lived installs are bearing the transition cost.
- **Windows and macOS lifecycle/reliability** issues around `hermes update` (#132645, #135171) are recurring.
- **Policy/heuristic false positives** frustrate automation: terminal lifecycle guard (#105758), skill-security scanner deadlock (#94353).
- **Structured-output redaction** breaks plugin parsing — `shell.exec` rewrites 32-hex tokens to `abcd12...ef34` ([#149778](https://github.com/NousResearch/hermes-agent/issues/149778)).

**Use cases visible in the data:** multi-profile gateways on macOS with 5 launchd-supervised instances (#135171); remote Desktop backends with basic auth (#139818, #147387); Bot Mode group rooms (#98004, #149682); cron-driven automation (#145314, #148389); messaging-bot integrations (Slack #110374, Matrix #148265); plugin-driven domain automation (3D printing, accounting).

**Satisfaction:** High engagement (666 contributors, 850 issues closed) and constructive, detailed bug reports reflect an invested community. Dissatisfaction concentrates on **transitional regressions** rather than core functionality; several high-visibility items were closed today, which should improve sentiment.

---

## 8. Backlog Watch

Long-lived/open items needing maintainer attention:

| Item | Age | Why it matters |
|---|---|---|
| [#75833](https://github.com/NousResearch/hermes-agent/pull/75833) — Cron state out of `jobs.json` | Since **2026-08-01** | Large infra refactor; carries 6 risk labels (session-state, message-delivery, security-boundary, compatibility, Windows, blast-moderate). Long-running, high-blast-radius. |
| [#94353](https://github.com/NousResearch/hermes-agent/issues/94353) — Skill approval **deadlock** | Since 2026-08-25 | Scanner-refused writes can never be human-approved — a logic deadlock, not a preference issue. |
| [#96906](https://github.com/NousResearch/hermes-agent/issues/96906) — Delegation closeout | Since 2026-08-28 | Awaiting **maintainer decision**; prior PR deferred. Roadmap ambiguity. |
| [#97681](https://github.com/NousResearch/hermes-agent/issues/97681) — Cross-gateway bot collaboration | Since 2026-08-29 | Highest-demand feature; "current review queued" — needs prioritization signal. |
| [#105758](https://github.com/NousResearch/hermes-agent/issues/105758) — Lifecycle guard false-positive | Since 2026-09-08 | Dev-workflow blocker; `needs-decision` label outstanding. |
| [#110374](https://github.com/NousResearch/hermes-agent/issues/110374) — Slack status broken (👍16) | Since 2026-09-13 | Highest-reaction bug; simple dependency-surface fix with outsized user-visible impact. |
| [#121796](https://github.com/NousResearch/hermes-agent/issues/121796) — xAI grok-4.7 effort allowlist | Since 2026-09-24 | One-line allowlist extension; `needs-repro` despite trivial root cause. |

---

**Bottom line:** Hermes Agent is a high-throughput, fast-moving platform in the middle of a major architectural consolidation (unified gateway). Release cadence and contributor base are strong; the primary health risk is **migration-path regressions** across legacy DBs, split topologies, and Desktop remote connections. Clearing the tagged fix PRs and completing the entry-point cutover (#145171) should be the maintainers' near-term focus, alongside the cheap-win Slack allowlist fix (#110374).

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

No activity in the last 24 hours.

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw Project Digest — 2026-11-06

## 1. Today's Overview

QwenPaw is in a high-velocity, release-quiet phase: 46 tracked items moved in the last 24h (14 issues, 32 PRs) with **zero new releases**, indicating the project is mid-cycle and accumulating fixes/features rather than shipping. Activity is strongly PR-weighted (32 PR updates vs. 14 issues, 20 open / 12 closed or merged), and roughly half of the visible PRs have already moved to `Under Review`. The work clusters around four themes: **plugin ecosystem hardening** (install, dependency checks, hook isolation, console routing), **durable session storage and pagination**, **Matrix channel reliability**, and **Windows platform parity**. Issue traffic is dominated by memory/retrieval correctness bugs (#8040, #8580, #8276) and a hard blocker in Creator review workflows (#8592). Overall health reads as *actively maintained with a growing bug surface in secondary subsystems* — no release pressure, but several multi-week-open defects are now recurring.

## 2. Releases

**None.** No new versions published in the last 24h, and the "Latest Releases" feed is empty.

Context inferred from issue reports: the most recent published build referenced by users is **2.2.2.post1**, while testers are running **Desktop 2.3.0b2 / 2.3.0b3** with a **`qwenpaw` 2.4** backend package and `reme_ai` 0.4.2.0. This version skew between the release channel and the beta branch is itself a signal worth watching — several reports (e.g. #8585, #8580) were filed against beta/desktop builds that are ahead of the public release.

## 3. Project Progress

Of the 20 highest-signal PRs, **6 are closed/merged** (the remaining 6 of the 12 closures fall outside the top-20 sample). Notable landings:

| PR | Change | Impact |
|---|---|---|
| [#7842](https://github.com/agentscope-ai/QwenPaw/pull/7842) | Isolate synchronous plugin hooks + event-loop lag watchdog (#7840) | **High** — a blocking `time.sleep`/`subprocess.run` in any plugin previously froze the host's single-threaded asyncio loop; this is the most consequential stability fix in the batch |
| [#6659](https://github.com/agentscope-ai/QwenPaw/pull/6659) | Model fallback with cooldown (fixes #2199, #1327, #2089) | **High** — automatic failover on rate limits, timeouts, service errors |
| [#6788](https://github.com/agentscope-ai/QwenPaw/pull/6788) | Shared root profile workspace for ACL store | Fixes Telegram/ACP channels rejecting previously approved users on every new task |
| [#7738](https://github.com/agentscope-ai/QwenPaw/pull/7738) | Filter unrecognized kwargs before OpenAI `completions.create()` | Removes `streamIdleTimeoutMs`-class TypeError from proxy/middleware layers |
| [#8558](https://github.com/agentscope-ai/QwenPaw/pull/8558) | Simplify Matrix legacy state migration and device locking | Unblocks valid Matrix accounts; follow-up to #8187 |
| [#8597](https://github.com/agentscope-ai/QwenPaw/pull/8597) | `view_audio` tool catalog translations | Console i18n completeness |

**In-flight majors:** [#8532](https://github.com/agentscope-ai/QwenPaw/pull/8532) (XXXL: append-only per-session SQLite history, paginated history, native Turn forks), [#8586](https://github.com/agentscope-ai/QwenPaw/pull/8586) (XXXL: unify ACP and native session configuration), [#8598](https://github.com/agentscope-ai/QwenPaw/pull/8598) (XXL: plugin dependency-check refactor), [#8472](https://github.com/agentscope-ai/QwenPaw/pull/8472) (XL: reranker model center with multi-query precision history).

## 4. Community Hot Topics

Engagement is concentrated on **Issues**; PR comment counts are unreported (`undefined`) in this dataset, so ranking below is issue-driven.

1. **[#8040](https://github.com/agentscope-ai/QwenPaw/issues/8040) — 8 comments** (opened 2026-09-30, still open). Embedding reindex silently drops a whole batch when one CJK chunk exceeds the provider's per-item token limit, while the loop logs `processed=126/126`. Marked a **recurrence of #5950**.
   *Underlying need:* users need **observability and atomicity in batch jobs** — partial failures must not be reported as success, and index reconciliation must be trustworthy. The recurrence label suggests the first fix addressed the symptom, not the root batching contract.
2. **[#7026](https://github.com/agentscope-ai/QwenPaw/issues/7026) — 4 comments, CLOSED.** `chat_template_kwargs` auto-injected without `extra_body` wrapping → `openai` SDK `TypeError` on all calls with `deepseek-v4-pro`.
   *Underlying need:* the provider compatibility layer must not leak raw kwargs into SDK calls; related to merged #7738.
3. **[#8276](https://github.com/agentscope-ai/QwenPaw/issues/8276) — 4 comments.** ReMe reranker enablement accepts an empty `api_key`, surfacing only as "unexpected error".
   *Underlying need:* **fail-fast config validation with actionable messages** rather than generic error swallowing — the second ReMe memory-config defect this cycle.
4. **[#3570](https://github.com/agentscope-ai/QwenPaw/issues/3570) — 4 comments** (open since 2026-04-19). "All Chats" list has no pagination; large chat counts load slowly, and control-sessions pagination reportedly malfunctions.
   *Underlying need:* **scale-out usability** — directly addressed by #8532's paginated history work.

## 5. Bugs & Stability

Ranked by severity, with fix-PR status:

| Severity | Issue | Status / Fix |
|---|---|---|
| **Critical** | [#8040](https://github.com/agentscope-ai/QwenPaw/issues/8040) embedding reindex drops batches silently (CJK token limit); recurrence of #5950 | **No linked fix PR.** Open 37 days — highest-risk item |
| **Critical** | [#8592](https://github.com/agentscope-ai/QwenPaw/issues/8592) Creator review round permanently undecidable — `REJECT` always 422 "visual artifact reference references missing id"; plus expired `source_url` 502 and `frame_stats` ignoring `CREATOR_FFMPEG_PATH` | **No linked fix PR.** Bundles 3 defects; author offers to split |
| **High** | [#8580](https://github.com/agentscope-ai/QwenPaw/issues/8580) Memory tag index never rebuilds → `/memory/tags` 503; tags generated but unreadable | **No linked fix PR.** Beta/desktop stack |
| **High** | [#8585](https://github.com/agentscope-ai/QwenPaw/issues/8585) `recall_history

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw Project Digest — 2026-11-06

## 1. Today's Overview

ZeroClaw shows a **high-velocity, security-hardening phase** consistent with the run-up to the `v0.9.0` release: of 30 issues touched in the last 24h, 25 remain open/active and 5 closed — three of which were `priority:p1` / `release:v0.9.0` identity-access security bugs (#11127, #11123, #12051, #12046). PR throughput is unusually high at 50 updated (48 open, only 2 merged/closed), indicating a **review bottleneck**: many substantial PRs (#11289, #11313, #12244, #11286) are marked `do-not-merge` or `status:blocked` and are stacking on dependencies. A large cluster of issues and PRs authored by `JordanTheJet` and `Audacity88` (#12245–#12254, #12246, #12247, #12248, #12249) reveals a coordinated **"scoped dashboard chat" + identity-access follow-up campaign** in flight. No new releases were cut today.

## 2. Releases

**None.** No new releases were published in the last 24h. Note that multiple merged/closed items and open PRs carry the `release:v0.9.0` label, suggesting the next cut is being staged but is not yet ready (several blockers below).

## 3. Project Progress

Only **2 PRs merged/closed** in the window (both outside the top-20 shown), so concrete landing is light relative to activity. However, **5 issues closed**, three of which are security-relevant:
- #11127 [CLOSED] Session-data tools bypass principal ownership checks (`S0` security)
- #11123 [CLOSED] SOP execution accepts wildcard tool selectors without `tools:execute` (`S0`)
- #12051 [CLOSED] `sop_workshop apply` scoped-principal privilege escalation
- #12046 [CLOSED] Non-admin wildcard tool selectors retain operator-only tools
- #10526 [CLOSED] RFC accepted/completed: append-only session event history + deterministic state replay

Notable PRs advancing features (open, not yet merged):
- #12258 `sessions_prompt` tool for inter-agent prompting
- #12259 scaffold for async `sessions_message` (RFC #11027 / #11119)
- #12059 runtime-profile thinking for OpenAI
- #11194 Microsoft Teams channel
- #11680 Firecrawl web search provider
- #11744 reasoning streaming in Telegram/Slack drafts

## 4. Community Hot Topics

| Item | Signal | Link |
|---|---|---|
| #10526 RFC: append-only session history / deterministic replay | 16 comments (most discussed), now **closed** | [Issue #10526](https://github.com/zeroclaw-labs/zeroclaw/issues/10526) |
| #8132 Evaluate Rust/WASM web UI vs React/Vite | 13 comments, 1 👍, still open since June | [Issue #8132](https://github.com/zeroclaw-labs/zeroclaw/issues/8132) |
| #10926 Matrix `send_via` treats peers as rooms | 8 comments, `priority:p2` | [Issue #10926](https://github.com/zeroclaw-labs/zeroclaw/issues/10926) |
| #9779 SOP `sops_dir` default silently ignored | 7 comments, `priority:p1` | [Issue #9779](https://github.com/zeroclaw-labs/zeroclaw/issues/9779) |
| #11703 Deferred tool activation busts prompt cache | 5 comments, cost/perf | [Issue #11703](https://github.com/zeroclaw-labs/zeroclaw/issues/11703) |

**Underlying needs:** The community is converging on **durable, replayable, attributable session state** (#10526, #9597) — the recurring theme across the most-discussed threads is that ZeroClaw's conversational state model is too mutable and fragmentation across logs/receipts/control-plane is causing observability and attribution gaps. The WASM web-UI debate (#8132) reflects a desire to eliminate the Node/Vite toolchain from the runtime for supply-chain and simplicity reasons.

## 5. Bugs & Stability

Ranked by severity (fix PR status noted):

**S0 / p1 — Security (mostly resolved):**
- ~~#11127~~, ~~#11123~~, ~~#12051~~, ~~#12046~~ — **closed today**, but reviewers explicitly flag that **#11126 (OPEN)** is only *partially* addressed by PR #10412 and "landing that PR alone must not be treated as" full resolution. [#11126](https://github.com/zeroclaw-labs/zeroclaw/issues/11126)
- #11124 [OPEN] SOP decision gate ignores strict fallback — `S0`, **no fix PR referenced** [#11124](https://github.com/zeroclaw-labs/zeroclaw/issues/11124)
- #11126 [OPEN] Queued session ops retain revoked admin ownership — **partial fix in #10412** [#11126](https://github.com/zeroclaw-labs/zeroclaw/issues/11126)

**S2 / degraded behavior:**
- #10926 Matrix `send_via` peer-vs-room misresolution [#10926](https://github.com/zeroclaw-labs/zeroclaw/issues/10926)
- #9572 Gateway WebSocket turn overflows Tokio worker stack (dev profile) [#9572](https://github.com/zeroclaw-labs/zeroclaw/issues/9572)
- #12145 Plugin store limiter leaves wasmtime default 10,000 memories/tables per store — **memory budget bypass** [#12145](https://github.com/zeroclaw-labs/zeroclaw/issues/12145)
- #12043 & #12225 `glob_search` silently drops authorized matches under allowed roots (two related defects, opposite directions) [#12043](https://github.com/zeroclaw-labs/zeroclaw/issues/12043), [#12225](https://github.com/zeroclaw-labs/zeroclaw/issues/12225)
- #12228 `cron update --agent` reports success but never reassigns [#12228](https://github.com/zeroclaw-labs/zeroclaw/issues/12228)
- #12194 Unknown model silently falls back to 32k context window, failing valid turns [#12194](https://github.com/zeroclaw-labs/zeroclaw/issues/12194)
- #12047 OIDC bearer verification has no attempt limiter/outbound cap (DoS/amplification) [#12047](https://github.com/zeroclaw-labs/zeroclaw/issues/12047)
- #12050 OIDC callback page has no browser binding (login CSRF) [#12050](https://github.com/zeroclaw-labs/zeroclaw/issues/12050)

**S3 / minor:**
- #11703 prompt cache bust on deferred tool activation [#11703](https://github.com/zeroclaw-labs/zeroclaw/issues/11703)
- #12236 cached HTTP clients shared across test runtimes → flaky "dispatch task is gone" [#12236](https://github.com/zeroclaw-labs/zeroclaw/issues/12236)

**Assessment:** The S0 cluster closure is a genuine health win, but the **security surface is still active** — OIDC (CSRF, rate-limiting), WASM sandbox limits, and glob_search authorization semantics all have open, high-risk threads with `v0.9.0` labels and no confirmed fix PRs.

## 6. Feature Requests & Roadmap Signals

- **Session durability & inter-agent messaging** — #9597 (durable peer-agent turns), plus PRs #12258 (`sessions_prompt`) and #12259 (async `sessions_message`). Strongly points to a **sessions/messaging subsystem** landing in the next version.
- **Web-managed attachments** — #12181 (dashboard document upload, first #9488 Phase 2 Web slice) [#12181](https://github.com/zeroclaw-labs/zeroclaw/issues/12181)
- **Non-admin dashboard chat** — #11984 & #12247 (scoped chat for signed-in non-admins) [#11984](https://github.com/zeroclaw-labs/zeroclaw/issues/11984)
- **Provider breadth** — Firecrawl web search (#11680), Microsoft Teams channel (#11194), OpenRouter balance (#11845/#11846)
- **Rust/WASM UI migration** — #8132, still under evaluation

**Prediction for next version (v0.9.0):** session/messaging primitives, scoped dashboard chat, and identity-access hardening are the most likely headline features; provider integrations (Firecrawl, Teams) may slip unless review unblocks.

## 7. User Feedback Summary

- **Cost & performance sensitivity:** User `Audacity88` repeatedly flags **prompt-cache busting** (#11703) and **hidden 32k context fallback** (#12194) — real production friction around token spend and silent degradation.
- **Silent-failure frustration:** A recurring theme across #9779 (SOP never loads), #12228 (`cron update` false success), #12043/#12225 (`glob_search` silently drops files) — users are **repeatedly burned by operations that report success but do nothing**. This is the single strongest dissatisfaction signal in the data.
- **Security researcher engagement:** `IftekharUddin` and `Audacity88` are filing detailed, well-scoped S0/p1 reports, indicating active adversarial review — good for long-term health, but signals the **authorization model needs a consolidation pass** rather than point fixes.
- **Maintainer-load pressure:** `JordanTheJet`'s `follow-up` PRs suggest the team is doing disciplined post-review cleanup, but the volume of stacked `do-not-merge`/blocked PRs implies **review capacity, not contribution, is the current bottleneck**.

## 8. Backlog Watch

Long-lived or blocked items warranting maintainer attention:

- **#8132** (open since **2026-06-22**, 13 comments) — WASM UI evaluation, `needs-author-action`; decision debt accumulating for ~4.5 months. [#8132](https://github.com/zeroclaw-labs/zeroclaw/issues/8132)
- **#9597** (open since **2026-07-31**) — durable peer-agent turns, `status:in-progress`, no landing yet. [#9597](https://github.com/zeroclaw-labs/zeroclaw/issues/9597)
- **#9779 / #9770** (open since early **August**) — SOP and cron silent-failure bugs, both `priority:p1`, both still open ~3 months. [#9779](https://github.com/zeroclaw-labs/zeroclaw/issues/9779), [#9770](https://github.com/zeroclaw-labs/zeroclaw/issues/9770)
- **PR #11313** — `config set/patch` not published to running daemon; `priority:p1`, `release-gate`, `needs-maintainer-review`, open since **2026-10-01**. Auth-policy staleness is a real operational risk. [#11313](https://github.com/zeroclaw-labs/zeroclaw/pulls/11313)
- **PR #11289** — stable RPC denial reason identifiers; open since **2026-09-30**, `do-not-merge`, `risk:high`. [#11289](https://github.com/zeroclaw-labs/zeroclaw/pulls/11289)
- **PR #11286** — bind gateway pairing tokens to roster users; open since **2026-09-30**, `domain:security`, `priority:p2`. [#11286](https://github.com/zeroclaw-labs/zeroclaw/pulls/11286)
- **PR #12244** — scoped dashboard chat; `status:blocked` on #11964 and #12232, `do-not-merge`, `release:v0.9.0`. This is the critical path for the dashboard-chat campaign. [#12244](https://github.com/zeroclaw-labs/zeroclaw/pulls/12244)

**Bottom line:** ZeroClaw is healthy on contribution volume and has clearly closed a meaningful security milestone today, but **review throughput and stacked dependencies (esp. #12244 → #11964/#12232, and #11126 → #10412) are the gating factors** for the `v0.9.0` release. The most actionable health signal is the cluster of silent-failure bugs, which represent the clearest user-trust risk.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/SnowEagle-Kn/agents-radar).*