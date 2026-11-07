# OpenClaw Ecosystem Digest 2026-11-07

> Issues: 365 | PRs: 500 | Projects covered: 5 | Generated: 2026-11-07 02:20 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw Deep Dive

# OpenClaw Project Digest — 2026-11-07

---

## 1. Today's Overview

OpenClaw remains in a high-velocity, high-contention maintenance phase: **365 issues** and **500 pull requests** were updated in the last 24 hours, with **160 issues closed** (43.8% of touched issues) and **123 PRs merged/closed** (~24.6% of touched PRs). No new release shipped today, and the most-commented issue in the tracker is a **P0 crash-loop regression** ([#160959](https://github.com/openclaw/openclaw/issues/160959), 80 comments) that has been open since late September, signaling that some release-blocking defects are outliving multiple release cycles. The PR queue is the main health concern: **377 PRs remain open** versus 123 closed today, and a visible share of the top-commented PRs are drafts, placeholders, or "needs proof" candidates rather than merge-ready work. Activity is strong, but throughput is skewed toward intake and triage rather than landing fixes — consistent with a project under heavy user load and aggressive automated triage (`clawsweeper:*` labels on nearly every issue).

---

## 2. Releases

**No new releases today.** The Latest Releases section is empty.

Context from issue traffic: the current stable line appears to be **2026.10.10**, with a prerelease **2026.11.5-beta.1** referenced in [#141474](https://github.com/openclaw/openclaw/issues/141474) and [#193962](https://github.com/openclaw/openclaw/issues/193962). Notably, [#193962](https://github.com/openclaw/openclaw/issues/193962) reports that affected users on 2026.10.10 **cannot reach the fix in 2026.11.5-beta.1 via `openclaw update`** because the installed updater's 10-second SQLite header probe times out — a distribution-channel blocker worth prioritizing before the next stable cut.

---

## 3. Project Progress

**Merged/closed PRs today (selected):**

| PR | Change |
|---|---|
| [#194384](https://github.com/openclaw/openclaw/pull/194384) | `fix(cron)`: one-shot finalization and enable/disable transitions now work on PostgreSQL 16 (`function json(unknown) does not exist`); also repairs PostgreSQL 18 cache conformance assertion |
| [#194399](https://github.com/openclaw/openclaw/pull/194399) | `fix(media)`: interrupted attachment downloads are now cancelled instead of running until process exit |
| [#194407](https://github.com/openclaw/openclaw/pull/194407) | `fix(ci)`: preserves compiled-worker reuse when Vitest metadata grows |
| [#194405](https://github.com/openclaw/openclaw/pull/194405) | `test(ui)`: fixes flaky configured-provider icon test (scheduled run 42319656353) |
| [#194411](https://github.com/openclaw/openclaw/pull/194411) | `fix(plugins)`: restores missing OpenClaw ID Access artwork (unblocks artwork check) |
| [#194190](https://github.com/openclaw/openclaw/pull/194190) | `fix(ui)`: same-day timestamps show time only in tight rows |
| [#194152](https://github.com/openclaw/openclaw/pull/194152) | `ui`: dialog closes and ghost icon actions unified onto `IconButton` |

**Issues closed today (selected):** [#166017](https://github.com/openclaw/openclaw/issues/166017) (MCP session idle-TTL default not applied → 256-child spawn-broker exhaustion), [#178291](https://github.com/openclaw/openclaw/issues/178291) (P0: 2026.10.4→10.5 chat update fails post-activation Doctor), [#164923](https://github.com/openclaw/openclaw/issues/164923) (short-term promotion 0/512 in one workspace), [#155457](https://github.com/openclaw/openclaw/issues/155457) (Anthropic malformed streamed JSON args), [#168794](https://github.com/openclaw/openclaw/issues/168794) (Codex relay >32 MiB image → 502), [#165433](https://github.com/openclaw/openclaw/issues/165433) (reconnect envelope rejection), [#164690](https://github.com/openclaw/openclaw/issues/164690) (duplicate stdio MCP spawn under `claude-cli`).

**Assessment:** the day's landed work is predominantly **correctness and CI/test-hygiene**, not user-facing features. The most consequential closure is [#166017](https://github.com/openclaw/openclaw/issues/166017) — a hard-cap exhaustion bug that bricked all exec/read/MCP starts — but the fix is not yet confirmed shipped in a stable release.

---

## 4. Community Hot Topics

**#160959 — [P0] Gateway blocks for minutes while capturing large external plugins (2026.9.6 regression)** · 80 comments · [link](https://github.com/openclaw/openclaw/issues/160959)
The single hottest thread in the project. Since plugin generation capture landed (#144252), a Gateway with a large external/workspace plugin dependency tree blocks its event loop for **minutes** at startup and on every prepared-runtime republish. Labeled `impact:crash-loop`, `P0`, `clawsweeper:no-new-fix-pr`. The underlying need is architectural: plugin capture must move off the main V8 thread or become incremental/cached. Related liveness evidence appears in [#166641](https://github.com/openclaw/openclaw/issues/166641) (`web_fetch` fallback synchronously capturing plugins).

**#164294 — Gateway state DB WAL auto-checkpoint never completes** · 39 comments · [link](https://github.com/openclaw/openclaw/issues/164294)
A PASSIVE checkpoint is pinned by an in-process reader (`checkpointedFrames=866`, `logFrames=17091`, `consecutiveBlocked` climbing). Users are effectively watching unbounded WAL growth with no remediation path. Needs a long-lived-reader audit of `state/openclaw.sqlite`.

**#177656 — [P1] Native host exec returns shared-state integrity-admission error** · 30 comments · [link](https://github.com/openclaw/openclaw/issues/177656)
≥28 failures on stable 2026.10.5, child effects unproved, no fix. Error: `Direct shared-state reader requires current integrity admission`. This is the highest-severity issue without a linked fix PR.

**#150635 — Dreaming deep phase never promotes (short-term recall eviction)** · 25 comments · [link](https://github.com/openclaw/openclaw/issues/150635)
At the 512-entry cap, nightly ingestion stages hundreds of zero-recall entries that evict genuinely recalled ones, so promotion never occurs. Coupled with [#164923](https://github.com/openclaw/openclaw/issues/164923) (closed today), this points to a systemic **memory-tier capacity/eviction policy** problem rather than two isolated bugs.

**#191944 — `gateway stop` / `stop --force` fail on macOS** · 23 comments · [link](https://github.com/openclaw/openclaw/issues/191944)
`ServiceInspectionError: Process ancestry could not be fully inspected`. A basic lifecycle operation is broken on a first-class desktop platform — high UX blast radius for low implementation complexity.

**PR-side:** the most notable open candidates are [#194376](https://github.com/openclaw/openclaw/pull/194376) (`feat(gateway)`: admission boundary for `sessions_send` wake starts — addresses concurrent-sender bypass of batching/rate limits), [#182383](https://github.com/openclaw/openclaw/pull/182383) (`feat(plugin-sdk)`: access-policy revision caching, security-sensitive), and [#194133](https://github.com/openclaw/openclaw/pull/194133) (UI menu-row anatomy normalization). The queue also contains explicit non-work — [#191509](https://github.com/openclaw/openclaw/pull/191509) ("empty iOS handoff placeholder — do not use") and [#188726](https://github.com/openclaw/openclaw/pull/188726) (investigation placeholder) — which inflates the open count.

---

## 5. Bugs & Stability

Ranked by severity. "Fix PR" indicates a linked/merged candidate exists in the data.

| Sev | Issue | Symptom | Fix PR? |
|---|---|---|---|
| **P0** | [#160959](https://github.com/openclaw/openclaw/issues/160959) | Gateway event-loop blocked for minutes by plugin capture; crash-loop | No |
| **P1** | [#177656](https://github.com/openclaw/openclaw/issues/177656) | Native host exec: shared-state integrity-admission error, 28+ occurrences | No |
| **P1** | [#191847](https://github.com/openclaw/openclaw/issues/191847) | 2026.10.10 regression: large session repeatedly OOMs the 512 MB SQLite worker; **every** turn fails with "admission is closed" | No |
| **P1** | [#192959](https://github.com/openclaw/openclaw/issues/192959) | Gateway refuses to start after K8s pod restart (fsGroup remount invalidates client credential dir) | No |
| **P1** | [#185656](https://github.com/openclaw/openclaw/issues/185656) | Large zstd transcript archive exceeds Node string limit in media-persistence migration — **regression of #170847 despite merged fix #170853**; blocks updates | Merged but ineffective |
| **P1** | [#182647](https://github.com/openclaw/openclaw/issues/182647) | Cloud-worker turns fail pre-inference: "Session capability metadata requires worker preparation" | `queueable-fix` |
| **P1** | [#166641](https://github.com/openclaw/openclaw/issues/166641) | `web_fetch` cold fallback synchronously captures plugins on Gateway main thread → liveness stalls (V8 profile captured) | No |
| **P1** | [#155563](https://github.com/openclaw/openclaw/issues/155563) | 2026.9.5 regression: `service-child-relay`/`group-anchor`/MCP groups retained after successful turn | No |
| **P1** | [#126950](https://github.com/openclaw/openclaw/issues/126950) | Telegram `tgcb1:` opaque callback payloads silently dropped for non-plugin callers (message loss) | Linked PR open |
| **P1** | [#157182](https://github.com/openclaw/openclaw/issues/157182) | Control UI forces `/chat/main` and retries forever when ambient main session isn't visible (security-flagged) | No |
| **P1** | [#162087](https://github.com/openclaw/openclaw/issues/162087) | 9.7: scheduled `announce` fails for pre-9.7 account-mode cron jobs (missing `ownerOrigin`) | No |
| **P2** | [#164294](https://github.com/openclaw/openclaw/issues/164294) | WAL checkpoint permanently blocked | No |
| **P2** | [#141474](https://github.com/openclaw/openclaw/issues/141474) | `sessions_yield` strands `agents_wait` forever; `outputSchema` inert on `claude-cli` — **fix only in 2026.11.5-beta.1, not stable** | In prerelease |
| **P2** | [#193962](https://github.com/openclaw/openclaw/issues/193962) | SQLite header-probe timeout fix (#187197) unreachable from 2026.10.10 via `openclaw update` | Fix exists, distribution blocked |
| **P2** | [#182891](https://github.com/openclaw/openclaw/issues/182891) | Prompt cache never reused — `cache read dropped N -> 0` on every request | No |
| **P2** | [#191944](https://github.com/openclaw/openclaw/issues/191944) | macOS gateway stop fails (process ancestry inspection) | No |
| **P2** | [#158127](https://github.com/openclaw/openclaw/issues/158127) | 2026.9.6 multi-agent runtime publication failures + Codex startup timeouts | No |
| **P2** | [#167609](https://github.com/openclaw/openclaw/issues/167609) | Code Mode reports guest TypeErrors as `internal_error` (live repro on main `e29577b7d7d`) | `fix-shape-clear` |
| **P2** | [#123792](https://github.com/openclaw/openclaw/issues/123792) | Assistant turns persisted/rendered twice with `claude-cli` backend | No |

**Stability read:** the dominant failure cluster is **Gateway main-thread blocking and SQLite worker pressure** (#160959, #166641, #191847, #164294, #185656). A second cluster is **runtime lifecycle leakage** (#155563, #166017 now closed, #164690 closed). A third is **update/installation integrity** (#185656, #193962, #178291) — worrying because it can trap users on known-bad versions. Two P1 regressions (#185656, #191847) are confirmed present in the current stable line.

---

## 6. Feature Requests & Roadmap Signals

**Cost & observability**
- [#13219](https://github.com/openclaw/openclaw/issues/13219) — Per-model usage logging for cost tracking (open since Feb 2026, 10 comments, 👍1). Repeatedly reinforced by [#143017](https://github.com/openclaw/openclaw/issues/143017) (21.3M cache-write tokens measured) and [#130257](https://github.com/openclaw/openclaw/issues/130257) (delegated ACP child run $14.14 vs parent $0.04, unattributable). **Strong candidate for near-term roadmap**: three independent cost-visibility issues with production numbers attached.
- [#178373](https://github.com/openclaw/openclaw/issues/178373) — No run-level turn/token budget; tool-loop detection opt-in (defaults off); retained history re-billed every turn.

**Agent/session management**
- [#166678](https://github.com/openclaw/openclaw/issues/166678) — Hide agents from pickers while keeping them spawnable (WebUI/macOS/iOS), 👍1.
- [#158800](https://github.com/openclaw/openclaw/issues/158800) — Browse/fork prior channel-chat segments after `/new` in Control UI.
- [#103067](https://github.com/openclaw/openclaw/issues/103067) — Centralize chat-session naming; define subagent session lifetime & cross-channel persistence.

**Channels & integrations**
- [#136271](https://github.com/openclaw/openclaw/issues/136271) — msteams: dynamically resolve team SharePoint site for channel uploads.
- [#162087](https://github.com/openclaw/openclaw/issues/162087) — cron `announce` account-mode ownership (bug, but drives a delivery-model decision).

**UX & configuration**
- [#163739](https://github.com/openclaw/openclaw/issues/163739) — Make Ctrl+Shift+A (Archive) configurable; collides with browser shortcuts, 👍1. Low effort, clear win.
- [#79223](https://github.com/openclaw/openclaw/issues/79223) — Configurable Dream Diary language (hardcoded English prompt in `memory-core`), 👍2 — a recurring complaint from non-English workspaces.
- [#164620](https://github.com/openclaw/openclaw/issues/164620) — Setting to disable the hosted plugin catalog feed (`clawhub.ai` hardcoded).
- [#130247](https://github.com/openclaw/openclaw/issues/130247) — Cron jobs cannot end: no `--until`/`--max-runs`, no cumulative run counter.

**Prediction:** the highest-probability next-cycle items are **cost/usage aggregation (#13219 family)**, **cron lifecycle bounds (#130247)**, and **keybinding configurability (#163739)** — all are narrow, well-specified, and backed by repeated reports. The memory/dreaming work (#150635, #79223) likely needs a design decision (`clawsweeper:needs-product-decision`) before implementation.

---

## 7. User Feedback Summary

**Pain points, by frequency of signal:**

1. **Startup/upgrade fragility.** Users on 2026.9.6→2026.10.10 report Gateways that block for minutes, migrations that fail on large archives, and updates that cannot be applied to escape the broken version (#160959, #185656, #193962, #178291). This is the single largest source of dissatisfaction.
2. **Memory feature disappointment.** The "dreaming"/short-term-recall system is widely reported as not working as advertised — 0/512 promotion in one workspace while siblings hit 50–90% (#164923, #150635). Users are doing careful comparative debugging themselves, which suggests documentation/observability gaps.
3. **Cost opacity.** Multiple users quantify large, unattributable spend (#143017: 21.3M cache-write tokens; #130257: 100× parent/child cost asymmetry). The prompt cache is reported as never reused (#182891), directly converting into billed tokens.
4. **Platform-specific lifecycle breakage.** macOS `gateway stop` (#191944), Kubernetes fsGroup restarts (#192959), Android polling an unknown agent every 0.8 s (#167316, closed). Desktop/container users feel like second-class citizens relative to the Linux systemd path.
5. **Multi-agent complexity.** Runtime publication superseded errors, Codex startup timeouts, duplicate MCP spawns, 145 MB wrapper overhead per stdio child (#158127, #164690, #164017-family). Operators running many agents report unpredictable resource growth.

**Satisfaction signals:** reports are consistently detailed, reproducible, and version-pinned — the user base is technically sophisticated and invested. Several closures today (#166017, #164923, #178291) drew substantive multi-week threads, indicating users do track fixes through to resolution. The main dissatisfaction is **time-to-fix on P0/P1 items**, not responsiveness to reports.

---

## 8. Backlog Watch

**Long-unanswered, high-value issues:**

| Age | Item | Why it matters |
|---|---|---|
| ~9 months | [#13219](https://github.com/openclaw/openclaw/issues/13219) Per-model usage logging (Feb 2026) | Cheapest possible answer to the loudest cost complaints; still `needs-product-decision` |
| ~6 months | [#79223](https://github.com/openclaw/openclaw/issues/79223) Dream Diary language (May 2026) | Hardcoded prompt in `memory-core`; trivial fix, international users blocked |
| ~4 months | [#112638](https://github.com/openclaw/openclaw/issues/112638) `session.maintenance` enforce mode doesn't bound the store | Silent unbounded disk growth; `maxEntries`/`maxDiskBytes` are effectively advisory |
| ~4 months | [#103067](https://github.com/openclaw/openclaw/issues/103067) Centralize chat-session naming / subagent lifetime | Foundational for multi-agent UX; keeps generating duplicate reports |
| ~3 months | [#126950](https://github.com/openclaw/openclaw/issues/126950) Telegram opaque callbacks dropped | Message loss on a primary channel; linked PR open but stalled |
| ~3 months | [#130247](https://github.com/openclaw/openclaw/issues/130247) / [#130257](https://github.com/openclaw/openclaw/issues/130257) Cron has no end condition; ACP run cost unattributable | Both filed by the same operator; scheduler lacks basic lifecycle semantics |
| ~2.5 months | [#123792](https://github.com/openclaw/openclaw/issues/123792) Double-rendered assistant turns (`claude-cli`) | Visible data-integrity/UX defect on Control UI + Android |

**Stale PRs needing maintainer attention:**

- [#75225](https://github.com/openclaw/openclaw/pull/75225) — `feat(agents)`: expose configured descriptions in `agents_list`. Open since **2026-04-30**, `proof: sufficient`, `status: ⏳ waiting on maintainer`. Over six months with a sufficient proof packet is a process failure, not a technical one.
- [#125969](https://github.com/openclaw/openclaw/pull/125969) — `fix(gateway)`: scope image capability discovery. Open since 2026-08-18, `ready for maintainer look`.
- [#130231](https://github.com/openclaw/openclaw/pull/130231) — `ci(i18n)`: non-publishing locale refresh. Open since 2026-08-26, flagged `merge-risk: 🚨 security-boundary`, `needs proof`.
- [#182383](https://github.com/openclaw/openclaw/pull/182383) — `feat(plugin-sdk)`: access-policy revision caching (XL, security-sensitive). Needs a security reviewer, not more author iteration.
- [#161362](https://github.com/openclaw/openclaw/pull/161362) — `fix(agents)`: stop warning that documented core tool groups need a plugin. `security-review-required`, waiting on author since 2026-09-29.

**Recommendation:** the open-PR backlog (377) is now large enough to obscure real work — several top-listed PRs are explicitly non-mergeable placeholders ([#191509](https://github.com/openclaw/openclaw/pull/191509), [#188726](https://github.com/openclaw/openclaw/pull/188726)). Closing or converting those to draft issues, and prioritizing the two P0/P1 regressions with no fix PR ([#160959](https://github.com/openclaw/openclaw/issues/160959), [#177656](https://github.com/openclaw/openclaw/issues/177656)), would materially improve both perceived and actual project health.

---

## Cross-Ecosystem Comparison

⚠️ Comparative analysis generation failed.

---

## Peer Project Reports

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — Project Digest, 2026-11-07

## 1. Today's Overview

Activity is high and heavily maintenance-weighted: 50 issues and 50 PRs were touched in the last 24 hours, with 5 issues closed and 13 PRs merged/closed, but **no new release** was cut. The day's work clusters into three streams: cleanup and follow-ups from the npm→pnpm workspace switch (#149746 lineage), a large stack of update-path hardening PRs from `alt-glitch` (tracked under #150383), and continued pressure on gateway/session-store scalability and the "session authority" cutover (#145171). The most-discussed item, #139818 (42 comments), closed today after a long thread on remote-backend Desktop sessions being unresumable — a sign that session-state correctness is the project's dominant pain point. Health reads as **active but regression-heavy**: recent infrastructure changes (pnpm, state-store relocation, update swap mechanics) are generating a visible tail of follow-up bugs, including one open **P0 data-integrity issue on Windows** (#147938).

---

## 3. Project Progress

The 20 most-commented PRs shown are all still **open**; the 13 merged/closed PRs are not itemized in this sample, so merged-feature attribution is limited to what is visibly in flight.

**In-flight, high-impact work:**

| Area | PR | Status |
|---|---|---|
| Platform extraction | [#150210](https://github.com/NousResearch/hermes-agent/pull/150210) — Yuanbao moves out of core into a Hermes Labs plugin, with automatic migration for existing users | Open, CI-reviewed |
| Session read API | [#150102](https://github.com/NousResearch/hermes-agent/pull/150102) — `session.transcript`: read a stored session without attaching (picker fields, transcript pages, compactions, subagent transcripts) | Open |
| State backend | [#150421](https://github.com/NousResearch/hermes-agent/pull/150421) — optional PostgreSQL backend for conversation state (stage B), stacked on #150332 | Open |
| Update path | [#150385](https://github.com/NousResearch/hermes-agent/pull/150385), [#150386](https://github.com/NousResearch/hermes-agent/pull/150386), [#150387](https://github.com/NousResearch/hermes-agent/pull/150387), [#150405](https://github.com/NousResearch/hermes-agent/pull/150405), [#150406](https://github.com/NousResearch/hermes-agent/pull/150406), [#150407](https://github.com/NousResearch/hermes-agent/pull/150407), [#150408](https://github.com/NousResearch/hermes-agent/pull/150408) | Open |
| Test/CI hardening | [#150411](https://github.com/NousResearch/hermes-agent/pull/150411) (upgrade suites start from real releases), [#150418](https://github.com/NousResearch/hermes-agent/pull/150418) (upgrade E2E shards on update-path PRs), [#150195](https://github.com/NousResearch/hermes-agent/pull/150195) (de-flake refusal-stamp test) | Open |
| pnpm follow-ups | [#150430](https://github.com/NousResearch/hermes-agent/pull/150430) (cache inputs, undeclared deps, lock attributes), [#150419](https://github.com/NousResearch/hermes-agent/pull/150419) (android-arm64 pin for linux-arm64-bionic/Termux) | Open |
| Refactor | [#150388](https://github.com/NousResearch/hermes-agent/pull/150388) — removes the CJK FTS5 tokenizer from core; [#150401](https://github.com/NousResearch/hermes-agent/pull/150401) — package-qualified logger routing | Open |

**Closed today (issue-side):** #139818 (remote-backend Desktop resume), #106960 (systemd dashboard inventoried as manual-serve), #149861 (macOS Desktop build failure after pnpm switch), #150035 and #150036 (pnpm npmrc reader / pin consolidation).

Notable direction: several PRs explicitly require shipping **before** the state-store directory move (#150407: "must reach a stable release before the move"), indicating a coordinated, staged migration rather than ad-hoc patching.

---

## 4. Community Hot Topics

| Item | Signal | Underlying need |
|---|---|---|
| [#139818](https://github.com/NousResearch/hermes-agent/issues/139818) — remote-backend Desktop sessions unresumable (`session.resume` → not_found) | **42 comments**, 3 👍, CLOSED | Users run `hermes serve` on a remote host and expect Desktop conversations to survive process restarts. The long thread reflects confusion about where sessions are persisted and who owns them. |
| [#146643](https://github.com/NousResearch/hermes-agent/issues/146643) — gateway re-reads full session history on every change; CPU and Desktop timeouts scale with store size | 17 comments, P2 perf | **Scalability ceiling**: on a few-thousand-session store the gateway burns 1.5–2.5 cores and Desktop requests hit 30 s timeouts. This is the top structural risk to power users. |
| [#106960](https://github.com/NousResearch/hermes-agent/issues/106960) — systemd dashboard inventoried as manual serve after update | 14 comments, CLOSED | Self-hosters using systemd units want first-class service discovery, not fallback heuristics. |
| [#139347](https://github.com/NousResearch/hermes-agent/issues/139347) — bounded hosted-room transcript drops users still referenced by settlements | 9 comments, 1 👍 | Multi-participant/hosted-room correctness: eviction logic conflicts with validation invariants. |
| [#147938](https://github.com/NousResearch/hermes-agent/issues/147938) — state.db verify mistakes a live lock for corruption | 7 comments, **P0** | Trust in the updater. Users need "don't touch my data" guarantees. |
| [#105163](https://github.com/NousResearch/hermes-agent/issues/105163) — English-only stall-guard intent detector | 7 comments | Non-English operators are silently losing anti-stall protection. |

**Read:** the community's center of gravity has shifted from feature requests to **operational trust** — session durability, update safety, and performance at scale. The 42-comment #139818 thread is the clearest signal that session authority (#145171) is the right architectural bet.

---

## 5. Bugs & Stability

Ranked by severity. "Fix PR" = a PR visible in today's sample.

**P0 — data integrity**
- [#147938](https://github.com/NousResearch/hermes-agent/issues/147938) *(open, Windows)* — `state.db` verification cannot distinguish a live lock from corruption during the update pause window; it restores a **months-stale snapshot over healthy data** and then reports the successful restore as "did not complete." Highest-risk open issue today. No linked fix PR in sample.

**High — data loss / security boundary**
- [#150436](https://github.com/NousResearch/hermes-agent/issues/150436) *(open, v0.23.0 Docker)* — any config save (e.g. an "always allow" approval) **deletes user values** for keys also set in the managed `/etc/hermes/config.yaml` scope. Silent, user-invisible config loss. No fix PR.
- [#147720](https://github.com/NousResearch/hermes-agent/issues/147720) *(open, P3 security)* — profile-routed subagents inherit the **parent's** toolsets instead of the target profile's; a profile with terminal disabled can still run shell commands when delegated to. Privilege-boundary violation; label understates impact.
- [#137723](https://github.com/NousResearch/hermes-agent/issues/137723) *(open, P2)* — Codex quota recovery marks a credential `ok` in memory but persists `exhausted`, so pool reloads resurrect stale quota errors. No fix PR.

**Medium — functional breakage**
- [#148329](https://github.com/NousResearch/hermes-agent/issues/148329) *(open, P2)* — locking a skill inside a git working tree blocks **all** git writes in that repo, with no agent-side remedy. Protection feature becoming a work blocker.
- [#146643](https://github.com/NousResearch/hermes-agent/issues/146643) *(open, P2 perf)* — see hot topics; CPU/timeouts grow with store size.
- [#133006](https://github.com/NousResearch/hermes-agent/issues/133006) *(open, P2, macOS)* — Desktop form-filling: stale/empty browser targets plus unverified agent recovery loops.
- [#146280](https://github.com/NousResearch/hermes-agent/issues/146280) *(open, P2)* — Anthropic usage: `utilization <= 1%` reported as 100% due to fraction scaling on a percent field. Cosmetic but erodes billing trust.
- [#148277](https://github.com/NousResearch/hermes-agent/issues/148277) *(open, P2)* — Desktop message that landed is also restored into the composer; re-sending runs it twice under a new submission id.
- [#150397](https://github.com/NousResearch/hermes-agent/issues/150397) *(open)* — multiplexed gateway (11 profiles) has the local terminal backend overwrite the routed profile's explicit `terminal.cwd` with the process cwd.
- [#150056](https://github.com/NousResearch/hermes-agent/issues/150056) *(open)* — `message_agent` deliveries refused with `admission_conflict` for hours after compression; **cause not identified**, prior hypothesis disproven by replay.

**Regression cluster from the pnpm switch (#149746)**
- [#149861](https://github.com/NousResearch/hermes-agent/issues/149861) *(CLOSED)* — pnpm 12.15.0 silently drops the `get-windows` optional dependency; every macOS Desktop build fails.
- [#150146](https://github.com/NousResearch/hermes-agent/issues/150146) *(open)* — same failure, still reproducible on `main` (v0.23.0+209). Two files disagree on whether a missing optional dep is fatal. **Fix PRs:** [#150430](https://github.com/NousResearch/hermes-agent/pull/150430), [#150419](https://github.com/NousResearch/hermes-agent/pull/150419).

**Update-path correctness (open, with fix PRs)**
- [#150398](https://github.com/NousResearch/hermes-agent/issues/150398) → [#150408](https://github.com/NousResearch/hermes-agent/pull/150408)
- [#150399](https://github.com/NousResearch/hermes-agent/issues/150399) → [#150407](https://github.com/NousResearch/hermes-agent/pull/150407)
- [#150403](https://github.com/NousResearch/hermes-agent/issues/150403) → [#150406](https://github.com/NousResearch/hermes-agent/pull/150406)
- [#150402](https://github.com/NousResearch/hermes-agent/issues/150402) → [#150405](https://github.com/NousResearch/hermes-agent/pull/150405)

**Low**
- [#105163](https://github.com/NousResearch/hermes-agent/issues/105163) (i18n stall guard), [#79836](https://github.com/NousResearch/hermes-agent/issues/79836) (Desktop sidebar missing platform sections), [#150369](https://github.com/NousResearch/hermes-agent/issues/150369) (iPhone Safari mic requires double-tap; AudioContext gesture gating), [#150439](https://github.com/NousResearch/hermes-agent/issues/150439) (re-authorization path after an explicit "Deny" on a Dangerous Command prompt is unclear).

---

## 6. Feature Requests & Roadmap Signals

**User-requested:**
- [#150431](https://github.com/NousResearch/hermes-agent/issues/150431) — managed scope should be able to **lock** `mcp_servers` (mapping-level lock, not leaf-merge). Enterprise/admin deployment signal.
- [#148307](https://github.com/NousResearch/hermes-agent/issues/148307) — suppress routine mobile push notifications while the user is active on Desktop. Cross-device presence awareness.
- [#145171](https://github.com/NousResearch/hermes-agent/issues/145171) — tracking issue: cut `serve`, remote Desktop, and web over to the gateway session authority.

**Likely in the next release (high confidence):**
1. **pnpm stabilization** — macOS Desktop build fix plus lock/pin consolidation (#150146, #150430, #150419, #150041 tracking).
2. **Update-path hardening bundle** — the #150383 stack (#150385–#150408, #150411, #150418) is explicitly sequenced and partly gated on landing before the state-store move.
3. **`session.transcript` read API** (#150102) — unblocks third-party frontends (hermes-tern cited) and is a prerequisite for scalable session pickers.

**Plausible but not imminent:** PostgreSQL state backend stage B (#150421, stacked and opt-in); Yuanbao core→plugin extraction (#150210); catalog additions for `wally` (#150438) and Yandex Mail (#150433).

**Structural bet worth flagging:** the state-store relocation (`src/hermes_agent/`) plus #150388's FTS5 CJK removal and #150421's Postgres seam together suggest a session-storage rearchitecture in progress. Expect migration notes and a minor-version bump when it lands.

---

## 7. User Feedback Summary

**Pain points, in order of intensity:**

1. **Update safety dominates anxiety.** Users are not asking for features; they are reporting that updates can strand a macOS build (#149861, #150146), misdiagnose a live DB lock as corruption and roll back to stale data (#147938), or silently drop user config (#150436). The `alt-glitch` update-path stack reads as a direct response to accumulated trust erosion.
2. **Session durability across restarts.** #139818's 42 comments and #146643's timeouts both describe the same lived experience: conversations that "disappear" or become unreachable when a gateway/serve process cycles. Remote-backend and multi-profile self-hosters are the most affected cohort.
3. **Scale.** A few thousand sessions is enough to saturate cores and break Desktop UX (#146643). Power users are hitting the SQLite-backed design's practical ceiling.
4. **Multi-profile / multi-tenant operations.** #150397 (11 profiles in one gateway), #147720 (profile toolset leakage), #150431 (admin locking) all come from operators running Hermes as shared infrastructure, not a single-user assistant.
5. **Client parity gaps.** Desktop sidebar omits enabled platforms (#79836), iPhone Safari needs double-taps (#150369), mobile notifications duplicate Desktop activity (#148307), and a send can be duplicated into the composer (#148277).

**Satisfaction signals:** thoughtful, reproducible reports with version/commit pins and even self-corrections (#150056 explicitly retracts its own hypothesis after a failed replay) indicate an engaged, technically sophisticated user base. Three 👍 on the top issue and one each on #139347/#148307 are low absolute numbers — this community signals agreement through detailed comments rather than reactions.

**Dissatisfaction signals:** repeated "no agent-side remedy" (#148329), "cause not identified" (#150056), and "successful restore reported as did not complete" (#147938) phrasing point to a perception that failure modes are opaque and self-diagnosis is unreliable.

---

## 8. Backlog Watch

Issues with meaningful age and no visible fix PR:

| Issue | Age (as of 2026-11-07) | Why it matters |
|---|---|---|
| [#79836](https://github.com/NousResearch/hermes-agent/issues/79836) — Desktop messaging sidebar missing `raft` and other enabled platforms | ~93 days | Platform adapters ship but are invisible in the UI; a persistent feature-parity gap for multi-platform users. 6 comments, no fix PR. |
| [#105163](https://github.com/NousResearch/hermes-agent/issues/105163) — English-only stall-guard intent detector | ~61 days | Silently disables anti-stall for every non-English agent run. Small, well-scoped fix (`_TRAILING_CONTINUE_INTENT_RE`) with clear ROI. |
| [#133006](https://github.com/NousResearch/hermes-agent/issues/133006) — macOS Desktop form-filling failure, stale browser targets | ~33 days | Tagged `needs-repro`; blocks the flagship computer-use use case on macOS. |
| [#139347](https://github.com/NousResearch/hermes-agent/issues/139347) — bounded transcript eviction breaks settlement references | ~23 days | `DiscussionValidationError` leaves rooms stuck; correctness bug in hosted rooms with 1 👍 and no fix PR. |
| [#145171](https://github.com/NousResearch/hermes-agent/issues/145171) — entry-point cutover tracking issue | ~12 days | Meta-issue; needs sequencing clarity so #139818-class bugs stop recurring. |

**PRs needing maintainer attention:** [#150418](https://github.com/NousResearch/hermes-agent/pull/150418) is an explicit **draft awaiting a CI-owner cost decision** (adds runner cost to update-path PRs) and is stacked behind #150411. [#150421](https://github.com/NousResearch/hermes-agent/pull/150421) (Postgres stage B) is stacked on #150332 and will stall if stage A is not reviewed. [#150210](https://github.com/NousResearch/hermes-agent/pull/150210) (Yuanbao extraction) is a cross-repo migration and carries the highest coordination cost of any open PR.

---

*Data basis: 50 issues and 50 PRs updated 2026-11-07; issue/PR lists truncated to top 30 and top 20 by comment count respectively, so merged-PR attribution and zero-comment items are under-represented. All links point to `github.com/NousResearch/hermes-agent`.*

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

No activity in the last 24 hours.

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw Project Digest — 2026-11-07

**Repository:** [agentscope-ai/QwenPaw](https://github.com/agentscope-ai/QwenPaw)
**Data window:** last 24 hours (through 2026-11-06)

---

## 1. Today's Overview

QwenPaw had a **high-velocity, triage-heavy day** with no release: 35 PRs and 23 Issues were updated in 24 hours, of which 18 PRs were merged/closed and 11 Issues were closed. The dominant pattern is **same-day issue→fix conversion** — at least five Issues updated today already have a matching open PR from the same day ([#8576](https://github.com/agentscope-ai/QwenPaw/issues/8576)→[#8608](https://github.com/agentscope-ai/QwenPaw/pull/8608), [#8596](https://github.com/agentscope-ai/QwenPaw/issues/8596)→[#8600](https://github.com/agentscope-ai/QwenPaw/pull/8600), [#5110](https://github.com/agentscope-ai/QwenPaw/issues/5110)→[#8609](https://github.com/agentscope-ai/QwenPaw/pull/8609), [#7436](https://github.com/agentscope-ai/QwenPaw/issues/7436)→[#8610](https://github.com/agentscope-ai/QwenPaw/pull/8610), [#8549](https://github.com/agentscope-ai/QwenPaw/issues/8549)→[#8550](https://github.com/agentscope-ai/QwenPaw/pull/8550)), indicating an engaged contributor base and responsive maintainer triage. Activity clusters around four subsystems: **Console/UI ergonomics**, **memory & reranker**, **IM channel plugins (Matrix)**, and **Windows compatibility**. Notably, **every Issue and PR in this window has 0 👍**, so engagement is comment-driven rather than reaction-driven — a signal worth watching for a project of this size. Overall health: **strong throughput, but a growing long-tail of "under review" large PRs and a recurring pattern of Chinese-Windows edge cases**.

---

## 2. Releases

**None.** No new versions were published in the last 24 hours, and the "Latest Releases" feed is empty. No breaking changes or migration notes to report.

Context from Issues: the newest line in circulation appears to be **2.3.0b2 / 2.3.0-beta.1** (Desktop), with **2.2.2.post1** as the latest stable referenced by reporters. Several fixes merged today target 2.3.0b2 regressions (e.g., Matrix plugin conversion in [#8538](https://github.com/agentscope-ai/QwenPaw/issues/8538)), which suggests a **2.3.0 release is the near-term target** — see §6.

---

## 3. Project Progress

**18 PRs merged/closed** in the window (top-20 sample shows 8). Highlights:

| PR | What advanced | Linked Issue |
|---|---|---|
| [#8550](https://github.com/agentscope-ai/QwenPaw/pull/8550) | **Model config write-path validation** — enforces `thinking_budget >= 1` and `max_input_length >= 1000`, preventing persisted bad values that silently dropped a provider after restart | [#8549](https://github.com/agentscope-ai/QwenPaw/issues/8549) |
| [#8411](https://github.com/agentscope-ai/QwenPaw/pull/8411) | **Matrix channel hardening** — restores cached sessions instead of re-login on every start; re-uploads E2EE device keys after server-side key loss | [#8365](https://github.com/agentscope-ai/QwenPaw/issues/8365) |
| [#8457](https://github.com/agentscope-ai/QwenPaw/pull/8457) | **Memory recall semantics** — automatic recall now injected as transient untrusted `TextBlock` reference context instead of a synthetic assistant tool-call trajectory, stopping models from imitating it and re-calling `memory_search` | — |
| [#8535](https://github.com/agentscope-ai/QwenPaw/pull/8535) | **Third-party backend cron fix** — converts dict requests to `AgentRequest` before backend selection, fixing `Third-party agent requests require text or attachments` for Codex/Qoder | [#8533](https://github.com/agentscope-ai/QwenPaw/issues/8533) |
| [#8600](https://github.com/agentscope-ai/QwenPaw/pull/8600) | **Console `/clear` cache hygiene** — reset chats stay empty after reopen/refresh, empty-state greeting restored | [#8596](https://github.com/agentscope-ai/QwenPaw/issues/8596) |
| [#8598](https://github.com/agentscope-ai/QwenPaw/pull/8598) | **Plugin dependency check refactor** (size/XXXL) — rework of the install-time dependency validation path | — |
| [#8604](https://github.com/agentscope-ai/QwenPaw/pull/8604) | **CI stability** — stops Windows shell-cancellation tests from hanging | — |
| [#373](https://github.com/agentscope-ai/QwenPaw/pull/373) | **Dynamic MCP tool persistence** — per-session persistence of dynamically loaded MCP connections; closed after ~8 months open | — |

**Theme:** the day's merged work is predominantly *correctness and lifecycle* fixes (config validation, session/crypto recovery, cache state, async test hygiene) rather than net-new capability — a maturing-project signature.

---

## 4. Community Hot Topics

Ranked by comment volume (all items show 0 👍):

1. **[#2420](https://github.com/agentscope-ai/QwenPaw/issues/2420) — [CLOSED] Cross-agent collaboration UX feedback** · 4 comments · open since 2026-03-27
   Reports four distinct problems in v0.2.0.post1: onboarding for cross-agent collaboration, triggering of cross-agent communication, collaboration coherence, and **identity confusion** between agents. Closed today after ~7.5 months.
   *Underlying need:* users are attempting multi-agent compositions the product does not yet guide them through; "identity confusion" points at a real **agent identity/attribution model gap** rather than a UI bug.

2. **[#8580](https://github.com/agentscope-ai/QwenPaw/issues/8580) — [OPEN] Memory tag index never rebuilds, `/memory/tags` → 503** · 4 comments
   `auto_memory_generate_tags = true` is accepted and persisted, tags are generated, but the index never rebuilds so the read API is permanently unavailable. Config read-back verified; on-disk diff confirmed.
   *Underlying need:* memory is being sold as a first-class capability, but **write path and read path are decoupled with no reconciliation/rebuild trigger**. This is the highest-impact open bug in the window.

3. **[#8596](https://github.com/agentscope-ai/QwenPaw/issues/8596) — [CLOSED] `/clear` message reappears on chat reopen** · 4 comments · fixed same day by [#8600](https://github.com/agentscope-ai/QwenPaw/pull/8600)

4. **[#1453](https://github.com/agentscope-ai/QwenPaw/issues/1453) — [CLOSED] CoPaw offline install can't reach offline Qwen3/3.5 but works with DeepSeek R1** · 4 comments · open since 2026-03-13
   *Underlying need:* **air-gapped / on-prem deployment** is a real use case; provider-specific offline behavior is inconsistent.

5. **[#8276](https://github.com/agentscope-ai/QwenPaw/issues/8276) — [CLOSED] ReMe reranker accepts empty `api_key`, surfaces only "unexpected error"** · 4 comments
   *Underlying need:* configuration validation and **actionable error messages** — the same root complaint as [#8549](https://github.com/agentscope-ai/QwenPaw/issues/8549).

6. **[#7436](https://github.com/agentscope-ai/QwenPaw/issues/7436) — [OPEN] `tool_call_format` for compact tool display in IM channels** · 3 comments · open since 2026-08-31 → addressed by [#8610](https://github.com/agentscope-ai/QwenPaw/pull/8610)

7. **[#5110](https://github.com/agentscope-ai/QwenPaw/issues/5110) — [OPEN] Quote/reference response text for follow-up (Perplexity-style)** · 3 comments · open since 2026-06-11 → addressed by [#8609](https://github.com/agentscope-ai/QwenPaw/pull/8609)

**Read:** the comment distribution shows users are **not** arguing about direction — they are filing precise, code-located defect reports and feature asks, and the maintainers are converting them to PRs. The friction is in *validation and observability* (silent failures, empty-state errors), not in product vision.

---

## 5. Bugs & Stability

Ranked by severity. "Fix PR" = a PR exists in this window.

### Critical

- **[#8580](https://github.com/agentscope-ai/QwenPaw/issues/8580) — Memory tag index permanently unavailable (503)** · OPEN · no fix PR
  Tags generate but never index; `/memory/tags` is dead for any user who enables the feature. Feature is effectively non-functional, with no self-healing path.

- **[#8592](https://github.com/agentscope-ai/QwenPaw/issues/8592) — Creator review round becomes permanently undecidable** · OPEN · no fix PR
  `REJECT` always returns **422 "visual artifact reference references missing id"** — a hard blocker: a review round can never be resolved. Bundles two further defects (expired provider `source_url` → 502; `frame_stats` ignores `CREATOR_FFMPEG_PATH`). Reporter explicitly offers to split into three Issues.

- **[#8517](https://github.com/agentscope-ai/QwenPaw/issues/8517) — Hub Docker runtime fails to start: Permission denied on `working/config.json`** · OPEN · no fix PR
  Blocks any **non-root Docker deployment** of the Hub. Deployment-class blocker for self-hosted/enterprise users.

### High

- **[#8585](https://github.com/agentscope-ai/QwenPaw/issues/8585) — `recall_history` broken on Windows** · OPEN · no fix PR
  `workspace_dir` canonicalized to `\\?\` extended-length path, breaking the SQLite read-only URI. **Cross-version** (also on 2.2.x, earliest log 20xx) — a long-standing, unaddressed platform bug affecting Desktop users' memory recall.

- **[#8538](https://github.com/agentscope-ai/QwenPaw/issues/8538) — Matrix plugin fails to activate with multiple workspaces** · OPEN · related fix [#8411](https://github.com/agentscope-ai/QwenPaw/pull/8411) closed but this specific 10s-window defect is not covered
  A 10s shared startup window only covers the first workspace; with three Matrix-enabled workspaces, **none** come online. Regression introduced by the 2.3.0b2 Matrix→plugin conversion.

- **[#8595](https://github.com/agentscope-ai/QwenPaw/issues/8595) — Plugin install fails: "No package metadata was found" for `qwenpaw-creator` v2.0.4** · OPEN
  Fails via **both** extension UI and CLI hot-install path. Blocks the flagship Creator plugin. Possibly related to the dependency-check refactor in [#8598](https://github.com/agentscope-ai/QwenPaw/pull/8598).

- **[#8603](https://github.com/agentscope-ai/QwenPaw/issues/8603) — `run_code` invoked with empty `{}` arguments on custom OpenAI-compatible providers** · OPEN
  Every `run_code` call on user-defined providers (`kimi`, `kimi-codingplan` / `k3.1-pro`) fails validation: `'code' is a required property`. Breaks tool use entirely on custom providers — a **tool-schema serialization** issue, same family as [#8607](https://github.com/agentscope-ai/QwenPaw/pull/8607).

### Medium

- **[#8576](https://github.com/agentscope-ai/QwenPaw/issues/8576) — Default agent edit button disabled although PUT API allows it** · OPEN · **fix PR [#8608](https://github.com/agentscope-ai/QwenPaw/pull/8608)**
  Restriction compiled into the served React bundle; single-agent installs cannot edit their only agent.

- **[#8276](https://github.com/agentscope-ai/QwenPaw/issues/8276) — Reranker enablement without credential, opaque error** · CLOSED
  Silent misconfiguration; error surfaces only as "unexpected error."

- **[#8590](https://github.com/agentscope-ai/QwenPaw/issues/8590) — Plugin-registered settings route loses the settings sidebar** · CLOSED
  Navigating to a plugin page under `primary.settings` drops back to the conversation list; built-in settings pages are unaffected. Plugin-navigation state bug.

### Low / Resolved

- **[#8596](https://github.com/agentscope-ai/QwenPaw/issues/8596)** `/clear` bubble resurrected on reopen — **fixed** ([#8600](https://github.com/agentscope-ai/QwenPaw/pull/8600))
- **[#8549](https://github.com/agentscope-ai/QwenPaw/issues/8549)** Invalid `thinking_budget`/`max_input_length` persisted → provider vanishes after restart — **fixed** ([#8550](https://github.com/agentscope-ai/QwenPaw/pull/8550))
- **[#8365](https://github.com/agentscope-ai/QwenPaw/issues/8365)** Matrix re-login every start / no E2EE key re-upload — **fixed** ([#8411](https://github.com/agentscope-ai/QwenPaw/pull/8411))
- **[#8533](https://github.com/agentscope-ai/QwenPaw/issues/8533)** Cron tasks misjudged as empty on Codex/Qoder — **fixed** ([#8535](https://github.com/agentscope-ai/QwenPaw/pull/8535))
- **[#8516](https://github.com/agentscope-ai/QwenPaw/issues/8516)** SSE missing `X-Accel-Buffering: no` → first event buffered behind nginx — CLOSED
- **[#7857](https://github.com/agentscope-ai/QwenPaw/issues/7857)** ACP shutdown fallback skips session cleanup, leaks event loop — CLOSED
- **[#7858](https://github.com/agentscope-ai/QwenPaw/issues/7858)** Unawaited-coroutine warnings from test mocks — CLOSED (test hygiene)

**Stability read:** 11 of 23 Issues closed today is a strong burn-down, but the **open critical set has zero fix PRs attached** ([#8580](https://github.com/agentscope-ai/QwenPaw/issues/8580), [#8592](https://github.com/agentscope-ai/QwenPaw/issues/8592), [#8517](https://github.com/agentscope-ai/QwenPaw/issues/8517), [#8585](https://github.com/agentscope-ai/QwenPaw/issues/8585)) — that is the main health risk this week.

---

## 6. Feature Requests & Roadmap Signals

| Request | Issue | Status | Likelihood for next version |
|---|---|---|---|
| **Quote/reference response text for follow-up** (Perplexity-style) | [#5110](https://github.com/agentscope-ai/QwenPaw/issues/5110) | Open 5 months; PR [#8609](https://github.com/agentscope-ai/QwenPaw/pull/8609) opened today | **High** — implementation already drafted (multiline/code support, repeat quotes, draft preservation) |
| **Compact tool-call display for IM channels** (`tool_call_format`: `full`/`name_only`/`compact`) | [#7436](https://github.com/agentscope-ai/QwenPaw/issues/7436) | Open 2 months; PR [#8610](https://github.com/agentscope-ai/QwenPaw/pull/8610) opened today | **High** — backward-compatible default `full` |
| **Model deletion in Console model pool** | [#8195](https://github.com/agentscope-ai/QwenPaw/issues/8195) → [#8410](https://github.com/agentscope-ai/QwenPaw/pull/8410) / [#8611](https://github.com/agentscope-ai/QwenPaw/pull/8611) | Two competing PRs | **High**, but **PR duplication needs maintainer arbitration** |
| **Tab title reflects current session name** | [#8605](https://github.com/agentscope-ai/QwenPaw/issues/8605) | New, 1 comment | Medium — small, well-localized (`KB` component, route-name-only title source) |
| **Creator UI overhaul** (button contrast, model-start state desync, forced shared API key across image/video/LLM models) | [#8591](https://github.com/agentscope-ai/QwenPaw/issues/8591) | New, 1 comment | Medium — #3 (per-modality credentials) is a config-model change, not just UI |
| **Reranker model center** with service test, multi-query precision history, auto-verify | PR [#8472](https://github.com/agentscope-ai/QwenPaw/pull/8472) | Open, size/XXL, under review since 2026-10-28 | Medium — large surface, mirrors existing Embedding model center |
| **Cross-agent collaboration guidance & identity model** | [#2420](https://github.com/agentscope-ai/QwenPaw/issues/2420) | Closed after 7.5 months, no linked PR | **Low near-term** — closed as feedback, but the need is structural |

**Prediction:** the 2.3.x line likely ships **Console polish** (#8608, #8609, #8611, tab titles), **IM channel display control** (#8610), and **provider/model config validation hardening** (#8550, #8607). The memory subsystem (reranker center #8472, tag index #8580) is the most likely **2.4** headline feature area.

---

## 7. User Feedback Summary

**Pain points, in order of frequency:**

1. **Silent failures with useless error messages.** The single most repeated complaint: reranker enabled with empty key → "unexpected error" ([#8276](https://github.com/agentscope-ai/QwenPaw/issues/8276)); out-of-range config accepted → provider silently disappears ([#8549](https://github.com/agentscope-ai/QwenPaw/issues/8549)); tags generated → index never built → 503 ([#8580](https://github.com/agentscope-ai/QwenPaw/issues/8580)). Users are doing the debugging work themselves and locating exact file/line — a sign the project is **under-instrumented**, not under-documented.

2. **Windows is a second-class platform.** GBK/CP936 files silently byte-stripped by `errors="ignore"` ([#8234](https://github.com/agentscope-ai/QwenPaw/pull/8234)); `\\?\` path canonicalization breaking SQLite URIs ([#8585](https://github.com/agentscope-ai/QwenPaw/issues/8585)); hanging cancellation tests ([#8604](https://github.com/agentscope-ai/QwenPaw/pull/8604)). Chinese Windows users are a **core, vocal constituency** — the GBK report describes Notepad/legacy `.txt`/`.csv`/`.md` notes losing data with **no log line**.

3. **Deployment friction in constrained environments.** Air-gapped/offline installs where Qwen3/3.5 are unreachable but DeepSeek R1 works ([#1453](https://github.com/agentscope-ai/QwenPaw/issues/1453)); non-root Docker Hub startup failure ([#8517](https://github.com/agentscope-ai/QwenPaw/issues/8517)); nginx reverse-proxy SSE buffering ([#8516](https://github.com/agentscope-ai/QwenPaw/issues/8516)). Self-hosted enterprise users are hitting **first-run blockers**.

4. **Plugin ecosystem immaturity.** Install fails with a bare "No package metadata was found" ([#8595](https://github.com/agentscope-ai/QwenPaw/issues/8595)); plugin routes break sidebar state ([#8590](https://github.com/agentscope-ai/QwenPaw/issues/8590)). The extension API is being used, but **the loading/registration contract is still leaky**.

**Use cases evidenced:** multi-workspace Matrix bridging, cross-agent collaboration and delegation, Creator (image/video generation) pipelines, offline/air-gapped deployment, third-party backend routing (Codex/Qoder/Kimi), scheduled agent tasks over IM, and long-term memory recall.

**Satisfaction signals:** reporters are precise, reproducible, and cooperative ("Happy to split this into three issues"); first-time contributors are filing docs fixes ([#8606](https://github.com/agentscope-ai/QwenPaw/pull/8606)) and validation patches ([#8550](https://github.com/agentscope-ai/QwenPaw/pull/8550)). **Dissatisfaction signals:** zero 👍 across the entire window, repeated "permanently broken / never rebuilds / cannot be decided" phrasing, and a 7.5-month-old collaboration-feedback thread closed without a linked fix.

---

## 8. Backlog Watch

Items needing maintainer attention, ranked by risk:

1. **[#8580](https://github.com/agentscope-ai/QwenPaw/issues/8580) — Memory tag index 503** · OPEN, 4 comments, **no fix PR, no assignee signal**
   Highest-severity open bug with no owner. Every user who enables `auto_memory_generate_tags` is silently broken.

2. **[#8592](https://github.com/agentscope-ai/QwenPaw/issues/8592) — Creator REJECT permanently 422** · OPEN, 3 defects bundled
   Needs a maintainer decision on **splitting into three Issues** so defect 1 (the blocker) isn't buried under defects 2–3.

3. **[#8517](https://github.com/agentscope-ai/QwenPaw/issues/8517) — Hub Docker non-root Permission denied** · OPEN since 2026-10-31
   Blocks a whole deployment class; reporter could not even capture a version tag, which itself hints at packaging/tagging gaps.

4. **[#8410](https://github.com/agentscope-ai/QwenPaw/pull/8410) vs [#8611](https://github.com/agentscope-ai/QwenPaw/pull/8611) — duplicate "remove models from pool" work**
   Two contributors solving the same Issue ([#8195](https://github.com/agentscope-ai/QwenPaw/issues/8195)). **Needs immediate arbitration** to avoid contributor burnout — this is the clearest process risk in today's data.

5. **[#8234](https://github.com/agentscope-ai/QwenPaw/pull/8234) & [#8236](https://github.com/agentscope-ai/QwenPaw/pull/8236) — size/XXL & XL, open since 2026-10-14, "Under Review"**
   GBK/CP936 decoding fix (data-loss class) and Ollama `num_ctx` context-window fix have been sitting **3+ weeks** without merge. Both are high user impact; #8234 in particular silently destroys user file content.

6. **[#8472](https://github.com/agentscope-ai/QwenPaw/pull/8472) — reranker model center, size/XXL** · open since 2026-10-28
   Large feature PR with no review comments recorded; at risk of bit-rot.

7. **[#8100](https://github.com/agentscope-ai/QwenPaw/pull/8100) — resolved media capabilities at runtime** · open since 2026-10-03 (**5+ weeks**)
   A one-line-class correctness fix (image-capable models rejected and image content stripped) stuck in review. Also **[#8274](https://github.com/agentscope-ai/QwenPaw/pull/8274)** (chat-switch state loss, since 2026-10-16).

8. **[#5110](https://github.com/agentscope-ai/QwenPaw/issues/5110) — Quote/reference for follow-up** · open since 2026-06-11
   Now unblocked by [#8609](https://github.com/agentscope-ai/QwenPaw/pull/8609); the Issue should be linked to the PR so it closes on merge.

9. **[#2420](https://github.com/agentscope-ai/QwenPaw/issues/2420) — cross-agent identity confusion** · CLOSED with no linked fix
   Closed as feedback, but "identity confusion between agents" and "collaboration coherence" are **architectural**, not cosmetic. Recommend a follow-up design Issue so the closure doesn't read as dismissal.

---

**Bottom line:** QwenPaw is in a **productive, contributor-driven consolidation phase** — high fix throughput, fast issue-to-PR turnaround, and a clear focus on validation, lifecycle correctness, and Console ergonomics. The risks are concentrated in (a) **four critical open bugs with no fix PR**, (b) **duplicate/competing PRs** on the model-management path, and (c) a **review queue of large PRs aging 3–5 weeks**. A 2.3.x patch release closing [#8549](https://github.com/agentscope-ai/QwenPaw/issues/8549), [#8533](https://github.com/agentscope-ai/QwenPaw/issues/8533), [#8596](https://github.com/agentscope-ai/QwenPaw/issues/8596) and [#8365](https://github.com/agentscope-ai/QwenPaw/issues/8365) would be a well-supported next step.

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw Project Digest — 2026-11-07

## 1. Today's Overview

ZeroClaw is in a high-throughput hardening phase: 17 issues were touched in the last 24h (10 closed, 7 open/active) and 50 PRs were updated (14 merged/closed, 36 still open), with **no new release**. The closed-issue set is dominated by security and identity-access fixes — OIDC bearer verification limits (#12047), X-Forwarded-For spoofing (#12052), RPC session exhaustion (#11962) and a RUSTSEC-blocking dependency (#12272) — indicating the maintainers are clearing a release-gate backlog rather than shipping features. At the same time, several new S1/S2 defects landed today, including a permanently poisoned provider session (#12263) and a boot-time transcript overwrite that risks message loss (#12279). Overall project health looks strong on responsiveness (fast close rate, multiple follow-up PRs filed within hours), but the open security-adjacent bugs in approval and peer-resolution paths deserve attention.

*Data note: only 2 of the 14 merged/closed PRs and the top 20 of 50 updated PRs were included in the supplied dataset; PR comment counts were unavailable.*

## 2. Releases

No new releases in the last 24h, and none listed in the latest-release feed. No changelog, breaking-change or migration notes to report. Several open PRs carry the `release:v0.9.0` label (#9746, #12239, #12270, #12282), suggesting the next version is being staged but is not yet cut.

## 3. Project Progress

**Closed issues (10) — fixes landed or resolved:**

| Issue | Area | Outcome |
|---|---|---|
| [#12047](https://github.com/zeroclaw-labs/zeroclaw/issues/12047) | Gateway / OIDC | Attempt limiter + outbound cap added via [PR #12233](https://github.com/zeroclaw-labs/zeroclaw/pull/12233) |
| [#12052](https://github.com/zeroclaw-labs/zeroclaw/issues/12052) | Gateway / XFF spoofing | Rate-limit keying moved to proxy-appended hop via [PR #12114](https://github.com/zeroclaw-labs/zeroclaw/pull/12114) |
| [#11962](https://github.com/zeroclaw-labs/zeroclaw/issues/11962) | Runtime / daemon | Idle RPC sessions now releasable (64-session cap) |
| [#12176](https://github.com/zeroclaw-labs/zeroclaw/issues/12176) | Tools / MCP | Deferred tool list ordering stabilized — restores prompt caching |
| [#12272](https://github.com/zeroclaw-labs/zeroclaw/issues/12272) | Dependencies | `vodozemac` RUSTSEC-2026-0445 / -0451 unblocked; Security gate green again |
| [#12248](https://github.com/zeroclaw-labs/zeroclaw/issues/12248) | Runtime / approval | No approval prompt for tool names absent from the turn |
| [#12178](https://github.com/zeroclaw-labs/zeroclaw/issues/12178) | Tools | `deliver_file` description no longer instructs URI citation in reply text |
| [#12155](https://github.com/zeroclaw-labs/zeroclaw/issues/12155) | Onboarding | Quickstart cancel message now matches actual config state |
| [#12217](https://github.com/zeroclaw-labs/zeroclaw/issues/12217) | Docs | Email tools-only setup clarified re: IMAP listener |
| [#12249](https://github.com/zeroclaw-labs/zeroclaw/issues/12249) | Gateway / web | Config pages hidden from accounts lacking `config` grant |

**Notable open PRs advancing work:** [PR #12281](https://github.com/zeroclaw-labs/zeroclaw/pull/12281) (forwarded-loopback SOP cancel fix, follow-up to #12114), [PR #12239](https://github.com/zeroclaw-labs/zeroclaw/pull/12239) (holding authority through session effects, p1/release-gate), [PR #12276](https://github.com/zeroclaw-labs/zeroclaw/pull/12276) (streaming Telegram voice synthesis), [PR #12275](https://github.com/zeroclaw-labs/zeroclaw/pull/12275) (MCP local config controls), [PR #12270](https://github.com/zeroclaw-labs/zeroclaw/pull/12270) (daemon dashboard via relay, #10315 phase 2).

## 4. Community Hot Topics

1. **[Issue #10926](https://github.com/zeroclaw-labs/zeroclaw/issues/10926)** — *Matrix `send_via` treats peer user identities as room destinations* — **9 comments**, open since 2026-09-17, labeled `status:accepted`, `priority:p2`, `risk:medium`. The most-discussed item in the window. Underlying need: a coherent distinction between *peer identity* and *channel destination* in the outbound routing model — the same conceptual gap appears in today's #12283 (peer-group `ignore` handling) and in [PR #12116](https://github.com/zeroclaw-labs/zeroclaw/pull/12116) (Discord peer rejection). Three separate reports point to peer-resolution semantics being the project's most fragile abstraction.
2. **[Issue #12047](https://github.com/zeroclaw-labs/zeroclaw/issues/12047)** (3 comments, closed) and **[Issue #12052](https://github.com/zeroclaw-labs/zeroclaw/issues/12052)** (2 comments, closed) — both identity-access gateway bugs reported by the same author and fixed within days. Underlying need: operators deploying behind reverse proxies and external IdPs want *consistent* trust-boundary rules across pairing, webhook, enrollment and config routes.
3. **Long-running high-risk PRs** — [#9809](https://github.com/zeroclaw-labs/zeroclaw/pull/9809) (multi-model provider profiles, open since 2026-08-07, `needs-author-action`), [#9746](https://github.com/zeroclaw-labs/zeroclaw/pull/9746) (per-agent ownership scoping, `priority:p1`, `release-gate`), [#11076](https://github.com/zeroclaw-labs/zeroclaw/pull/11076) (`agy_cli` tool, `size:XL`). These carry the widest label sets and touch many subsystems; they are the de facto centers of design debate even without visible comment counts.

## 5. Bugs & Stability

Ranked by severity as reported:

| Severity | Issue | Status | Fix PR? |
|---|---|---|---|
| **S1 — workflow blocked** | [#12263](https://github.com/zeroclaw-labs/zeroclaw/issues/12263) Strict provider rejects a long tool-heavy session **forever** (422 `missing field tool_call_id`); conversation permanently unusable after ~38 messages / ~10 tool rounds | OPEN | None visible |
| **S1 — workflow blocked** | [#12272](https://github.com/zeroclaw-labs/zeroclaw/issues/12272) `vodozemac` 0.11.0 RUSTSEC advisories fail `cargo deny` on every PR | CLOSED | Resolved same day |
| **S2 (possibly S0)** | [#12279](https://github.com/zeroclaw-labs/zeroclaw/issues/12279) Channel startup caps a long transcript without a lock, **overwriting a message appended in between** — data loss in a boot window; reporter notes triage may prefer S0 | OPEN | None visible |
| **S2 — security** | [#12260](https://github.com/zeroclaw-labs/zeroclaw/issues/12260) RPC `cron/add` and `cron/patch` pass `approved = true` for shell commands, treating any RPC caller as an operator approval | OPEN | None visible |
| **S2 — security** | [#12283](https://github.com/zeroclaw-labs/zeroclaw/issues/12283) `resolve_peer_set` applies peer-group `ignore` per group over a `HashMap`, so outbound sends can reach ignored peers | OPEN (filed today) | None visible |
| **S2** | [#10926](https://github.com/zeroclaw-labs/zeroclaw/issues/10926) Matrix `send_via` misroutes to peer user identity as a room | OPEN, accepted | None visible |
| **S2** | [#11962](https://github.com/zeroclaw-labs/zeroclaw/issues/11962) 64 live RPC sessions never released when idle | CLOSED | Fixed |
| **S2** | [#12176](https://github.com/zeroclaw-labs/zeroclaw/issues/12176) Non-deterministic MCP tool ordering busts prompt caching every session | CLOSED | Fixed |
| **S2 — security** | [#12047](https://github.com/zeroclaw-labs/zeroclaw/issues/12047) OIDC bearer verification had no attempt limiter / outbound cap | CLOSED | [PR #12233](https://github.com/zeroclaw-labs/zeroclaw/pull/12233) |
| **S2 — security** | [#12052](https://github.com/zeroclaw-labs/zeroclaw/issues/12052) First-XFF-entry keying let clients claim the loopback exemption | CLOSED | [PR #12114](https://github.com/zeroclaw-labs/zeroclaw/pull/12114) |
| **S3** | [#12155](https://github.com/zeroclaw-labs/zeroclaw/issues/12155) "No config written" shown while `config.toml` was written | CLOSED | Fixed |

**Assessment:** the closed set shows good regression hygiene, but two patterns recur: (a) *loopback/forwarded-header trust* decisions implemented inconsistently per call site (#12052 → #12280 → #12261), and (b) *approval semantics* leaking across layers (#12260, #12248). A shared "is this request local / is this approved" helper would likely retire several of these at once.

## 6. Feature Requests & Roadmap Signals

No new `enhancement`-labeled issues appeared in this window; feature direction is driven by open PRs:

- **Provider flexibility** — [#9809](https://github.com/zeroclaw-labs/zeroclaw/pull/9809) multi-model profiles, `[providers.connections]` / `[providers.model_presets]` / `[providers.fallback]`; [#11846](https://github.com/zeroclaw-labs/zeroclaw/pull/11846) OpenRouter balance reporting with per-alias opt-in; [#12277](https://github.com/zeroclaw-labs/zeroclaw/pull/12277) SSE `[DONE]` sentinel tolerance at EOF; [#12073](https://github.com/zeroclaw-labs/zeroclaw/pull/12073) Codex stream retry fix.
- **Channel depth** — [#12276](https://github.com/zeroclaw-labs/zeroclaw/pull/12276) Telegram voice replies synthesized during generation; [#12065](https://github.com/zeroclaw-labs/zeroclaw/pull/12065) Discord bot approval hardening.
- **Extensibility** — [#11076](https://github.com/zeroclaw-labs/zeroclaw/pull/11076) Antigravity CLI (`agy`) delegation tool; [#12216](https://github.com/zeroclaw-labs/zeroclaw/pull/12216) multiple plugin registry origins; [#12275](https://github.com/zeroclaw-labs/zeroclaw/pull/12275) MCP-exposed local config.
- **Web/UX** — [#12269](https://github.com/zeroclaw-labs/zeroclaw/pull/12269) paired-device voice settings; [#12270](https://github.com/zeroclaw-labs/zeroclaw/pull/12270) daemon dashboard served through ZeroRelay.

**Prediction for v0.9.0:** the `release:v0.9.0` label clusters tightly around identity/access correctness — [#9746](https://github.com/zeroclaw-labs/zeroclaw/pull/9746) (per-agent ownership scoping), [#12239](https://github.com/zeroclaw-labs/zeroclaw/pull/12239) (authority held through session effects), [#12282](https://github.com/zeroclaw-labs/zeroclaw/pull/12282) (placement-exception doc), plus [#12270](https://github.com/zeroclaw-labs/zeroclaw/pull/12270) (relay dashboard). Expect v0.9.0 to be framed as a **multi-tenant isolation and access-control release**, not a feature release.

## 7. User Feedback Summary

- **Configuration trust is a recurring sore point.** #12155 ("cancelled, no config written" — but it was written) and #12249 (config pages visible to accounts without a `config` grant) show users do not trust the config layer's stated state or its visibility rules.
- **Long-running sessions are the biggest reliability complaint.** #12263 (permanently rejected conversation after a tool-heavy session), #11962 (RPC session slots never freed), and #12279 (transcript overwrite at boot) all cluster around session lifecycle. This is the clearest signal of where real-world usage outgrows the current design.
- **Cost/efficiency awareness is explicit.** #12176 was filed specifically because non-deterministic system prompts bust provider prompt caching — a user-visible cost regression, not just a cosmetic one.
- **Onboarding friction is low-severity but frequent.** Quickstart messaging and email tools-only docs both needed correction.
- **Satisfaction indicators:** 10 issues closed in 24h, with fixes often landing within 1–3 days of filing (#12047 → #12233 in ~11 days; #12280 → #12281 in under a day). Contributors are actively self-filing follow-ups (#12280, #12282, #12261), which suggests a healthy review culture rather than user frustration.

## 8. Backlog Watch

Items needing maintainer attention:

1. **[PR #9809](https://github.com/zeroclaw-labs/zeroclaw/pull/9809)** — multi-model provider profiles. Open since **2026-08-07** (~3 months), `size:XL`, `risk:high`, `risk:manual`, `needs-author-action`. Touches nearly every subsystem label in the repo. Highest-risk merge in the backlog; needs a decision or a split plan.
2. **[PR #9746](https://github.com/zeroclaw-labs/zeroclaw/pull/9746)** — per-agent ownership scoping for session tools and `discord_search`. Open since **2026-08-04**, `priority:p1`, `release-gate`, `release:v0.9.0`, `topic:identity-access`. This is a release blocker that has been open for three months.
3. **[PR #11076](https://github.com/zeroclaw-labs/zeroclaw/pull/11076)** — `agy_cli` Antigravity CLI tool. Open since **2026-09-23**, `size:XL`; its placement-exception doc ([PR #12229](https://github.com/zeroclaw-labs/zeroclaw/pull/12229)) is still open too, so it is blocked on process, not code.
4. **[Issue #10926](https://github.com/zeroclaw-labs/zeroclaw/issues/10926)** — Matrix `send_via` misrouting. Accepted, `priority:p2`, **9 comments**, open since **2026-09-17**, no linked fix PR. Highest-discussion bug with no visible remediation path.
5. **[PR #11846](https://github.com/zeroclaw-labs/zeroclaw/pull/11846)** — OpenRouter balance reporting; explicitly depends on unmerged #11845, so it is stalled in a chain.
6. **[PR #12065](https://github.com/zeroclaw-labs/zeroclaw/pull/12065)** / **[PR #12116](https://github.com/zeroclaw-labs/zeroclaw/pull/12116)** — Discord approval and peer-delivery fixes open since 2026-10-27 / 2026-10-30. Small (`XS`/`S`) but `risk:high` on #12065; both relate directly to the still-open #10926 peer-resolution theme and could be batched into one peer-routing decision.

**Health verdict:** strong triage velocity and clear release discipline, with risk concentrated in (a) the three multi-month XL PRs and (b) inconsistent identity/trust primitives across gateway, RPC and peer-resolution code paths.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/SnowEagle-Kn/agents-radar).*