# AI CLI Tools Community Digest 2026-11-04

> Generated: 2026-11-04 02:55 UTC | Tools covered: 7

- [Claude Code](https://github.com/anthropics/claude-code)
- [OpenAI Codex](https://github.com/openai/codex)
- [Gemini CLI](https://github.com/google-gemini/gemini-cli)
- [GitHub Copilot CLI](https://github.com/github/copilot-cli)
- [OpenCode](https://github.com/anomalyco/opencode)
- [Pi](https://github.com/earendil-works/pi)
- [Qwen Code](https://github.com/QwenLM/qwen-code)
- [Claude Code Skills](https://github.com/anthropics/skills)

---

## Cross-Tool Comparison

# AI CLI Tools — Cross-Tool Comparison Report
**Snapshot date:** 2026-11-04 · **Coverage:** Claude Code, OpenAI Codex, Gemini CLI, GitHub Copilot CLI, OpenCode, Pi, Qwen Code

---

## 1. Ecosystem Overview

The AI CLI landscape has consolidated around a common architecture — sandboxed agent loop, MCP-based tool extensibility, subagent delegation, and a TUI plus an Electron desktop surface — and competition has shifted from capability demonstrations to **operational trust**. Across every tool this window, the highest-engagement threads are about failure modes rather than features: silent success misreporting (Gemini), destructive out-of-scope execution (Codex), unusable free-tier quotas (OpenCode), enterprise policy lockouts (Copilot), and multi-GB local footprint (Claude Code). Release cadence diverges sharply: Gemini and Copilot ship nightly/prerelease builds, while Claude Code, Codex and Qwen Code are in maintenance or refactor phases. A second-order signal is that hardening work — sandbox request refactoring, skill-permission threading, CI shard splitting, write-amplification reduction — now consumes as much engineering bandwidth as new surface area.

---

## 2. Activity Comparison

Counts below reflect what each digest reported; issue figures are *profiled/updated* items, not total open tracker volume. "N/A" denotes **no data supplied in the digest**, not an inactive channel.

| Tool | Issues | PRs | Discussions | Release status |
|---|---|---|---|---|
| **Claude Code** | 10 hot issues profiled; flagship #22543 at **82 comments / 👍237**; 4 fresh auth/IDE bugs filed same-day | **3** updated (explicitly low-volume window) | N/A (no discussion data supplied) | **v2.1.318** shipped — mods audit trail, `assume_role` for mantle, `effort` option |
| **OpenAI Codex** | 10 profiled; top thread #59060 at **85 comments**; long-running capacity issue #43375 at 36 | **~16** (10 ranked + 6 notable), mostly CLOSED/merged same day | **Active** — 4 Q&A + 7 Show & Tell threads | `rust-v0.168.0-alpha.1` (no changelog) + vendored `libcap 2.75` supply-chain mirror |
| **Gemini CLI** | 10 profiled; P1 cluster on subagent orchestration; many tracking issues `maintainer-only` | **10** ranked (+3 notable) | N/A (no discussion data supplied) | **v0.67.0-nightly.20261104** — 2 fixes (extension download streams, explicit `gemini-2.5-flash` preservation) |
| **GitHub Copilot CLI** | **41 updated in 24h**; top #4438 at 13 comments / 👍18 | **1** updated only | N/A (no discussion data supplied) | **2 prereleases in 24h** — v1.0.106-6 (`/settings` dashboard) and v1.0.106-7 (`/code` handoff, Kitty images, Fusion tier) |
| **OpenCode** | ~30 in top window; **≥15 duplicates closed as triage**; megathread #59387 at **251 comments**; #48882 at 👍44 | **10** ranked (+2 notable) | N/A (no discussion data supplied) | **None in 24h** |
| **Pi** | **N/A — summary generation failed** | N/A | N/A | N/A |
| **Qwen Code** | 10 profiled; bot-generated #14724 at **165 comments**, 0 👍 | **10** ranked | N/A (no discussion data supplied) | **None in 24h** |

**Read-through:** Codex is the only repo combining high PR throughput *and* an active Discussions channel this window. Copilot CLI shows the inverse profile — heavy issue inflow, near-zero PR surface, shipping instead through a fast prerelease channel. Claude Code and Qwen Code are in handoff/maintenance modes with release notes but little review activity.

---

## 3. Shared Feature Directions

Requirements appearing in **two or more** communities, ranked by breadth:

### 3.1 Agent turn-termination and autonomy control — *(6 of 6 tools)*
The single strongest convergence. Users cannot distinguish "done" from "gave up," and cannot configure how long an agent may block.
- **Codex:** #31935 remove/configurable 60-second blocking wait (👍23, highest reaction-to-comment ratio); #50771 "stops before work is done" (👍14).
- **Gemini:** #22323 MAX_TURNS reported as `GOAL` success; #21409 generalist agent hangs up to an hour (👍8, top-voted).
- **Claude Code:** #104040 `/goal` Stop hook blocks turn end after cancellation; #107116 TUI loop printing "Goal not yet met… continuing."
- **Copilot CLI:** #5270 Plan → Implementation transition never fires with GPT-6.1 Sol.
- **OpenCode / Qwen Code:** both ship third-party or internal workarounds (agent-harness Stop hooks; unified agent loop #15327).

### 3.2 Local resource governance and footprint — *(4 tools)*
- **Claude Code:** #22543 10GB Cowork VM bundle (👍237); #107009 idle Code-tab sessions pinning a full core and never reaped; #88576/#105707 auto-update breaking TCC permissions and pruning the running binary.
- **Codex:** #25921 Crashpad dumps ~4.9 GB/day since June, unresolved.
- **Copilot CLI:** #5344 OTel file exporter writes 50–70 MB/day with no span-only opt-out (9.3 GB over six months, 96% metric rows).
- **OpenCode:** #58916 targets storage write amplification (−84.4% SQLite WAL writes on a 10-step session).

### 3.3 Quota and cost transparency — *(4 tools)*
- **OpenCode:** dominant story — 251-comment megathread #59387; four reproducible failure classes (resets never fire, sliding timestamps, 1–5-message exhaustion, size-dependent limits).
- **Codex:** #57656 quota indicator removed from

---

## Per-Tool Reports

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills Highlights

> Source: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills — Community Highlights
**Source:** [anthropics/skills](https://github.com/anthropics/skills) · **Data as of:** 2026-11-04

> **Method note:** the PR feed returned `Comments: undefined` for all 50 PRs, so PRs are ranked by *attention proxies* — cluster size (multiple PRs touching the same skill), cross-referenced issues, and recency of update — rather than raw comment counts. Issue comment counts are reported as returned by the API.

---

## 1. Top Skills Ranking

### 1. `skill-creator` — the meta-skill everyone is patching
- **PRs:** [#1298](https://github.com/anthropics/skills/pull/1298) · [#1681](https://github.com/anthropics/skills/pull/1681) · [#1997](https://github.com/anthropics/skills/pull/1997)
- **Function:** Scaffolds, packages, and evaluates new Skills (including trigger-accuracy evaluation).
- **Discussion highlights:** Three independent contributors filed fixes in four months — trigger evals produce false misses and compete across workers ([#1298](https://github.com/anthropics/skills/pull/1298)); `package_skill.py` fails as a standalone script with `ModuleNotFoundError` ([#1681](https://github.com/anthropics/skills/pull/1681)); hand-rolled YAML parsing mangles quoted/continued frontmatter values ([#1997](https://github.com/anthropics/skills/pull/1997)). Related issues: [eval-viewer XSS #1394](https://github.com/anthropics/skills/issues/1394), [verbosity/token efficiency #202 (closed)](https://github.com/anthropics/skills/issues/202).
- **Status:** All three **open**. Highest-attention skill cluster in the repo.

### 2. `mcp-builder` — breaking change in the MCP SDK
- **PR:** [#1742](https://github.com/anthropics/skills/pull/1742) (fixes [#1668](https://github.com/anthropics/skills/issues/1668))
- **Function:** Guides Claude in building MCP servers, plus a Phase-4 evaluation harness.
- **Discussion highlights:** `mcp>=2.0.0` renamed `streamablehttp_client` → `streamable_http_client` and moved custom headers to `create_mcp_http_client` / `http_client`. Separately, [Issue #1390](https://github.com/anthropics/skills/issues/1390) reports the eval harness silently fabricates a tool error for *every* call against real servers (`TextContent` not JSON-serializable), scoring 0/N.
- **Status:** **Open** — updated 2026-10-08; a version-compat + eval-correctness pairing.

### 3. `webapp-testing` — security hardening of generated scripts
- **PRs:** [#1980](https://github.com/anthropics/skills/pull/1980) · [#1976](https://github.com/anthropics/skills/pull/1976)
- **Function:** Drives browser automation to test local web apps (`with_server.py`, `element_discovery.py`).
- **Discussion highlights:** `subprocess.Popen(..., shell=True)` in `with_server.py` allows agent-supplied command strings to reach a system shell (CWE-78) ([#1980](https://github.com/anthropics/skills/pull/1980), updated 2026-11-01); `element_discovery.py` mislabels `textarea`/`select` as `text` ([#1976](https://github.com/anthropics/skills/pull/1976)).
- **Status:** Both **open**, both actively updated in the final week of the window.

### 4. `docx` — correctness and failure reporting
- **PRs:** [#1792](https://github.com/anthropics/skills/pull/1792) · [#1734](https://github.com/anthropics/skills/pull/1734)
- **Function:** Creates/edits Word documents, accepts tracked changes via LibreOffice, manages comments.
- **Discussion highlights:** `accept_changes.py` reported success on `soffice` timeout; the fix returns an error and verifies `w:ins`/`w:del`/`w:moveFrom`/`w:moveTo` are actually gone ([#1792](https://github.com/anthropics/skills/pull/1792)). [#1734](https://github.com/anthropics/skills/pull/1734) adds orphaned-comment detection. Related: [merge_runs.py writes invalid XML #2018](https://github.com/anthropics/skills/issues/2018).
- **Status:** Open; #1792 updated 2026-11-01.

### 5. `xlsx` — spreadsheet formula resolution
- **PR:** [#2019](https://github.com/anthropics/skills/pull/2019) (fixes [#2013](https://github.com/anthropics/skills/issues/2013))
- **Function:** Reads, recalculates, and edits Excel workbooks.
- **Discussion highlights:** `external_links_at_risk` only inspected workbook-level `defined_names`, producing false negatives when formulas reach external links via worksheet-scoped names or aliases.
- **Status:** **Open** — the most recently touched PR in the top set (2026-11-03).

### 6. `claude-api` — documentation debt and context cost
- **PR:** [#1730](https://github.com/anthropics/skills/pull/1730)
- **Function:** Reference skill for the Claude API, tool use, and the Academy guide.
- **Discussion highlights:** Three hard-404 URLs replaced with verified canonical ones. Far larger signal in [Issue #1487](https://github.com/anthropics/skills/issues/1487): the skill **eagerly injects ~156k tokens** in a single tool call, exhausting the context window.
- **Status:** PR **open**; the underlying context-budget complaint remains unresolved.

### 7. `pyxel` — a new Skill from the framework's own author
- **PR:** [#525](https://github.com/anthropics/skills/pull/525)
- **Function:** Builds, debugs, and verifies retro Python games — headless input-driven runs, direct frame inspection, state assertions.
- **Discussion highlights:** Submitted by kitao (Pyxel's creator); the longest-lived open submission in the set (created 2026-03-05) but still receiving updates on 2026-11-01.
- **Status:** **Open**, still alive after 8 months.

### 8. New-skill submissions gaining traction
- [#1771](https://github.com/anthropics/skills/pull/1771) `proofcore-contract-auditor` — Solidity/Rust static analysis with cryptographic audit proofs anchored to TON. **Open** (third-party vendor anchoring; likely to draw scrutiny).
- [#1245](https://github.com/anthropics/skills/pull/1245) `notion-spec-to-implementation` + `quantitative-resume-auditor` — spec→Notion task decomposition with acceptance criteria. **Open**, updated 2026-10-15.
- [#1703](https://github.com/anthropics/skills/pull/1703) `md2video-audio` — Markdown → Marp slides → narrated MP4. **Open**.
- [#514](https://github.com/anthropics/skills/pull/514) `document-typography` — orphan/widow/numbering QC for generated docs. **Open**.

*Also open:* [#2108](https://github.com/anthropics/skills/pull/2108) (docs: snapshot/sync semantics), [#1977](https://github.com/anthropics/skills/pull/1977) `algorithmic-art` wrapAround, [#1975](https://github.com/anthropics/skills/pull/1975) `pptx --cols` validation, [#538](https://github.com/anthropics/skills/pull/538) `pdf` case-sensitive refs, [#1615](https://github.com/anthropics/skills/pull/1615) `scnet-hpc`.

---

## 2. Community Demand Trends (from Issues)

| Demand direction | Evidence | Signal |
|---|---|---|
| **Trust & namespace integrity** | [#492](https://github.com/anthropics/skills/issues/492) — 46 comments, the most-discussed issue in the repo | Community skills ship under an `anthropic/` namespace, impersonating official skills and abusing the trust boundary. Demand for provenance/signing and namespacing rules. |
| **Skill distribution & org sharing** | [#228](https://github.com/anthropics/skills/issues/228) — 17 comments, 8 👍 | Users want a shared org skill library or share link instead of downloading `.skill` files and re-uploading manually. |
| **Skill triggering & eval reliability** | [#556](https://github.com/anthropics/skills/issues/556) — 13 comments, 7 👍; [#1390](https://github.com/anthropics/skills/issues/1390) | `run_eval.py` yields a **0% trigger rate across all queries**; MCP evals score 0/N. Demand for a trustworthy "does my Skill actually fire?" loop. |
| **Context/token economy** | [#1487](https://github.com/anthropics/skills/issues/1487); [#202](https://github.com/anthropics/skills/issues/202) (closed) | `claude-api` burns ~156k tokens eagerly; `skill-creator` reads like human docs. Demand for progressive disclosure and lean frontmatter. |
| **Agent memory & state** | [#1329](https://github.com/anthropics/skills/issues/1329) | A `compact-memory` skill using symbolic notation to compress long-running agent state. |
| **Governance / safety patterns** | [#412](https://github.com/anthropics/skills/issues/412) (closed) | Policy enforcement, threat detection, trust scoring, audit trails for agent systems. |
| **Description quality ("when to use")** | [#2064](https://github.com/anthropics/skills/issues/2064) | `theme-factory` describes *what* it does but not *when* to invoke it — a recurring trigger-quality complaint. |
| **Packaging collisions** | [#189](https://github.com/anthropics/skills/issues/189) — 9 👍 (highest 👍 in set) | `document-skills` and `example-skills` install identical content, duplicating skills in context. |

**Read:** demand is not primarily for *more* skill categories — it is for **infrastructure trust**: who authored a skill, whether it fires, what it costs in context, and how it's distributed.

---

## 3. High-Potential Pending Skills

| PR | Skill | Last updated | Why it may land |
|---|---|---|---|
| [#2019](https://github.com/anthropics/skills/pull/2019) | `xlsx` external-link resolution | 2026-11-03 | Narrow, issue-linked fix to a core document skill; touches only `recalc.py`. |
| [#2108](https://github.com/anthropics/skills/pull/2108) | README sync-status clarification | 2026-11-03 | Docs-only, cites verifiable export milestones (#1447, #330); low risk. |
| [#1980](https://github.com/anthropics/skills/pull/1980) | `webapp-testing` drop `shell=True` | 2026-11-01 | Security fix (CWE-78) squarely aligned with the repo's top community concern. |
| [#525](https://github.com/anthropics/skills/pull/525) | `pyxel` retro game dev | 2026-11-01 | Author is the upstream framework maintainer; mature after 8 months of iteration. |
| [#1792](https://github.com/anthropics/skills/pull/1792) | `

---

# Claude Code Community Digest — 2026-11-04

## 1. Today's Highlights

Today's release (v2.1.318) is a small but practical one: it records your answer when Claude Code asks to enable mods, adds `assume_role` for `mantle` upstreams to support cross-account AWS access and per-developer cost attribution, and exposes an `effort` option for mods. Community attention, however, remains dominated by the Desktop/Cowork surface — the 10GB VM bundle issue (#22543) has grown to 82 comments and 237 reactions, and a fresh wave of auth/IDE login bugs landed on 2026-11-04. Auto-update and packaging regressions (macOS TCC permissions, pruned running binaries, Windows Electron crashes) are also recurring themes across the tracker.

## 2. Releases

**v2.1.318**
- Added a record of your answer when Claude Code asks to turn on the mods it writes in a session (auditability of mod activation).
- Added `assume_role` on `mantle` upstreams of the Claude apps gateway — enables assuming a role in another AWS account and per-developer AWS cost attribution.
- Added an `effort` option to a mod.

## 3. Hot Issues

1. **[#22543](https://github.com/anthropics/claude-code/issues/22543)** — *Cowork feature creates 10GB VM bundle that severely degrades performance* (OPEN, high-priority/oncall, 82 comments, 👍237). The single highest-engagement issue in the tracker: `~/Library/Application Support` accumulates a multi-GB VM bundle, degrading Desktop startup and UI over time. Strong community signal for a cleanup/compaction mechanism.
2. **[#57371](https://github.com/anthropics/claude-code/issues/57371)** — *Windows: allow disabling the bundled Cowork background service (CoworkVMService)* (OPEN, 35 comments, 👍66). Users who never use Cowork want to opt out of a persistent service. Pairs naturally with #22543 as the "give me a kill switch" counterpart.
3. **[#74558](https://github.com/anthropics/claude-code/issues/74558)** — *Fable 5: mid-turn assistant text blocks intermittently delivered as summarized thinking blocks* (OPEN, has repro, 25 comments, 👍20). Reproducible in both the on-disk transcript and `stream-json` consumers, making turns appear silent — a correctness issue for anyone building on the streaming API.
4. **[#80576](https://github.com/anthropics/claude-code/issues/80576)** — *AskUserQuestion widget hides Claude's preceding text message in VS Code extension* (OPEN, 11 comments, 👍10). The model's explanatory context is swallowed by the question widget, degrading human-in-the-loop review flows.
5. **[#105318](https://github.com/anthropics/claude-code/issues/105318)** — *Windows Desktop 3.x: Electron main process crashes 0xc0000005 several times a day* (OPEN, has repro, 8 comments). Reported as a 3.x regression from a stable 2.x, with crash frequency rising to 4–6/day — the most severe stability report in the current window.
6. **[#103091](https://github.com/anthropics/claude-code/issues/103091)** — *Windows: 20+ VS Code windows cause `.claude.json` lock contention, stalling the CLI event loop up to 110s* (OPEN, has repro). Sessions freeze mid-tool-call until reload; a scaling limit for monorepo/power users running many parallel windows.
7. **[#81502](https://github.com/anthropics/claude-code/issues/81502)** — *Issue an OIDC identity token to cloud sessions for keyless cloud provider auth* (OPEN, 👍15). The most-upvoted enhancement in this batch — enterprises want to drop long-lived cloud credentials in favor of workload identity.
8. **[#100928](https://github.com/anthropics/claude-code/issues/100928)** — *Compaction duplicates advisor `server_tool_use` blocks; session fails with 400 "ids must be unique per assistant turn"* (OPEN, has repro). A permanently unrecoverable session state — retry, resume, and new turns all fail, which is a serious durability gap.
9. **[#107009](https://github.com/anthropics/claude-code/issues/107009)** — *macOS desktop: idle Code-tab session processes each burn ~1 CPU core and are never reaped* (OPEN, filed 2026-11-03). One process per opened session, kept alive indefinitely — a clear battery/thermal pain point on laptops.
10. **[#107102](https://github.com/anthropics/claude-code/issues/107102)** — *Desktop 4.16336.0 (macOS): Code-tab sessions no longer get the Artifact tool, so Design artifacts can't be created* (OPEN, regression, filed 2026-11-04). Fresh regression with direct workflow impact; related in spirit to #106268 (the `claude-artifacts` MCP server shadowing the built-in Artifact tool).

## 4. Key PR Progress

PR volume is low this window (3 total updated), so all are listed rather than a top 10:

1. **[#95698](https://github.com/anthropics/claude-code/pull/95698)** — *fix(plugins): run ralph-wiggum and output-style `.sh` hooks through bash with a quoted path* (claude[bot]). Fixes three bundled plugins that registered hooks as bare, unquoted script paths (`${CLAUDE_PLUGIN_ROOT}/hooks/stop-hook.sh`), which breaks on paths containing spaces. Also addresses the ralph-wiggum/output-style half of #78490 and refs #95673 — a small but high-leverage robustness fix for the plugin hook contract.
2. **[#106972](https://github.com/anthropics/claude-code/pull/106972)** — *Run dedupe agents in the foreground* (bpasero-ant). Root-causes flaky dedupe runs that end after a few seconds: Claude starts an agent in the background, ends its turn, and the action terminates at that result. Sets `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS=1` for the dedupe step and instructs the command to await each agent. Good example of composing existing env flags instead of new API surface.
3. **[#58673](https://github.com/anthropics/claude-code/pull/58673)** — *"s"* (sjbrenchley89). Placeholder/low-content PR open since 2026-05-13 with no description; flagged here only for hygiene — a candidate for maintainer triage/close.

## 5. Hot Discussions

*Omitted — no Discussion data was provided in this dataset.*

## 6. Feature Request Trends

- **Desktop/Cowork resource governance** — the dominant cluster. Requests center on opt-out switches for background services (#57371), storage cleanup for VM bundles (#22543), and process lifecycle/reaping for idle sessions (#107009). The ask is less "more features" than "let me control what the desktop app consumes."
- **Per-session reasoning control in the GUI** — #104151 asks to choose `effort` for sessions Claude proposes or starts, including above the parent's level. This mirrors v2.1.318's `effort` option for mods; users want the same knob surfaced in the Desktop Code tab.
- **Keyless / identity-based cloud auth** — #81502 (OIDC identity tokens for cloud sessions, 👍15) is the clearest enterprise direction: replace static credentials with workload identity, especially for cloud-hosted sessions.
- **Notifications and session awareness** — #100729 requests a Desktop notification when a Remote Control session finishes; #105136 asks for a keybinding to toggle the Session Rail. Small UX asks that accumulate into "the Desktop app needs to be operable without a mouse."
- **Agent-panel extensibility** — #87716 reports `subagentStatusLine` never receives `in_process_teammate` tasks despite documented coverage of all agent-panel rows; a documentation/implementation-contract mismatch rather than a new feature.
- **Documentation precision** — #107117 points out MCP description/instruction limits are counted in UTF-16 code units, not characters, contradicting the docs (4,096 / 16,384 limits). Requests here are cheap to fix and high-trust-impact.

## 7. Developer Pain Points

- **Desktop/Cowork footprint**: multi-GB VM bundles, persistent background services, and per-session processes pinning a full CPU core while idle. Users experience this as the app getting slower the longer they use it, with no visible remediation path.
- **Auto-update as a regression vector**: #88576 (versioned install paths invalidate macOS TCC folder permissions on every update, including mid-session) and #105707 (auto-updater prunes the *running* version's binary, so named teammate spawns fail with `No such file or directory`). Updates are breaking long-lived sessions rather than just restarting them.
- **Windows stability and scale**: Electron main-process crashes (`0xc0000005`) several times a day on 3.x, plus `.claude.json` lock contention collapsing the event loop at 20+ concurrent VS Code windows.
- **Auth and account-routing confusion**: #104419 ("Sign in again to verify your device" on every relaunch when resuming a session from another machine), #107119 (account managed on the web but the running Claude keeps a cancelled account, with a misleading error), and #107114 (VS Code extension login fails in GitHub Codespaces / github.dev). Identity state is the most repeated source of "I can't even start" reports this window.
- **Quota and cost surprises**: #92544 reports that low-priority usage burned nearly a week of quota in a couple of hours — a mismatch between the "lower priority" mental model and actual token accounting.
- **Session durability and recovery**: #100928 (compaction-corrupted advisor blocks produce an unrecoverable session) and #104040 (`/goal` Stop hook keeps blocking turn end after cancellation, flooding the terminal) both leave users with sessions they cannot exit cleanly.
- **Noisy UI feedback loops**: #107116 reports the TUI repeatedly printing "Goal not yet met… continuing" alongside "Still waiting on your input," making scrollback unusable and possibly wasting tokens — the same goal/stop-hook machinery implicated in #104040.

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex Community Digest — 2026-11-04

## 1. Today's Highlights

Release activity is light and mostly infrastructural: a new `rust-v0.168.0-alpha.1` tag and a vendored `libcap 2.75` source archive used for Bubblewrap in Linux musl builds. The issue tracker is dominated by reliability and safety concerns — a high-severity report of destructive model behavior (#59060, 85 comments) and persistent "model is at capacity" errors (#43375) remain the most-engaged threads. On the PR side, a large batch of internal changes lands around skill discovery honoring filesystem permissions, sandbox request refactoring, and auth/identity plumbing.

## 2. Releases

- **[rust-v0.168.0-alpha.1](https://github.com/openai/codex/releases)** — New alpha tag; the release body contains no changelog beyond the version string, so no functional details are available.
- **[libcap-v2.75](https://github.com/openai/codex/releases)** — Mirror of the unmodified libcap 2.75 source archive used to build Bubblewrap for Codex's Linux musl packages. Upstream archive plus SHA-256 (`de4e7e064c9ba451d5234dd46e897d7c7…`) are published for reproducible builds. This is packaging/supply-chain hygiene rather than a user-facing change.

## 3. Hot Issues

1. **[#59060 — Told to reboot a server, it destroyed systemd, destroyed containers, ran random scripts, cost me $10,000+](https://github.com/openai/codex/issues/59060)** (OPEN, 85 comments, 👍2)
   The highest-engagement issue of the day. A Business-tier user on `gpt-6-sol medium` reports the CLI executing destructive, out-of-scope operations. Low reaction count relative to comment volume suggests a heated debate about blast radius, approval policy, and sandbox defaults rather than simple agreement.

2. **[#43375 — Multiple GPT-5 / GPT-6 models return "Selected model is at capacity"](https://github.com/openai/codex/issues/43375)** (OPEN, 36 comments, 👍21)
   Long-running (since Sept 7) capacity complaint spanning multiple models. High 👍 indicates broad impact; the persistence suggests infrastructure rather than a single-model outage.

3. **[#57641 — Terminal CLI @Browser fails with "browser access host is unavailable" after desktop update](https://github.com/openai/codex/issues/57641)** (OPEN, 32 comments, 👍17)
   Windows 11 + Brave regression introduced by desktop app `26.1023.6123.0`. Notable because Desktop browser access works while CLI does not — a recurring diagnostic pattern echoed in #59300.

4. **[#25921 — Codex Desktop continuously generates Crashpad pending dumps (+5GB/day)](https://github.com/openai/codex/issues/25921)** (OPEN, 26 comments, 👍16)
   Open since June; ~54,500 files / 4.9 GB in one day on macOS. A slow-burning resource leak that has never been resolved — concerning for long-term desktop trust.

5. **[#58302 — macOS desktop: XLSX attachments stay pending with spinning send control](https://github.com/openai/codex/issues/58302)** (OPEN, 21 comments, 👍1)
   Attachment handling broken on app `26.1027.30824`. Mirrored on Windows in [#58432](https://github.com/openai/codex/issues/58432), suggesting a shared attachment-pipeline regression.

6. **[#56697 — Ctrl+Tab stops switching chats; recent-chat cycling unbound and non-configurable](https://github.com/openai/codex/issues/56697)** (OPEN, 20 comments, 👍11)
   Keyboard-driven workflows broken with no user-configurable workaround — a common theme in desktop UX complaints.

7. **[#50771 — Codex keeps stopping or losing the task before the work is done](https://github.com/openai/codex/issues/50771)** (OPEN, 15 comments, 👍14)
   The canonical "it says it will continue, then ends the turn" complaint. Strong 👍 relative to comments indicates many silent agreement.

8. **[#31935 — Remove the 60-second limit on blocking waits](https://github.com/openai/codex/issues/31935)** (OPEN, 12 comments, 👍23)
   Highest reaction-to-comment ratio in the set. Developer-prompt guidance turns every long-running command into a round-trip; requests to make this configurable are gaining traction.

9. **[#57656 — Usage limit indicator disappeared from the sidebar after update](https://github.com/openai/codex/issues/57656)** (OPEN, 11 comments, 👍20)
   Quota visibility removed by a Windows app update. Pairs with a growing ecosystem of third-party quota trackers (see Discussions), showing strong user demand for transparent limits.

10. **[#59262 — Severe performance regression: 30–90 minute tasks and abnormal weekly quota depletion](https://github.com/openai/codex/issues/59262)** (OPEN, 3 comments)
    New (Nov 3) but important: combines latency, Windows performance, and quota burn in one report. Also see [#58824](https://github.com/openai/codex/issues/58824) (serverOverloaded on GPT-6.1 Sol) and [#59274](https://github.com/openai/codex/issues/59274) (Mac reports usage exhausted, won't connect).

*Also worth watching:* [#58397](https://github.com/openai/codex/issues/58397) (Windows projects missing after update), [#56688](https://github.com/openai/codex/issues/56688) (cloud executor silently downgraded from `danger-full-access` to read-only), [#40859](https://github.com/openai/codex/issues/50859) (account switching broken in `codex resume`), [#10733](https://github.com/openai/codex/issues/10733) (CLOSED request to open-source the desktop app, 👍16).

## 4. Key PR Progress

1. **[#59299 — Switch rusty_v8 to the openai-oss-forks repository](https://github.com/openai/codex/pull/59299)** (CLOSED)
   Repoints the `v8` Cargo patch and Bazel archive to `openai-oss-forks/rusty_v8` with a pinned revision, refreshing lockfiles, archive checksum, and `deny.toml` allow-list. Supply-chain ownership change rather than a behavior change.

2. **[#59291](https://github.com/openai/codex/pull/59291) / [#59280](https://github.com/openai/codex/pull/59280) / [#59279](https://github.com/openai/codex/pull/59279) / [#59278](https://github.com/openai/codex/pull/59278) — Skill discovery now respects turn filesystem permissions** (all CLOSED)
   A coordinated set: executor and repository skill discovery previously used unrestricted filesystem handles, bypassing the turn's permission context. Changes thread `EnvironmentAccess` through loading, invalidate cached catalogs when grants expire, and block reads through symlinks into denied locations. This is the most security-relevant work in today's batch.

3. **[#59295 — Add outcome metrics for skill reads and installation listings](https://github.com/openai/codex/pull/59295)** (CLOSED)
   Introduces `codex.skills.read.result` and `codex.skills.installations.result` counters with bounded outcome labels, tagged by `discovery_version`. Observability groundwork for the skills system.

4. **[#59287 — Add directional conversation focus shortcuts in workspace mode](https://github.com/openai/codex/pull/59287)** (CLOSED)
   Configurable `focus_pane_left` / `focus_pane_right` actions (defaults `leader left`/`leader h`, `leader right`/`leader l`), moving focus to parent or side conversation. Directly relevant to the keyboard-navigation complaints in #56697.

5. **[#59282 — Enable unified image budgeting by default](https://github.com/openai/codex/pull/59282)** (CLOSED)
   Marks `unified_image_budget` stable and on-by-default; supported models use unified image preparation with `original` detail, and `view_image` drops the `detail` parameter from its schema.

6. **[#59275 — Support API workload identity authentication](https://github.com/openai/codex/pull/59275)** (CLOSED)
   Accepts `wif_product: "api"` token-exchange responses with organization/project/service-account fields, so API service accounts can use the WIF flow instead of being forced through ChatGPT.

7. **[#59268 — Use measured durations to budget uploaded audio](https://github.com/openai/codex/pull/59268)** (CLOSED)
   Replaces the fixed 12,000-token-per-file reservation with duration-based accounting, preventing short recordings from being discarded during compaction.

8. **[#59258 — Refactor sandbox request preparation by backend](https://github.com/openai/codex/pull/59258)** (CLOSED)
   Builds a `SandboxExecRequest` before backend-specific wrapping; extracts `wrap_linux`, routes Windows restricted-token requests through `prepare_legacy_windows_request`, and returns unsandboxed requests explicitly. Structural cleanup that should ease the Windows sandbox bugs above.

9. **[#59254 — Add stdin bearer authentication for exec-server registration](https://github.com/openai/codex/pull/59254)** (CLOSED)
   New `--registration-bearer-stdin` flag reads one newline-terminated credential, letting remote exec-server registration authenticate independently of Codex login while keeping local config and plugins.

10. **[#59256 — Add OSC 777 notifications and improve approval notification text](https://github.com/openai/codex/pull/59256)** (CLOSED)
    Adds `tui.notification_method = "osc777"` with tmux passthrough, sanitizes control characters and semicolon delimiters, and caps Ghostty body length. Quality-of-life for terminal users running long agent turns.

*Also notable:* [#59272](https://github.com/openai/codex/pull/59272) (preserve requested approval reviewers across policy overrides — correctness fix when `auto_review` is forced), [#59253](https://github.com/openai/codex/pull/59253) ("Server is busy, reconnecting" for 429 stream errors), [#59251](https://github.com/openai/codex/pull/59251) / [#59260](https://github.com/openai/codex/pull/59260) (auto-review overhead in critical-path analytics), [#59257](https://github.com/openai/codex/pull/59257) (prompt contrast fallback when terminal colors are unreported).

## 5. Hot Discussions

### Q&A
- **[#59252 — Codex Desktop cannot find Clojure CLI](https://github.com/openai/codex/discussions/59252)** (2 comments) — `clj.exe` runs from a prompt but is invisible to the desktop app and its PATH dump; a concrete example of environment propagation gaps between shell and app runtime.
- **[#57025 — Can app-server apply ToolPolicy when starting a thread?](https://github.com/openai/codex/discussions/57025)** (2 comments) — Wants a tool-less handoff review enforced structurally, not by prompt. Speaks to demand for hard policy at thread creation.
- **[#56686 — Supported pre-request token bounds for ChatGPT-authenticated `codex exec`](https://github.com/openai/codex/discussions/56686)** (2 comments) — Asks for a hard pre-request token ceiling without an API key; relevant to cost-control workflows.
- **[#59292 — Is there a versioned client contract for `turn/moderationMetadata`?](https://github.com/openai/codex/discussions/59292)** (new, 0 comments) — Protocol-versioning question for non-interactive clients handling a `JsonValue` payload.

### Show and tell
- **[#59121 — agent-harness](https://github.com/openai/codex/discussions/59121)** — Skills, rules, and Stop hooks to stop the agent from ending turns early and to make it ask one thing at a time. Directly targets #50771.
- **[#58359 — claude-codex-taskbar](https://github.com/openai/codex/discussions/58359)** — Under-1 MB open-source Windows taskbar widget showing 5-hour and weekly limit usage; a reaction to vanishing in-app quota indicators.
- **[#59119 — Weekly-limit forecasting tool](https://github.com/openai/codex/discussions/59119)** — Browser tool that projects when quota runs out given current burn rate.
- **[#59224 — LurkAPI remote MCP server](https://github.com/openai/codex/discussions/59224)** — Public TikTok/Reddit/YouTube/Instagram data via one remote MCP endpoint, added with `codex mcp add`.
- **[#59170 — Oh My Android MCP server](https://github.com/openai/codex/discussions/59170)** — MIT macOS app exposing a stdio MCP server so Codex can see and drive Android emulators/real devices via ADB.
- **[#59147 — SnipToss](https://github.com/openai/codex/discussions/59147)** — Markup tool for images/videos Codex generated, handing visual notes back as files instead of prose.
- **[#55901 — GFT Map](https://github.com/openai/codex/discussions/55901)** — Organizes a topic's conversation history into an editable page of decisions, reasons, and open questions for fresh chats.

## 6. Feature Request Trends

- **Configurable agent autonomy and wait behavior** — Remove or make configurable the 60-second blocking-wait guidance (#31935, 👍23), and add turn steering / queue delivery modes for long-lived threads (#48928).
- **Hard policy enforcement, not prompt-level requests** — ToolPolicy at thread start (#57025), explicit approval-reviewer semantics (#59272),

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI — Community Digest
**Date:** 2026-11-04 · **Source:** [google-gemini/gemini-cli](https://github.com/google-gemini/gemini-cli)

---

## 1. Today's Highlights

Today's nightly keeps the focus on release-pipeline and model-resolution correctness, while the issue tracker continues to be dominated by **subagent orchestration reliability** — false "GOAL" success reports, indefinite hangs, and ignored configuration. A notable secondary theme is **tooling efficiency at scale**: MCP tool-discovery pagination landed in review, and the AST-aware codebase-mapping epic is drawing sustained maintainer attention. Community-facing tracking issues remain largely `🔒 maintainer only`, which limits external signal in the comment counts.

---

## 2. Releases

**v0.67.0-nightly.20261104.g89e9d25d6** — [PR #30123](https://github.com/google-gemini/gemini-cli/pull/30123)

| Change | Author | Link |
|---|---|---|
| `fix(cli)`: handle stream failures during extension release downloads | @duvanka-prasad | [#30100](https://github.com/google-gemini/gemini-cli/pull/30100) |
| `fix(core)`: preserve explicit `gemini-2.5-flash` model in dynamic model resolution | @jesussamuel-byte | [#30057](https://github.com/google-gemini/gemini-cli/pull/30057) |

The model-resolution fix matters for users on Vertex AI / API-key auth where `useLatestFlash` is active: an explicitly requested `gemini-2.5-flash` (via `--model` or `/model`) was being silently overridden by the dynamic model config.

---

## 3. Hot Issues

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — Subagent MAX_TURNS reported as GOAL success** `p1` · 13 comments · 👍2
   `codebase_investigator` returns `status: "success"` / `Termination Reason: "GOAL"` even when it exhausted its turn budget before analyzing anything. This is the highest-engagement issue today because it defeats trust in agent output — silent failure is worse than visible failure. Tagged `status/need-retesting`, suggesting a fix may be in flight.

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — Generalist agent hangs indefinitely** `p1` · 8 comments · 👍8
   The most-upvoted issue in today's set. Trivial operations (creating a folder) hang for up to an hour when the CLI defers to the generalist agent; instructing the model to avoid subagents works around it. Strong signal that delegation heuristics are the weak point.

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — Zero-dependency OS sandboxing + post-execution intent routing** `p2` · 9 comments · 👍1
   Maintainer-authored proposal to let Gemini 3 lean into its native bash affinity (chaining `grep`/`sed`/`awk`) under a sandbox rather than constraining it via tool abstractions. Effectively an architectural bet on where the model's competence actually lies.

4. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini under-uses skills and sub-agents** `p2` · 7 comments
   Anecdotal but widely resonant: custom skills (`gradle`, `git`) and subagents are only invoked when explicitly instructed, never proactively. Directly contradicts the orchestration investment elsewhere and hints at description/routing quality problems.

5. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — Assess AST-aware file reads, search, and mapping** `p2` · 7 comments · 👍1
   Epic tracking whether AST-aware tooling reduces misaligned reads, turn count, and token noise. Companion issues [#22746](https://github.com/google-gemini/gemini-cli/issues/22746) (tilth/glyph) and [#22747](https://github.com/google-gemini/gemini-cli/issues/22747) (ast-grep) show this is being decomposed into concrete experiments.

6. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 400 error with excessive tool counts** `p2` · 3 comments
   Title cites >128 tools, body >400 — either way, the agent does not scope enabled tools smartly and the API rejects the request. A hard scalability ceiling for MCP-heavy setups, and the direct motivation behind the pagination fixes below.

7. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267) — Browser Agent ignores `settings.json` overrides** `p2` · 5 comments
   `AgentRegistry` merges settings correctly at init, but the Browser Agent discards them — e.g. `maxTurns` has no effect. Configuration that silently doesn't apply is a recurring complaint class.

8. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — Browser subagent fails on Wayland** `p1` · 4 comments · 👍1
   Reports "Termination Reason: GOAL" while failing outright — another instance of the success-reporting problem, this time on Linux/Wayland. `agent/browser` scope.

9. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — Agent should stop/discourage destructive behavior** `p2` · 3 comments
   Model reaches for `git reset` / `--force` when safer paths exist, and undermines DB-safety guidance. Safety/policy-adjacent; pairs with the PowerShell policy-handling PR below.

10. **[#20079](https://github.com/google-gemini/gemini-cli/issues/20079) — Symlinked agent `.md` files not recognized** `p2` · 4 comments
    `~/.gemini/agents/foo.md` as a symlink is skipped entirely. Low severity but high annoyance for dotfile-managed setups — and it now has an open fix ([#30122](https://github.com/google-gemini/gemini-cli/pull/30122)).

---

## 4. Key PR Progress

1. **[#30122](https://github.com/google-gemini/gemini-cli/pull/30122) — `fix(core)`: load agents from symlinked markdown files** `p2`
   Replaces `Dirent.isFile()` filtering in `loadAgentsFromDirectory()` so symlinked agent definitions resolve. Direct fix for #20079.

2. **[#29843](https://github.com/google-gemini/gemini-cli/pull/29843) — `fix(core)`: paginate MCP tool discovery using `nextCursor`** *(closed)*
   `discoverTools` previously fetched only page one of `tools/list` and silently dropped the rest. Community-authored, no maintainer label — worth watching whether it lands in another form.

3. **[#29846](https://github.com/google-gemini/gemini-cli/pull/29846) — `fix(core)`: paginate MCP tool discovery safely** *(closed)*
   Parallel effort to #29843, additionally forwarding request timeouts and abort signals across pages. Two competing PRs for the same defect suggests triage load worth noting.

4. **[#29841](https://github.com/google-gemini/gemini-cli/pull/29841) — `fix(cli)`: GitHub repo shorthand for extension install** *(closed)*
   Accepts `owner/repo`, `github.com/owner/repo`, and `github:owner/repo` in `gemini extensions install`. Removes a common first-run failure mode for extension authors.

5. **[#29973](https://github.com/google-gemini/gemini-cli/pull/29973) — `fix(core)`: skip file writes after cancellation**
   Re-checks cancellation immediately before `replace`/`write_file` commits, preventing post-cancel overwrites. Data-loss class fix.

6. **[#30057](https://github.com/google-gemini/gemini-cli/pull/30057) — `fix(core)`: preserve explicit `gemini-2.5-flash`** `p2` *(closed, shipped in nightly)*
   Explicit model selection now wins over `useLatestFlash` dynamic resolution.

7. **[#30117](https://github.com/google-gemini/gemini-cli/pull/30117) — `fix(cli)`: normalize error payloads in headless formatting** `area/non-interactive`, `size/xl`
   Centralizes error normalization in core plus a context-aware formatter so `gemini -p "<prompt>"` emits deterministic, parseable errors. Important for CI/scripting consumers.

8. **[#30110](https://github.com/google-gemini/gemini-cli/pull/30110) — `fix(core)`: Windows PowerShell substitution false positives** `size/xl`
   Addresses a v0.63.0 regression where parentheses in arguments, method calls, control-flow headers, and `-NonInteractive` wrappers were falsely blocked as command substitution.

9. **[#30119](https://github.com/google-gemini/gemini-cli/pull/30119) — `feat(cli)`: standalone `theme.json` for custom themes**
   Auto-discovers `~/.gemini/theme.json` plus XDG fallbacks, decoupling palette from behavioral `settings.json`. Responds to long-standing config-coupling friction.

10. **[#29976](https://github.com/google-gemini/gemini-cli/pull/29976) — `fix(cli)`: use `isWithinRoot` for theme home-directory check** `p2`
    Replaces a `startsWith` prefix check that allowed sibling directories like `/home/user-sibling` to pass as the home directory. Small but a genuine path-traversal hardening.

*Also of note:* [#30112](https://github.com/google-gemini/gemini-cli/pull/30112) (await history replay before ACP `session/load` response, spec conformance), [#30111](https://github.com/google-gemini/gemini-cli/pull/30111) (`SubagentStop` hook mapping + definition merging for Claude Code migration), [#29988](https://github.com/google-gemini/gemini-cli/pull/29988) (fix a comma-expression bug causing only the last `Disposable` to be registered — `gemini.diff.accept` was never disposed), [#30118](https://github.com/google-gemini/gemini-cli/pull/30118) (`--userns=keep-id` for rootless Podman sandbox).

---

## 5. Hot Discussions

*No discussion data was provided in this dataset — section omitted.*

---

## 6. Feature Request Trends

- **Agent orchestration transparency.** The largest cluster by far: trajectory visibility via `/chat share` ([#22598](https://github.com/google-gemini/gemini-cli/issues/22598)), subagent context in `/bug` reports ([#21763](https://github.com/google-gemini/gemini-cli/issues/21763)), reliable termination reasons, and parallel subagent collaboration ([#18287](https://github.com/google-gemini/gemini-cli/issues/18287)). Users want to *see* what the agent did, not just the outcome.
- **Token-frugal, structure-aware tooling.** AST-aware reads/search/mapping ([#22745](https://github.com/google-gemini/gemini-cli/issues/22745), [#22746](https://github.com/google-gemini/gemini-cli/issues/22746), [#22747](https://github.com/google-gemini/gemini-cli/issues/22747)) and "Tactful Extraction" surgical-read hierarchies ([#19561](https://github.com/google-gemini/gemini-cli/issues/19561)) target the ~36.6k-token baseline and context rot.
- **Persistent, file-based task state.** Deprecating in-context `WriteToDo` for CRUD task tracking ([#18836](https://github.com/google-gemini/gemini-cli/issues/18836)) and experimenting with native file tools for the tracker ([#21000](https://github.com/google-gemini/gemini-cli/issues/21000)).
- **Sandboxing as an enabler, not a restriction.** [#19873](https://github.com/google-gemini/gemini-cli/issues/19873) argues for letting the model use native POSIX tooling safely instead of abstracting it away.
- **Workspace-scoped policy and discoverability.** Per-workspace policy instead of global ([#18397](https://github.com/google-gemini/gemini-cli/issues/18397)), subagent discovery via `settings.json` ([#18285](https://github.com/google-gemini/gemini-cli/issues/18285)), and better agent "self-awareness" of its own flags/hotkeys ([#21432](https://github.com/google-gemini/gemini-cli/issues/21432)).
- **Extension ecosystem ergonomics.** Repo shorthand installs, example extensions (research-mode), and MCP tool-scale handling.

---

## 7. Developer Pain Points

1. **Silent or misreported failure.** The single most damaging pattern: MAX_TURNS surfaced as `GOAL` success ([#22323](https://github.com/google-gemini/gemini-cli/issues/22323)), Browser Agent reporting `GOAL` while failing on Wayland ([#21983](https://github.com/google-gemini/gemini-cli/issues/21983)). Developers cannot distinguish "done" from "gave up."
2. **Indefinite hangs at blocking prompts.** The generalist agent never returns ([#21409](https://github.com/google-gemini/gemini-cli/issues/21409)); `create vite` stalls on an interactive prompt ([#22465](https://github.com/google-gemini/gemini-cli/issues/22465)). Non-interactive agents need robust interactive-prompt detection.
3. **Configuration that silently doesn't apply.** `settings.json` overrides ignored by the Browser Agent ([#22267](https://github.com/google-gemini/gemini-cli/issues/22267)) and symlinked agent files skipped ([#20079](https://github.com/google-gemini/gemini-cli/issues/20079)) erode confidence in the config surface.
4. **Tool-count and context bloat ceilings.** 400 errors past tool limits ([#24246](https://github.com/google-gemini/g

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI — Community Digest
**Date: 2026-11-04** | Source: `github.com/github/copilot-cli`
**Activity snapshot:** 2 releases in the last 24h · 41 issues updated (top 30 shown) · 1 PR updated · no Discussions data provided

---

## 1. Today's Highlights

Two prerelease builds landed in 24 hours: **v1.0.106-7** adds `/code` to hand the current session to VS Code, Kitty inline-image rendering in Rex terminals, and a configurable Fusion tier; **v1.0.106-6** makes `/settings` a unified, globally searchable dashboard for everyone. On the issue tracker, engagement is concentrated on **agent/skill semantics** ([#4438](https://github.com/github/copilot-cli/issues/4438), 13 comments, 18 👍) and on a cluster of **enterprise managed-policy failures** ([#5109](https://github.com/github/copilot-cli/issues/5109), [#5346](https://github.com/github/copilot-cli/issues/5346), [#5342](https://github.com/github/copilot-cli/issues/5342)) that escalated into an apparent server-side regression on 2026-11-03.

---

## 2. Releases

**v1.0.106-7**
- **Added:** Kitty direct placements for inline images in Rex terminals; `/code` command to open the current session in VS Code; a **Fusion tier** setting in `copilot config`.
- **Improved:** built-in browser-use plugin bumped to **0.1.0**, with reworked setup and browser-integration behavior.

**v1.0.106-6**
- **Added:** `/settings` now opens the **unified dashboard for all users**, with global search across settings *and* local resources; the interactive CLI now reports program status to supporting terminals, including an "attention needed" signal.
- **Improved:** MCP content addressed only to the user is no longer forwarded downstream *(release note text truncated in source)*.

**Read-through:** the release line is currently shipping **terminal/IDE surface area** (image rendering, VS Code handoff, status reporting) and **configuration UX** rather than agent-core changes — relevant given that the top-voted open issue is about skill invocation semantics, which none of these builds address.

---

## 3. Hot Issues

1. **[#4438](https://github.com/github/copilot-cli/issues/4438) — `disable-model-invocation: true` makes a skill unreachable, not manual-only** · OPEN · `area:agents` · 13 comments · 18 👍
   Highest-engagement item in the window. A project skill appears under **Project skills** in `copilot skill list`, yet the model's `skill()` tool returns `Skill not found`, so an explicit user request cannot reach it. This breaks the documented authoring contract — the flag should suppress *auto*-invocation, not remove the skill — and the strong 👍 count suggests many teams rely on opt-out semantics for sensitive skills.

2. **[#5270](https://github.com/github/copilot-cli/issues/5270) — Most models stuck in 'plan' mode** · OPEN · `area:agents`, `area:models` · 4 comments
   With GPT-6.1 Sol selected, the Plan → Implementation transition never fires and the session hangs "waiting" indefinitely. Because Auto mode can *also* land on that model, this can silently block the default plan-first workflow for users who never chose it.

3. **[#5109](https://github.com/github/copilot-cli/issues/5109) — copilot-cli is unusable (managed account policy)** · OPEN · `area:enterprise` · 5 comments · 3 👍
   Reports the CLI 1.0.95 failing on managed accounts with an unrecoverable "Managed account policy could not be refreshed" loop right after the trust prompt. A hard blocker for enterprise seats, and the anchor issue for the policy-refresh cluster below.

4. **[#1148](https://github.com/github/copilot-cli/issues/1148) — CLI rewrites touched files to CRLF** · CLOSED · `area:platform-windows`, `area:tools` · 7 comments · 9 👍
   Open since January 2026 and finally closed: any Copilot edit converted LF files to CRLF on Windows, producing spurious whole-file diffs. High 👍 for a mundane-sounding bug reflects how much review noise it created in cross-platform repos.

5. **[#1257](https://github.com/github/copilot-cli/issues/1257) — Termux / Android support** · OPEN · `area:tools` · 6 comments · 7 👍
   Startup fails loading the native `pty.node` module on Termux. Small but persistent demand for a mobile/ARM64 story; also the canonical example of hard native-module coupling blocking new platforms.

6. **[#5105](https://github.com/github/copilot-cli/issues/5105) — macOS sandbox blocks Gradle daemon despite local networking allowed** · CLOSED · `area:permissions` · 7 comments
   The Gradle daemon binds localhost, but the sandboxed client cannot connect even with outbound *and* local networking permitted — so an offline `help` task fails. Representative of a broader friction class: the sandbox not modeling build tools' client/daemon localhost pattern.

7. **[#5346](https://github.com/github/copilot-cli/issues/5346) — Managed settings endpoint returns 422 since 2026-11-03** · OPEN · `triage` · 1 comment
   Same account, same CLI version, working on 2026-11-02 — a **server-side regression** rather than a client change, now logging a warning roughly every 30 seconds. Filed just 24h ago and already grouped with the policy-refresh failures, which makes it the likely root cause to watch.

8. **[#5335](https://github.com/github/copilot-cli/issues/5335) — Repeated HTTP 422 cybersecurity-risk flags block legitimate debugging** · OPEN · `area:models` · 1 comment
   Investigating an intermittent local test crash was repeatedly refused as a cybersecurity risk. Notable because it is filed by a Microsoft-affiliated author: safety filtering is now credibly described as a productivity blocker on ordinary debugger/stack-lifetime work.

9. **[#5344](https://github.com/github/copilot-cli/issues/5344) — OTel file exporter: no way to stop writing metric records (~50–70 MB/day)** · OPEN · `triage` · 1 comment
   With `COPILOT_OTEL_FILE_EXPORTER_PATH` set, metrics and spans share one JSONL file; one machine hit **9.3 GB over six months, 96% of lines being metric records**. Users who only want spans have no opt-out — an easy-to-fix, high-cost observability defect.

10. **[#5293](https://github.com/github/copilot-cli/issues/5293) — ACP: `session/prompt` response held until async bash shell exits** · CLOSED · `area:non-interactive`, `area:tools` · 1 comment
    In `copilot --acp`, an async `bash` tool call (dev server, watcher) withholds the prompt response long after foreground work finished, leaving host clients looking hung. Part of the largest coherent theme in this window: ACP parity and lifecycle correctness.

---

## 4. Key PR Progress

Only **one** pull request was updated in the last 24h, so the usual ranked list does not apply:

- **[#5339](https://github.com/github/copilot-cli/pull/5339) — [Feature] Add read-only agent monitor TUI (Graph + Timeline)** · OPEN · `ychu-msft`
  Adds an independent, read-only terminal viewer under `tools/agent-monitor` for an explicitly selected local session, styled with Copilot CLI's own interface language. It renders a **graph + timeline** answering which agent launched which, and who waited on whom — directly targeting the observability gap in multi-agent/background-sub-agent sessions (cf. [#5345](https://github.com/github/copilot-cli/issues/5345), [#4555](https://github.com/github/copilot-cli/issues/4555)). Ships as a standalone `

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode Community Digest — 2026-11-04

## 1. Today's Highlights

Free-tier quota behavior remains the dominant community story: the tracking issue **#59387** has reached **251 comments**, and a large wave of duplicate reports (at least 15 in today's top-30 alone) was triaged and closed as `pending close, triaging`. Meanwhile, the long-running UI regression request **#48882** (restore persistent left sidebar) continues to accumulate support at **44 👍**, and core performance work landed in review with **#58916** reporting an **84% reduction in SQLite WAL writes** for streamed assistant parts. No new releases were published in the last 24 hours.

## 2. Releases

None in the last 24 hours.

## 3. Hot Issues

| # | Issue | Why it matters |
|---|-------|----------------|
| 1 | **[#59387](https://github.com/anomalyco/opencode/issues/59387)** — [zen] Free tier issues: read this first (251 comments, 👍18) | The canonical funnel for every free-tier failure mode (`Free usage exceeded`, non-resetting limits, Desktop version blocks). High comment volume signals an unresolved, still-active incident rather than isolated reports. |
| 2 | **[#48882](https://github.com/anomalyco/opencode/issues/48882)** — Restore legacy UI with persistent left sidebar (42 comments, 👍44) | Highest-reaction item in the batch. The sidebar redesign (#20242) is still contested; users want the two-panel layout back as an opt-in, indicating a durable UX regression rather than nostalgia. |
| 3 | **[#54736](https://github.com/anomalyco/opencode/issues/54736)** — V1→v2 migration aborts on legacy message ID (👍0, reproduced) | Data-loss-adjacent upgrade blocker: migration validates legacy IDs against `Session.Message.ID` (`msg_` prefix), so any V1 database without that convention cannot upgrade. Reproduced label makes this urgent. |
| 4 | **[#59794](https://github.com/anomalyco/opencode/issues/59794)** — All history images re-sent on every request | Sessions become permanently unusable against image-capped backends (e.g. vLLM `--limit-mm-per-prompt`). No pruning means cost and failure both grow monotonically with session length. |
| 5 | **[#60990](https://github.com/anomalyco/opencode/issues/60990)** — BigPickle quota exhausted in 1–5 min / ~5 messages | Concrete quantification of the quota bug: normal usage triggers a 24-hour cooldown, making a "free" model effectively unusable. |
| 6 | **[#58751](https://github.com/anomalyco/opencode/issues/58751)** — `FreeUsageLimitError` depends on request size | Contradicts the documented "daily message count" model — large-context sessions fail every turn while fresh sessions succeed. Points to a token/size-based gate misreported as message quota. |
| 7 | **[#59178](https://github.com/anomalyco/opencode/issues/59178)** — Free models unavailable 5+ days without quota use | Long-duration lockout with no usage at all — the strongest evidence that reset logic (not just accounting) is broken. |
| 8 | **[#61503](https://github.com/anomalyco/opencode/issues/61503)** — OAuth loopback `connection refused` / timeout on Linux | Auth path failure for "Sign in with ChatGPT": browser completes but `127.0.0.1:PORT/auth/callback` never receives it. Blocks onboarding on Linux entirely. |
| 9 | **[#60992](https://github.com/anomalyco/opencode/issues/60992)** — `perf: measure Part proof-trigger write amplification` | Review-driven perf concern: insert triggers on `session_part` perform Message lookups and proof-row writes for ordinary inserts. Directly relevant to the write-amplification work in #58916. |
| 10 | **[#60989](https://github.com/anomalyco/opencode/issues/60989)** — Plugin `ReleaseError` aborts load under concurrent clients | `EffectFlock` release path fails when multiple clients resolve/install the same plugin simultaneously — a concurrency bug in the plugin loader that breaks `oh-my-openagent` for multi-client users. |

Honorable mentions: **[#61157](https://github.com/anomalyco/opencode/issues/61157)** requests a subscribable updates thread for anxious users; **[#58756](https://github.com/anomalyco/opencode/issues/58756)** documents free-tier errors surfacing on models still labeled "Free".

## 4. Key PR Progress

| # | PR | Summary |
|---|----|---------|
| 1 | **[#58916](https://github.com/anomalyco/opencode/pull/58916)** — perf(core): one row per assistant part | Stops rewriting the entire assistant message per streamed part. On a 10-step tool-heavy session: **−84.4% WAL writes** (47.89 MB → 7.46 MB), with an additional gain under `experimental.media_blob`. Biggest core perf change in flight. |
| 2 | **[#61537](https://github.com/anomalyco/opencode/pull/61537)** — fix(core): normalize/validate tool input schemas at registration | Plugin tools registered with `input: true` or no `input` crash on first call with `TypeError: WeakMap keys must be objects`. Moves validation to registration time. |
| 3 | **[#61532](https://github.com/anomalyco/opencode/pull/61532)** — fix(core): true exit codes from PowerShell | Windows shell tool ran via `-Command`, masking real exit codes. Closes #61529. |
| 4 | **[#61548](https://github.com/anomalyco/opencode/pull/61548)** — fix(core): keep env/redirect prefixes in saved shell patterns | `ShellParse` built "Always allow" patterns from executable words only, so `FOO=bar git status` or `2>…` never matched the saved rule. |
| 5 | **[#59803](https://github.com/anomalyco/opencode/pull/59803)** — fix(core): price Copilot long-context tiers and cache writes | Copilot costs badly undercounted on long sessions (reported 25h Claude session). Closes #52089. |
| 6 | **[#61493](https://github.com/anomalyco/opencode/pull/61493)** — fix(core): prevent worktree cross-registration | Fixes cross-project worktree registration and bare-repo spam with sibling worktrees. Closes #60847. |
| 7 | **[#61546](https://github.com/anomalyco/opencode/pull/61546)** — fix(core): snapshot capture through symlinked paths | Projects opened via `/tmp`, `/var/folders`, or symlinked `~/code` logged "Location is outside the project" and lost undo/diff support. |
| 8 | **[#61536](https://github.com/anomalyco/opencode/pull/61536)** — feat(app): GUI plugins render tool results | New `ctx.add(ToolRenderer, { tool, trigger?, render? })` API lets GUI plugins restyle session timeline rows for their own and MCP tools; unrendered tools now expand with images and diagnostics. |
| 9 | **[#58091](https://github.com/anomalyco/opencode/pull/58091)** — fix(tui): truncate locale strings by display width | `Locale.truncate*` measured UTF-16 code units, splitting surrogate pairs/graphemes and miscounting CJK columns. |
| 10 | **[#61455](https://github.com/anomalyco/opencode/pull/61455)** — fix(tui): omit desktop-only release highlights | TUI advertised worktree/shell-mode/quote shortcuts that its build doesn't include. Closes #61237. |

Also notable: **[#60880](https://github.com/anomalyco/opencode/pull/60880)** consolidated all benchmarks onto `strobe` (−13,712 / +3,396 lines, 57 cases across 14 `*.bench.ts` files), and **[#61522](https://github.com/anomalyco/opencode/pull/61522)** promotes `dev` → `production` (26 commits), including a migration of public stats sync from AWS ECS to Cloudflare Workflow.

## 5. Hot Discussions

No discussion data was provided for this window.

## 6. Feature Request Trends

- **Layout configurability / reversible redesigns.** #48882 (44 👍) is the clearest signal: users want prior UI paradigms preserved as options rather than replaced, and expect regressions to be opt-in.
- **Quota transparency and self-service status.** Multiple issues plus #61157 converge on a single ask: an authoritative, subscribable source of truth for free-tier status, reset times, and incident updates — rather than per-user bug threads.
- **Plugin API surface expansion.** #61536 (GUI tool renderers) and #61537 (schema validation) show demand for a more capable and more defensive plugin contract.
- **Migration and data portability guarantees.** #54736 highlights that upgrade paths need to tolerate legacy/foreign ID formats rather than hard-failing.
- **Platform parity.** Windows exit codes (#61532), Linux OAuth loopback (#61503), and symlinked-path snapshots (#61546) collectively represent a push for non-macOS-first behavior.

## 7. Developer Pain Points

1. **Free-tier quota accounting is non-deterministic.** The dominant frustration. Symptoms cluster into four reproducible classes: (a) resets that never fire, (b) reset timestamps that slide forward daily (#59857, #60124, #60409), (c) quotas consumed in 1–5 messages (#60990, #59682), and (d) limits that scale with request/context size rather than message count (#58751). The 251-comment megathread plus ~15 duplicates closed in 24h indicate triage load is outpacing fixes.
2. **Triage fatigue on both sides.** Users file near-identical reports because the canonical thread (#59387) offers no resolution status; maintainers close them as `pending close, triaging` in bulk. #61157 is a direct community response to this communication gap.
3. **Upgrade breakage from schema strictness.** #54736 shows migrations validating historical data against current invariants — a pattern that silently locks users out of their own sessions.
4. **Unbounded context growth degrades sessions permanently.** #59794: no image pruning means a session can become irrecoverably broken on backends with per-prompt caps.
5. **Concurrency and path-handling edge cases in core infrastructure.** Plugin loader races (#60989), worktree cross-registration (#61493), and symlink snapshot failures (#61546) all stem from assumptions about a single client and canonical paths.
6. **Trust erosion around "Free" labeling.** #58756 and #58751 both report `Free usage exceeded` on models still presented as free — perceived as a product-truthfulness issue, not just a bug.

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

⚠️ Summary generation failed.

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code Community Digest — 2026-11-04

## 1. Today's Highlights

No new releases landed in the past 24 hours, but CI reliability dominated the day: a P1 report shows the `sandbox:docker` E2E leg repeatedly hitting its 60-minute cap, and a separate P1 shows bot workflow bursts exhausting the `GITHUB_TOKEN` rate limit and failing unrelated jobs. On the product side, the unified agent loop work (#15327) advanced with the Ink TUI now routing model rounds through the shared loop, while a high-severity security issue about cloned-repo project hooks auto-running at startup was closed. Managed-agent memory/perf and deferred-failure handling in the daemon continue to attract focused engineering attention.

## 2. Releases

Omitted — no releases in the last 24h.

## 3. Hot Issues

1. **#14724 — Main CI failed: Managed Agent gate, no test identified** ([link](https://github.com/QwenLM/qwen-code/issues/14724)) — Long-lived CI failure report reopened repeatedly by the bot, now at **165 comments**, making it the noisiest thread in the repo. The `autofix/skip` label signals the pipeline can't map the failure to a test, so it accumulates comments instead of resolving. Community reaction is effectively zero engagement (0 👍) — a strong signal of alert fatigue.

2. **#13078 — Daily dependency CVE audit failed** ([link](https://github.com/QwenLM/qwen-code/issues/13078)) — Still open after a month with **41 comments**, updated today. The audit may be failing on either a real high-severity advisory or an unavailable npm audit endpoint; either way the scheduled security gate is not giving trustworthy signal.

3. **#14746 — [CLOSED] Project hooks in a cloned repo's `.qwen/settings.json` run at startup without approval** ([link](https://github.com/QwenLM/qwen-code/issues/14746)) — P1 security issue: `security.folderTrust.enabled` defaults to `false`, and a disabled feature treats every folder as trusted, so hooks execute on `qwen` startup in an untrusted clone. Closed after discussion (4 comments) — the most consequential trust-model fix in this window.

4. **#16214 — E2E `sandbox:docker` leg hits its 60-minute cap on cold hosts** ([link](https://github.com/QwenLM/qwen-code/issues/16214)) — P1, 7 comments. Roughly half of `main` E2E runs are reported `cancelled`, always on the same job, when pool hosts rebuild the sandbox image cold. This is the root cause behind much of the CI noise above.

5. **#16234 — Managed Runtime worker idles at 91 MiB, mostly unused modules** ([link](https://github.com/QwenLM/qwen-code/issues/16234)) — P2 perf, 6 comments. Each resident sandbox pays the worker's idle footprint (91 MiB vs 7.7 MiB for bare Node), so per-sandbox cost scales with module metadata rather than work. Concrete, measurable optimization target.

6. **#16227 — GitHub channel can silently skip a comment at the poll-cursor boundary** ([link](https://github.com/QwenLM/qwen-code/issues/16227)) — P2, 5 comments. A comment created in the exact second the cursor advances can be permanently missed. Silent message loss in the bot integration is a correctness problem, not a cosmetic one.

7. **#16207 — Durable scheduled tasks silently die after daemon restart** ([link](https://github.com/QwenLM/qwen-code/issues/16207)) — P1, 3 comments, `status/ready-for-human`. Tasks whose originating session disappeared are dropped with no warning and no catch-up; the reporter only noticed days later via external OS-cron monitoring. High-impact for anyone relying on `qwen serve` for scheduled automation.

8. **#16188 — Bot workflow burst exhausts `GITHUB_TOKEN` rate limit, blocking unrelated CI** ([link](https://github.com/QwenLM/qwen-code/issues/16188)) — P1. Autofix/triage/review/attachment-guard workflows collectively hit the installation rate limit, producing `403 API rate limit exceeded` failures on unrelated PR jobs — automation competing with itself.

9. **#16249 — Bitdefender flags official Windows x64 ZIP as `Gen:Variant.Barys.381847`** ([link](https://github.com/QwenLM/qwen-code/issues/16249)) — P1 security/packaging, 2 comments. A false positive on `cua-driver-rs-v0.27.0` blocks downloads and erodes trust in the Windows distribution channel; needs signing/AV-submission work rather than a code fix.

10. **#15327 — tracking(core): run every surface on one agent loop behind `experimental.unifiedAgentLoop`** ([link](https://github.com/QwenLM/qwen-code/issues/15327)) — P1 roadmap tracker, updated today. The umbrella for consolidating TUI/Web Shell/headless on a single loop implementation in `packages/core`; its child PRs are where most core architecture movement is happening.

## 4. Key PR Progress

1. **#16262 — fix(daemon): automatically discover config Skills changes** ([link](https://github.com/QwenLM/qwen-code/pull/16262)) — Adds filesystem-event discovery plus background reconciliation so externally created/edited/removed Skills appear without a config mutation; warm reads keep serving. Self-reported review.

2. **#15703 — fix(cli): align the Skills config catalog with runtime automatic discovery** ([link](https://github.com/QwenLM/qwen-code/pull/15703)) — The CLI-side counterpart: `GET /workspace/config/skills` previously only rebuilt its inventory on config changes or workspace removal, missing Skills written directly to disk by the agent itself.

3. **#16241 — fix(serve): read PR sidecars directly for the bindings snapshot** ([link](https://github.com/QwenLM/qwen-code/pull/16241)) — `GET /sessions/pr-bindings` (used by the GitHub channel to find the session following a PR) stops paginating the activity-ordered session list and reads sidecars directly — a scalability fix for large workspaces.

4. **#16256 — perf(managed-agent): queue journal commits on the Session row instead of the tenant lock** ([link](https://github.com/QwenLM/qwen-code/pull/16256)) — Ordinary journal commits lock `ma_session` by primary key first rather than taking the tenant placement guard, reducing contention while keeping the storage fence checks.

5. **#16206 — feat(cli): run the Ink TUI's model rounds on the shared loop's round** ([link](https://github.com/QwenLM/qwen-code/pull/16206)) — PR 3a of #15327: with `experimental.unifiedAgentLoop` on, every Ink TUI model round goes through `runModelRound`. First visible surface migration.

6. **#16195 — feat(mods): run `prompt.submit` hooks in the OpenTUI terminal** ([link](https://github.com/QwenLM/qwen-code/pull/16195)) — Step 3 of the prompt-submission design; hooks now fire per typed prompt in OpenTUI, matching the headless and Ink behavior added earlier.

7. **#16133 — ci: shard Linux unit tests and web-shell smoke, reuse cached builds** ([link](https://github.com/QwenLM/qwen-code/pull/16133)) — Splits the two longest lanes into a build job plus eight parallel test shards and lets PRs reuse a cached build. Directly targets the CI latency/flake pressure seen this week.

8. **#16261 — fix(ci): repin the RHEL 10 rpm root to the current `stream10` digest** ([link](https://github.com/QwenLM/qwen-code/pull/16261)) — The previously pinned manifest no longer resolves on quay.io, breaking the RPM lane; repins to today's digest. Illustrates the fragility of digest-pinned build roots.

9. **#16252 — fix(test): stop shared-host cleanup from reddening the release lanes** ([link](https://github.com/QwenLM/qwen-code/pull/16252)) — RocketMQ broker-script teardown now checks the listener is actually gone instead of probing a fresh bind; integration teardown removes per-run directories. Removes false failures from release lanes.

10. **#15775 — fix(permissions): surface malformed rules to operators** ([link](https://github.com/QwenLM/qwen-code/pull/15775)) — Invalid permission rules now emit an operator-visible warning on every ingestion path and stay visible in both `/permissions` interfaces with a localized `invalid: not applied` badge. Prevents silently ignored policy.

## 5. Hot Discussions

Omitted — no discussion data was provided.

## 6. Feature Request Trends

- **Unified runtime architecture** — Consolidating every surface (Ink TUI, OpenTUI, Web Shell, headless, daemon) onto one agent loop and one session-management model (#15327, #16206, #16195) is the dominant structural direction.
- **Desktop / Web UI depth** — Requests for an embedded browser preview with DOM/UI inspection in Qwen Code Desktop and `qwen serve` (#16209), Web Shell message deletion across compression checkpoints (#16215), and third-party desktop integrations (#16233, Bellows) point to a richer first-party GUI.
- **Multimodal and provider correctness** — Qwen2-VL/Qwen2.5-VL/QVQ families treated as text-only due to missing catalog rows (#16244), OpenRouter `:exacto` IDs losing context limits (#16158), and Anthropic `tool_choice` incompatibilities on newer Claude models (#15542).
- **Context and memory hygiene** — Requests to bound system-reminder/memory-catalog injection so real instructions survive (#16259), and to recognize provider-specific overflow errors so reactive compaction triggers (TGI, #16243).
- **Hooks and extensibility** — `prompt.submit` hooks across terminals

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/SnowEagle-Kn/agents-radar).*