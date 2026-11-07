# AI CLI Tools Community Digest 2026-11-07

> Generated: 2026-11-07 02:20 UTC | Tools covered: 7

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

⚠️ Comparative analysis generation failed.

---

## Per-Tool Reports

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills Highlights

> Source: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills Community Highlights Report
**Source:** github.com/anthropics/skills · Data as of 2026-11-07

> **Data note:** All 50 PRs report `Comments: undefined` and `👍: 0` in the supplied extract, so absolute engagement counts cannot be verified. Rankings below follow the repository's comment-sorted order (the top 20 of 50), cross-checked against update recency and linked issues. Every PR in the dataset is **OPEN** — no merged submissions appear in this window.

---

## 1. Top Skills Ranking

| # | Skill / PR | Function | Discussion Highlights | Status |
|---|---|---|---|---|
| 1 | **mcp-builder** — [#1742](https://github.com/anthropics/skills/pull/1742) | Authoring and testing MCP servers | Compatibility break in `mcp>=2.0.0`: `streamablehttp_client` → `streamable_http_client`, custom headers now via `create_mcp_http_client`/`http_client` instead of a kwarg. Breaks `skills/mcp-builder/scripts/connections.py`; fixes #1668 | OPEN (upd. 2026-10-08) |
| 2 | **skill-creator** — [#1298](https://github.com/anthropics/skills/pull/1298) | Authoring/optimizing Skills; trigger evals | Trigger evals report false misses: competing per-worker command probes, `select()` on subprocess pipes fails on Windows, unrelated tools abort the scan, runtime failures silently counted as non-triggers (false-passing negative examples) | OPEN (upd. 2026-09-16) |
| 3 | **notion-spec-to-implementation** + **quantitative-resume-auditor** — [#1245](https://github.com/anthropics/skills/pull/1245) | Spec → Notion task breakdown; resume auditing | Two-for-one submission bundling an enterprise workflow Skill with a document-audit Skill; long review window suggests scope-splitting questions | OPEN (upd. 2026-10-15) |
| 4 | **webapp-testing** — [#1980](https://github.com/anthropics/skills/pull/1980) | Browser-driven web app testing | `with_server.py` spawned servers with `subprocess.Popen(..., shell=True)` to support `cd && cmd` chains → CWE-78 command injection from agent-supplied strings | OPEN (upd. 2026-11-01) |
| 5 | **docx** — [#1792](https://github.com/anthropics/skills/pull/1792) | Word document editing / tracked changes | `accept_changes.py` reported success on LibreOffice timeout; now returns an Error and verifies output has no `w:ins`/`w:del`/`w:moveFrom`/`w:moveTo` remnants | OPEN (upd. 2026-11-01) |
| 6 | **proofcore-contract-auditor** — [#1771](https://github.com/anthropics/skills/pull/1771) | Solidity/Rust smart-contract static analysis | Anchors audit proofs on TON via a vendor Merkle protocol — a third-party-dependency Skill, likely to draw trust/neutrality scrutiny | OPEN (upd. 2026-09-16) |
| 7 | **pyxel** — [#525](https://github.com/anthropics/skills/pull/525) | Retro game dev in Python | Longest-running active PR (created 2026-03-05, updated 2026-11-01): headless input-driven runs, frame inspection, task-specific state checks, split reference docs | OPEN |
| 8 | **md2video-audio** — [#1703](https://github.com/anthropics/skills/pull/1703) | Markdown → narrated MP4 | "Zero-cost" Marp-slides + TTS pipeline; competes with existing pptx/artifact tooling | OPEN (upd. 2026-09-15) |

**Also notable:** **document-typography** [#514](https://github.com/anthropics/skills/pull/514) (orphan/widow control, numbering alignment — a broadly applicable quality Skill), **scnet-hpc** [#1615](https://github.com/anthropics/skills/pull/1615) (Slurm/SSH HPC operations), and **xlsx** [#2019](https://github.com/anthropics/skills/pull/2019) (worksheet-scoped defined names in `external_links_at_risk`, fixes #2013).

---

## 2. Community Demand Trends

**a) Trust, security, and namespace governance — the loudest thread.**
[#492](https://github.com/anthropics/skills/issues/492) (46 comments, the top issue by far) argues community Skills shipping under the `anthropic/` namespace enable impersonation and privilege escalation. Reinforced by concrete vulnerability reports: [#1394](https://github.com/anthropics/skills/issues/1394) (attribute-unsafe `escapeHtml` → display-path XSS in eval-viewer) and [#2018](https://github.com/anthropics/skills/issues/2018) (`merge_runs.py` writes XML with unbound namespace prefixes). Demand: **signed/provenanced Skill distribution and hardened default scripts.**

**b) Reliable evaluation harnesses.**
[#556](https://github.com/anthropics/skills/issues/556) (13 comments, 7 👍) reports `run_eval.py` achieving a **0% trigger rate across all queries**; [#1390](https://github.com/anthropics/skills/issues/1390) reports `evaluation.py` scoring **0/N against every real MCP server** while silently fabricating tool errors. Demand: **evals that actually measure skill triggering**, not just harness plumbing.

**c) Context economy / token efficiency.**
[#1487](https://github.com/anthropics/skills/issues/1487): `claude-api` eagerly injects ~156k tokens in a single tool call. [#202](https://github.com/anthropics/skills/issues/202) (CLOSED) criticized skill-creator's verbose, documentation-style prose. [#1329](https://github.com/anthropics/skills/issues/1329) proposes `compact-memory` (symbolic notation for agent state). Demand: **lazy-loading references and compact Skill bodies.**

**d) Distribution and team workflows.**
[#228](https://github.com/anthropics/skills/issues/228) (17 comments, 8 👍): org-wide Skill sharing instead of manual `.skill` file transfer via Slack. [#189](https://github.com/anthropics/skills/issues/189) (6 comments, 9 👍 — highest 👍 ratio): `document-skills` and `example-skills` plugins install identical content, duplicating context. Demand: **a shared Skill library and clean plugin composition.**

**e) Skill authoring quality and metadata.**
[#2064](https://github.com/anthropics/skills/issues/2064): `theme-factory`'s description states *what* it does but not *when* to use it — a trigger-precision defect. Demand: **description conventions that drive correct invocation.**

**f) Process and governance Skills.**
[#1385](https://github.com/anthropics/skills/issues/1385) proposes a three-gate quality pipeline (calibration → adversarial review → delivery verification); [#412](https://github.com/anthropics/skills/issues/412) (CLOSED) proposed `agent-governance`. Demand is real but acceptance is cautious.

---

## 3. High-Potential Pending Skills

All PRs in the window are unmerged, so "pending" here means **active within the last 30 days and scoped for near-term landing**:

| PR | Skill | Why it may land soon |
|---|---|---|
| [#2019](https://github.com/anthropics/skills/pull/2019) | xlsx (external-link resolution) | Narrow fix, explicitly closes #2013, updated 2026-11-03 — freshest activity in the set |
| [#2108](https://github.com/anthropics/skills/pull/2108) | docs (snapshot status) | Documentation-only, cites verifiable milestones (#1447, #330), updated 2026-11-03 |
| [#1980](https://github.com/anthropics/skills/pull/1980) | webapp-testing (shell injection) | Security fix with CWE reference; updated 2026-11-01 |
| [#1792](https://github.com/anthropics/skills/pull/1792) | docx (timeout + output verification) | Self-contained correctness fix; updated 2026-11-01 |
| [#525](https://github.com/anthropics/skills/pull/525) | pyxel (retro game dev) | Sustained maintenance over 8 months; still updated 2026-11-01 |
| [#1742](https://github.com/anthropics/skills/pull/1742) | mcp-builder (MCP 2.x) | Blocking compatibility break; closes #1668 |
| [#1245](https://github.com/anthropics/skills/pull/1245) | notion-spec-to-implementation + quantitative-resume-auditor | Highest-ranked *new-Skill* PR; updated 2026-10-15 |
| [#1997](https://github.com/anthropics/skills/pull/1997) · [#1681](https://github.com/anthropics/skills/pull/1681) | skill-creator (YAML frontmatter parsing; `package_skill.py` direct execution) | Two independent authoring-tool fixes, both narrow and testable |

**Pattern:** the near-term queue is dominated by *maintenance* PRs against bundled Skills (docx, xlsx, pptx, pdf, mcp-builder, skill-creator, webapp-testing) rather than net-new domain Skills. The `gerardrecinto` cluster ([#1975](https://github.com/anthropics/skills/pull/1975), [#1976](https://github.com/anthropics/skills/pull/1976), [#1977](https://github.com/anthropics/skills/pull/1977)) exemplifies this: three small, issue-linked fixes filed the same day.

---

## 4. Skills Ecosystem Insight

**The community's demand has shifted from "add more Skills" to "make the Skill layer trustworthy and correct" — namespace integrity and security hardening, evals that actually trigger, and token-efficient, correctly-described Skills — with bundled-Skill maintenance PRs now outnumbering new-Skill proposals in the active queue.**

---

# Claude Code Community Digest — 2026-11-07

**Source:** github.com/anthropics/claude-code
**Data window:** last 24 hours (50 issues updated, 1 release, 1 PR)

---

## 1. Today's Highlights

A single patch release, **v2.1.319**, tightens auto-mode behavior when telemetry or feature flags are unavailable — notably behind LLM gateways — by failing closed (ask/deny) instead of falling back to the local classifier, with an opt-out env var. Meanwhile, the issue tracker remains dominated by **authentication and session-persistence failures**: the long-running login-loop thread (#1757) is now at 90 comments and 78 👍, and several new desktop sign-in/save regressions landed this week. Platform hygiene — auto-updater replacing live binaries, TCC permission loss, and silent save failures — is the second major cluster.

---

## 2. Releases

**v2.1.319** ([releases](https://github.com/anthropics/claude-code/releases))

- **Auto mode now fails closed when the server can't check a call.** With telemetry or feature flags off — including behind LLM gateways — calls the server cannot validate are surfaced to the user (asked about) or denied, with no fallback to the built-in classifier.
- **Escape hatch:** set `CLAUDE_CODE_AUTO_MODE_SERVER=0` to keep using the local classifier.
- **Unchanged:** Bedrock, Vertex, and Foundry paths are explicitly out of scope for the new behavior.

*Analyst note:* this is a security/consistency hardening release, not a feature release. Teams running Claude Code through corporate gateways or air-gapped proxies should verify that auto-mode prompts don't disrupt existing automation, and set the env var deliberately rather than by accident.

---

## 3. Hot Issues

1. **[#1757](https://github.com/anthropics/claude-code/issues/1757) — Constant re-login required (OPEN, 90 comments, 78 👍)**
   The highest-engagement open thread in the repo by a wide margin. Users report being forced through web auth nearly every day despite authenticating the night before. Long-lived auth-state instability is the single loudest complaint in the community; the 👍 ratio suggests it's broad, not a niche config problem.

2. **[#25739](https://github.com/anthropics/claude-code/issues/25739) — Portable project memory across machines (OPEN, 30 comments, 27 👍)**
   Requests memory that travels between devices for multi-machine workflows. High comment count on an enhancement indicates real workflow pressure — developers are running CC on a laptop, a desktop, and a remote VM and losing context at each boundary.

3. **[#103788](https://github.com/anthropics/claude-code/issues/103788) — Desktop (macOS) sign-in fails: "We couldn't verify your browser" (OPEN, 20 comments)**
   Both Google and email paths return 400, independent of profile, version, and VPN; only an exit-IP change let the email path through. Points at IP/browser-fingerprint-based verification heuristics rather than client config — a hard blocker for affected users, with no workaround.

4. **[#107232](https://github.com/anthropics/claude-code/issues/107232) — Cannot handle API requests >5 minutes (OPEN, 7 comments)**
   A recurring, apparently unresolved timeout ceiling on long-running requests (Linux, API, networking). Directly limits long agentic or large-context workloads — one of the few issues that constrains what power users can build, not just how convenient it is.

5. **[#69181](https://github.com/anthropics/claude-code/issues/69181) — Vim mode: Escape in INSERT clears the whole prompt (OPEN, 6 comments, 7 👍)**
   Reproducible data loss in the agent screen: switching INSERT→NORMAL wipes the prompt buffer. Small blast radius but severe when hit, and the high 👍-to-comment ratio shows vim-mode users are quietly relying on it.

6. **[#63251](https://github.com/anthropics/claude-code/issues/63251) — Lazy/on-demand MCP server connections (OPEN, 5 comments, 4 👍)**
   All configured MCP servers connect at session startup regardless of use, inflating RAM and startup latency. A structurally important request: MCP adoption is growing faster than the eager-connection model scales.

7. **[#95539](https://github.com/anthropics/claude-code/issues/95539) — All naver.com domains blocked by safety restrictions (OPEN, 8 comments, 9 👍)**
   A false positive in the default safety-category blocklist takes out South Korea's dominant portal, while daum.net and tistory.com pass. Illustrates how coarse domain-level blocking creates regional breakage with no per-site override.

8. **[#107467](https://github.com/anthropics/claude-code/issues/107467) — Artifact `read` no longer accepts `path`/`paths` in 2.1.318 (OPEN, regression)**
   Migrated Design canvas sub-files became unreadable between 2.1.316 and 2.1.318. A clean, recent, version-bisected regression — exactly the kind of report that should be cheap to fix.

9. **[#107175](https://github.com/anthropics/claude-code/issues/107175) — Use Varlink for cross-session messaging (OPEN, 10 👍, 1 comment)**
   Proposes Varlink as the transport for inter-session messaging. The 10 👍 against a single comment signals strong silent agreement from infrastructure-minded users — a design-direction signal worth more than its comment count suggests.

10. **[#108074](https://github.com/anthropics/claude-code/issues/108074) — Auto-dream unavailable on Ultra plan (OPEN, filed today)**
    Feature flags are present in the binary (`autoMemoryEnabled`, `autoDreamEnabled`) but the capability is gated off (`tengu_onyx_plover` disabled). Raises a recurring question: why do paid top-tier plans see features their binary already contains?

**Also worth watching:** [#94794](https://github.com/anthropics/claude-code/issues/94794) — no "modified since read" guard before `Edit`, so external file changes are silently overwritten (tagged `data-loss`). [#107558](https://github.com/anthropics/claude-code/issues/107558) reports a $10 credit reload consumed in ~15 minutes of light use.

---

## 4. Key PR Progress

Only **one** pull request was updated in the window — the digest reflects the full available PR data rather than padding the list.

**[#107768](https://github.com/anthropics/claude-code/pull/107768) — mods: add the privacy mod (CLOSED, by poteat)**
Publishes the source of CC's built-in `privacy` mod under `mods/privacy`: the hooks module, manifest, and README. The mod refuses a shell call that contains the signed-in account's email address and names a User-Agent. No new behavior — this is a transparency/source-publication change. Notably, it was **closed** rather than merged, which is worth flagging: the community now has visibility into the hook's logic but not necessarily a maintained in-tree copy.

---

## 5. Hot Discussions

*Omitted — no Discussions data was provided in this dataset.*

---

## 6. Feature Request Trends

Distilled from the enhancement-tagged issues in this window:

- **Cross-machine continuity.** Portable project memory (#25739) and Remote Control reachability (#106996: sessions drop after ~4h idle, and not every session is attachable) both point at the same unmet need — a session that follows the developer across laptop, desktop, phone, and remote VM. #107064 compares Desktop SSH environments against hand-rolled SSH + tmux + Remote Control workflows.
- **MCP lifecycle control.** Lazy/on-demand MCP connections (#63251) and an `effort` argument for the `claude-code-remote` `create_session` tool (#107292) are both about not paying full cost for capabilities you aren't using.
- **Granular permission semantics.** #107363 asks for exceptions inside `ask` rules (e.g. `ask Bash(rm *)` except `/tmp/claude/**`) — the current model is all-or-nothing at the rule level.
- **Cross-session and cross-tool plumbing.** Varlink for session messaging (#107175); connector access for the Projects (beta) coordinator chat (#107601), which currently gets no connectors while its cloud threads get all of them.
- **Desktop/Code-tab parity.** #108070 asks for 1Password sign-in in the Code tab, not just Cowork, to stop forcing website-login tasks into a separate session with re-granted folder access.

---

## 7. Developer Pain Points

- **Auth state doesn't survive.** #1757 (daily re-login), #103788 (browser verification 400s), #86616 (macOS keychain corrupted twice in three days under ~20 concurrent instances), #97341 (Cowork: scheduled tasks can't be saved — "sign in again on this computer" while the same machine signs in fine from cloud). Four independent surfaces, one theme: credential lifecycle is the weakest part of the product.
- **The auto-updater breaks running sessions.** #94687 (voice dictation dies after the updater replaces `claude.exe`; TCC denies the old PID) and #105824 (updater *deletes* the binary a live session is using, and the resulting TCC denial blocks `~/Downloads` for the entire terminal despite Full Disk Access). Updating in place under a live process is causing collateral damage outside Claude Code itself.
- **Silent failure is worse than loud failure.** #108079 — Code sessions stopped saving for 12 days with no visible error after EFS app protection was lost on a non-system drive. #94794 — edits overwrite external changes with no "modified since read" guard. #95593 — chat completion notifications silently don't fire on desktop while Code/Cowork notifications do.
- **Regressions ship in patch releases.** #107467 (Artifact `path`/`paths` dropped in 2.1.318), #108019 (Desktop sidebar lost running/attention dots), #107905 (Chrome tab group vanishes on Vivaldi). Users are bisecting versions themselves — a sign that release notes aren't surfacing behavioral changes clearly enough.
- **Cost and metering surprises.** #107558 ($10 burned in ~15 min under light use, versus a normal half-day) sits alongside long-standing latency ceilings (#107232) as the two things that make developers distrust running large jobs.
- **Blocklist false positives have no override.** #95539 shows a whole national portal unreachable via default safety categories, with no per-domain escape hatch — a pattern likely to recur across regions.

---

*Data gaps: 1 PR total and no Discussions data were available for this window; sections 4 and 5 reflect that rather than inferring activity.*

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex Codex Community Digest — 2026-11-07

## 1. Today's Highlights

A single alpha release landed (`rust-v0.169.0-alpha.2`) with no public changelog detail, but the real activity was in the PR queue: a large batch of closed TUI/UI-tree rendering PRs signals an in-flight rework of the terminal footer, status line, and extension-rendering stack. On the issue side, Windows desktop regressions dominate — two separate reports of local projects vanishing from the sidebar (#42739 with 79 comments, plus #59895 filed yesterday) — while session/context integrity issues (compaction losing state, stale app-servers, runaway context injection) continue to burn user quota across platforms.

## 2. Releases

- **rust-v0.169.0-alpha.2** — alpha bump only; the release note contains no itemized changes. Treat as a pre-release tracking build rather than a feature drop.

## 3. Hot Issues

1. **[#42739](https://github.com/openai/codex/issues/42739)** — *Windows: local projects disappear from sidebar after desktop update* (79 comments, 👍7). The single highest-traffic thread in the window; chats survive under Recents but project grouping is lost. Persistent since September, still OPEN — the strongest signal of a data-migration bug in the Windows app.
2. **[#51524](https://github.com/openai/codex/issues/51524)** — *Windows Computer Use stops at `get_window_state`* (72 comments, 👍8). Window enumeration works, then the turn aborts before any click/typing because the browser URL can't be determined. High engagement reflects Computer Use being blocked outright for Chrome/Edge users.
3. **[#52503](https://github.com/openai/codex/issues/52503)** — *Web/Dots: repeated approvals after "Always allow," lost long-running uploads* (23 comments). Approval state is not durable across `create_blob` calls, blocking authorized GitHub work — a trust/UX failure in the permission model.
4. **[#41849](https://github.com/openai/codex/issues/41849)** — *VS Code Remote-SSH: stale app-server holds thread writer* (20 comments, 👍16 — highest reaction count). A reconnect spawns a second server while the orphan locks the thread, yielding "This is open in another app." Clean repro, clear ownership bug.
5. **[#35935](https://github.com/openai/codex/issues/35935)** — *Windows regression: compaction loses task state, repeats work, exhausts weekly usage* (16 comments). Combines correctness and cost impact; a long-lived (July) regression that keeps resurfacing.
6. **[#57620](https://github.com/openai/codex/issues/57620)** — *macOS: worktree icons missing after 26.1023.50603* (12 comments, 👍11). High reaction-to-comment ratio indicates broad but silent impact on worktree-heavy workflows.
7. **[#59895](https://github.com/openai/codex/issues/59895)** — *Windows: projects disappear after update + switching back to personal account* (9 comments). Corroborates #42739 with a narrower trigger (account switch), which may help isolate the migration path.
8. **[#56889](https://github.com/openai/codex/issues/56889)** — *Update removed usage percentages and reset times from avatar menu* (5 comments, 👍5). Small UI change with outsized frustration: users lose their only at-a-glance quota signal.
9. **[#53861](https://github.com/openai/codex/issues/53861)** — *Computer Use doubles every keystroke system-wide on macOS* (5 comments). Severe side effect while driving a background app; potential data-corruption risk in target apps.
10. **[#9286](https://github.com/openai/codex/issues/9286)** — *`git push --dry-run` fails: SSH config owned by nobody inside sandbox* (11 comments, 👍6). Open since January 2026 — a durable sandbox/permissions papercut affecting Linux CLI users on every git-over-SSH operation.

## 4. Key PR Progress

All items below are CLOSED and authored by `copyberry[bot]`, i.e. automated/internal projection PRs rather than community contributions — but they reveal the direction of the TUI renderer and auth/runtime work.

1. **[#60035](https://github.com/openai/codex/pull/60035)** — Fix authentication for git-attribution settings requests; honors runtime auth per destination and lets world-state contribution finish after credential refresh.
2. **[#60034](https://github.com/openai/codex/pull/60034)** — Omit sensitive process inputs (args, `arg0`, env vars) from spawn trace logs — a credential-leak fix in child-process tracing.
3. **[#60032](https://github.com/openai/codex/pull/60032)** — Extension status content may span multiple footer rows (up to four), fixing clipping of multiline/wrapped text at narrow widths.
4. **[#60029](https://github.com/openai/codex/pull/60029)** — Render UI trees directly into terminal buffers via shared layout traversal; adds natural and viewport-constrained measurement.
5. **[#60026](https://github.com/openai/codex/pull/60026)** — Multiline layout and row wrapping for UI trees, preserving authored line breaks, styles, and hyperlink ranges.
6. **[#60020](https://github.com/openai/codex/pull/60020)** — Replace CSI-only sanitizer with `vtparse` so OSC payloads (including embedded hyperlink URLs) no longer render as visible text — a rendering/security hardening fix.
7. **[#60019](https://github.com/openai/codex/pull/60019)** — Theme-aware semantic colors (`primary`, `surface`, …) for extension UI trees, following the active syntax theme.
8. **[#60014](https://github.com/openai/codex/pull/60014)** — `requirements.toml` can pin `features.runtime_agent_instances.mode` to `dual_header` or `assertion_only`, with conflict rejection — managed/enterprise auth configurability.
9. **[#60009](https://github.com/openai/codex/pull/60009)** — Disabled-by-default `gateway_setup_wizard`: choose a saved custom gateway during TUI sign-in, persisted as `model_provider` with app-server restart.
10. **[#60007](https://github.com/openai/codex/pull/60007)** — Cloud threads start from the local GitHub project's origin, enabling repo checkout provisioning and preserving the attached project across backend switches.

Also worth noting: **#60018** (turn-trigger propagation into usage analytics) and **#60011** (multi-agent mode guidance adapted for hosted tools) indicate ongoing investment in attribution telemetry and multi-agent orchestration.

## 5. Hot Discussions

**Q&A**

- **[#57025](https://github.com/openai/codex/discussions/57025)** — Can `app-server` apply `ToolPolicy` at thread start? User wants a handoff-review thread with *no* tools available, and correctly notes that a prompt-level instruction is insufficient. Points to a missing pre-execution policy hook.
- **[#59594](https://github.com/openai/codex/discussions/59594)** — Plus subscriber suspects Codex allowance was cut ~50%, possibly correlated with GPT-6.1 Sol (low) drift. Bilingual thread (EN/中文); representative of quota-opacity complaints.
- **[#57675](https://github.com/openai/codex/discussions/57675)** — Pro account receiving "Selected model is at capacity" on every model and surface for 7 days. Cross-posted from #56324 for visibility; an account-level (not model-level) capacity pattern.

**Show and tell**

- **[#58849](https://github.com/openai/codex/discussions/58849)** — `code-review-skill`: static-first review with four probes (behavior, tests, rules, …) run in fresh contexts, producing auditable findings and a JSON merge gate.
- **[#59844](https://github.com/openai/codex/discussions/59844)** — macOS catalog for keeping skills in sync across Codex, Claude Code, and OpenCode — addresses skill drift across multiple agent directories.
- **[#59896](https://github.com/openai/codex/discussions/59896)** — Tessera: run one task across Codex/Claude Code/OpenClaw/Hermes, keep the answer that passes tests; `tessera sync` writes one source into each agent's native format.
- **[#58359](https://github.com/openai/codex/discussions/58359)** — `claude-codex-taskbar`: <1 MB open-source Windows tool surfacing 5-hour and weekly `/status` limits in the taskbar.
- **[#59316](https://github.com/openai/codex/discussions/59316)** — ML Research & Paper skill: frozen experiments, evidence ledgers, resumable review across literature → experiment → writing.
- **[#60012](https://github.com/openai/codex/discussions/60012)** — Zozo Local prerequisite checker: Node major, OS family, Git, and `gh` validation to separate toolchain failures from login/startup failures.
- **[#59871](https://github.com/openai/codex/discussions/59871)** — Account Access: portable skill + CLI for owner-approved scoped account reads and resumable sign-in tasks (preview).

**Ideas** — No thread in this window carried an Ideas tag; the closest product proposal is issue **#59324** (coordinator-managed child task cards), covered under trends below.

## 6. Feature Request Trends

- **Durable, coordinator-style multi-agent workflows.** #59324 asks for a first-class coordinator conversation that creates and supervises visual, durable child task cards; #60011's PR (bundled mode policies for hosted tools) shows this is already being built internally.
- **Portable skills and cross-agent config sync.** The Show-and-tell cluster (#58849, #59844, #59896, #59316) converges on one request: a canonical skill/instruction source that projects into Codex, Claude Code, and OpenCode without drift.
- **Quota transparency and cost control.** #56889 (restore percentages/reset times), #59594 (suspected 50% cut), #57675 (account-level capacity), #45383 and #59970 (usage burned rebuilding context) all point at the same gap: users cannot predict or audit spend.
- **Session durability and correct compaction.** #35935, #45383, #54423, #50509, #59970 request resumable sessions that preserve task state, don't pin threads to unavailable models, and don't re-inject oversized continuity payloads.
- **Cross-surface continuity.** #60007 (cloud threads from local project origin), #56600 (Android remote pairing), #41849 (Remote-SSH reconnect) — users expect one thread to move across desktop, CLI, web, and remote hosts.
- **Bring-your-own provider/gateway.** #60009 (gateway setup wizard) and #54423 (custom Responses provider) reflect demand for first-class custom endpoints rather than workarounds.
- **Desktop UI ergonomics on macOS.** #59386 (full view hides Projects sidebar; main conversation has no tab of its own) and #60005 (chat tab missing in full-view layout) ask for tab-model consistency.

## 7. Developer Pain Points

- **Windows desktop regressions after updates are the top complaint by volume.** Sidebar/project loss (#42739, #59895), blocked git writes from workspace-write DENY ACLs on linked worktrees (#32880), extension UI failing to initialize even in a clean profile (#56682), and Computer Use breakage (#51524, #60023). Multiple reports share a root shape: an app update silently invalidates persisted state.
- **State and lock integrity across reconnects.** Orphaned `app-server` processes holding thread writers (#41849), compaction that loses task state and repeats completed work (#35935), and threads pinned to unavailable models after a failed compaction (#54423) all force users to abandon sessions.
- **Quota is consumed by the agent's own overhead, not the user's task.** #35935, #45383, and #59970 describe the same failure loop: resume/compaction re-injects large context, usage spikes, work is still unfinished. Compounded by removal of the usage readout (#56889).
- **Permission and safety systems produce false positives and non-durable approvals.** #52503 ("Always allow" not honored, approval cards unavailable), #56475 (authorized local dev blocked by cybersecurity check), #41649 (macOS Files & Folders permission silently regained after revocation). Each erodes trust in the sandbox model in a different direction — too strict, then too permissive.
- **Computer Use reliability remains uneven.** URL detection failure on Windows (#51524) and system-wide keystroke duplication on macOS (#53861) are both turn-blocking or environment-damaging, not cosmetic.
- **Long-tail sandbox friction on Linux CLI.** #9286 (SSH config owned by nobody inside the sandbox) has been open since January 2026 and still breaks `git push` — a reminder that older, low-comment issues can represent persistent daily blockers.
- **Duplicate reports suggest poor known-issue discoverability.** #42739 vs #59895, and #57675 cross-posted from #56324, indicate users cannot find existing threads — worth a triage/dedup or in-product status signal.

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI Community Digest — 2026-11-07

## 1. Today's Highlights

No new releases landed in the last 24 hours, but triage activity was heavy: 50 issues and 45 PRs were touched, with the bulk of maintainer attention concentrated on **agent/subagent reliability** — false success reporting, generalist-agent hangs, and browser-agent configuration gaps. Alongside that, a cluster of small but high-value `core`/`cli` correctness fixes (PTY leaks, path handling, `.gitignore` negation, hook migration) moved through review.

---

## 3. Hot Issues

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — Subagent recovery after `MAX_TURNS` is reported as GOAL success** (P1, 13 comments)
   The `codebase_investigator` subagent returns `status: "success"` / `Termination Reason: "GOAL"` even though its own output admits it hit the turn limit before doing any analysis. This is the single most-discussed issue of the day and arguably the most dangerous class of bug in an agent CLI: silent truncation that automation cannot detect. Community reaction (+2 👍) is muted but the maintainer-only rollup label signals it's on the internal roadmap.

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — Generalist agent hangs indefinitely** (P1, 8 comments, **8 👍**)
   Any deferral to the generalist agent hangs forever — even trivial folder creation — with waits of up to an hour. The highest-reaction issue in this batch, and the workaround (telling the model never to use subagents) effectively disables the feature. This is the top user-facing pain point right now.

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — Zero-dependency OS sandboxing + post-execution intent routing** (P2, 9 comments)
   Proposes leaning into Gemini 3's native bash affinity (chaining `grep`/`sed`/`awk`) while containing it via OS-level sandboxing and intent routing after execution. A significant architectural bet — it would reshape how the shell tool is gated and audited.

4. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — Assess AST-aware file reads, search, and mapping** (P2, 7 comments)
   Epic investigating whether AST-aware tools reduce misaligned reads, wasted turns, and token noise. Companion issues [#22746](https://github.com/google-gemini/gemini-cli/issues/22746) (CLI tooling, recommending `tilth`/`glyph`) and [#22747](https://github.com/google-gemini/gemini-cli/issues/22747) (`ast-grep` for reads/searches) show this is being decomposed into concrete experiments.

5. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini does not use skills and sub-agents enough** (P2, 7 comments)
   Anecdotal but widely echoed: custom skills and subagents are only invoked when explicitly instructed, not when contextually relevant (e.g., a `gradle` skill during Gradle work). Suggests the model's tool-selection policy is under-tuned, undermining the whole extensibility story.

6. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267) — Browser Agent ignores `settings.json` overrides** (P2, 5 comments)
   `AgentRegistry` correctly merges settings at init, but the Browser Agent then discards them — so `maxTurns` and similar knobs silently do nothing. Config that parses but doesn't apply is a particularly corrosive bug class for users debugging agent behavior.

7. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — Browser subagent fails on Wayland** (P1, 4 comments)
   The browser subagent reports `Termination Reason: GOAL` while failing outright under Wayland. Linux/Wayland users remain a persistent blind spot for the browser tooling, and the false-GOAL pattern recurs here too.

8. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 400 error with >128 tools** (P2, 3 comments)
   Registering more than ~128 tools produces a 400 from the API (the body text says 400, the title says 128). Directly constrains MCP-heavy setups, where tool counts balloon quickly — the fix likely needs scoped tool selection rather than a hard cap.

9. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — Agent should stop/discourage destructive behavior** (P2, 3 comments)
   The model reaches for `git reset` or `--force` when safer equivalents exist, and lacks guardrails around destructive DB/resource operations. Paired with #19873, this is the safety thread running through the agent workstream.

10. **[#23571](https://github.com/google-gemini/gemini-cli/issues/23571) — Model creates tmp scripts in random spots** (P2, 3 comments)
    When restricted to shell execution, the model scatters ad-hoc edit scripts across directories, wrecking clean-commit workflows. A small UX issue with outsized cost for anyone doing reviewable, incremental work.

*Also worth watching:* [#21763](https://github.com/google-gemini/gemini-cli/issues/21763) (`/bug` reports omit subagent context — P1), [#20079](https://github.com/google-gemini/gemini-cli/issues/20079) (symlinked agent files in `~/.gemini/agents/` are not discovered), and [#18836](https://github.com/google-gemini/gemini-cli/issues/18836) (replace `WriteToDo` with persistent file-based task tracking to combat context rot).

---

## 4. Key PR Progress

1. **[#30185](https://github.com/google-gemini/gemini-cli/pull/30185) — Prevent recursive interactive `gemini` sessions in shell mode** (P1, open)
   Blocks the CLI from spawning nested interactive sessions inside Shell Mode (`!`) or the `[Shell]` tool, and rejects unsupported config subcommands. A meaningful stability fix for a failure mode that can wedge a terminal.

2. **[#30189](https://github.com/google-gemini/gemini-cli/pull/30189) — `MessageBus.request()` rejects immediately when publish fails** (P2, open, maintainer-only)
   Previously a failed publish waited out the full 60s timeout and then surfaced a generic timeout error. Now it rejects with the original error and releases its timer and listener immediately — much better diagnosability for inter-agent messaging.

3. **[#30051](https://github.com/google-gemini/gemini-cli/pull/30051) — Kill active shells and persist tool calls on headless interruption** (open)
   Fixes headless `--yolo` runs exiting with code 0 on SIGINT/SIGTERM/SIGHUP and leaving detached shell commands alive. Critical for CI and scripted use, where exit codes are the entire contract.

4. **[#30183](https://github.com/google-gemini/gemini-cli/pull/30183) — Override the 60s API headers timeout across all auth modes** (closed)
   Adds `model.requestTimeoutMs` / `GEMINI_API_HEADERS_TIMEOUT_MS` so long-thinking requests (`thinkingLevel: HIGH` on large contexts) stop dying with `UND_ERR_HEADERS_TIMEOUT` and replaying from scratch.

5. **[#30184](https://github.com/google-gemini/gemini-cli/pull/30184) — Close orphan `/dev/ptmx` master FD leaked on macOS PTY spawn** (closed)
   Addresses FD exhaustion (`kern.tty.ptmx_max`) during long sessions with `@lydell/node-pty@1.1.0` on macOS — a slow-burn bug that only bites power users after hours of work.

6. **[#30187](https://github.com/google-gemini/gemini-cli/pull/30187) — Prevent multi-hour quota retries, notify ACP client on retries** (closed)
   Stops ACP-mode requests from hanging indefinitely on HTTP 429 with multi-hour reset windows, and surfaces retry attempts to the client instead of failing silently.

7. **[#30052](https://github.com/google-gemini/gemini-cli/pull/30052) — Honor nested `.gitignore` negations under `dir/**` patterns**
   `GitIgnoreParser` was ignoring files that a nested `.gitignore` re-included under a parent wildcard. Directly affects which files the agent can see and edit — quiet but high-impact correctness.

8. **[#30055](https://github.com/google-gemini/gemini-cli/pull/30055) — Map Claude `SubagentStop` hooks to `AfterAgent` on migrate** (P2)
   `gemini hooks migrate --from-claude` used the key `SubAgentStop`, so the event passed through unmapped and was silently dropped. Fixes hook parity for migrating Claude Code users.

9. **[#30054](https://github.com/google-gemini/gemini-cli/pull/30054) — Only tildeify paths at a home-directory boundary** (P2)
   With home `/Users/al`, `/Users/albert/proj` rendered as `~bert/proj`. Cosmetic, but path display bugs erode trust in tool output.

10. **[#30027](https://github.com/google-gemini/gemini-cli/pull/30027) — Support separate custom theme configuration** (P1, open)
    Splits custom theme config out from the general settings surface. Lower risk than the P1 label suggests, but it's the most-anticipated UX change in the open queue.

*Also merged/closed today:* [#29901](https://github.com/google-gemini/gemini-cli/pull/29901) (ACP `_session/steering` — let clients inject messages into a running turn, mirroring `experimental.modelSteering`), [#29895](https://github.com/google-gemini/gemini-cli/pull/29895) (preserve structured API errors in headless mode instead of `[object Object]`), [#30053](https://github.com/google-gemini/gemini-cli/pull/30053) (`Background PIDs: NaN` parser fix), [#30056](https://github.com/google-gemini/gemini-cli/pull/30056) (`__GCLI_EXPAND_TARGET__` leaking into `expandEnvVars`), and [#30165](https://github.com/google-gemini/gemini-cli/pull/30165) (mid-stream retries concatenating partial text/tool calls in ACP).

---

## 6. Feature Request Trends

- **Subagent observability and honesty.** The largest coherent cluster: true termination reasons (#22323), subagent context in `/bug` reports (#21763), shareable subagent trajectories via `/chat share` (#22598), and subagent discovery via `settings.json` (#18285). Users increasingly run agents they cannot see into.
- **Making the model actually use its extensions.** #21968 (skills/subagents ignored), #21432 (agent "self-awareness" of its own flags, hotkeys, and CLI invocation), and #18283 (curated in-repo agent examples) all point at tool-selection policy, not missing capability.
- **Context economy.** AST-aware reads/search/mapping (#22745, #22746, #22747), "Tactful Extraction" for surgical reads (#19561, motivated by a ~36.6k token/turn baseline), and persistent file-based task tracking to replace in-context `WriteToDo` (#18836, #21000).
- **Sandboxing and destructive-action guardrails.** #19873 (OS sandbox + intent routing) and #22672 (stop `git reset`/`--force` when safer paths exist) form a safety workstream that pairs naturally with the bash-affinity direction.
- **Per-workspace policy and resilience.** #18397 (per-workspace policy instead of global), #22232 (browser session takeover / lock recovery), and #23166 (stabilizing internal evals) reflect a shift from "does it work" to "does it work repeatably, unattended, in the right scope."

## 7. Developer Pain Points

- **Silent failure is the dominant frustration.** False `GOAL`/`success` status (#22323), `GOAL` on outright Wayland failure (#21983), exit code 0 on interrupted headless runs (#30051), and hung generalist agents (#21409) all share a root cause: the CLI reports success when it did not succeed. For anyone wiring Gemini CLI into automation, this is the single biggest blocker.
- **Configuration that parses but doesn't apply.** Browser Agent ignoring `settings.json` (#22267), symlinked agent definitions not discovered (#20079), and the Claude hook key mismatch (#30055) all cost users debugging time in the same way: no error, no effect.
- **Subagents are hard to debug.** With no trajectory visibility (#22598) and no subagent context in bug reports (#21763), users diagnosing #21409-style hangs are working blind — which likely inflates comment counts on those issues.
- **Resource and scale limits bite power users.** Tool-count ceilings (#24246), macOS PTY FD exhaustion (#30184), and multi-hour 429 hangs in ACP (#30187) all appear only in long-running or MCP-heavy sessions — precisely the users most invested in the tool.
- **Workspace hygiene.** Scattered temp scripts (#23571) and destructive git operations (#22672) mean the agent's output frequently needs manual cleanup or repair before it can be committed.

*Note: the near-simultaneous `updated` timestamps across all 50 issues suggest a bulk triage/labeling pass rather than 50 independent user reports — comment counts, not update times, are the reliable signal of engagement here.*

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI — Community Digest
**Date:** 2026-11-07 · **Repo:** [github/copilot-cli](https://github.com/github/copilot-cli)

---

## 1. Today's Highlights

Two releases landed in the last 24 hours: **v1.0.107** brings inline image rendering in Rex terminals, a `/code` command to jump into VS Code, a new **Fusion tier** setting, and a unified `/settings` dashboard; the pre-release **v1.0.108-0** adds Windows enumerate-only filesystem grants in the sandbox policy and enables managed **shared tgrep indexing** via new `/index` commands. Meanwhile, the issue tracker is dominated by **Microsoft Entra / remote MCP authentication failures** across Windows and macOS, and a cluster of new triage items around **Fusion/Auto mode** behavior (withheld agent output, model blocklisting, and provider config being ignored in server modes).

---

## 2. Releases

### [v1.0.108-0](https://github.com/github/copilot-cli/releases/tag/v1.0.108-0) *(pre-release)*
**Added**
- `sandbox.userPolicy.filesystem.enumeratePaths` in `settings.json` — enables **enumerate-only directory grants on Windows**, refining the sandbox filesystem permission model beyond all-or-nothing read access.
- New `/index` commands, with **managed shared tgrep indexing enabled by default** for eligible repositories — a notable step toward faster repo-wide code search out of the box.

**Improved**
- Answers to questions about Copilot CLI behavior can now be sourced from the current build (release note text is truncated upstream).

### [v1.0.107](https://github.com/github/copilot-cli/releases/tag/v1.0.107) — 2026-11-06
- **Rex terminals** render inline images using Kitty direct placements.
- **`/code`** opens the current session in VS Code — a useful bridge for users who start in the TUI and want to continue in the editor.
- **Fusion tier setting** added to `copilot config`, giving users a first knob over model routing.
- **`/settings`** now opens the unified dashboard for all users, with global search across settings and local resources.

> ⚠️ Note: Issue [#5377](https://github.com/github/copilot-cli/issues/5377) points out that the 1.0.106 and 1.0.107 release notes differ only by date, and that `changelog.md` disagrees with the published notes — worth watching if you track changes precisely.

---

## 3. Hot Issues

1. **[#1972 — Allow users to configure submit key (e.g., Ctrl+Enter) to prevent accidental submission with IME input](https://github.com/github/copilot-cli/issues/1972)** · 👍 48 · 4 comments
   The highest-upvoted item in this window. CJK users hit Enter for IME conversion confirmation and accidentally submit half-composed prompts. Strong signal that **input handling is not internationalized** — a small fix with outsized UX impact.

2. **[#2776 — Shift+Enter submits the prompt instead of inserting a new line](https://github.com/github/copilot-cli/issues/2776)** · 👍 5 · 9 comments
   The most-discussed issue today. Affects a core composition workflow and is likely the same root cause as #3768 — together they represent the single most repeated complaint in the tracker.

3. **[#3768 — Shift+Enter multiline input does not work](https://github.com/github/copilot-cli/issues/3768)** · 👍 5 · 2 comments
   Reported since v1.0.61 and still open. Duplicate-report volume suggests users are not finding a workaround, which usually precedes churn.

4. **[#5236 — Windows: Entra broker token cache stays 0 bytes after MCP sign-in, every new process re-prompts](https://github.com/github/copilot-cli/issues/5236)** · 6 comments
   The **token cache file is written but never populated**, so each process re-triggers the account picker. Highly visible friction for enterprise Windows users on every single CLI launch.

5. **[#5250 — macOS: Entra-broker MCP servers fail at startup ("sign-in is already in progress")](https://github.com/github/copilot-cli/issues/5250)**
   Two Entra-protected servers (Azure DevOps remote MCP, WorkIQ) race each other at session start and the cached-token rejection is not recovered. Same subsystem as #5236 — **Entra broker concurrency is a cross-platform design gap**, not a platform bug.

6. **[#5366 — Remote MCP OAuth (Entra): calls fail after token expiry while `mcp.list` still says connected](https://github.com/github/copilot-cli/issues/5366)**
   Only `restartServer` recovers. Silent staleness in connection state is the worst failure mode for agentic workflows — the model keeps calling a tool that cannot succeed.

7. **[#3679 — BYOK Azure: 429 throttling exhausts all retries in ~0.15s with no effective backoff](https://github.com/github/copilot-cli/issues/3679)**
   Five retries burned near-instantly, and neither retry count nor wait is configurable. Especially damaging for **self-hosted / air-gapped Azure OpenAI** deployments where throttling is routine.

8. **[#5367 — Agent regularly loses MCP tool schemas](https://github.com/github/copilot-cli/issues/5367)**
   "I struggle with this every single day" — schemas vanish mid-session and `/compact` restores them. Points at **context pruning dropping tool definitions** rather than at the MCP servers themselves.

9. **[#5370 — Agent output withheld until next user turn when long shell commands are backgrounded (Fusion/Auto mode)](https://github.com/github/copilot-cli/issues/5370)** · new triage
   The agent appears hung until the user types again. A **streaming/backpressure regression specific to Fusion/Auto**, filed the same day the Fusion tier setting shipped.

10. **[#4150 — `permissions-config.json` `commandIdentifiers` with spaces still require approval](https://github.com/github/copilot-cli/issues/4150)** · 👍 3
    `"make"` is allowed but `"make fix"` is not, contradicting the documented examples. Precision in allowlisting matters for anyone automating builds through the agent.

**Closed in this window (good triage throughput):** [#5368](https://github.com/github/copilot-cli/issues/5368) (Windows sandbox probe `CreateProcessW` error), [#4845](https://github.com/github/copilot-cli/issues/4845) (sessions stuck "In use"), [#4943](https://github.com/github/copilot-cli/issues/4943) (Esc discarding `ask_user` forms), [#2731](https://github.com/github/copilot-cli/issues/2731) (`@` autocomplete with spaces in paths), [#5265](https://github.com/github/copilot-cli/issues/5265) (input history last-writer-wins across processes).

---

## 4. Key PR Progress

> Only **one** pull request was updated in the last 24 hours — community PR throughput is very low relative to issue volume (29 issues vs. 1 PR), which is itself worth flagging.

1. **[#5373 — docs: add Simplified Chinese Copilot CLI README](https://github.com/github/copilot-cli/pull/5373)** *(OPEN)*
   Adds a Simplified Chinese translation of the README plus bidirectional EN/中文 navigation links, preserving commands, config keys, product names, and upstream doc links. Notable pairing with [#1972](https://github.com/github/copilot-cli/issues/1972) (IME/submit-key): the **CJK user base is actively investing in the tool** while blocked by input-handling behavior.

No feature or bugfix PRs were updated in this window.

---

## 5. Hot Discussions

*Omitted — no Discussion data was provided in this dataset.*

---

## 6. Feature Request Trends

Distilled from all issues in this window, five directions dominate:

| Direction | Evidence | Ask |
|---|---|---|
| **Configurable input/keybindings** | #1972 (48👍), #2776, #3768, #4995 | User-defined submit key, reliable Shift+Enter multiline, better scrollback navigation |
| **Model routing control** | #5369, #5363, v1.0.107 Fusion tier | Blocklists for Fusion/Auto, accurate `model` reporting over ACP |
| **MCP auth lifecycle** | #5236, #5250, #5366, #5367 | Persistent token caches, concurrency-safe sign-in, correct connection state, schema retention |
| **Sandbox/permission granularity** | #5365, #4150, #5368, v1.0.108-0 | Named policy rules, opt-outs for temp grants, multi-word command identifiers, enumerate-only paths |
| **Extensibility & context control** | #5375, #5099, #5362, #5374 | Disable built-in slash commands, display-only hooks, model-judged tool-result pruning, hook rendering in desktop app |

Two smaller but telling asks: **parity for BYOK providers** across `--server` / `--host-environment` modes ([#5371](https://github.com/github/copilot-cli/issues/5371)), and **observability** — `copilot --acp` exports no OpenTelemetry signals at all ([#5376](https://github.com/github/copilot-cli/issues/5376)).

---

## 7. Developer Pain Points

1. **Entra ID + remote MCP is the single biggest reliability sink.** Three separate open issues (#5236, #5250, #5366) cover cache-not-persisted on Windows, concurrent sign-in rejection on macOS, and stale-but-"connected" state after expiry. The common thread: **authentication state is inconsistent across OSes and process boundaries**, and failures surface as repeated prompts rather than actionable errors.

2. **Silent failures over loud ones.** `mcp.list` reporting `connected` while every call fails (#5366), lost tool schemas mid-session (#5367), and output withheld until the next turn (#5370) all look like "the agent is broken" to the user. Developers consistently ask for *detectable* failure states.

3. **Keyboard behavior breaks composition.** The Shift+Enter pair (#2776, #3768) plus IME accidental submission (#1972) mean the most basic interaction — typing a multi-line prompt — is unreliable for a meaningful share of users, with 48 upvotes on the fix.

4. **Retry/backoff is not tunable.** #3679's ~0.15s exhaustion of 5 retries with no configurable wait makes BYOK Azure deployments fragile under routine throttling.

5. **Configuration surface inconsistency.** Documented examples don't work (`make fix` in #4150), and env-var BYOK config silently disappears in `--server` and `--host-environment` modes (#5371). Users can't predict which config path a given mode honors.

6. **Release notes don't match the changelog.** #5377 flags 1.0.106 and 1.0.107 notes differing only by date — a small thing that erodes trust for teams pinning versions.

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode Community Digest — 2026-11-07

## 1. Today's Highlights

OpenCode shipped **v2.2.6**, headlined by shared MCP connection pooling (one connection per MCP server across projects with identical setup) and new GUI plugin theming slots. Meanwhile the issue tracker is dominated by two very different signals: a 316-comment free-tier megathread (#59387) that has become the de facto support hub, and a cluster of "silent failure" bugs where invalid agent/config frontmatter is dropped without any warning. On the PR side, maintainer `kitlangton` landed a broad sweep of `[contributor]` correctness fixes across core, TUI, and Code Mode.

## 2. Releases

**v2.2.6**
- **Shared MCP Connections** — opt-in pooling lets one MCP connection be shared across projects with the same setup, reducing process count and startup time. ([#61185](https://github.com/anomalyco/opencode/pull/61185))
- **GUI Plugin Themes and New Slots** — GUI plugins can now theme the interface and hook into additional UI slots. (Release notes truncated in source data.)

## 3. Hot Issues

1. **[#59387](https://github.com/anomalyco/opencode/issues/59387) — [zen] Free tier megathread** (316 comments, 👍23). The single highest-traffic thread in the repo, consolidating reports of `Free usage exceeded`, non-resetting quotas, and Desktop version rejections. Effectively functioning as an unofficial status page.
2. **[#27110](https://github.com/anomalyco/opencode/issues/27110) — Limit max parallel subagents** (14 comments, 👍42 — the most-upvoted item here). Local-model users hit context/memory ceilings when subagents fan out; a cap is the most broadly endorsed request in the current backlog.
3. **[#40993](https://github.com/anomalyco/opencode/issues/40993) — Support agent-plugins.org standard** (17 comments, 👍21). A vendor-neutral packaging spec bundling Agent Skills and MCP servers into portable plugins; framed as multi-vendor rather than OpenCode-specific, which raises the stakes on adoption.
4. **[#51949](https://github.com/anomalyco/opencode/issues/51949) — Post-compaction agent loses top-level tools** (15 comments). After auto-compact, the agent routes everything through Code Mode, then reports having no tools at all. A high-severity correctness bug in the compaction path.
5. **[#62442](https://github.com/anomalyco/opencode/issues/62442) — Desktop stuck session** (6 comments). Session remains `running`, interrupt returns `false`, and DELETE times out (HTTP 499 after ~61s) — no recovery path short of restart.
6. **[#62447](https://github.com/anomalyco/opencode/issues/62447) — TUI model picker shows subset of custom provider models** (5 comments). Only 3 of 7 `@ai-sdk/openai-compatible` Ollama models appear despite `/api/model` returning all 7; persists across restarts.
7. **[#40096](https://github.com/anomalyco/opencode/issues/40096) — Collapsible tool output in TUI** (5 comments, 👍2). Write/Edit/Shell blocks render fully expanded, drowning long sessions; asks for a `/collapse` toggle.
8. **[#62460](https://github.com/anomalyco/opencode/issues/62460) — Beam Free stalls mid-turn** (4 comments, filed today). Agent emits "let me do this now" and terminates without acting; mirrors the Crouton Alpha stall pattern in [#61511](https://github.com/anomalyco/opencode/issues/61511).
9. **[#57844](https://github.com/anomalyco/opencode/issues/57844) — CJK glyph leakage into Russian output** (4 comments). GLM-5.3-Flash inserts stray Chinese/Japanese tokens mid-sentence; a companion report in [#62411](https://github.com/anomalyco/opencode/issues/62411) shows the same for Polish, suggesting a shared multilingual decoding issue.
10. **[#62138](https://github.com/anomalyco/opencode/issues/62138) — `/goal` never runs after `/undo`** (3 comments, `[reproduced]`). The goal shows active but the kickoff stays in the session inbox, so the 30-minute timer never arms and the goal hangs indefinitely.

## 4. Key PR Progress

1. **[#62491](https://github.com/anomalyco/opencode/pull/62491)** (closed) — Fixes a `deploy-site` failure: `ProductHero` was pulling the full products bundle into `/product/browse`, pushing it to 116.9 kB against a 99 kB budget.
2. **[#62490](https://github.com/anomalyco/opencode/pull/62490)** (open) — Adds a location chooser to the new `/export` dialog, building on #62253.
3. **[#62253](https://github.com/anomalyco/opencode/pull/62253)** (open) — `/export` now writes to the launch working directory instead of `/tmp/session-<uuid>.md`, restoring v1 behavior. Closes #53748.
4. **[#62480](https://github.com/anomalyco/opencode/pull/62480)** (open) — Sizes the compaction output limit to the room left by shortened requests, fixing throttling of the summary request itself. Closes #62185 — relevant to the #51949 fallout.
5. **[#62477](https://github.com/anomalyco/opencode/pull/62477)** (open, `[contributor]`) — Patch application preserves trailing blank lines and CRLF endings instead of deleting newlines or producing mixed line endings.
6. **[#62457](https://github.com/anomalyco/opencode/pull/62457)** (closed) — Watches symlink targets for config files, so a dotfiles-managed `opencode.json` reloads on edit rather than serving a stale document.
7. **[#62252](https://github.com/anomalyco/opencode/pull/62252)** (closed) — Localizes the desktop right-click menu (Cut/Copy/Paste/Select All). Closes #55912.
8. **[#62474](https://github.com/anomalyco/opencode/pull/62474)** (open, `[contributor]`) — File autocomplete skips malformed frecency entries instead of scoring them `NaN` and ranking them permanently.
9. **[#62476](https://github.com/anomalyco/opencode/pull/62476)** (open, `[contributor]`) — A missing local plugin folder now errors with the offending path rather than a bare `ENOENT` and a generic "Plugin failed to load".
10. **[#62478](https://github.com/anomalyco/opencode/pull/62478)** (open, `[contributor]`) — Batches instruction blob reads/writes to stay under Durable Object SQLite's 100-value-per-statement limit, unblocking sessions with many instruction entries.

## 5. Hot Discussions

No discussion data was provided for this period; this section is omitted.

## 6. Feature Request Trends

- **Packaging & extensibility** — Adoption of the vendor-neutral [agent-plugins.org spec](https://github.com/anomalyco/opencode/issues/40993), plus the v2.2.6 GUI plugin theming/slots and shared MCP pooling, point to plugins becoming the primary extension surface.
- **Resource governance for local models** — Capping parallel subagents ([#27110](https://github.com/anomalyco/opencode/issues/27110), 42 👍) and collapsing verbose tool output ([#40096](https://github.com/anomalyco/opencode/issues/40096)) are both about keeping context and screen real estate under user control.
- **Permission and config durability** — Requests to disable persisting "Allow always" approvals ([#62432](https://github.com/anomalyco/opencode/issues/62432)) and to stop silently dropping misconfigured agents/commands ([#45876](https://github.com/anomalyco/opencode/issues/45876), [#52065](https://github.com/anomalyco/opencode/issues/52065), [#54200](https://github.com/anomalyco/opencode/issues/54200)).
- **Platform-native polish** — Custom Linux titlebars ([#36225](https://github.com/anomalyco/opencode/issues/36225)), localized desktop context menus, and full Linux download coverage on the site.
- **Output-language control** — An explicit output-language guard for multilingual models ([#57844](https://github.com/anomalyco/opencode/issues/57844)).

## 7. Developer Pain Points

- **Free-tier quota and reset loops remain the top support burden.** The megathread ([#59387](https://github.com/anomalyco/opencode/issues/59387)) shows no sign of slowing, with users reporting reset timers that advance every day ([#62233](https://github.com/anomalyco/opencode/issues/62233)) and persistent `ECONNRESET` on free models ([#62179](https://github.com/anomalyco/opencode/issues/62179)). Escalation friction is real: [#62224](https://github.com/anomalyco/opencode/issues/62224) reports an unanswered refund request via email with Discord inaccessible in-region.
- **Silent failures are the most consistent bug class.** Invalid YAML frontmatter, descriptions starting with `[`, and unresolvable `model:` values all cause agents or commands to vanish with zero logging. Developers only discover the problem when the agent doesn't exist — a debuggability gap, not just a parsing bug.
- **Compaction is the weakest link in long sessions.** Post-compact tool loss ([#51949](https://github.com/anomalyco/opencode/issues/51949)) plus compaction output throttling ([#62480](https://github.com/anomalyco/opencode/pull/62480)) suggest the summarize-and-continue path needs hardening before it's trustworthy on large tasks.
- **Session lifecycle is not always recoverable.** Stuck-running desktop sessions ([#62442](https://github.com/anomalyco/opencode/issues/62442)) and goals that queue forever after an undo ([#62138](https://github.com/anomalyco/opencode/issues/62138)) leave users without an escape hatch other than restart.
- **Model/provider plumbing is inconsistent between layers.** The server reports 7 models while the TUI shows 3 ([#62447](https://github.com/anomalyco/opencode/issues/62447)), and Desktop on Windows 11 shows an empty model list despite toggles being on ([#61481](https://github.com/anomalyco/opencode/issues/61481)).
- **Streaming/decoding instability on hosted models** — mid-turn stalls (Beam Free, Crouton Alpha) and foreign-script glyph corruption in non-English output both erode trust in the free and `opencode-go` tiers.

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi Community Digest — 2026-11-07

**Source:** [github.com/earendil-works/pi](https://github.com/earendil-works/pi) · 50 issues, 19 PRs, and 1 discussion updated in the last 24h.

---

## 1. Today's Highlights

No releases shipped in the last 24 hours; the day was dominated by a large bug-fix close-out, with roughly a dozen fix PRs landing against issues opened earlier in the week — including the mixed CRLF/LF line-ending corruption in `edit` ([#8544](https://github.com/earendil-works/pi/issues/8544)), the macOS/Node 24 image-resize worker crash ([#10695](https://github.com/earendil-works/pi/issues/10695)), and the `openai-completions` empty `tools: []` rejection ([#11843](https://github.com/earendil-works/pi/issues/11843)). The one new escalation is [p0] [#11869](https://github.com/earendil-works/pi/issues/11869), where the new `StringEnum` in Pi 1.5.0 silently degrades tool-parameter types to `unknown` at compile time — still open. SDK embedders also pushed forward on prompt/identity customization ([#11317](https://github.com/earendil-works/pi/issues/11317), [PR #11904](https://github.com/earendil-works/pi/pull/11904)).

---

## 2. Releases

None in the last 24 hours.

---

## 3. Hot Issues

1. **[#1188](https://github.com/earendil-works/pi/issues/1188) [CLOSED] Better compatibility with screen readers** — 14 comments, the most-discussed item in the window. The 80 ms spinner/status-bar repaint flood makes streaming sessions unusable with assistive tech. Closing it is a meaningful accessibility milestone for the TUI.
2. **[#9932](https://github.com/earendil-works/pi/issues/9932) [OPEN] `before_agent_start` forced system prompt keeps tools removed via `setActiveTools`** — 7 comments. Handler ordering means a tool removed by one extension can still be advertised to the model if another handler forces a system prompt. This is an extension-composition correctness bug with real security-adjacent implications (capability leakage).
3. **[#11317](https://github.com/earendil-works/pi/issues/11317) [OPEN] Let SDK users customize the identity preamble without replacing default sections** — 5 comments. Requests `systemPromptOptions.sections.preamble` so personas can be set without hand-rebuilding Pi's tools/rules/docs. Paired with [PR #11904](https://github.com/earendil-works/pi/pull/11904) (already closed).
4. **[#11869](https://github.com/earendil-works/pi/issues/11869) [OPEN] [bug, inprogress, prio:p0] New `StringEnum` in Pi 1.5.0 breaks compile-time typing** — 2 👍, the only p0 still open. `registerTool` derives `TParam` via typebox's `TSchema`, but the new `StringEnum` no longer does, so enum fields become `unknown`. Type safety in the tool-authoring surface is a headline regression.
5. **[#10585](https://github.com/earendil-works/pi/issues/10585) [OPEN] bash temp-file stream errors still crash the whole process on 1.0.4** — 4 comments. An `ENOSPC` on a full `/tmp` produces an uncaught exception and loses the in-flight session, with no tool error surfaced. Durability concern for long agent runs.
6. **[#10650](https://github.com/earendil-works/pi/issues/10650) [OPEN] codemode: preserve nested `Error` messages in `allSettled` output** — 4 comments. Rejections serialize to `reason: {}`, so the model reasons over missing information. Directly degrades codemode's self-correction loop.
7. **[#9817](https://github.com/earendil-works/pi/issues/9817) [CLOSED] [no-action] Extensions can't resolve npm packages declared via `package.json` `main`/`exports`** — 5 comments, 1 👍. Only packages with a root `index.js` resolve; `yaml` and similar fail at runtime. Closed without action, which leaves a sharp edge for the extension ecosystem.
8. **[#11858](https://github.com/earendil-works/pi/issues/11858) [CLOSED] [no-action] codemode script repeated an identical tool call 24,018 times** — 3 comments. ~680 calls/s for 35 s, each reading a 28 KB file, stopped only by the wall-clock abort. No per-script repetition guard; a strong signal that sandbox guardrails are thin.
9. **[#11876](https://github.com/earendil-works/pi/issues/11876) [CLOSED] `edit` and `read` tools are bypassed by shell commands** — 3 comments. On Windows 11 / 1.5.0 the model reached for `sed`, sidestepping tool instrumentation and rewriting line endings. Underscores the need for tool-preference steering.
10. **[#8544](https://github.com/earendil-works/pi/issues/8544) [CLOSED] `edit` rewrites line endings of untouched lines in mixed CRLF/LF files** — 3 comments. Editing one character silently mutates bytes on other lines. Fixed by [PR #11751](https://github.com/earendil-works/pi/pull/11751); the related non-UTF-8 case is split out.

*Also notable:* [#11676](https://github.com/earendil-works/pi/issues/11676) (open, inprogress) `Harness.close()` retains provider WebSocket + idle timer; [#11863](https://github.com/earendil-works/pi/issues/11863) asks for context-management guidance for long-running embedded agents and was closed no-action.

---

## 4. Key PR Progress

1. **[#11751](https://github.com/earendil-works/pi/pull/11751) fix(coding-agent): edit keeps line endings of untouched lines** — Rewrites only around the actual change and avoids the post-write diff OOM. The highest-impact data-integrity fix of the day.
2. **[#11896](https://github.com/earendil-works/pi/pull/11896) fix(coding-agent): wait for image resize worker to terminate** — `void worker.terminate()` in `finally` let the next worker start mid-teardown; awaiting it stops the macOS arm64 / Node 24 process aborts.
3. **[#11891](https://github.com/earendil-works/pi/pull/11891) fix(ai): omit empty `tools` array for tool history on `openai-completions`** — Unblocks vLLM 0.20+ and LiteLLM gateways that 400 on `"tools": []`.
4. **[#11890](https://github.com/earendil-works/pi/pull/11890) fix(tui): draw fullscreen Kitty images after the rows they cover** — Fixes Orca/WezTerm xterm.js tile rendering, where only the first row of a fullscreen image appeared.
5. **[#11888](https://github.com/earendil-works/pi/pull/11888) fix(coding-agent): record the live thinking level on the branch before each prompt** — Closes the stale-level-after-`/tree`-rewind resume bug ([#11451](https://github.com/earendil-works/pi/issues/11451)).
6. **[#11882](https://github.com/earendil-works/pi/pull/11882) openai-completions: split tool-call blocks when stream index and id disagree** — Prevents two distinct tool calls from being concatenated into one malformed argument blob by non-conforming providers.
7. **[#11895](https://github.com/earendil-works/pi/pull/11895) fix(coding-agent): match grep globs relative to `path`** — Glob with a folder component now works when `path` isn't the cwd, instead of returning a false "No matches found".
8. **[#11873](https://github.com/earendil-works/pi/pull/11873) fix(coding-agent): keep apostrophes in prompt template arguments** — `/ask why doesn't the user's cache work` no longer becomes `why doesnt the users cache work`.
9. **[#11885](https://github.com/earendil-works/pi/pull/11885) fix(ai): retry streams cut short after the reply had begun** — Adds the Magpie/Anthropic mid-reply truncation message to `RETRYABLE_PROVIDER_ERROR_PATTERN`.
10. **[#11894](https://github.com/earendil-works/pi/pull/11894) [OPEN] fix(coding-agent): edit refuses files that are not valid UTF-8** — Currently the tool silently substitutes U+FFFD and reports success on UTF-16/GBK/Windows-1252 files. Still open; worth watching as the companion to #11751.

*Also merged/closed:* [#11904](https://github.com/earendil-works/pi/pull/11904) custom system prompt preamble, [#11887](https://github.com/earendil-works/pi/pull/11887) fullscreen stderr corruption, [#11893](https://github.com/earendil-works/pi/pull/11893) `reasoning_effort` now routed through `thinkingLevelMap`, [#11874](https://github.com/earendil-works/pi/pull/11874) Windows Bun virtual entry path, [#11900](https://github.com/earendil-works/pi/pull/11900) CONTRIBUTING RFC link. *Still open:* [#11889](https://github.com/earendil-works/pi/pull/11889) Windows console-close shutdown cleanup, [#11878](https://github.com/earendil-works/pi/pull/11878) codemode local `tools`/`console` declarations.

---

## 5. Hot Discussions

**Show and tell**
- **[#11870](https://github.com/earendil-works/pi/discussions/11870) How do you keep skills organized across Pi and other agents?** (1 👍, 0 comments) — Duplicate skills across Pi, Claude Code, and OpenCode directories drift apart; the author built a macOS app + CLI, **AgentKeeper**, to catalog them. No replies yet — an open invitation for the community to compare approaches to cross-agent skill/package management.

---

## 6. Feature Request Trends

- **SDK/embedding control surfaces.** The clearest cluster: replaceable identity preamble without losing default sections ([#11317](https://github.com/earendil-works/pi/issues/11317), [PR #11904](https://github.com/earendil-works/pi/pull/11904)), correct handler ordering for forced prompts vs. active tools ([#9932](https://github.com/earendil-works/pi/issues/9932)), per-conversation compaction policy in `pi-durable` ([#11771](https://github.com/earendil-works/pi/issues/11771)), queue-intent disposition reporting ([#11872](https://github.com/earendil-works/pi/issues/11872)), and documented patterns for long, tool-heavy multimodal sessions ([#11863](https://github.com/earendil-works/pi/issues/11863)). Users are past "can I embed Pi" and into "can I govern it."
- **Model catalog currency.** Three separate requests to add models: `k3.1-pro` / video modality for `kimi-coding` ([#11883](https://github.com/earendil-works/pi/issues/11883)), `glm-5.3-flashx` for `zai` ([#11884](https://github.com/earendil-works/pi/issues/11884)), and `deepseek-v4.1-flash-1017` for `qw` ([#11877](https://github.com/earendil-works/pi/issues/11877)). All closed no-action — suggesting a maintained catalog-update path is still missing.
- **Codemode robustness and sandboxing.** Error fidelity in serialization ([#10650](https://github.com/earendil-works/pi/issues/10650)), correct `NaN`/`Infinity` printing ([#11901](https://github.com/earendil-works/pi/issues/11901)), repetition guardrails ([#11858](https://github.com/earendil-works/pi/issues/11858)), and shadowed parameter names ([#11878](https://github.com/earendil-works/pi/pull/11878)).
- **Accessibility and terminal fidelity.** Screen-reader-safe rendering ([#1188](https://github.com/earendil-works/pi/issues/1188)), fullscreen inline images across Kitty-capable terminals ([#11835](https://github.com/earendil-works/pi/issues/11835)), and out-of-band stderr isolation ([PR #11887](https://github.com/earendil-works/pi/pull/11887)).
- **Windows parity.** SIGHUP-only shutdown handling ([PR #11889](https://github.com/earendil-works/pi/pull/11889)) and the Bun SEA `B:/~BUN/root/` entry path leaking into subagent argv ([#11865](https://github.com/earendil-works/pi/issues/11865)).

---

## 7. Developer Pain Points

- **Silent file corruption from `edit`.** Mixed line endings ([#8544](https://github.com/earendil-works/pi/issues/8544)) and non-UTF-8 encodings ([#11894](https://github.com/earendil-works/pi/pull/11894)) both produce "success" while mutating bytes the user never touched. Trust in the edit tool is the recurring theme.
- **Unhandled errors that kill the process, not the tool call.** `ENOSPC` on a full `/tmp` ([#10585](https://github.com/earendil-works/pi/issues/10585)) and the worker teardown race ([#10695](https://github.com/earendil-works/pi/issues/10695)) both lose the in-flight run. Users expect per-tool error boundaries; they're getting process aborts.
- **Model-hostile output from codemode.** `reason: {}`, `avg null`, and 24k unguarded repeated calls all feed the model bad state or burn tokens with no guardrail ([#10650](https://github.com/earendil-works/pi/issues/10650), [#11901](https://github.com/earendil-works/pi/issues/11901), [#11858](https://github.com/earendil-works/pi/issues/11858)).
- **Provider non-conformance leaking into Pi.** Empty `tools: []` ([#11843](https://github.com/earendil-works/pi/issues/11843)), duplicate stream `index` ([#11881](https://github.com/earendil-works/pi/issues/11881)), expired Copilot tokens returning 401/403 ([#10238](https://github.com/earendil-works/pi/issues/10238)), and Kimi K2.7 printing thinking as the reply ([#10237](https://github.com/earendil-works/pi/issues/10237)) — all closed, several no-action.
- **Extension ecosystem friction.** npm packages using `main`/`exports` fail to resolve ([#9817](https://github.com/earendil-works/pi/issues/9817)), and `before_agent_start` handler ordering is unspecified ([#9932](https://github.com/earendil-works/pi/issues/9932)). Extension authors are absorbing platform gaps.
- **Resource and state lifecycle bugs in embedded use.** `Harness.close()` leaving provider sessions and WebSockets alive ([#11676](https://github.com/earendil-works/pi/issues/11676)) and stale thinking levels after `/tree` ([#11451](https://github.com/earendil-works/pi/issues/11451)) are the kind of defects that only surface in long-lived host processes.

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code Community Digest — 2026-11-07

## 1. Today's Highlights

Qwen Code shipped **v0.26.9**, headlined by a full skill installation and activation pipeline (ZIP uploads, daemon APIs, TypeScript SDK support, bulk activation, and recovery protections), alongside a same-day nightly. Issue traffic remains dominated by **session/transcript durability** problems — a P1 unopenable-session bug from quadratic snapshot growth (#13113) and a P1 headless auto-memory write denial (#15597) are both still open. Meanwhile the managed-agent surface is expanding fast, with two large PRs adding Skills/MCP self-service (#16500) and execution diagnostics plus an agent graph (#16498).

---

## 2. Releases

**v0.26.9** — [Release](https://github.com/QwenLM/qwen-code/releases)
- No known breaking changes.
- **Skill installation and activation**: skill ZIP uploads, daemon APIs, TypeScript SDK support, bulk activation, and recovery protections make installing and managing skills a first-class workflow rather than a manual file operation.

**v0.26.9-nightly.20261106.80fd7c3353** — [Release](https://github.com/QwenLM/qwen-code/releases)
- Nightly cut from `release/v0.26.9-nightly.20261106.80fd7c3353`.
- Test-infrastructure work: interactive tests now poll the foreground-shell frame instead of snapshotting it ([#16455](https://github.com/QwenLM/qwen-code/pull/16455)), plus managed-agent test adjustments.

---

## 3. Hot Issues

1. **[#13113](https://github.com/QwenLM/qwen-code/issues/13113) — Session becomes unopenable: "Transcript snapshot is too large to index" (P1, 11 comments)**
   `file_history_snapshot` grows quadratically and the 256 MiB index limit is hardcoded, so long sessions with many file touches become permanently unloadable — every `POST /session/:id/load` fails. This is the highest-severity durability bug in the tracker: it is silent, unrecoverable from the user's side, and blocks the core value proposition of long-running agent sessions.

2. **[#15597](https://github.com/QwenLM/qwen-code/issues/15597) — Headless `-p` auto-memory can never save (P1, 8 comments)**
   In non-interactive mode with default/Auto approval, the implicit `edit`/`write_file` deny overrides the memory agents' scoped allow. Memory runs after every prompt, writes nothing, and the user still pays for the tokens. A permission-precedence bug with a direct cost impact.

3. **[#16418](https://github.com/QwenLM/qwen-code/issues/16418) — Repeated auto-compaction turns a review-only skill into unauthorized implementation (P1, 4 comments)**
   A long-running session invoked `/ce-doc-review` (review + limited safe fixes only) and ended up implementing the plan in application source. Filed as Critical: this is a **scope-adherence** failure that surfaces only under compaction, which makes it a trust and safety concern rather than a routine bug.

4. **[#16508](https://github.com/QwenLM/qwen-code/issues/16508) — Idle-time auto-compaction (P2, 3 comments)**
   Proposal to run the summarizer while the user is away instead of mid-turn, so compaction latency never lands inside a user-visible turn and the next turn starts from an already-compacted, cacheable prefix. Filed 2026-11-07 and already paired with a design doc PR (#16509) — the fastest-moving idea in the tracker.

5. **[#14724](https://github.com/QwenLM/qwen-code/issues/14724) — Main CI failed: Managed Agent gate, no test identified (218 comments)**
   The single highest-traffic issue in the repo, open since 2026-10-19 and still accumulating bot comments. Symptomatically, the CI failure reporter cannot attribute a failing test, so the issue can never self-close. The comment count is a signal about **bot noise and CI triage debt**, not community interest.

6. **[#15412](https://github.com/QwenLM/qwen-code/issues/15412) — Long project paths prevent chat recording and break the sessions list (P2, 7 comments)**
   The per-project session directory is derived from the full absolute working directory and flattened into a single path component, which can exceed filesystem limits on macOS. Deeply nested monorepos silently lose chat history.

7. **[#15310](https://github.com/QwenLM/qwen-code/issues/15310) — Tell the model which shell it uses instead of letting it assume bash (P3, 6 comments)**
   On Windows, commands run through `cmd.exe` but nothing in the system prompt says so, so the model emits POSIX commands that cannot work. The reporter observed two wasted turns in a single unattended run — a cheap prompt fix with outsized reliability payoff.

8. **[#16417](https://github.com/QwenLM/qwen-code/issues/16417) — Reject an oversized `write_file` before generating it (P2, 3 comments)**
   A `write_file` larger than the remaining output budget is generated in full and only then rejected with `Response truncated due to token limit`. Nothing is written and the whole turn is lost; the reporter hit it three times on the same file. Clear argument for a pre-flight check against the output budget.

9. **[#15800](https://github.com/QwenLM/qwen-code/issues/15800) — Background shell status file stays "running" forever after abrupt session end (P2, 3 comments)**
   The background process completes, but its persisted status file never updates, leaving stale state that later sessions and tooling read as live. Part of a cluster of background-automation lifecycle gaps.

10. **[#16444](https://github.com/QwenLM/qwen-code/issues/16444) — CLI background-view auto-surface UX edges (P3, 4 comments)**
    Deferred from #16432: an auto-opened dialog becomes the Ctrl+C target, so the first interrupt dismisses the dialog instead of reaching the composer. Representative of the repo's disciplined "defer, don't drop" review culture — non-blocking findings are filed rather than lost.

*Also worth noting:* [#15675](https://github.com/QwenLM/qwen-code/issues/15675) (sed simulation diverges from real `sed` — `.` doesn't match `\r`, emoji handling differs) closed after 5 comments; [#15833](https://github.com/QwenLM/qwen-code/issues/15833) closed — cancelling a shell command used to tell the model "the tool had already completed."

---

## 4. Key PR Progress

1. **[#16500](https://github.com/QwenLM/qwen-code/pull/16500) — feat(managed-agent): Skills and MCP self-service**
   Lets managed-agent operators manage Skills and MCP servers themselves rather than through maintainer intervention. The largest surface expansion in this window and the natural follow-on to the v0.26.9 skill pipeline.

2. **[#16498](https://github.com/QwenLM/qwen-code/pull/16498) — feat(managed-agent): execution diagnostics and agent graph**
   Adds persisted, session-owned tool/Skill usage, native file-operation and line-change measurements, and an authorized agent-relationship view. Notably keeps conversation outcomes separate from physical execution evidence, so a successful write stays visible after cancellation.

3. **[#16432](https://github.com/QwenLM/qwen-code/pull/16432) — fix(cli): surface workflow agent approvals in the interactive TUI (#16425)**
   Fixes a deadlock: a background workflow agent hitting a tool-approval gate parked the approval with nothing able to answer it, so the run never progressed. The registry now requires a real responder before parking.

4. **[#16404](https://github.com/QwenLM/qwen-code/pull/16404) — fix(cli): surface stacked-skill overflow in non-interactive and ACP runs**
   Warns when a prompt stacks more than five skills. Headless runs write to stderr (including under JSON/stream-JSON), ACP clients get a live notice before the model response, and channel bridges preserve it across tool round-trips.

5. **[#16439](https://github.com/QwenLM/qwen-code/pull/16439) — fix(core): replay the persisted startup prelude on resume**
   Resumed sessions now replay the exact startup prelude originally sent instead of rebuilding it from the current date, directory tree, git snapshot, MCP instructions, skills, and deferred-tool list. Directly improves prefix-cache reuse and resume determinism.

6. **[#16509](https://github.com/QwenLM/qwen-code/pull/16509) — docs(core): design the idle-time auto-compaction pass**
   Bilingual design doc for #16508, fixing the reviewable contract: dual-condition trigger (idle duration + soft watermark). Landed the same day as the issue — an unusually fast issue-to-design loop.

7. **[#16507](https://github.com/QwenLM/qwen-code/pull/16507) — perf(core): compare a grown local journal prefix in chunks**
   Follow-up to #16483: a file that grew during the read is now compared in 64 KiB chunks instead of re-reading wholesale. Journal-path performance hardening that matters for large managed sessions.

8. **[#16352](https://github.com/QwenLM/qwen-code/pull/16352) — fix(managed-agent): mask digit-led passwords and refuse empty Harness user info**
   Closes review items from #16110/#16175 in the upgrade command's `start`/`verify` phases: a refusal previously hid everything up to a URL's last `@`, including when the URL parsed without user info. Credential-leak hardening.

9. **[#16461](https://github.com/QwenLM/qwen-code/pull/16461) — feat(cli): add `ui.startExpanded`**
   Boolean (default `false`) that starts the TUI with thinking blocks and tool output expanded. Fills the gap left by retiring `ui.compactMode`, which removed any way to say "I always want the reasoning visible."

10. **[#15852](https://github.com/QwenLM/qwen-code/pull/15852) — ci(release): only file issues on failure and reconcile stale ones**
    Both fan-out release notifiers treated a cancelled run as a failure and filed an issue, and neither could close it after a later attempt shipped. Directly targets the stale-issue accumulation visible in #14724.

*Also merged/active:* [#16489](https://github.com/QwenLM/qwen-code/pull/16489) closed — a late snapshot publication now waits for the in-flight claim instead of being rolled back as a deadlock victim per page; [#16492](https://github.com/QwenLM/qwen-code/pull/16492) adds the deferred-acquire-vs-close acceptance test requested by #14674; [#16482](https://github.com/QwenLM/qwen-code/pull/16482) routes the `@qwen-code /review` and `/resolve` permission gate to the self-hosted ECS pool; [#14353](https://github.com/QwenLM/qwen-code/pull/14353) honors configured loading phrases in the Web Shell.

---

## 5. Hot Discussions

No discussion data was provided for this window, so this section is omitted.

---

## 6. Feature Request Trends

- **Skills as a managed product surface.** v0.26.9's ZIP upload / bulk activation / SDK support, plus #16500 (Skills and MCP self-service) and #16404 (stacked-skill overflow warnings), show skills moving from a prompt convention to a governed, quota-aware platform feature. Expect pressure next on skill permissions and provenance.
- **Context and compaction performance.** #16508/#16509 (idle-time compaction), #16439 (prelude replay for cache reuse), and #16417 (pre-flight output-budget checks) all attack the same cost center: wasted tokens and latency inside a turn. Idle-time compaction is the clearest emerging architectural direction.
- **Managed-agent observability.** #16498 (execution diagnostics, agent graph), #14629 (OpenRouter routing provider and generation ID as span attributes), and #15414 (dropped cache-write token counts) form a consistent ask for richer, more truthful telemetry.
- **Session durability at scale.** #13113, #15412, #15800, and #14674 all concern state that must survive long sessions, deep paths, and abrupt termination.
- **Shell correctness across platforms.** #15310 (declare the shell in the system prompt), #15675 (sed simulation fidelity), and #16449 (amend-guard recognizer coverage) point at the shell tool being the most common source of silent divergence from real-world behavior.
- **Web Shell / UI ergonomics.** #16474 (selection-anchored inline branch threads), #13911 (auto-recap ignores the setting), #16471 (managed-host review deferrals), and #14353 (custom loading phrases) show the companion UI maturing from prototype to configurable product.
- **Provider breadth.** #16506 requests a Wally (RunAnywhere) `OpenAI`-compatible provider preset, continuing steady demand for first-class presets rather than manual endpoint config.

---

## 7. Developer Pain Points

- **CI noise is drowning the issue tracker.** #14724 has 218 comments from an automated reporter that cannot identify a failing test, and #16191 and #13953 show the same pattern at smaller scale. Several PRs (#15852, #16319, #16482) are explicitly about making CI reporting quieter and self-reconciling — maintainers are treating this as a real tax on triage.
- **Permission semantics contradict each other.** #15597 (implicit deny overriding a scoped allow in headless mode) and #16418 (a review-only skill escalating to source implementation after repeated compaction) are the same underlying complaint: the model's effective permissions are not predictable from the configuration the user wrote.
- **Silent, unrecoverable session loss.** #13113 (unopenable transcript), #15412 (path length breaks recording), and #15800 (stale "running" status) all fail without a clear error at the moment of damage. Users discover the loss later, which is the worst failure mode for a coding agent.
- **Wasted turns from avoidable mistakes.** #15310 (wrong shell assumed), #16417 (oversized write generated then discarded), and #16418 (compaction-induced drift) each burn a full model round-trip. In unattended runs these compound quickly and are the most common motivation cited in feature requests.
- **Latency landing inside the user's turn.** Compaction fires mid-turn today, so users feel summarization as a stall. This is the single most consistently cited performance complaint and the reason #16508 moved from issue to design doc in one day.
- **Background execution lacks a lifecycle.** #15800 (status never clears) and #16444 (auto-opened dialog steals Ctrl+C) show background shells and background agents working in the happy path but leaving stale or hijacked state at the edges.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/SnowEagle-Kn/agents-radar).*