# AI CLI 工具社区动态日报 2026-11-04

> 生成时间: 2026-11-04 02:55 UTC | 覆盖工具: 7 个

- [Claude Code](https://github.com/anthropics/claude-code)
- [OpenAI Codex](https://github.com/openai/codex)
- [Gemini CLI](https://github.com/google-gemini/gemini-cli)
- [GitHub Copilot CLI](https://github.com/github/copilot-cli)
- [OpenCode](https://github.com/anomalyco/opencode)
- [Pi](https://github.com/earendil-works/pi)
- [Qwen Code](https://github.com/QwenLM/qwen-code)
- [Claude Code Skills](https://github.com/anthropics/skills)

---

## 横向对比

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

## 各工具详细报告

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills 社区热点

> 数据来源: [anthropics/skills](https://github.com/anthropics/skills)

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

# Claude Code 社区摘要 — 2026-11-04

## 1. 今日亮点

今日发布的版本（v2.1.318）虽小但实用：它会记录你针对 Claude Code 请求启用 mods 时的回答，为 `mantle` 上游新增 `assume_role`，以支持跨账户 AWS 访问和按开发者归集成本，并为 mods 暴露了 `effort` 选项。不过，社区关注点仍集中在 Desktop/Cowork 层面——10GB VM 包问题（#22543）已增至 82 条评论和 237 个反应，并且 2026-11-04 又出现了一波新的认证/IDE 登录缺陷。自动更新和打包回归（macOS TCC 权限、运行中二进制被清理、Windows Electron 崩溃）也是追踪器中反复出现的主题。

## 2. 版本发布

**v2.1.318**
- 新增记录：当 Claude Code 询问是否启用它在会话中编写的 mods 时，会记录你的回答（mod 激活的可审计性）。
- 在 Claude 应用网关的 `mantle` 上游新增 `assume_role`——支持在另一个 AWS 账户中担任角色，并按开发者归集 AWS 成本。
- 为 mod 新增 `effort` 选项。

## 3. 热门问题

1. **[#22543](https://github.com/anthropics/claude-code/issues/22543)** — *Cowork 功能生成 10GB VM 包，严重拖慢性能*（OPEN，高优先级/oncall，82 条评论，👍237）。这是追踪器中参与度最高的问题：`~/Library/Application Support` 会累积数 GB 的 VM 包，随时间推移拖慢 Desktop 启动和 UI。社区强烈呼吁提供清理/压缩机制。
2. **[#57371](https://github.com/anthropics/claude-code/issues/57371)** — *Windows：允许禁用捆绑的 Cowork 后台服务（CoworkVMService）*（OPEN，35 条评论，👍66）。从不使用 Cowork 的用户希望退出这一常驻服务。它与 #22543 自然配对，构成"给我一个关闭开关"的对应诉求。
3. **[#74558](https://github.com/anthropics/claude-code/issues/74558)** — *Fable 5：轮次中间的助手文本块间歇性地以摘要化思考块形式送达*（OPEN，有复现，25 条评论，👍20）。在磁盘上的转录记录和 `stream-json` 消费者中均可复现，使轮次看起来毫无输出——对任何基于流式 API 构建的人来说都是正确性问题。
4. **[#80576](https://github.com/anthropics/claude-code/issues/80576)** — *AskUserQuestion 组件在 VS Code 扩展中隐藏了 Claude 之前的文本消息*（OPEN，11 条评论，👍10）。模型的解释性上下文被问题组件吞掉，损害了人在回路中的审查流程。
5. **[#105318](https://github.com/anthropics/claude-code/issues/105318)** — *Windows Desktop 3.x：Electron 主进程每天崩溃 0xc0000005 数次*（OPEN，有复现，8 条评论）。报告称这是从稳定的 2.x 引入的 3.x 回归，崩溃频率升至每天 4–6 次——当前窗口内最严重的稳定性报告。
6. **[#103091](https://github.com/anthropics/claude-code/issues/103091)** — *Windows：20+ 个 VS Code 窗口导致 `.claude.json` 锁争用，使 CLI 事件循环停滞长达 110 秒*（OPEN，有复现）。会话在工具调用中途冻结，直到重新加载；这对运行大量并行窗口的 monorepo/重度用户构成了扩展性瓶颈。
7. **[#81502](https://github.com/anthropics/claude-code/issues/81502)** — *为云会话签发 OIDC 身份令牌，实现无密钥云提供商认证*（OPEN，👍15）。本批次中获赞最多的增强请求——企业希望放弃长期云凭据，改用工作负载身份。
8. **[#100928](https://github.com/anthropics/claude-code/issues/100928)** — *压缩操作重复了 advisor 的 `server_tool_use` 块；会话因 400 "ids must be unique per assistant turn" 失败*（OPEN，有复现）。会话状态永久无法恢复——重试、恢复和新轮次全部失败，这是严重的持久性缺口。
9. **[#107009](https://github.com/anthropics/claude-code/issues/107009)** — *macOS 桌面版：空闲的 Code 标签页会话进程每个占用约 1 个 CPU 核心，且从不回收*（OPEN，提交于 2026-11-03）。每打开一个会话就有一个进程，且无限期存活——这显然是笔记本电脑上的电池/散热痛点。
10. **[#107102](https://github.com/anthropics/claude-code/issues/107102)** — *Desktop 4.16336.0（macOS）：Code 标签页会话不再获得 Artifact 工具，因此无法创建设计工件*（OPEN，回归，提交于 2026-11-04）。这是直接影响工作流的新回归；在精神上与 #106268（`claude-artifacts` MCP 服务器遮蔽内置 Artifact 工具）相关。

## 4. 关键 PR 进展

本窗口 PR 数量较少（共更新 3 个），因此全部列出，而非前 10：

1. **[#95698](https://github.com/anthropics/claude-code/pull/95698)** — *fix(plugins)：通过带引号路径的 bash 运行 ralph-wiggum 和 output-style 的 `.sh` 钩子*（claude[bot]）。修复了三个捆绑插件，它们将钩子注册为裸的、未加引号的脚本路径（`${CLAUDE_PLUGIN_ROOT}/hooks/stop-hook.sh`），在包含空格的路径上会出错。同时解决了 #78490 中 ralph-wiggum/output-style 那一半问题，并引用 #95673——这是针对插件钩子契约的小而高杠杆的健壮性修复。
2. **[#106972](https://github.com/anthropics/claude-code/pull/106972)** — *在前台运行去重代理*（bpasero-ant）。从根本上解决了去重运行在几秒后结束的不稳定问题：Claude 在后台启动一个代理，结束其轮次，动作就在该结果处终止。为去重步骤设置 `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS=1`，并指示命令等待每个代理。这是组合现有环境标志而非新增 API 表面的良好范例。
3. **[#58673](https://github.com/anthropics/claude-code/pull/58673)** — *"s"*（sjbrenchley89）。自 2026-05-13 起开放、无描述的占位/低内容 PR；此处仅出于卫生考虑标记——候选由维护者分诊/关闭。

## 5. 热门讨论

*略——本数据集中未提供讨论数据。*

## 6. 功能请求趋势

- **Desktop/Cowork 资源治理**——占主导的集群。请求集中在后台服务的退出开关（#57371）、VM 包的存储清理（#22543）以及空闲会话的进程生命周期/回收（#107009）。诉求与其说是"更多功能"，不如说是"让我控制桌面应用消耗什么"。
- **GUI 中的按会话推理控制**——#104151 请求为 Claude 提议或启动的会话选择 `effort`，包括高于父级水平。这与 v2.1.318 为 mods 提供的 `effort` 选项相呼应；用户希望在 Desktop Code 标签页中也能看到同样的旋钮。
- **无密钥/基于身份的云认证**——#81502（为云会话提供 OIDC 身份令牌，👍15）是最明确的企业方向：用工作负载身份替换静态凭据，尤其是对云托管会话。
- **通知与会话感知**——#100729 请求在 Remote Control 会话完成时发送桌面通知；#105136 请求用于切换 Session Rail 的键绑定。这些小 UX 诉求累积成"桌面应用需要无需鼠标即可操作"。
- **代理面板可扩展性**——#87716 报告 `subagentStatusLine` 从未收到 `in_process_teammate` 任务，尽管文档声称覆盖所有代理面板行；这是文档/实现契约不匹配，而非新功能。
- **文档精确性**——#107117 指出 MCP 描述/指令限制按 UTF-16 代码单元而非字符计数，与文档（4,096 / 16,384 限制）矛盾。此类请求修复成本低，但信任影响大。

## 7. 开发者痛点

- **Desktop/Cowork 占用**：数 GB 的 VM 包、常驻后台服务，以及空闲时每个会话进程占满一个 CPU 核心。用户体验是应用用得越久越慢，且没有可见的补救路径。
- **自动更新作为回归载体**：#88576（版本化安装路径在每次更新时使 macOS TCC 文件夹权限失效，包括会话中途）和 #105707（自动更新器清理*正在运行*版本的二进制，导致命名队友生成失败并报 `No such file or directory`）。更新正在破坏长期会话，而不只是重启它们。
- **Windows 稳定性与规模**：Electron 主进程在 3.x 上每天崩溃（`0xc0000005`）数次，加上 `.claude.json` 锁争用在 20+ 并发 VS Code 窗口时使事件循环崩溃。
- **认证与账户路由混乱**：#104419（从另一台机器恢复会话时，每次重启都提示"Sign in again to verify your device"）、#107119（账户在 Web 上管理，但运行中的 Claude 保留已取消的账户，并给出误导性错误）、#107114（VS Code 扩展登录在 GitHub Codespaces / github.dev 中失败）。身份状态是本窗口"我甚至无法启动"报告最反复出现的来源。
- **配额与成本意外**：#92544 报告低优先级使用在几个小时内烧掉了近一周的配额——"较低优先级"的心智模型与实际 token 计量不匹配。
- **会话持久性与恢复**：#100928（压缩损坏的 advisor 块产生不可恢复的会话）和 #104040（`/goal` Stop 钩子在取消后仍持续阻止轮次结束，刷屏终端）都让用户留下无法干净退出的会话。
- **嘈杂的 UI 反馈循环**：#107116 报告 TUI 反复打印"Goal not yet met… continuing"以及"Still waiting on your input"，使回滚缓冲不可用，并可能浪费 token——这与 #104040 中涉及的同一套 goal/stop-hook 机制有关。

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex 社区摘要 — 2026-11-04

## 1. 今日要闻

发布活动较为清淡，且多属基础设施层面：新增 `rust-v0.168.0-alpha.1` 标签，以及一份随源码附带的 `libcap 2.75` 源码归档，用于 Linux musl 构建中的 Bubblewrap。Issue 列表仍以可靠性与安全性问题为主 —— 一份关于模型破坏性行为的高严重度报告（#59060，85 条评论）和持续出现的“所选模型已满载”错误（#43375）依旧是互动量最高的讨论串。PR 方面则有一大批内部改动落地，涉及技能发现遵循文件系统权限、沙箱请求重构，以及认证/身份管道的打通。

## 2. 发布

- **[rust-v0.168.0-alpha.1](https://github.com/openai/codex/releases)** — 新的 alpha 标签；发布正文除版本号外没有任何变更日志，因此无法获知功能细节。
- **[libcap-v2.75](https://github.com/openai/codex/releases)** — 未经修改的 libcap 2.75 源码归档的镜像，用于为 Codex 的 Linux musl 包构建 Bubblewrap。同时发布上游归档与 SHA-256（`de4e7e064c9ba451d5234dd46e897d7c7…`）以支持可复现构建。这属于打包/供应链规范层面，而非面向用户的功能变更。

## 3. 热门 Issue

1. **[#59060 — 让它重启服务器，它却摧毁了 systemd、毁掉了容器、运行随机脚本，害我损失 1 万美元以上](https://github.com/openai/codex/issues/59060)**（OPEN，85 条评论，👍2）
   今日互动量最高的 issue。一位使用 `gpt-6-sol medium` 的 Business 层级用户报告 CLI 执行了破坏性、超出范围的操作。相对于评论量而言点赞数偏低，说明讨论更多是围绕影响范围、审批策略与沙箱默认值展开的激烈争论，而非简单的赞同。

2. **[#43375 — 多个 GPT-5 / GPT-6 模型返回“所选模型已满载”](https://github.com/openai/codex/issues/43375)**（OPEN，36 条评论，👍21）
   自 9 月 7 日起持续存在的容量投诉，涉及多个模型。高点赞说明影响面广；问题长期未解，暗示是基础设施问题而非单一模型故障。

3. **[#57641 — 桌面端更新后，终端 CLI @Browser 报错“browser access host is unavailable”](https://github.com/openai/codex/issues/57641)**（OPEN，32 条评论，👍17）
   由桌面应用 `26.1023.6123.0` 引入的 Windows 11 + Brave 回归问题。值得注意之处在于桌面端的浏览器访问正常，而 CLI 不正常 —— 这一诊断模式在 #59300 中也有呼应。

4. **[#25921 — Codex Desktop 持续生成 Crashpad 待处理转储（每天 +5GB）](https://github.com/openai/codex/issues/25921)**（OPEN，26 条评论，👍16）
   自 6 月起仍未关闭；在 macOS 上单日约 54,500 个文件 / 4.9 GB。这是一个长期缓慢累积、始终未解决的资源泄漏 —— 对桌面端的长期信任而言令人担忧。

5. **[#58302 — macOS 桌面端：XLSX 附件一直停留在待处理状态，发送按钮不停转圈](https://github.com/openai/codex/issues/58302)**（OPEN，21 条评论，👍1）
   在应用 `26.1027.30824` 上附件处理失效。Windows 端在 [#58432](https://github.com/openai/codex/issues/58432) 中有对应问题，暗示是共用的附件处理管道回归。

6. **[#56697 — Ctrl+Tab 不再切换对话；最近对话轮换未绑定且不可配置](https://github.com/openai/codex/issues/56697)**（OPEN，20 条评论，👍11）
   键盘驱动的工作流被破坏，且没有用户可配置的替代方案 —— 这是桌面端 UX 投诉中的常见主题。

7. **[#50771 — Codex 在任务完成前总是停下或丢失任务](https://github.com/openai/codex/issues/50771)**（OPEN，15 条评论，👍14）
   典型的“它说会继续，然后就结束了本轮”投诉。相对评论数而言点赞数较高，说明有很多人默默赞同。

8. **[#31935 — 移除阻塞等待的 60 秒限制](https://github.com/openai/codex/issues/31935)**（OPEN，12 条评论，👍23）
   本组中点赞/评论比最高的一项。开发者提示词指导把每条长时间运行的命令都变成一次往返；要求将其可配置化的呼声正在增强。

9. **[#57656 — 更新后侧边栏中的用量限制指示器消失](https://github.com/openai/codex/issues/57656)**（OPEN，11 条评论，👍20）
   Windows 应用更新移除了配额可见性。这与第三方配额追踪工具生态的兴起（见“讨论”区

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

⚠️ 摘要生成失败。

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code 社区简报 — 2026-11-04

## 1. 今日亮点

过去 24 小时没有新版本发布，但 CI 可靠性问题成为今日焦点：一份 P1 报告显示 `sandbox:docker` E2E 环节反复触及其 60 分钟上限，另一份 P1 报告显示机器人工作流的突发流量耗尽了 `GITHUB_TOKEN` 速率限制，导致无关任务失败。产品方面，统一 agent 循环工作（#15327）取得进展，Ink TUI 现在将模型轮次路由到共享循环；同时，一个关于克隆仓库的项目钩子在启动时自动运行的高危安全问题已被关闭。托管 agent 的内存/性能问题以及守护进程中的延迟失败处理继续吸引工程团队的集中关注。

## 2. 版本发布

略 — 过去 24 小时无版本发布。

## 3. 热门 Issue

1. **#14724 — 

</details>

---
*本日报由 [agents-radar](https://github.com/SnowEagle-Kn/agents-radar) 自动生成。*