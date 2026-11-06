# AI CLI Tools Community Digest 2026-11-06

> Generated: 2026-11-06 02:48 UTC | Tools covered: 7

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

# Cross-Tool Comparison Report — AI CLI Developer Tools Ecosystem
**Date:** 2026-11-06 · **Tools covered:** Claude Code, OpenAI Codex, Gemini CLI, GitHub Copilot CLI, OpenCode, Pi, Qwen Code

---

## 1. Ecosystem Overview

The AI CLI tooling landscape is consolidating around three shared engineering problems: **context economics** (cache TTLs, pruning, token-frugal navigation), **agent reliability signaling** (subagents reporting false success, hangs, mid-stream tool-call corruption), and **MCP operational maturity** (OAuth lifecycle, TLS trust, schema drift across session resumes). Simultaneously, the maturity gap between tools is widening: Gemini CLI and Copilot CLI are shipping deep platform-level fixes (path traversal patches, ACP policy enforcement, retry correctness), while Claude Code's day was dominated by a *trust* incident rather than a feature race — a signal that privacy and safety defaults are now competitive differentiators, not just compliance checkboxes. Windows and cross-platform correctness (path semantics, process trees, libc/native addon compatibility) is an over-represented pain point across nearly every community. Meanwhile, several projects (Codex, Qwen Code) had no community signal today, and two tools (OpenCode, Codex) show PR- or release-only momentum with zero issue activity — evidence that community health is no longer uniform across the ecosystem.

---

## 2. Activity Comparison

| Tool | Issues (updated) | PRs (updated) | Discussions | Release Status (24h) |
|---|---|---|---|---|
| **Claude Code** | ~10 hot + honorable mentions (1 dominant: #107757, 172 👍) | 1 | N/A (not provided) | No release |
| **OpenAI Codex** | ⚠️ N/A (summary failed) | ⚠️ N/A | ⚠️ N/A | ⚠️ Unknown |
| **Gemini CLI** | ~10 hot + 50 updated in window | 10+ (security + correctness heavy) | N/A (not provided) | No release |
| **GitHub Copilot CLI** | 22 updated (10 hot listed) | 0 | N/A (not provided) | **2 releases** (v1.0.106 + pre-release) |
| **OpenCode** | **0 updated** (reporting gap flagged) | 10+ | N/A (not provided) | **1 release** (process-daemon v0.5.0) |
| **Pi** | 10 hot listed, tracker "unusually active" | 10+ | 1 Ideas + 1 Q&A + 2 Show-and-tell | No release |
| **Qwen Code** | ⚠️ N/A (summary failed) | ⚠️ N/A | ⚠️ N/A | ⚠️ Unknown |

**Notes on data quality:**
- **OpenAI Codex** and **Qwen Code** summaries failed to generate — they are marked **N/A**, not "inactive." No inference should be drawn from their absence.
- **OpenCode** reported literally zero issue updates; the digest itself flags this as "a reporting gap rather than a quiet codebase" and uses PR descriptions as a proxy. Treat as **N/A for issue activity**.
- **Discussions** data was not provided by any source; where GitHub Discussions appear to be a primary channel (Pi, OpenCode), this should not be read as community silence.
- **Claude Code**, **Gemini CLI**, and **Pi** had no release in the window; **Copilot CLI** and **OpenCode** were the only shippers today.

---

## 3. Shared Feature Directions

Requirements appearing across **two or more** tool communities:

| Direction | Tools | Specific Need |
|---|---|---|
| **Context / cache economics** | Claude Code, Copilot CLI, Pi, Gemini CLI | Longer Anthropic prompt-cache TTL (Copilot #5220), cache warming to stop 5-min expiry (Pi #11857 — 26% of p2 spend), model-judged pruning of junk tool output (Copilot #5362), token-frugal context management & "Tactful Extraction" (Gemini #19561), auto-memory overreach undermining user control (Claude #107555) |
| **MCP reliability & lifecycle** | Claude Code, Copilot CLI, OpenCode, Pi | MCP servers killed after first turn in pre-warmed sessions (Claude #107179/#107060), tool schemas lost mid-session (Copilot #5367), stale OAuth "connected" state (Copilot #5366), OAuth hang on callback close (Pi #11855), per-server TLS trust for private CAs (OpenCode #60039/#60041) |
| **Headless / automation & terminal-host integration** | Copilot CLI, Claude Code, Pi, Gemini CLI | `remote-control --headless` (Claude #30447), OSC 7501 agent-status reporting for terminal hosts (Copilot #5320; Pi #11670/#11816), device-code sign-in without browser side effects (Copilot #5358), Workers-runtime process injection (Pi #10846) |
| **Sandboxing + unsafe-command safety** | Gemini CLI, Claude Code, Copilot CLI | Zero-dependency OS sandboxing to unlock native bash (Gemini #19873), stop destructive `git reset --force`/DB writes (Gemini #22672), `pkill -f 'cat'` SIGTERMing macOS apps (Claude #106029), Tab executing `/clear` data-loss (Claude #106074), opaque sandbox policy reporting (Copilot #5365/#5360) |
| **Session persistence & multiplexing** | Claude Code, Copilot CLI, Pi | Cross-surface shared session list (Claude #107611), trustworthy session resume (Copilot #5359/#5361), session detach instead of teardown on switch (Pi #11829/#11830), multi-device session sync (Pi #11696) |
| **Subagent/skill reliability & observability** | Gemini CLI, Claude Code | False-positive `GOAL` success after MAX_TURNS (Gemini #22323/#21983), under-triggered skills/subagents (Gemini #21968), `settings.json` overrides ignored by Browser Agent (Gemini #22267), permanent-memory overreach (Claude #107555) |
| **Windows / cross-platform correctness** | OpenCode, Pi, Copilot CLI, Gemini CLI | Git Bash `PWD` normalization (OpenCode #61619), orphaned process trees (Pi #11854), `.cmd` exec failures (Pi #10616), multi-minute Windows turn stalls (Copilot #5356), Termux/Bionic broken 6 months (Copilot #3333), Windows `dev: 0` stat fix (Gemini #30033) |
| **Privacy & data hygiene defaults** | Claude Code, (Copilot implicitly) | Remove email-in-`User-Agent` exposure entirely, not make it configurable (Claude #107757); open-sourcing the privacy mod (Claude #107768) |
| **AST-aware / semantic code navigation** | Gemini CLI, Claude Code (implicitly) | Three-issue EPIC on AST grep/mapping to cut misaligned reads and token noise (Gemini #22745/#22746/#22747) |

**Strongest cross-cutting theme:** *MCP is the new integration tax.* Four of the six active communities independently filed MCP failures — this is now the single largest source of "works, then stops working" reports.

---

## 4. Differentiation Analysis

**Feature focus**
- **Claude Code** — Agent autonomy depth (auto-memory, remote control, daemon mode) with the heaviest investment in *behavioral* correctness: memory overreach, destructive-action guards, desktop/session unification. Today's agenda was trust-centric (privacy leak, data-loss Tab bug).
- **Gemini CLI** — Deep platform engineering: security hardening (path traversal), correctness of streaming/retry semantics, and a strategic bet on AST-aware navigation + native-bash sandboxing. The most *architecturally forward-looking* backlog.
- **GitHub Copilot CLI** — Terminal-environment integration and editor handoff (`/code`, inline Kitty images, `/settings` dashboard) plus model-routing transparency (Fusion over ACP). Clearly targeting the **VS Code / terminal-native enterprise developer**.
- **OpenCode** — Infrastructure and self-hosting: site/worker consolidation, per-server MCP networking, session ergonomics (`--all`). Positioning as a **configurable, deployment-flexible** CLI rather than a hosted-first product.
- **Pi** — SDK/embedding and extensibility: Workers runtime support, custom identity preambles, session sync, extension sandboxing. The most **library-like** of the set — building a platform, not just a CLI.
- **Copilot CLI vs. Claude Code** — Copilot leans into editor/terminal-host embedding and enterprise policy visibility; Claude leans into agent autonomy and desktop ergonomics.

**Target users**
| Tool | Primary user |
|---|---|
| Claude Code | Power users running long, autonomous desktop/daemon sessions |
| Gemini CLI | Engineering teams building on a model + agent platform; editor integrations (`--acp`) |
| Copilot CLI | Enterprise VS Code / terminal developers; GitHub-centric workflows |
| OpenCode | Self-hosters, infra-minded teams, private-network MCP users |
| Pi | SDK integrators, extension authors, multi-device power users |
| Codex / Qwen Code | ⚠️ Insufficient data today |

**Technical approach**
- **Gemini CLI** is the most *defensive*: p1 security patches, retry/discard correctness for streamed tool calls, hook policy enforcement in ACP.
- **Copilot CLI** is the most *release-driven*: shipping directly without visible PR activity, iterating on ACP surface and auth ergonomics.
- **OpenCode** is the most *infrastructure-first*: PTY protocol bump, build-time Worker smoke tests, deploy gating.
- **Pi** is the most *SDK/extension-oriented*: prompt-section customization, runtime-agnostic process injection, durable session sync.
- **Claude Code** is the most *desktop-app-weighty* alongside the strongest privacy/trust incident of the day.

---

## 5. Community Momentum & Maturity

**Most active communities (today):**
1. **Gemini CLI** — Highest volume of substantive engineering (50 issues updated, 10+ PRs, multiple p1 security/correctness fixes). Momentum is high and *directed*.
2. **Pi** — "Unusually active" tracker with 10+ PRs, a full editorial backlog (Windows process trees, file encoding, cache warming, OAuth), and organic community tooling (Slack bot, CaseClip). Strong *ecosystem-building* signal.
3. **GitHub Copilot CLI** — 22 issue updates + 2 releases; high day-to-day friction surfaced (MCP state drift, session resume, Windows stalls) but also active shipping. Momentum high, **maturity mid** — reliability issues are the dominant theme.
4. **Claude Code** — Dominated by a single high-engagement trust incident (172 👍) and a wave of desktop-app triage. High *sentiment* engagement, lower feature velocity today (no release, 1 PR).
5. **OpenCode** — PR-only momentum today; infra consolidation and MCP networking. Community *feedback* signal is absent (0 issues) — maturity assessment is provisional.
6. **OpenAI Codex / Qwen Code** — ⚠️ **Insufficient data.** Do not rank.

**Rapidly iterating:** Gemini CLI and Copilot CLI (Gemini through security/correctness patches, Copilot through releases). **Maturing toward platform stability:** OpenCode and Pi (protocol bumps, smoke tests, SDK surface). **Trust-repair mode:** Claude Code (privacy controversy) and Copilot CLI (MCP/OAuth/session-resume reliability cluster).

**Maturity read:** Copilot CLI shows the highest *shipping cadence* but the lowest *reliability consistency* (fragile MCP/OAuth/session state). Gemini CLI shows the strongest *fix discipline* but a backlog of false-positive success-signal bugs that erode automation trust. Neither Codex nor Qwen Code can be assessed today due to data gaps.

---

## 6. Trend Signals

1. **MCP operational maturity is the top reliability frontier.** Across Claude Code, Copilot CLI, OpenCode, and Pi, the recurring failure pattern is *state drift*: connection state, tool registry, and auth tokens fall out of sync with no automatic reconciliation. Expect "MCP lifecycle management" (health checks, auto-restart, schema reconciliation) to become a first-class feature category. **Action for developers:** budget engineering time for MCP supervision; do not assume "connected" means "usable."

2. **Context economics is becoming a budget line item.** Pi quantified Anthropic cache expiry at **~$1,430 / 26% of p2 spend** in three weeks; Copilot is requesting longer cache TTLs and model-judged pruning; Gemini is designing AST tools to cut token noise. **Action:** treat prompt-cache configuration and context pruning as *cost-engineering*, not just UX.

3. **False-positive agent success is an emerging trust crisis.** Gemini's subagents report `GOAL`/`success` after hitting MAX_TURNS or failing on Wayland; Claude's auto-memory promotes one-off corrections to permanent rules. **Action:** build external verification (tests, diffs, exit codes) around agent claims — do not trust the agent's own status field.

4. **Automation and headless operation are demanded across the board.** `remote-control --headless`, Workers runtime support, OSC 7501 status reporting, device-code sign-in, and multi-device session sync all point the same direction: **AI CLIs are becoming background services**, not interactive toys. **Action:** design for TTY-free, daemonized, embeddable operation now.

5. **Windows and cross-platform correctness is a persistent tax.** Six of the seven tools surfaced Windows-specific defects (path semantics, process trees, `.cmd` exec, multi-minute stalls, glibc/Bionic incompatibility). **Action:** if your team is Windows-heavy, weight platform-fix velocity heavily in tool selection — it lags across the board.

6. **Privacy and safety defaults are now competitive.** The Claude Code `User-Agent`/email leak (172 👍, demand for removal *not* configurability) and the open-sourcing of the privacy mod show users treating data hygiene as non-negotiable. **Action:** audit what your CLI sends at the network boundary (headers, telemetry, query params) — this is now a procurement criterion.

7. **Sandboxing + native-bash is a deliberate design tension.** Gemini's zero-dependency sandboxing proposal aims to unlock `grep`/`sed`/`awk` chaining *without* losing security. **Action:** watch this space — the winning approach to "agentic bash" will likely define the next generation of tool execution.

8. **Editor/terminal-host embedding is the next integration layer.** ACP Fusion advertisement, `/code` handoff, OSC status protocols, and inline image rendering signal that CLIs are being absorbed into editors and terminal multiplexers rather than used standalone. **Action:** prioritize ACP/status-protocol support if you build editor or terminal tooling.

---

**Methodology note:** This report is synthesized from provided community digests only. OpenAI Codex and Qwen Code are marked **N/A** due to summary generation failure and are excluded from momentum rankings rather than treated as inactive. Issue/PR counts reflect the reporting window and are not normalized across repos.

---

## Per-Tool Reports

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills Highlights

> Source: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills — Community Highlights Report
**Data source:** github.com/anthropics/skills · Snapshot date: 2026-11-06

> **Method note:** PR comment counts are unavailable in this dataset (`Comments: undefined` for all 50 PRs). Rankings below are therefore inferred from *contemporaneity of updates, cross-linked issues, recurring contributors, and per-Skill PR clusters* — not raw comment volume. Issue rankings use actual comment/👍 counts.

---

## 1. Top Skills Ranking (by attention signal)

| # | Skill / PR | What it does | Discussion highlights | Status |
|---|---|---|---|---|
| 1 | **skill-creator** — [#1298](https://github.com/anthropics/skills/pull/1298), [#1997](https://github.com/anthropics/skills/pull/1997), [#1681](https://github.com/anthropics/skills/pull/1681) | Meta-skill for authoring, packaging, and evaluating Skills | Largest PR cluster; three independent fixes (trigger-eval isolation + Windows pipes, YAML frontmatter scalar parsing, direct `package_skill.py` execution). Ties into issue #202 ("should be updated to best practice") — authoring tooling is the ecosystem's weakest link | All OPEN |
| 2 | **mcp-builder** — [#1742](https://github.com/anthropics/skills/pull/1742) | Generates and evaluates MCP servers from specs | Breaking rename in `mcp>=2.0.0` (`streamablehttp_client` → `streamable_http_client`), plus custom headers now require `create_mcp_http_client`. Directly paired with issue #1390 (eval harness scores 0/N) — the skill's *runtime and* harness both need repair | OPEN (cf. #1668) |
| 3 | **webapp-testing** — [#1980](https://github.com/anthropics/skills/pull/1980), [#1976](https://github.com/anthropics/skills/pull/1976) | Browser-driven testing and element discovery | Security-first framing: removal of `shell=True` (CWE-78 command injection) in `with_server.py`, plus correctness fix for `textarea`/`select` mislabeled as `text` | Both OPEN |
| 4 | **docx** — [#1792](https://github.com/anthropics/skills/pull/1792), [#1734](https://github.com/anthropics/skills/pull/1734) | Word document manipulation (revisions, comments) | Silent-failure pattern dominates: LibreOffice timeout reported as success without verifying `w:ins`/`w:del` removal; request to detect orphaned comments. Mirrors issue #2018 (invalid XML on merge) — docx is the most bug-reported bundled skill | Both OPEN |
| 5 | **xlsx** — [#2019](https://github.com/anthropics/skills/pull/2019) | Spreadsheet recalculation and external-link risk analysis | Most recently updated substantive PR; fixes false negatives in `external_links_at_risk` for worksheet-scoped defined names and aliases. Fixes #2013 | OPEN |
| 6 | **pyxel** — [#525](https://github.com/anthropics/skills/pull/525) | Retro game development in Python | Longest-lived high-visibility skill proposal (opened 2026-03-05, still active 2026-11-01). Scope covers headless input-driven runs, frame inspection, and evidence-based verification — an unusually rigorous test story | OPEN |
| 7 | **New Skill submissions** — [#1245](https://github.com/anthropics/skills/pull/1245), [#1703](https://github.com/anthropics/skills/pull/1703), [#1771](https://github.com/anthropics/skills/pull/1771), [#1615](https://github.com/anthropics/skills/pull/1615) | Notion spec→implementation + resume auditor; Markdown→MP4 video; smart-contract audit on TON; SCNet HPC/Slurm ops | Net-new domain coverage: product ops, media generation, Web3, and scientific compute. None merged | All OPEN |
| 8 | **document-typography** — [#514](https://github.com/anthropics/skills/pull/514) | Typographic QC for generated docs (orphans, widows, numbering) | Notable framing: "affects every document Claude generates, users rarely ask for it" — a proactive-quality skill archetype. Low activity since March | OPEN |

---

## 2. Community Demand Trends (from Issues)

**① Security, trust boundaries, and supply chain — the single loudest thread**
- [#492](https://github.com/anthropics/skills/issues/492) *"Community skills distributed under `anthropic/` namespace enable trust boundary abuse"* — **46 comments**, by far the most-discussed issue in the repo. Namespace impersonation lets community skills inherit official trust.
- [#1394](https://github.com/anthropics/skills/issues/1394) XSS in skill-creator's `eval-viewer` (`innerHTML` + non-attribute-safe `escapeHtml`).
- [#1980](https://github.com/anthropics/skills/pull/1980) command-injection class in webapp-testing.

**② Skill evaluation and triggering is unreliable — the loudest *technical* complaint**
- [#556](https://github.com/anthropics/skills/issues/556) `run_eval.py` reports **0% trigger rate across all queries** (13 comments, 7 👍).
- [#1390](https://github.com/anthropics/skills/issues/1390) `mcp-builder` evaluation scores **0/N** against every real MCP server (TextContent not JSON-serializable, errors swallowed).
- [#1298](https://github.com/anthropics/skills/pull/1298) / [#1997](https://github.com/anthropics/skills/pull/1997) attempt fixes in `skill-creator` itself.
→ Demand: a trustworthy, self-hosted regression harness for Skills.

**③ Distribution, sharing, and deduplication — the highest-*upvoted* asks**
- [#228](https://github.com/anthropics/skills/issues/228) org-wide skill sharing (**8 👍**) — ends the download-`.skill`→Slack→manual-upload loop.
- [#189](https://github.com/anthropics/skills/issues/189) `document-skills` and `example-skills` install identical content, duplicating context (**9 👍**, the most-upvoted issue shown).
- [#2108](https://github.com/anthropics/skills/pull/2108) docs PR clarifying the document-skills snapshot / `~/.claude/skills/synced/` sync relationship.

**④ Context-window economy**
- [#1487](https://github.com/anthropics/skills/issues/1487) `claude-api` eagerly injects **~156k tokens** in one tool call — a Skill that blows the window defeats its own purpose.
- [#1329](https://github.com/anthropics/skills/issues/1329) `compact-memory` symbolic notation for agent state.
→ Demand: progressive disclosure / lazy loading as a Skill design norm.

**⑤ Skill authoring quality standards**
- [#202](https://github.com/anthropics/skills/issues/202) skill-creator reads like developer docs, not operational instructions; name itself violates guidelines.
- [#2064](https://github.com/anthropics/skills/issues/2064) descriptions state *what* a skill does but not *when to use it* — a direct trigger-rate issue.
- [#514](https://github.com/anthropics/skills/pull/514) typographic QC as a proactive layer.

**⑥ Workflow / governance & quality gates (aspirational)**
- [#1385](https://github.com/anthropics/skills/issues/1385) three-gate pipeline: pre-task calibration → adversarial review → delivery verification.
- [#412](https://github.com/anthropics/skills/issues/412) `agent-governance` — policy enforcement, threat detection, trust scoring, audit trails.

---

## 3. High-Potential Pending Skills (not yet merged, recent activity)

Ranked by likelihood of landing soon — recency of update, presence of a linked/fixed issue, and narrow diff scope:

1. **[#2019](https://github.com/anthropics/skills/pull/2019) xlsx — worksheet-scoped defined names** · updated 2026-11-03 — newest substantive PR, fixes #2013, scoped regex/alias change.
2. **[#1980](https://github.com/anthropics/skills/pull/1980) webapp-testing — drop `shell=True`** · 2026-11-01 — small, security-motivated, CWE-labeled; high merge probability.
3. **[#1792](https://github.com/anthropics/skills/pull/1792) docx — LibreOffice timeout as error + output verification** · 2026-11-01 — converts a silent-success bug into a hard failure; narrow and well-scoped.
4. **[#525](https://github.com/anthropics/skills/pull/525) pyxel — retro game dev** · 2026-11-01 — revived after ~8 months idle; a mature, full-featured submission.
5. **[#1975](https://github.com/anthropics/skills/pull/1975) / [#1976](https://github.com/anthropics/skills/pull/1976) / [#1977](https://github.com/anthropics/skills/pull/1977) — gerardrecinto's fix trio** · Oct 18–27 — `pptx` argparse guard, `webapp-testing` element discovery, `algorithmic-art` `wrapAround()`. Each closes a filed issue with a one-para diff; classic fast-track batch.
6. **[#1997](https://github.com/anthropics/skills/pull/1997) skill-creator — YAML frontmatter parsing** · 2026-10-19 — fixes quote-stripping/continuation-line corruption in `parse_skill_md`.
7. **[#1245](https://github.com/anthropics/skills/pull/1245) notion-spec-to-implementation + quantitative-resume-auditor** · 2026-10-15 — two novel skills in one PR; risk is bundling (reviewers often prefer split submissions).
8. **[#1730](https://github.com/anthropics/skills/pull/1730) claude-api — dead URL replacement** · 2026-10-04 — cosmetic but fully verified (HTTP 200 checks).
9. **[#1703](https://github.com/anthropics/skills/pull/1703) md2video-audio** · 2026-09-15 — Markdown→MP4 with voiceover via Marp; higher review cost (media pipeline deps).

**Watch item:** [#2108](https://github.com/anthropics/skills/pull/2108) (docs) · 2026-11-03 — not a Skill, but it formalizes the repo's snapshot/sync contract, which affects how every bundled Skill ships.

---

## 4. Skills Ecosystem Insight

> **The community's demand has shifted from *"add more Skills"* to *"make Skills trustworthy and measurable"* — the top issue is namespace/trust abuse, the top technical complaints are evaluation harnesses that silently score 0%, and the most-active PR cluster is `skill-creator` itself: contributors are fixing the machinery that authors and scores Skills more than they are adding new ones.**

---

# Claude Code Community Digest — 2026-11-06

## 1. Today's Highlights

The dominant story today is a **privacy controversy**: issue #107757 (172 👍, 32 comments) alleges Claude Code leaks users' email addresses in the `User-Agent` header during web searches, prompting a related community PR to open-source the built-in `privacy` mod. Otherwise, the day was dominated by issue triage — a large wave of desktop-app bugs (Remote Control resetting after updates, MCP servers dying in pre-warmed sessions, deep-link workspace handling) were closed or consolidated. No new releases shipped in the last 24h.

## 2. Releases

*No new releases in the last 24 hours.*

## 3. Hot Issues

1. **[#107757](example/feedback Issue #107757) — Privacy violation in User-Agent header** (OPEN, 32 comments, 172 👍)
   The highest-engagement issue of the day. Users report receiving spam because Claude Code exposes their email to third parties in the `User-Agent` header during web searches. The author argues this must be removed entirely, not made configurable. Strong community support signals a trust/security concern rather than a routine bug.

2. **[#87891](example/feedback Issue #87891) — Background daemon leaks workers/spares (~7.1 GB after six weeks)** (OPEN, has repro)
   A long-running resource-leak report: 64 leaked processes accumulate because stale workers are re-adopted on every restart. High impact for always-on/daemon users; marked reproduced.

3. **[#107555](example/feedback Issue #107555) — Model turns one-off corrections into permanent memory rules** (OPEN)
   A subtle but important agent-behavior issue: Opus 5.5 persists a single correction as a general memory/skill rule and later cites it as the user's decision. Relevant to anyone relying on auto-memory in long projects.

4. **[#93216](example/feedback Issue #93216) — v2.1.267 OAuth login broken (`redirect_uri` rejected)** (OPEN)
   `claude auth login` / `setup-token` fail before the authorize screen, blocking all new subscription logins. Critical onboarding blocker despite low comment count.

5. **[#30447](example/feedback Issue #30447) — Feature request: `claude remote-control --headless`** (OPEN, 44 👍)
   Long-standing (since March) request to combine remote control with headless mode for always-on server deployments without a TTY. Steady community backing.

6. **[#107611](example/feedback Issue #107611) — Desktop Code tab doesn't share session list with CLI/VS Code** (CLOSED)
   Desktop only shows sessions it created; CLI/VS Code sessions never appear. Closed quickly, but highlights ongoing fragmentation across surfaces.

7. **[#107767](example/feedback Issue #107767) / [#107504](example/feedback Issue #107504) — Remote Control silently turns off after auto-update** (CLOSED, Windows + macOS)
   Two platform-specific reports of the same regression: after an update/restart, every existing session returns with Remote Control off and no setting to persist it. A notable UX regression for remote-workflow users.

8. **[#107179](example/feedback Issue #107179) / [#107060](example/feedback Issue #107060) — `.mcp.json` MCP servers killed after first turn in pre-warmed sessions** (CLOSED, macOS)
   Project-scoped MCP servers shut down at end of first turn and never restart in spare/pre-warmed sessions. A meaningful reliability hit for MCP-heavy desktop workflows.

9. **[#106074](example/feedback Issue #106074) — Tab on slash-command menu executes the command (`/cl` + Tab runs `/clear`)** (CLOSED, data-loss)
   Autocomplete submitting instead of completing, with conversation-wiping consequences, is a serious data-loss footgun. Closed with repro.

10. **[#99301](example/feedback Issue #99301) — Git Bash shell snapshot writes raw Windows PATH on probe timeout** (CLOSED)
    Under CPU load, sessions start with `ls`/`grep`/`date` "command not found" for the whole session. A frustrating, hard-to-diagnose environment bug now closed.

*Honorable mentions:* #106029 (agent ran `pkill -f 'cat'` and SIGTERMed macOS apps — sandbox guard ineffective on macOS), #97884 (Auto-mode safety classifier returning no verdict blocks all write/exec), #103455/#106664 (naver.com blocked wholesale in browser tools).

## 4. Key PR Progress

Only one PR was updated in the last 24h:

1. **[#107768](example/feedback PR #107768) — mods: add the privacy mod** (CLOSED, by poteat)
   Publishes the source of Claude Code's built-in `privacy` mod under `mods/privacy` — no new behavior. Notable because it directly intersects with today's #107757 privacy controversy, giving the community visibility into the hook that "refuses a shell call holding the signed-in account's email address and naming a User-Agent."

*No other PRs were updated in the window, so a top-10 list is not applicable today.*

## 5. Hot Discussions

*No discussion data was provided; this section is omitted.*

## 6. Feature Request Trends

Distilled from today's issues:

- **Headless / daemonized operation** — `remote-control --headless` (#30447) reflects demand for always-on, TTY-free deployments on servers.
- **Cross-surface session unification** — Users want one shared session list across Desktop, CLI, and VS Code (#107611).
- **MCP lifecycle reliability** — Multiple reports converging on project `.mcp.json` servers not surviving pre-warmed/spare sessions (#107179, #107060).
- **Remote Control persistence** — A setting to keep Remote Control on across restarts/updates (#107767, #107504).
- **Browser/tooling parity** — Requests like a `file_upload` tool matching Claude in Chrome (#106725), plus `target="_blank"` handling (#103785) and domain-allowlist fixes (#103455, #106664).
- **UI customization** — Custom themes/accent colors (#107136, #104757 duplicate) and a desktop session-rail section plus link hover previews for mods (#107811).
- **Privacy & data handling** — User-Agent/email exposure (#107757) and open-sourcing of the privacy mod (#107768) are pushing toward stricter default data hygiene.

## 7. Developer Pain Points

- **Data privacy and trust** — The #107757 User-Agent/email exposure dominates sentiment; users want it removed, not made opt-in/out.
- **Desktop app instability after updates** — Remote Control resets, routines sidebar hidden (#102703, #102735), and inconsistent deep-link workspace behavior (#92210, #98285) erode confidence in the desktop Code tab.
- **Resource leaks and lifecycle bugs** — Daemon worker/spare leaks (#87891) and MCP servers dying after the first turn (#107179, #107060) create unpredictable long-running sessions.
- **Overly broad tool execution** — `pkill -f 'cat'` taking down apps (#106029) and Tab executing `/clear` (#106074) point to a need for safer, confirmable destructive actions.
- **Permissions/safety false positives** — Auto-mode classifier returning no verdict (#97884) and blanket `naver.com` blocking (#103455, #106664) interrupt legitimate work.
- **Authentication fragility** — Broken OAuth redirect on v2.1.267 (#93216) blocks new users entirely, with no workaround reported.
- **Memory overreach** — Auto-memory promoting one-off corrections to permanent rules (#107555) undermines user control over agent behavior.

---
*Note: Links use the `example/feedback` shorthand as provided in the source data; substitute the real repository path for live URLs.*

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

⚠️ Summary generation failed.

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI Community Digest — 2026-11-06

## 1. Today's Highlights

No new releases landed in the last 24 hours, but maintainer activity remained intense across the agent workstream. The dominant themes are **subagent reliability** (misreported termination states, hangs, and config overrides being ignored) and **security/path-handling hardening** in core, with several high-priority (p1) fixes queued or closed. Meanwhile, a cluster of EPICs around AST-aware code navigation and model native bash usage signals a strategic push toward more token-efficient, sandboxed agent behavior.

---

## 2. Releases

_No new releases in the last 24h._

---

## 3. Hot Issues

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — Subagent recovery after MAX_TURNS is reported as GOAL success** `p1` · 13 comments
   The `codebase_investigator` subagent reports `status: "success"` / `Termination Reason: "GOAL"` even when it hit the turn limit before doing any analysis. This is a **correctness-of-signal bug**: callers cannot distinguish a completed investigation from a silently truncated one. Highest-comment issue in the window and actively retested.

2. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — Zero-Dependency OS Sandboxing & Post-Execution Intent Routing** `p2` · 9 comments
   Proposes letting Gemini exploit its native bash affinity (chaining `grep`/`sed`/`awk`) without sacrificing security. A foundational design issue — if resolved, it changes how the agent explores and edits codebases.

3. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — Generalist agent hangs** `p1` · 8 comments · 👍 8
   The generalist agent hangs indefinitely on trivial tasks (e.g., folder creation), and disabling subagent delegation works around it. Highest community upvote in the set — a clear top-of-mind reliability problem.

4. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — EPIC: AST-aware file reads, search, and mapping** `p2` · 7 comments
   Investigates whether AST-aware tools can reduce misaligned reads and token noise. Paired with [#22746](https://github.com/google-gemini/gemini-cli/issues/22746) and [#22747](https://github.com/google-gemini/gemini-cli/issues/22747), this is a multi-issue strategic bet on better codebase navigation.

5. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini does not use skills and sub-agents enough** `p2` · 7 comments
   Anecdotal but widely shared: the model ignores custom skills/subagents unless explicitly told. Directly undermines the value of the subagent ecosystem maintainers are building.

6. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267) — Browser Agent ignores `settings.json` overrides (e.g., `maxTurns`)** `p2` · 5 comments
   `AgentRegistry` merges settings correctly, but the Browser Agent bypasses them — a concrete configuration-plumbing defect with a clear repro.

7. **[#22232](https://github.com/google-gemini/gemini-cli/issues/22232) — Browser agent resilience: automatic session takeover and lock recovery** `p3` · 4 comments
   Requests replacing the current fail-fast behavior on locked persistent browser profiles with automatic recovery. Important for long-running browser automation workflows.

8. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — Browser subagent fails on Wayland** `p1` · 4 comments
   Reports `Termination Reason: GOAL` despite failing on Wayland. Combined with #22323, there's a recurring pattern of **false-positive success signaling**.

9. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 400 error with >128 tools** `p2` · 3 comments
   The agent exceeds API tool limits and returns a hard 400. Needs smarter tool scoping — increasingly relevant as the tool/subagent surface grows.

10. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — Agent should stop/discourage destructive behavior** `p2` · 3 comments
    Flags unsafe `git reset --force` usage and risky DB modifications. Directly tied to trust and safety for production-adjacent repos.

---

## 4. Key PR Progress

1. **[#29876](https://github.com/google-gemini/gemini-cli/pull/29876) — fix(core): prevent path traversal in legacy checkpoint fallback** `p1 security`
   Patches a traversal vulnerability in `Logger._getCheckpointPath()`/`deleteCheckpoint()` when falling back to unencoded checkpoint filenames. Highest-severity fix in the batch.

2. **[#29875](https://github.com/google-gemini/gemini-cli/pull/29875) — feat(core,cli): add support for `gemini-4-argon`**
   Registers a new model in `VALID_GEMINI_MODELS`, `defaultModelConfigs`, aliases, and capability checks. Signals upcoming model support; notable despite being closed.

3. **[#29881](https://github.com/google-gemini/gemini-cli/pull/29881) — fix(core,cli): ACP retry progress, retry delay cap, and model fallback**
   Fixes ACP mode silently retrying quota/capacity 429s without progress signals, and stops multi-hour quota resets from being treated as transient. Big UX win for `--acp` editor integrations.

4. **[#29879](https://github.com/google-gemini/gemini-cli/pull/29879) — fix(core,cli,sdk): discard buffered tool calls on mid-stream error or retry**
   Prevents partially-streamed `ToolCallRequest`s from being executed after failure/retry — a correctness fix that avoids duplicate side effects.

5. **[#30020](https://github.com/google-gemini/gemini-cli/pull/30020) — fix(core): drop tool calls streamed by failed attempts on retry** (open)
   Same class of bug as #29879: both attempts' tool calls were being collected and executed. Explicitly calls out side-effecting commands being run twice.

6. **[#29871](https://github.com/google-gemini/gemini-cli/pull/29871) — fix(core): keep the unsent request when loop recovery starts before a turn**
   Prevents loop recovery from dropping a pending `functionResponse`, which previously closed tool calls as `"Response was lost or interrupted."`

7. **[#30033](https://github.com/google-gemini/gemini-cli/pull/30033) — fix(core): tolerate `dev: 0` from Windows path stat in secure file reads**
   Works around Node 22.12–23.x libuv behavior on Windows that made every secure file read fail an inode/device check.

8. **[#30022](https://github.com/google-gemini/gemini-cli/pull/30022) — fix(cli): run ACP tool calls through hooks**
   Applies `BeforeTool`/`AfterTool` hook policies to editor-driven ACP sessions, closing a policy-enforcement gap.

9. **[#30043](https://github.com/google-gemini/gemini-cli/pull/30043) — feat(core): expose tool call ids to hooks**
   Adds `tool_call_id` to hook inputs so integrations can pair before/after events — a small but high-leverage change for observability tooling.

10. **[#30017](https://github.com/google-gemini/gemini-cli/pull/30017) — fix(core): keep committed turns when cancelling a tool-result continuation**
    Stops cancellation from rolling history back before the user prompt, which erased already-executed tool turns that may have modified files.

_Also noteworthy:_ [#29878](https://github.com/google-gemini/gemini-cli/pull/29878) (accurate blocked-path errors), [#29882](https://github.com/google-gemini/gemini-cli/pull/29882) (file-search cache correctness), [#30026](https://github.com/google-gemini/gemini-cli/pull/30026) (project temp paths treated as local), [#30027](https://github.com/google-gemini/gemini-cli/pull/30027) (separate custom theme config).

---

## 5. Hot Discussions

_No discussion data was provided for this window._

---

## 6. Feature Request Trends

Distilled from the 50 updated Issues:

- **Subagent reliability & observability** — accurate termination reasons, visible subagent trajectories via `/chat share` ([#22598](https://github.com/google-gemini/gemini-cli/issues/22598)), subagent context in bug reports ([#21763](https://github.com/google-gemini/gemini-cli/issues/21763)), and better `codebase_investigator` behavior ([#18282](https://github.com/google-gemini/gemini-cli/issues/18282)).
- **AST-aware code navigation** — a coordinated three-issue EPIC ([#22745](https://github.com/google-gemini/gemini-cli/issues/22745), [#22746](https://github.com/google-gemini/gemini-cli/issues/22746), [#22747](https://github.com/google-gemini/gemini-cli/issues/22747)) exploring AST grep and mapping tools to cut turns and token noise.
- **Sandboxing + native bash** — [#19873](https://github.com/google-gemini/gemini-cli/issues/19873) proposes zero-dependency OS sandboxing to safely unlock the model's POSIX tool chaining.
- **Token-frugal context management** — "Tactful Extraction" ([#19561](https://github.com/google-gemini/gemini-cli/issues/19561)) and persistent file-based task tracking replacing in-context `WriteToDo` ([#18836](https://github.com/google-gemini/gemini-cli/issues/18836)).
- **Config & policy plumbing** — per-workspace policies ([#18397](https://github.com/google-gemini/gemini-cli/issues/18397)) and subagent discovery via `settings.json` ([#18285](https://github.com/google-gemini/gemini-cli/issues/18285)).
- **Self-awareness** — the agent should know its own CLI flags, hotkeys, and invocation patterns ([#21432](https://github.com/google-gemini/gemini-cli/issues/21432)).

---

## 7. Developer Pain Points

1. **Agent hangs and indefinite waits** — the generalist agent ([#21409](https://github.com/google-gemini/gemini-cli/issues/21409), 👍 8) is the most-upvoted complaint; browser agent failures on Wayland ([#21983](https://github.com/google-gemini/gemini-cli/issues/21983)) compound it.
2. **Misleading success signals** — subagents reporting `GOAL`/`success` after hitting `MAX_TURNS` ([#22323](https://github.com/google-gemini/gemini-cli/issues/22323)) or after failing outright (#21983) erode trust in automation.
3. **Configuration silently ignored** — `settings.json` overrides not reaching the Browser Agent ([#22267](https://github.com/google-gemini/gemini-cli/issues/22267)) and symlinked agents not being discovered ([#20079](https://github.com/google-gemini/gemini-cli/issues/20079)).
4. **Skills/subagents under-triggered** — the model rarely invokes custom skills autonomously ([#21968](https://github.com/google-gemini/gemini-cli/issues/21968)), making the extension ecosystem feel inert.
5. **Tool-count ceilings** — hard 400 errors once tool count exceeds limits ([#24246](https://github.com/google-gemini/gemini-cli/issues/24246)) as subagents and MCP servers multiply.
6. **Workspace pollution** — the model scatters temporary edit scripts across directories ([#23571](https://github.com/google-gemini/gemini-cli/issues/23571)), making clean commits painful.
7. **Unsafe/destructive commands** — `git reset --force` and risky DB operations ([#22672](https://github.com/google-gemini/gemini-cli/issues/22672)) remain a trust blocker for production repos.
8. **Interactive-prompt deadlocks** — scaffolding tools like `create vite` hang the agent on interactive prompts ([#22465](https://github.com/google-gemini/gemini-cli/issues/22465)).
9. **Mid-stream retry correctness** — duplicate tool execution and lost tool results (#29879, #30020, #29871) are a recurring class of session-integrity bugs.
10. **Cross-platform fragility** — Windows stat/inode mismatches ([#30033](https://github.com/google-gemini/gemini-cli/pull/30033)) and Wayland browser issues highlight uneven platform coverage.

---
_Generated from GitHub activity for google-gemini/gemini-cli on 2026-11-06. Comment counts and reactions reflect the reporting window._

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI — Community Digest
**Date:** 2026-11-06 · Source: [github/copilot-cli](https://github.com/github/copilot-cli)

---

## 1. Today's Highlights

A busy 24 hours: two releases landed (`v1.0.106` and the `v1.0.106-11` pre-release) bringing inline Kitty image rendering in Rex terminals, a new `/code` command to jump into VS Code, and a Fusion tier setting surfaced in `copilot config`. Meanwhile, the issue tracker saw 22 updates dominated by **MCP reliability** (lost tool schemas, OAuth token lifecycle) and **session persistence** failures — the two areas causing the most day-to-day friction for active users.

---

## 2. Releases

### [v1.0.106](https://github.com/github/copilot-cli/releases) — 2026-11-05
- **Inline image rendering** in Rex terminals via Kitty direct placements — a meaningful step up for multimodal workflows in the terminal.
- **`/code` command** opens the current session in VS Code, tightening the CLI ↔ editor handoff.
- **Fusion tier setting** added to `copilot config`.
- **Unified settings dashboard**: `/settings` now opens the single dashboard for all users, with global search across settings and local resources.

### [v1.0.106-11](https://github.com/github/copilot-cli/releases) (pre-release) — 2026-11-05
- **Prompt editing fixes**: images removed via `Ctrl+U` / `Ctrl+K` / `Ctrl+W`, or replaced by a recalled history entry, are no longer sent — and `Ctrl+S` can stash the prompt afterward.
- **Paste handling**: text containing NEL or Unicode line/paragraph separators now correctly fills separate prompt lines.

> Notably, two of the top-reported issues (#5354 ACP/Fusion advertisement, #5365 sandbox policy reporting) reference `1.0.106` pre-release builds, so the Fusion work is still settling.

---

## 3. Hot Issues

1. **[#5220 — 1-hour prompt cache TTL for Claude models](https://github.com/github/copilot-cli/issues/5220)** · 👍 9 · 2 comments
   Anthropic's default 5-minute cache TTL expires during normal interactive think-time, forcing full conversation re-reads. The highest-engagement issue in the window; a straightforward cost/latency win with clear user demand.

2. **[#5320 — OSC 7501 agent-status reporting for Herdr](https://github.com/github/copilot-cli/issues/5320)** · 👍 10 · 2 comments
   Most-reacted item of the day. Terminal hosts can't detect Copilot sessions or track activity without a status protocol — evidence that terminal-multiplexer integrations are becoming a first-class use case.

3. **[#5367 — Agent regularly loses MCP tool schemas](https://github.com/github/copilot-cli/issues/5367)**
   A "every single day" report: returning to a session silently loses access to certain MCP tools, recoverable only via `/compact`. High-severity, low-workaround — this is the kind of state bug that erodes trust in long sessions.

4. **[#5359 — Resumed session 400: "Tool reference not found in available tools"](https://github.com/github/copilot-cli/issues/5359)**
   When an MCP server's tool set changes between launches, every request in the resumed session hard-fails with a 400. Pairs with #5367 into a coherent "MCP + session resume" reliability cluster.

5. **[#5366 — Remote MCP OAuth (Entra): calls fail after token expiry while `mcp.list` still says connected](https://github.com/github/copilot-cli/issues/5366)**
   Stale-state bug in the SDK path (`@github/copilot-sdk` 1.0.24): only `restartServer` recovers. Auth-state reporting that lies is worse than a clean failure.

6. **[#5356 — Windows/npm intermittent multi-minute stalls mid-turn](https://github.com/github/copilot-cli/issues/5356)**
   Turns hang for many minutes with no final reply; cancel-and-continue is the only escape. Reproduced across unrelated sessions — a serious interactivity regression on Windows.

7. **[#5354 — ACP does not advertise Copilot Fusion or its tier configuration](https://github.com/github/copilot-cli/issues/5354)** · 👍 2
   Fusion works interactively but ACP reports the model as "Auto" with no tier selector, blocking clients like T3 Code. Directly tied to today's release work.

8. **[#5361 — After 5-hour limit reset: 400 "model is not available for integrator"](https://github.com/github/copilot-cli/issues/5361)**
   `1.0.105` fails to resume after the session limit resets, with a Copilot-Integration-Id header complaint. A hard blocker for anyone hitting rate limits — a common long-day scenario.

9. **[#3333 — Android/Termux broken since v1.0.48 (glibc-compiled `runtime.node`)](https://github.com/github/copilot-cli/issues/3333)** · 👍 5 · 6 comments
   Longest-running thread in the set: the native Rust addon is compiled against glibc and can't load on Bionic libc. Sustained community interest over six months with no resolution.

10. **[#5362 — Model-judged context pruning (swap junk tool output for a one-line summary)](https://github.com/github/copilot-cli/issues/5362)**
    A well-specified proposal: let the model collapse an unintended tool result in-line instead of carrying it for the session. Fits the broader push for context/cost control.

*Also worth tracking:* [#5350](https://github.com/github/copilot-cli/issues/5350) (MCP OAuth code received but code→token exchange never runs), [#5352](https://github.com/github/copilot-cli/issues/5352) (subagents misread "don't write to /tmp" as "don't read"), [#5358](https://github.com/github/copilot-cli/issues/5358) (side-effect-free device sign-in for automation).

---

## 4. Key PR Progress

No pull requests were updated in the last 24 hours (0 items). All release content above shipped directly without visible community PR activity in this window.

---

## 5. Hot Discussions

No GitHub Discussions data was provided for this window — section omitted.

---

## 6. Feature Request Trends

- **Context economics** — Requests cluster around making the context window cheaper and cleaner: longer Claude prompt cache TTL ([#5220](https://github.com/github/copilot-cli/issues/5220)), model-judged pruning of junk tool output ([#5362](https://github.com/github/copilot-cli/issues/5362)), and implicit reliance on `/compact` as the only recovery path ([#5367](https://github.com/github/copilot-cli/issues/5367)).
- **Terminal-host integration surface** — Status protocols (OSC 7501), foreground-session change hooks ([#5279](https://github.com/github/copilot-cli/issues/5279)), and inline image rendering all point to Copilot CLI being embedded in richer terminal environments rather than used standalone.
- **Model routing transparency** — Fusion and tier configuration being invisible over ACP ([#5354](https://github.com/github/copilot-cli/issues/5354)) and model/mode mismatches in `session/new` ([#5363](https://github.com/github/copilot-cli/issues/5363)) suggest users want the routing layer to be inspectable and settable programmatically.
- **Headless / automation ergonomics** — Non-interactive requests for device-code sign-in without browser/clipboard side effects, plus token-free auth status and reload ([#5358](https://github.com/github/copilot-cli/issues/5358)).
- **Rendering parity** — Markdown in *submitted user messages* not rendering in the timeline ([#5353](https://github.com/github/copilot-cli/issues/5353)) is a small but repeatedly-noticed asymmetry with agent replies.

---

## 7. Developer Pain Points

1. **MCP state drift is the dominant reliability problem.** Tool schemas disappear mid-session, resumed sessions 400 on stale tool references, and `mcp.list` reports "connected" for expired tokens. The common thread is that MCP connection state and the model's tool registry fall out of sync with no automatic reconciliation.
2. **OAuth token lifecycle is fragile.** Both the Entra/remote-MCP path ([#5366](https://github.com/github/copilot-cli/issues/5366)) and the ACP interactive path ([#5350](https://github.com/github/copilot-cli/issues/5350), [#5229](https://github.com/github/copilot-cli/issues/5229)) show refresh-token grants not being attempted, or the code→token exchange not running at all. Only a server restart recovers.
3. **Session resume is not trustworthy.** Whether it's changed MCP tool sets, `--add-dir` breaking remote auto-publish ([#5355](https://github.com/github/copilot-cli/issues/5355)), or a 5-hour limit reset producing a 400 ([#5361](https://github.com/github/copilot-cli/issues/5361)), resuming an existing session is where users most often get stuck.
4. **Platform edges remain unaddressed.** Termux/Android has been broken for six months ([#3333](https://github.com/github/copilot-cli/issues/3333)); Windows shows both multi-minute turn stalls ([#5356](https://github.com/github/copilot-cli/issues/5356)) and sandbox probe failures (`CreateProcessW` error 2 under `failIfUnavailable: true`, [#5368](https://github.com/github/copilot-cli/issues/5368)).
5. **Sandbox policy is opaque.** With duplicate read-write rules appearing unexplained in `sandbox.decision` ([#5365](https://github.com/github/copilot-cli/issues/5365)) and enterprise-managed policies failing during option updates ([#5360](https://github.com/github/copilot-cli/issues/5360)), admins lack the observability needed to debug policy in managed environments.

---

*Generated 2026-11-06 from release, issue, and PR metadata for github/copilot-cli.*

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode Community Digest — 2026-11-06

*Source: [anomalyco/opencode](https://github.com/anomalyco/opencode)*

---

## 1. Today's Highlights

Release activity was light today, with the only publish being **process-daemon v0.5.0** (`@opencode/pty@0.5.0`, protocol 8). Engineering throughput was concentrated on the **website/infra split** — moving V2 docs into `services/site` under SolidStart and hardening the deploy pipeline with build-time Worker smoke tests and security headers — plus a **cluster of three MCP networking/tooling PRs** that together make remote MCP servers usable behind private CAs and fix a LaunchDarkly handshake failure. Notably, **zero issues were updated in the last 24h**, so all visible momentum is PR-driven; the most eye-catching item is a community fork PR (#62235) proposing a full rebrand to "harpcode" with local-model defaults, which was closed on compliance grounds.

---

## 2. Releases

**process-daemon v0.5.0** — published to npm as [`@opencode/pty@0.5.0`](https://github.com/anomalyco/opencode), bundling `opencode-pty` 0.5.0.
- Shipped from `packages/process-daemon`.
- Advertises **PTY protocol version 8**, a bump from the previous protocol revision.
- **Analyst note:** a protocol integer bump in the PTY layer usually signals a wire-format or handshake change between the daemon and its consumers. If you pin `@opencode/pty` or ship a custom daemon client, verify version negotiation before upgrading — the release note itself does not list behavioral changes.

No other releases in the last 24 hours.

---

## 3. Hot Issues

**No issues were updated in the last 24 hours (total: 0 items).**

There is no issue-side signal to report for this window — no new reports, no triage movement, no comment activity. Practically, this means the day's observable quality feedback is only visible through PR-linked fix descriptions (several PRs cite `Closes #NNNNN` against issues that received no update timestamp, e.g. #61547 → #61359, #62226 → #62225, #62227 → #61391, #61619 → #61603). Treat the absence as a reporting gap rather than a quiet codebase; the fix PRs below are the best proxy for what users are hitting.

---

## 4. Key PR Progress

1. **[#62229](https://github.com/anomalyco/opencode/pull/62229) [OPEN] feat(site): serve the V2 docs from `services/site` with SolidStart** — *adamdotdevin*
   Continues #62209 by relocating V2 documentation out of `services/www` into the new `services/site` worker. This is the structural keystone of the docs/site consolidation; expect follow-on churn in docs build config and routing.

2. **[#62222](https://github.com/anomalyco/opencode/pull/62222) [OPEN] fix(site): security headers, Workers Logs and launch fixes** — *adamdotdevin*
   Pre-launch audit fallout for `opencode.ai`: HSTS and standard browser protections on every page, plus Workers Logs wiring. Author explicitly claims pixel/timing parity, so this is safe to review as pure hardening.

3. **[#62230](https://github.com/anomalyco/opencode/pull/62230) [OPEN] chore(site): fail the build when the Worker can't start** — *adamdotdevin*
   The site build now boots the freshly built Worker and loads a few pages, failing the build instead of reaching `cf deploy`. Directly motivated by a 5 Nov incident where a `HttpHeader` import change broke a release page — good example of converting a prod regression into a CI gate.

4. **[#60039](https://github.com/anomalyco/opencode/pull/60039) [OPEN] feat(mcp): support per-server TLS trust for remote MCP servers** — *karup*
   Adds an optional `tls` block to remote MCP server config so self-signed or private-CA certs can be trusted per server rather than globally. Long-running (opened 28 Oct), part of umbrella #40111, and references a cluster of prior issues (#23506, #14696, #26862, #1694) — clearly a sustained pain point.

5. **[#60041](https://github.com/anomalyco/opencode/pull/60041) [OPEN] feat(mcp): trust private-network server certificates on first use** — *karup*
   Stacked on #60039; adds the first-use TOFU flow for private-network MCP certs. Reviewers should treat #60039/#60041 as a pair — the second diff is small only once the first lands.

6. **[#62226](https://github.com/anomalyco/opencode/pull/62226) [OPEN] fix(mcp): skip codemode query for LaunchDarkly** — *aNewMoose*
   Adds `mcp.launchdarkly.com` to the `rejectsUnknownQuery` host list so OpenCode stops appending `?codemode=false`, which LaunchDarkly rejects with HTTP 400. Small, surgical fix; the allowlist pattern suggests more hosts will need adding over time.

7. **[#60895](https://github.com/anomalyco/opencode/pull/60895) [OPEN] feat(cli): add `--all` to `session list`** — *ColonelToad*
   Currently `opencode session list` always filters to the current directory's project. `--all` exposes cross-project sessions. Low-risk ergonomics win for multi-repo users; closes #59004.

8. **[#62227](https://github.com/anomalyco/opencode/pull/62227) [CLOSED] fix(core): do not spend the image budget on payload-free images** — *jimmyallinai*
   `boundImages` was evicting remote images that carry no inline payload, wasting budget without reclaiming anything. Fix makes the eviction decision payload-aware. Contributor PR, already closed — likely merged or superseded.

9. **[#61547](https://github.com/anomalyco/opencode/pull/61547) [OPEN] fix(tui,app): only apply extended 5min retry to `ServiceUnavailableError` location sync** — *cxxde*
   Cold UNC workspace plugins caused slow startup and left a 5-minute retry blanket applied too broadly. Narrows the retry to the specific error class. Affects Windows/network-share users disproportionately.

10. **[#61619](https://github.com/anomalyco/opencode/pull/61619) [OPEN] fix(cli): normalize Windows PWD in `run` command** — *zpzjzj*
    Git Bash supplies `/c/...` for `PWD`; native `process.chdir()` needs `C:\...`. Routes through the existing `FSUtil.windowsPath()` before the noninteractive run path chdirs. Closes #61603.

**Also worth a glance:** [#62235](https://github.com/anomalyco/opencode/pull/62235) [CLOSED, `needs:compliance`] — a full rebrand of the CLI/app/config paths to "harpcode" with a built-in local `llama.cpp` provider layer and default MCP servers; closed rather than reviewed, reinforcing that config-path and branding changes to a fork are out of scope upstream. [#62067](https://github.com/anomalyco/opencode/pull/62067) [CLOSED] restored configured agent colors in Mini TUI; [#61607](https://github.com/anomalyco/opencode/pull/61607) [OPEN] fixes the What's New popup for markdown-style Highlights changelogs; [#57088](https://github.com/anomalyco/opencode/pull/57088) [OPEN] restores edge-snapped window bounds that `resolveWindowState` drops when Windows reports invisible resize borders.

---

## 5. Hot Discussions

*No discussion data was provided for this window, so this section is omitted.*

---

## 6. Feature Request Trends

**Caveat:** with zero issue and zero discussion activity in the window, these directions are inferred from PR intent and the issues those PRs cite — not from request volume.

- **Enterprise/private networking for MCP.** Two stacked PRs (#60039, #60041) plus an umbrella issue (#40111) and four historical cross-references point squarely at self-signed certs and private CAs. Remote MCP adoption is being gated by TLS trust ergonomics, not by tooling capability.
- **Per-server MCP configurability in general.** The TLS block and the host-specific `rejectsUnknownQuery` allowlist (#62226) both follow the same shape: move behavior from global heuristics to per-server config.
- **Multi-project session ergonomics.** `session list --all` (#60895) is a request to stop assuming cwd == the only project a developer works in.
- **Local-model / offline-first defaults.** The harpcode fork PR (#62235) proposed a built-in lowest-priority `llama.cpp` provider layer. Even though it was closed, it is the clearest recent artifact of demand for a zero-config local inference path.
- **Docs/site consolidation.** #62209 → #62229 signals an internal direction (SolidStart + a single `services/site` worker) that will shape contributor workflow more than any user-facing feature this cycle.

---

## 7. Developer Pain Points

- **Windows path semantics remain a recurring tax.** Git Bash `PWD` normalization (#61619), cold UNC workspace startup (#61547), and window-bounds math against invisible resize borders (#57088) are three separate Windows-specific defects in the active set. Windows is over-represented relative to its share of the PR queue.
- **MCP servers are hostile to OpenCode's default request shaping.** LaunchDarkly returning HTTP 400 on `?codemode=false` (#62226) is a symptom of appending query params speculatively. The allowlist approach fixes one host, not the class of problem.
- **Resource budgeting is being spent on nothing.** #62227 — evicting remote images that carry no inline payload burns the image budget without ever freeing memory. Budget accounting logic likely needs a broader audit beyond this one call site.
- **Deploy pipelines lacked a real smoke test.** The 5 Nov release-page breakage that motivated #62230 shows site changes could reach `cf deploy` without ever booting the Worker. Build-time verification is now being added reactively.
- **TUI/desktop rendering parity drift.** Mini TUI dropped configured agent colors (#62067) while the full TUI honored them; the desktop What's New popup failed on newer markdown changelogs (#61607). Both are "two code paths, one feature" failures.
- **Long-tail review latency on infrastructure PRs.** #60039 and #60041 have sat open since 28 Oct while newer, smaller PRs merged — MCP networking changes are accumulating as a stack, which means the eventual review is larger and riskier than either diff alone.
- **Contributor signal on `needs:compliance`.** #62235 shows a willingness to fork + rebrand rather than request a feature. Worth watching whether local-model defaults resurface as a legitimate config-layer proposal.

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi Community Digest — 2026-11-06

## 1. Today's Highlights

No new releases landed in the last 24 hours, but the issue tracker and PR queue were unusually active. The long-running macOS CPU bug (#7730) continues to dominate engagement with 29 comments, while a bumper crop of fixes landed around Windows process handling, file encoding, tool-search schema refs, and MCP OAuth hangs. Discussion activity centered on session multiplexing and community-built tooling (Slack bot, CaseClip).

## 2. Releases

No new releases in the last 24 hours.

## 3. Hot Issues

1. **[#7730] High CPU usage on macOS with long sessions** — 29 comments, 12 👍. The most-engaged open issue; users report CPU swinging between 50–110% and memory at 600–800MB, apparently correlated with context/session length. Still unresolved months after filing. [Link](https://github.com/earendil-works/pi/issues/7730)
2. **[#7885] npm search not indexing new pi-packages** — 19 comments. Closed, but notable: newly published `pi-package` keywords weren't discoverable, blocking the pi.dev/packages gallery (which mirrors npm search). Publication/discovery pipeline issue. [Link](https://github.com/earendil-works/pi/issues/7885)
3. **[#10741] Groq Qwen3.8 27B fails with HTTP 400 (`developer` role)** — Provider compatibility: Pi sends a `developer` role Groq's template rejects. Points to a broader need for provider-specific role mapping. [Link](https://github.com/earendil-works/pi/issues/10741)
4. **[#10287] `getContextUsage()` overestimates massively after retryable network error** — Jumped from ~42k to 330k tokens after a WebSocket failure. Corrupts context accounting and could trigger premature compaction. [Link](https://github.com/earendil-works/pi/issues/10287)
5. **[#10846] pi-env: inject daemon process so client runs in Workers** — Requests a `startProcess` callback to decouple `Connection` from `node:` imports, enabling Cloudflare Workers deployment. Important for portable SDK adoption. [Link](https://github.com/earendil-works/pi/issues/10846)
6. **[#10616] `pi.exec` fails for `.cmd` commands on Windows** — `npm`, `npx`, `pnpm` all fail via extension `exec` but work through the bash tool. In-progress; part of a wider Windows-support cluster. [Link](https://github.com/earendil-works/pi/issues/10616)
7. **[#10015] bash truncation notice reports 0B for oversized last line ending in newline** — Trailing newline resets the live byte counter, producing `line is 0B`. A fix PR (#11852) already merged. [Link](https://github.com/earendil-works/pi/issues/10015)
8. **[#11317] Allow SDK users to customize identity preamble** — Request to replace only `sections.preamble` while keeping default tools/rules/docs. Reflects growing SDK/embedded-agent use cases. [Link](https://github.com/earendil-works/pi/issues/11317)
9. **[#11820] `Submissions.wait` leaks unhandled rejection in pi-durable 1.5.0** — Race in waiter registration inside `readOnLine`; failure before adoption leaks a promise rejection. Stability concern for durable sessions. [Link](https://github.com/earendil-works/pi/issues/11820)
10. **[#11829] Let a session detach instead of dying on conversation switch** — Currently `/resume`, `/tree`, and pickers tear down the running turn. This request directly motivated the parallel discussion (#11830) on juggling live conversations. [Link](https://github.com/earendil-works/pi/issues/11829)

## 4. Key PR Progress

1. **[#11854] Kill whole process trees of bash commands on Windows** — `taskkill /T` misses orphaned descendants whose parent exited; proposes a more robust tree kill. Directly addresses runaway background processes. [Link](https://github.com/earendil-works/pi/pull/11854)
2. **[#11751] `edit` preserves line endings on untouched lines, diffs only around changes** — Fixes CRLF churn (#8544) and avoids OOM on large-file diffs by scoping diffing to changed regions. [Link](https://github.com/earendil-works/pi/pull/11751)
3. **[#11811] `read`/`edit` handle UTF-16 and non-UTF-8 files without corruption** — `edit` previously silently corrupted non-UTF-8 files; introduces a shared `file-encoding.ts` helper. [Link](https://github.com/earendil-works/pi/pull/11811)
4. **[#11850] Follow local schema `$ref`s in `tool_search` and codemode summaries** — Fixes #11803; Pydantic/FastMCP servers using `$defs` now have discoverable tools. Merged. [Link](https://github.com/earendil-works/pi/pull/11850)
5. **[#11857] Prompt cache warming in p2** — Anthropic's 5-minute cache was never refreshed; data shows 789 rewrites cost ~$1,430 (26% of p2 spend) over Oct 12–Nov 5. High-dollar-impact change. [Link](https://github.com/earendil-works/pi/pull/11857)
6. **[#11855] Close remaining MCP OAuth connections on callback server close** — Fixes the "stuck on Connecting…" hang after successful login by not awaiting `http.Server.close()` on lingering keep-alive sockets. [Link](https://github.com/earendil-works/pi/pull/11855)
7. **[#11853] Remove all extension overlays on `/reload` and session switch** — `hideOverlay()` only popped the topmost entry, leaving stacked overlays orphaned. [Link](https://github.com/earendil-works/pi/pull/11853)
8. **[#11851 / #11848] `ctx.ui.custom()` overlays can report blocked program status** — Extends the `blocked` option from inline components to overlays so agent dashboards (e.g. Herdr) see correct state. Closes #11816. [Link](https://github.com/earendil-works/pi/pull/11851)
9. **[#11840] Add jitter to agent retries** — Spreads concurrent retries, including at the delay ceiling, to avoid thundering-herd behavior in multi-subagent workflows. [Link](https://github.com/earendil-works/pi/pull/11840)
10. **[#11696] Experimental session sync (`pi-durable/sync`)** — Devices upload/pull session commits via an outbox + `applyBatch`; concurrent edits fork to a new session. Early but significant for multi-device workflows. [Link](https://github.com/earendil-works/pi/pull/11696)

## 5. Hot Discussions

**Ideas**
- **[#11830] How do you juggle two live conversations?** — Friction when a long turn is running but you need to consult another conversation; every switch kills the session or forces a new terminal tab. [Link](https://github.com/earendil-works/pi/discussions/11830)

**Q&A**
- **[#11404] Official Pi code agent sandbox by Docker?** — Four `sbx/pi*` Docker Hub images surfaced; community unsure whether these are official. 2 👍. [Link](https://github.com/earendil-works/pi/discussions/11404)

**Show and tell**
- **[#11860] CaseClip — save a Pi mistake as a reviewable case card** — `/case save` captures the latest request, tool result, and correction for later review. Addresses the "agent said it worked but it 404'd" failure mode. [Link](https://github.com/earendil-works/pi/discussions/11860)
- **[#11844] pi-mom-slack — community Slack bot for pi 1.x** — One pi session per Slack thread with persistent working directory via `SessionManager.continueRecent`; MIT licensed, `npx pi-mom-slack`. [Link](https://github.com/earendil-works/pi/discussions/11844)

## 6. Feature Request Trends

- **Session lifecycle flexibility**: Detach vs. teardown on conversation switch (#11829, #11830), session replication/sync across devices (#11696), and durable waits that survive restarts (#11846).
- **SDK/embedding customization**: Granular prompt sections (#11317) and runtime-agnostic process injection for Workers (#10846) signal demand for embedding Pi outside the CLI.
- **Agent-state observability**: Multiple issues (#11670, #11816/#11851) push toward standardizing state reporting (OSC 7501) so external dashboards can consume it.
- **Extension isolation/robustness**: Requests to skip failing extensions instead of exiting (#11822) and to provide a sandboxed runtime.
- **Cross-platform correctness**: Windows (process trees, `.cmd` exec, self-update) and Termux/Android (blocking `spawnSync`) parity requests keep appearing.

## 7. Developer Pain Points

- **Windows support remains rough**: `.cmd`-based tooling via `pi.exec` (#10616), orphaned process trees (#11854), `/bin/sh` spawn crashes on self-update (#11814).
- **Platform-sensitive helpers blocking in-process hosts**: `spawnSync` in `findExecutableOnPath`/`commandExists` freezes in-process hosts like Termux/Android (#11809).
- **File fidelity**: Silent corruption of non-UTF-8/UTF-16 files and line-ending churn from `edit` (#11751, #11811) erode trust in tool output.
- **TUI input fragility**: Split kitty CSI-u sequences leaking into the composer (#11802), stale `@`-completion prefixes (#11823, #11825), and lost prompts when copying output (#11379).
- **State/accounting bugs**: Context-usage overestimation after network errors (#10287), dropped full-output locators during compaction (#11845), and unhandled rejections in durable waits (#11820).
- **Cost visibility**: Prompt-cache expiry silently multiplying Anthropic spend (#11857) highlights a class of invisible-cost bugs developers want surfaced.

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

⚠️ Summary generation failed.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/SnowEagle-Kn/agents-radar).*