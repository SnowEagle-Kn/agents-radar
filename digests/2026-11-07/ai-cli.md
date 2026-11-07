# AI CLI 工具社区动态日报 2026-11-07

> 生成时间: 2026-11-07 02:20 UTC | 覆盖工具: 7 个

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

⚠️ 横向对比生成失败。

---

## 各工具详细报告

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills 社区热点

> 数据来源: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills 社区亮点报告
**来源：** github.com/anthropics/skills · 数据截至 2026-11-07

> **数据说明：** 在提供的摘录中，全部 50 个 PR 都显示 `Comments: undefined` 和 `👍: 0`，因此无法核实绝对互动量。以下排名依据仓库按评论数排序的顺序（50 个中的前 20 个），并结合更新时间的新近程度与关联 issue 做了交叉核对。数据集中的每个 PR 都是 **OPEN** 状态——本时间窗口内没有已合入的提交。

---

## 1. 热门 Skills 排名

| # | Skill / PR | 功能 | 讨论要点 | 状态 |
|---|---|---|---|---|
| 1 | **mcp-builder** — [#1742](https://github.com/anthropics/skills/pull/1742) | 编写与测试 MCP 服务器 | `mcp>=2.0.0` 存在兼容性破坏：`streamablehttp_client` → `streamable_http_client`，自定义 header 现在需通过 `create_mcp_http_client`/`http_client` 传入，而不再是关键字参数。这会导致 `skills/mcp-builder/scripts/connections.py` 失效；修复 #1668 | OPEN（更新于 2026-10-08） |
| 2 | **skill-creator** — [#1298](https://github.com/anthropics/skills/pull/1298) | 编写/优化 Skills；触发评测 | 触发评测报告了误判为未触发的问题：各 worker 并发探测命令相互竞争、在 Windows 上对子进程管道调用 `select()` 失败、无关工具中断扫描、运行时失败被静默计为未触发（负样本假通过） | OPEN（更新于 2026-09-16） |
| 3 | **notion-spec-to-implementation** + **quantitative-resume-auditor** — [#1245](https://github.com/anthropics/skills/pull/1245) | 规格 → Notion 任务拆解；简历审计 | 一次提交打包两个 Skill：一个企业工作流 Skill 加一个文档审计 Skill；审查周期较长，说明存在关于拆分范围的疑问 | OPEN（更新于 2026-10-15） |
| 4 | **webapp-testing** — [#1980](https://github.com/anthropics/skills/pull/1980) | 浏览器驱动的 Web 应用测试 | `with_server.py` 使用 `subprocess.Popen(..., shell=True)` 启动服务器以支持 `cd && cmd` 命令链 → 由 agent 提供的字符串导致 CWE-78 命令注入 | OPEN（更新于 2026-11-01） |
| 5 | **docx** — [#1792](https://github.com/anthropics/skills/pull/1792) | Word 文档编辑 / 修订跟踪 | `accept_changes.py` 在 LibreOffice 超时时仍报告成功；现在改为返回 Error，并校验输出中不残留 `w:ins`/`w:del`/`w:moveFrom`/`w:moveTo` | OPEN（更新于 2026-11-01） |
| 6 | **proofcore-contract-auditor** — [#1771](https://github.com/anthropics/skills/pull/1771) | Solidity/Rust 智能合约静态分析 | 通过厂商 Merkle 协议将审计证明锚定在 TON 上——这是一个依赖第三方的 Skill，很可能引发信任/中立性方面的审视 | OPEN（更新于 2026-09-16） |
| 7 | **pyxel** — [#525](https://github.com/anthropics/skills/pull/525) | Python 复古游戏开发 | 运行时间最长的活跃 PR（创建于 2026-03-05，更新于 2026-11-01）：无头输入驱动运行、帧检查、任务特定状态检查、拆分参考文档 | OPEN |
| 8 | **md2video-audio** — [#1703](https://github.com/anthropics/skills/pull/1703) | Markdown → 带旁白 MP4 | “零成本”的 Marp 幻灯片 + TTS 流水线；与现有 pptx/artifact 工具存在竞争 | OPEN（更新于 2026-09-15） |

**其他值得关注：** **document-typography** [#514](https://github.com/anthropics/skills/pull/514)（孤行/寡行控制、编号对齐——一个适用面很广的质量类 Skill）、**scnet-hpc** [#1615](https://github.com/anthropics/skills/pull/1615)（Slurm/SSH HPC 运维）、**xlsx** [#2019](https://github.com/anthropics/skills/pull/2019)（`external_links_at_risk` 中工作表级定义名称，修复 #2013）。

---

## 2. 社区需求趋势

**a) 信任、安全与命名空间治理——讨论最热烈的话题。**
[#492](https://github.com/anthropics/skills/issues/492)（46 条评论，远超其他 issue 居首）认为社区 Skills 以 `anthropic/` 命名空间发布，会带来冒充与权限提升风险。具体漏洞报告进一步印证了这一点：[#1394](https://github.com/anthropics/skills/issues/1394)（属性不安全的 `escapeHtml` → eval-viewer 中的展示路径 XSS）和 [#2018](https://github.com/anthropics/skills/issues/2018)（`merge_runs.py` 写入的 XML 含未绑定的命名空间前缀）。诉求：**带签名/来源溯源的 Skill 分发机制，以及加固过的默认脚本。**

**b) 可靠的评测框架。**
[#556](https://github.com/anthropics/skills/issues/556)（13 条评论，7 👍）报告 `run_eval.py` **在所有查询上的触发率均为 0%**；[#1390](https://github.com/anthropics/skills/issues/1390) 报告 `evaluation.py` 对**每个真实 MCP 服务器**的评分都是 **0/N**，同时还静默伪造工具错误。诉求：**真正能衡量 Skill 触发情况的评测**，而不只是评测框架的管道本身。

**c) 上下文经济 / token 效率。**
[#1487](https://github.com/anthropics/skills/issues/1487)：`claude-api` 在一次工具调用中急切注入约 156k tokens。[#202](https://github.com/anthropics/skills/issues/202)（已关闭）批评 skill-creator 的说明文字冗长、像文档。[#1329](https://github.com/anthropics/skills/issues/1329) 提议 `compact-memory`（用符号化表示记录 agent 状态）。诉求：**引用资料按需懒加载，Skill 正文保持紧凑。**

**d) 分发与团队工作流。**
[#228](https://github.com/anthropics/skills/issues/228)（17 条评论，8 👍）：希望支持组织级 Skill 共享，而不是通过 Slack 手动传 `.skill` 文件。[#189](https://github.com/anthropics/skills/issues/189)（6 条评论，9 👍——👍 比例最高）：`document-skills` 与 `example-skills` 两个插件安装的内容完全相同，造成上下文重复。诉求：**共享的 Skill 库与清晰的插件组合方式。**

**e) Skill 编写质量与元数据。**
[#2064](https://github.com/anthropics/skills/issues/2064)：`theme-factory` 的描述只说了它*做什么*，没说*何时*使用——这是触发精度上的缺陷。诉求：**能驱动正确调用的描述规范。**

**f) 流程与治理类 Skills。**
[#1385](https://github.com/anthropics/skills/issues/1385) 提议三道关口的质量流水线（校准 → 对抗式审查 → 交付验证）；[#412](https://github.com/anthropics/skills/issues/412)（已关闭）提议 `agent-governance`。需求真实存在，但接受态度谨慎。

---

## 3. 高潜力待合入 Skills

本窗口内所有 PR 均未合入，因此这里的“待定”指**最近 30 天内仍有活动、且范围明确、有望近期落地**：

| PR | Skill | 为何可能很快合入 |
|---|---|---|
| [#2019](https://github.com/anthropics/skills/pull/2019) | xlsx（外部链接解析） | 范围窄的修复，明确关闭 #2013，更新于 2026-11-03——是本批中活动最新的 |
| [#2108](https://github.com/anthropics/skills/pull/2108) | docs（快照状态） | 纯文档改动，引用了可核实的里程碑（#1447、#330），更新于 2026-11-03 |
| [#1980](https://github.com/anthropics/skills/pull/1980) | webapp-testing（shell 注入） | 带 CWE 引用的安全修复；更新于 2026-11-01 |
| [#1792](https://github.com/anthropics/skills/pull/1792) | docx（超时 + 输出校验） | 自包含的正确性修复；更新于 2026-11-01 |
| [#525](https://github.com/anthropics/skills/pull/525) | pyxel（复古游戏开发） | 持续维护 8 个月；2026-11-01 仍有更新 |
| [#1742](https://github.com/anthropics/skills/pull/1742) | mcp-builder（MCP 2.x） | 阻塞性的兼容性破坏；关闭 #1668 |
| [#1245](https://github.com/anthropics/skills/pull/1245) | notion-spec-to-implementation + quantitative-resume-auditor | 排名最高的*新 Skill* PR；更新于 2026-10-15 |
| [#1997](https://github.com/anthropics/skills/pull/1997) · [#1681](https://github.com/anthropics/skills/pull/1681) | skill-creator（YAML frontmatter 解析；`package_skill.py` 直接执行） | 两个独立的编写工具修复，范围都很窄且可测试 |

**规律：** 近期队列以针对内置 Skills 的*维护类* PR 为主（docx、xlsx、pptx、pdf、mcp-builder、skill-creator、webapp-testing），而非全新的领域 Skills。`gerardrecinto` 这一组（[#1975](https://github.com/anthropics/skills/pull/1975)、[#1976](https://github.com/anthropics/skills/pull/1976)、[#1977](https://github.com/anthropics/skills/pull/1977)）就是典型：三个小而精、关联 issue 的修复在同一天提交。

---

## 4. Skills 生态洞察

**社区的需求已经从“增加更多 Skills”转向“让 Skill 这一层可信且正确”——命名空间完整性与安全加固、真正能触发的评测、token 高效且描述准确的 Skills——而在活跃队列中，内置 Skill 的维护类 PR 数量已经超过新 Skill 提案。**

---

# Claude Code 社区摘要 — 2026-11-07

**来源：** github.com/anthropics/claude-code
**数据窗口：** 过去 24 小时（50 个 issue 更新，1 个 release，1 个 PR）

---

## 1. 今日亮点

本次仅有一个补丁版本 **v2.1.319**，它收紧了遥测或功能开关不可用时的自动模式行为——尤其是在 LLM 网关之后——改为失败关闭（询问/拒绝），而不是回退到本地分类器，并提供了一个可选择退出的环境变量。与此同时，issue 跟踪器仍由**身份验证与会话持久化故障**主导：长期存在的登录循环讨论串（#1757）目前已有 90 条评论和 78 个 👍，本周还新增了若干桌面端登录/保存回归问题。平台卫生——自动更新器替换正在运行的二进制文件、TCC 权限丢失以及静默保存失败——是第二大问题簇。

---

## 2. 版本发布

**v2.1.319**（[releases](

- **当服务器无法检查调用时，自动模式现在会失败关闭。** 在遥测或功能开关关闭时——包括位于 LLM 网关之后——服务器无法验证的调用会提示给用户（进行询问）或被拒绝，不会回退到内置分类器。
- **退出开关：** 设置 `CLAUDE_CODE_AUTO_MODE_SERVER=0` 可继续使用本地分类器。
- **未改变：** Bedrock、Vertex 和 Foundry 路径明确不在新行为的适用范围内。

*分析师注：* 这是一个安全性/一致性加固版本，而非功能版本。通过企业网关或气隙代理运行 Claude Code 的团队应确认自动模式提示不会干扰现有自动化，并有意识地设置该环境变量，而不是误设。

---

## 3. 热门 Issue

1. **[#1757]( —— 需要不断重新登录（OPEN，90 条评论，78 个 👍）**
   这是仓库中互动量遥遥领先的开放讨论串。用户报告称，尽管前一晚已经完成身份验证，几乎每天仍会被迫走一遍 Web 认证。长期存在的认证状态不稳定是社区中最响亮的单一抱怨；👍 比例表明这是一个广泛问题，而非小众配置问题。

2. **[#25739]( —— 跨机器的可移植项目记忆（OPEN，30 条评论，27 个 👍）**
   请求提供可在设备间迁移的记忆，以支持多机器工作流。一个增强请求获得高评论数，说明存在真实的工作流压力——开发者在笔记本、台式机和远程 VM 上运行 CC，并在每个边界处丢失上下文。

3. **[#103788]( —— 桌面端（macOS）登录失败：“We couldn't verify your browser”（OPEN，20 条评论）**
   Google 和邮箱两种方式都返回 400，且与配置文件、版本和 VPN 无关；只有更换出口 IP 后邮箱方式才能通过。这表明问题指向基于 IP/浏览器指纹的验证启发式规则，而非客户端配置——对受影响用户是硬性阻断，且没有变通方案。

4. **[#107232]( —— 无法处理超过 5 分钟的 API 请求（OPEN，7 条评论）**
   长时间运行的请求存在反复出现、似乎尚未解决的超时上限（Linux、API、网络）。这直接限制了长时间 agentic 或大上下文工作负载——这是少数不仅影响便利性、还限制高级用户能构建什么的问题之一。

5. **[#69181]( —— Vim 模式：在 INSERT 中按 Escape 会清空整个提示词（OPEN，6 条评论，7 个 👍）**
   agent 界面中可复现的数据丢失：从 INSERT 切换到 NORMAL 会清空提示词缓冲区。影响范围小，但一旦触发就很严重，且较高的 👍 与评论数之比表明 Vim 模式用户正在默默依赖它。

6. **[#63251]( —— 惰性/按需的 MCP 服务器连接（OPEN，5 条评论，4 个 👍）**
   所有已配置的 MCP 服务器都会在会话启动时连接，无论是否使用，导致内存占用和启动延迟膨胀。这是一个结构上重要的请求：MCP 的采用速度已经超过了预连接模型的扩展能力。

7. **[#95539]( —— 所有 naver.com 域名都被安全限制屏蔽（OPEN，8 条评论，9 个 👍）**
   默认安全类别阻止列表中的一次误报导致韩国主要门户网站不可用，而 daum.net 和 tistory.com 则可以通过。这说明粗粒度的域名级屏蔽会在没有按站点覆盖机制的情况下造成区域性故障。

8. **[#107467]( —— 在 2.1.318 中，Artifact `read` 不再接受 `path`/`paths`（OPEN，回归）**
   迁移后的 Design canvas 子文件在 2.1.316 和 2.1.318 之间变得不可读。这是一个干净、近期、已通过版本二分定位的回归——正是那种修复成本应该很低的报告。

9. **[#107175]( —— 使用 Varlink 进行跨会话消息传递（OPEN，10 个 👍，1 条评论）**
   提议将 Varlink 作为会话间消息传递的传输层。仅 1 条评论却获得 10 个 👍，这表示关注基础设施的用户在强烈地默默赞同——这是一个比评论数所暗示的更有价值的设计方向信号。

10. **[#108074]( —— Ultra 套餐无法使用 Auto-dream（OPEN，今日提交）**
    二进制文件中存在功能开关（`autoMemoryEnabled`、`autoDreamEnabled`），但该能力被门控关闭（`tengu_onyx_plover` 已禁用）。这引出一个反复出现的问题：为什么付费顶级套餐会看到其二进制文件中已经包含的功能？

**同样值得关注：** [#94794]( —— `Edit` 之前没有“自读取后已修改”的守卫，因此外部文件更改会被静默覆盖（标记为 `data-loss`）。[#107558]( 报告称，在一次轻度使用中，10 美元额度充值约 15 分钟就被消耗完。

---

## 4. 关键 PR 进展

本时间窗口内仅有 **一个** pull request 被更新——本摘要反映的是可获得的全部 PR 数据，而不是为了凑数。

**[#107768]( —— mods：添加 privacy mod（CLOSED，作者 poteat）**
在 `mods/privacy` 下发布 CC 内置 `privacy` mod 的源码：hooks 模块、manifest 和 README。该 mod 会拒绝包含已登录账户电子邮件地址并指定 User-Agent 的 shell 调用。没有新行为——这是一次透明度/源码公开变更。值得注意的是，它被**关闭**而非合并，这一点值得指出：社区现在可以看到该 hook 的逻辑，但不一定拥有一份在树内维护的副本。

---

## 5. 热门讨论

*已省略——本数据集中未提供 Discussions 数据。*

---

## 6. 功能请求趋势

从本时间窗口内标记为 enhancement 的 issue 中提炼：

- **跨机器连续性。** 可移植项目记忆（#25739）和 Remote Control 可达性（#106996：会话空闲约 4 小时后断开，且并非每个会话都可附加）都指向同一个未满足的需求——一个能跟随开发者跨越笔记本、台式机、手机和远程 VM 的会话。#107064 将 Desktop SSH 环境与手工搭建的 SSH + tmux + Remote Control 工作流进行比较。
- **MCP 生命周期控制。** 惰性/按需 MCP 连接（#63251）以及为 `claude-code-remote` 的 `create_session` 工具增加 `effort` 参数（#107292），都是为了避免为未使用的能力支付全部成本。
- **更细粒度的权限语义。** #107363 请求在 `ask` 规则内部支持例外（例如 `ask Bash(rm *)` 但排除 `/tmp/claude/**`）——当前模型在规则层面是全有或全无。
- **跨会话与跨工具连接。** 用 Varlink 进行会话消息传递（#107175）；为 Projects（beta）协调器聊天提供连接器访问权限（#107601），后者目前没有任何连接器，而其云端线程却拥有全部连接器。
- **桌面端/Code 标签页一致性。** #108070 请求在 Code 标签页中支持 1Password 登录，而不仅是 Cowork，以免将网站登录任务强行推入一个需要重新授予文件夹访问权限的单独会话。

---

## 7. 开发者痛点

- **认证状态无法持久保存。** #1757（每天重新登录）、#103788（浏览器验证返回 400）、#86616（在约 20 个并发实例下，macOS 钥匙串三天内损坏两次）、#97341（Cowork：计划任务无法保存——提示“sign in again on this computer”，而同一台机器从云端登录却正常）。四个独立界面，同一个主题：凭据生命周期是产品中最薄弱的部分。
- **自动更新器会破坏正在运行的会话。** #94687（更新器替换 `claude.exe` 后语音听写失效；TCC 拒绝旧 PID）和 #105824（更新器*删除*了活动会话正在使用的二进制文件，由此产生的 TCC 拒绝会在整个终端范围内阻止访问 `~/Downloads`，即使已授予完全磁盘访问权限）。在活动进程下就地更新正在对 Claude Code 本身之外的系统造成附带损害。
- **静默失败比显式失败更糟。** #108079——在非系统盘上丢失 EFS 应用保护后，Code 会话停止保存长达 12 天，且没有可见错误。#94794——编辑会覆盖外部更改，没有“自读取后已修改”的守卫。#95593——桌面端聊天完成通知静默不触发，而 Code/Cowork 通知却正常。
- **回归问题随补丁版本发布。** #107467（Artifact 的 `path`/`paths` 在 2.1.318 中被移除）、#108019（桌面端侧边栏丢失运行中/需关注圆点）、#107905（Chrome 标签组在 Vivaldi 上消失）。用户自己在做版本二分定位——这表明发布说明没有足够清晰地呈现行为变更。
- **成本与计量方面的意外。** #107558（轻度使用下约 15 分钟消耗 10 美元，而正常情况可用半天）与长期存在的延迟上限（#107232）并列，成为让开发者不信任运行大型任务的两件事。
- **阻止列表误报没有覆盖机制。** #95539 显示整个国家门户网站因默认安全类别而不可达，且没有按域名的逃生通道——这种模式很可能在各地区反复出现。

---

*数据缺口：本时间窗口内总共只有 1 个 PR，且没有 Discussions 数据；第 4 节和第 5 节反映的是这一情况，而非推断出的活动。*

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex Codex 社区简报 — 2026-11-07

## 1. 今日亮点

本次仅发布了一个 alpha 版本（`rust-v0.169.0-alpha.2`），且没有公开的变更日志细节；但真正的动静在 PR 队列里：一大批已关闭的 TUI/UI 树渲染 PR，表明终端页脚、状态栏和扩展渲染栈正在经历一轮重构。Issue 侧则以 Windows 桌面端回归问题为主——两起独立的“本地项目从侧边栏消失”报告（#42739，79 条评论；加上昨天提交的 #59895）；与此同时，会话/上下文完整性问题（压缩丢失状态、残留的 app-server、上下文注入失控）仍在各平台上持续消耗用户配额。

## 2. 版本发布

- **rust-v0.169.0-alpha.2** — 仅作 alpha 版本号递增；发布说明中没有任何逐项变更。应将其视为跟踪用的预发布构建，而非功能发布。

## 3. 热门 Issue

1. **[#42739](https://github.com/openai/codex/issues/42739)** — *Windows：桌面端更新后本地项目从侧边栏消失*（79 条评论，👍7）。本时间窗口内流量最高的帖子；会话仍保留在 Recents 下，但项目分组丢失。自 9 月起持续存在，仍为 OPEN——这是 Windows 应用数据迁移 bug 的最强信号。
2. **[#51524](https://github.com/openai/codex/issues/51524)** — *Windows 端 Computer Use 卡在 `get_window_state`*（72 条评论，👍8）。窗口枚举正常，但随后因无法确定浏览器 URL，回合在点击/输入之前就中止了。高互动量反映出 Computer Use 对 Chrome/Edge 用户被直接阻断。
3. **[#52503](https://github.com/openai/codex/issues/52503)** — *Web/Dots：选择“始终允许”后仍反复请求授权，长时间运行的上传丢失*（23 条评论）。授权状态无法在多次 `create_blob` 调用之间持久化，阻塞了已授权的 GitHub 操作——这是权限模型在信任/体验上的失败。
4. **[#41849](https://github.com/openai/codex/issues/41849)** — *VS Code Remote-SSH：残留的 app-server 占用线程写入者*（20 条评论，👍16——反应数最高）。重连时又启动了一个服务器，而孤儿进程仍锁着该线程，导致出现“This is open in another app.”。复现路径清晰，归属 bug 明确。
5. **[#35935](https://github.com/openai/codex/issues/35935)** — *Windows 回归：压缩丢失任务状态、重复劳动、耗尽每周用量*（16 条评论）。同时涉及正确性与成本影响；这是一个自 7 月起长期存在、反复出现的回归问题。
6. **[#57620](https://github.com/openai/codex/issues/57620)** — *macOS：26.1023.50603 之后 worktree 图标消失*（12 条评论，👍11）。反应数与评论数之比偏高，说明对重度使用 worktree 的工作流影响广泛但较为沉默。
7. **[#59895](https://github.com/openai/codex/issues/59895)** — *Windows：更新后并切回个人账户，项目消失*（9 条评论）。为 #42739 提供了佐证，触发条件更窄（账户切换），可能有助于定位迁移路径。
8. **[#56889](https://github.com/openai/codex/issues/56889)** — *更新后头像菜单中的用量百分比和重置时间被移除*（5 条评论，👍5）。一处小小的 UI 改动却引发格外强烈的不满：用户失去了唯一能一眼看到配额状态的信号。
9. **[#53861](https://github.com/openai/codex/issues/53861)** — *macOS 上 Computer Use 使系统范围内每次按键都重复一次*（5 条评论）。在操作后台应用时造成严重副作用；目标应用存在数据损坏风险。
10. **[#9286](https://github.com/openai/codex/issues/9286)** — *`git push --dry-run` 失败：沙箱内 SSH 配置的属主为 nobody*（11 条评论，👍6）。自 2026 年 1 月起开放至今——这是一个长期存在的沙箱/权限小毛病，影响 Linux CLI 用户每一次基于 SSH 的 git 操作。

## 4. 关键 PR 进展

以下条目全部为 CLOSED，且作者为 `copyberry[bot]`，即自动化/内部投影 PR，而非社区贡献——但它们揭示了 TUI 渲染器以及认证/运行时工作的方向。

1. **[#60035](https://github.com/openai/codex/pull/60035)** — 修复 git 归属设置请求的认证；按目标地址遵循运行时认证，并允许在凭证刷新后完成 world-state 贡献。
2. **[#60034](https://github.com/openai/codex/pull/60034)** — 从 spawn 追踪日志中省略敏感的进程输入（参数、`arg0`、环境变量）——修复子进程追踪中的凭证泄漏问题。
3. **[#60032](https://github.com/openai/codex/pull/60032)** — 扩展状态内容可跨多行页脚（最多四行），修复窄宽度下多行/换行文本被裁剪的问题。
4. **[#60029](https://github.com/openai/codex/pull/60029)** — 通过共享的布局遍历将 UI 树直接渲染到终端缓冲区；新增自然测量与视口约束测量。
5. **[#60026](https://github.com/openai/codex/pull/60026)** — 为 UI 树实现多行布局与行换行，保留作者设定的换行、样式和超链接范围。
6. **[#60020](https://github.com/openai/codex/pull/60020)** — 用 `vtparse` 替换仅处理 CSI 的清理器，使 OSC 载荷（包括内嵌的超链接 URL）不再作为可见文本渲染——一项渲染/安全加固修复。
7. **[#60019](https://github.com/openai/codex/pull/60019)** — 为扩展 UI 树提供主题感知的语义颜色（`primary`、`surface` 等），跟随当前语法主题。
8. **[#60014](https://github.com/openai/codex/pull/60014)** — `requirements.toml` 可将 `features.runtime_agent_instances.mode` 固定为 `dual_header` 或 `assertion_only`，并拒绝冲突配置——托管/企业认证可配置性。
9. **[#60009](https://github.com/openai/codex/pull/60009)** — 默认关闭的 `gateway_setup_wizard`：在 TUI 登录时选择已保存的自定义网关，持久化为 `model_provider` 并重启 app-server。
10. **[#60007](https://github.com/openai/codex/pull/60007)** — 云线程从本地 GitHub 项目的 origin 启动，从而支持仓库检出供给，并在后端切换时保留所关联的项目。

另需注意：**#60018**（将回合触发传播到用量分析）和 **#60011**（为托管工具适配的多智能体模式指导）表明在归因遥测和多智能体编排上的持续投入。

## 5. 热门讨论

**问答**

- **[#57025](https://github.com/openai/codex/discussions/57025)** — `app-server` 能否在线程启动时应用 `ToolPolicy`？用户想要一个*不*提供任何工具的交接评审线程，并正确指出提示词层面的指令并不足够。这指向一个缺失的执行前策略钩子。
- **[#59594](https://github.com/openai/codex/discussions/59594)** — Plus 订阅用户怀疑 Codex 额度被削减约 50%，可能与 GPT-6.1 Sol (low) 漂移有关。双语帖子（EN/中文）；是配额不透明类抱怨的典型代表。
- **[#57675](https://github.com/openai/codex/discussions/57675)** — Pro 账户连续 7 天在所有模型和所有界面上都收到“Selected model is at capacity”。为提升可见性从 #56324 转载；这是一种账户级（而非模型级）的容量模式。

**展示与分享**

- **[#58849](https://github.com/openai/codex/discussions/58849)** — `code-review-skill`：静态优先的评审，在全新上下文中运行四种探针（行为、测试、规则……），产出可审计的发现和 JSON 合并门禁。
- **[#59844](https://github.com/openai/codex/discussions/59844)** — macOS 技能目录，用于在 Codex、Claude Code 和 OpenCode 之间保持技能同步——解决多个智能体目录间的技能漂移问题。
- **[#59896](https://github.com/openai/codex/discussions/59896)** — Tessera：在 Codex/Claude Code/OpenClaw/Hermes 上运行同一任务，保留通过测试的那个答案；`tessera sync` 将同一份源写入各智能体的原生格式。
- **[#58359](https://github.com/openai/codex/discussions/58359)** — `claude-codex-taskbar`：小于 1 MB 的开源 Windows 工具，在任务栏中展示 5 小时与每周的 `/status` 限额。
- **[#59316](https://github.com/openai/codex/discussions/59316)** — ML Research & Paper 技能：冻结实验、证据账本，以及跨“文献 → 实验 → 写作”的可恢复评审。
- **[#60012](https://github.com/openai/codex/discussions/60012)** — Zozo Local 前置条件检查器：校验 Node 主版本、操作系统家族、Git 和 `gh`，以区分工具链故障与登录/启动故障。
- **[#59871](https://github.com/openai/codex/discussions/59871)** — Account Access：可移植的技能 + CLI，用于所有者批准的限定范围账户读取和可恢复的登录任务（预览）。

**想法** — 本时间窗口内没有带 Ideas 标签的帖子；最接近产品提案的是 issue **#59324**（由协调器管理的子任务卡片），见下文趋势部分。

## 6. 功能请求趋势

- **持久化的协调器式多智能体工作流。** #59324 要求提供一等公民的协调器会话，可创建并监督可视化的、持久化的子任务卡片；#60011 的 PR（为托管工具打包的模式策略）表明这一能力已在内部构建中。
- **可移植技能与跨智能体配置同步。** 展示与分享类帖子（#58849、#59844、#59896、#59316）汇聚到同一个诉求：一个规范的技能/指令源，可无漂移地投射到 Codex、Claude Code 和 OpenCode。
- **配额透明度与成本控制。** #56889（恢复百分比/重置时间）、#59594（疑似削减 50%）、#57675（账户级容量）、#45383 和 #59970（用量被重建上下文消耗）都指向同一个缺口：用户无法预测或审计支出。
- **会话持久性与正确的压缩。** #35935、#45383、#54423、#50509、#59970 要求可恢复的会话能够保留任务状态、不把线程钉死在不可用的模型上，也不重复注入过大的连续性载荷。
- **跨界面连续性。** #60007（云线程以本地项目 origin 为源）、#56600（Android 远程配对）、#41849（Remote-SSH 重连）——用户期望同一线程能在桌面端、CLI、Web 和远程主机之间流转。
- **自带服务商/网关。** #60009（网关设置向导）和 #54423（自定义 Responses 服务商）反映出对一等公民自定义端点的需求，而非变通方案。
- **macOS 桌面端 UI 易用性。** #59386（全屏视图隐藏 Projects 侧边栏；主对话没有自己的标签页）和 #60005（全屏布局中缺少聊天标签页）要求标签页模型的一致性。

## 7. 开发者痛点

- **更新后的 Windows 桌面端回归是数量最多的抱怨。** 侧边栏/项目丢失（#42739、#59895）、链接 worktree 上 workspace-write DENY ACL 导致的 git 写入被阻断（#32880）、扩展 UI 即使在全新配置下也无法初始化（#56682），以及 Computer Use 故障（#51524、#60023）。多份报告共享同一根因形态：应用更新悄悄使已持久化的状态失效。
- **重连过程中的状态与锁完整性。** 孤儿 `app-server` 进程占用线程写入者（#41849）、压缩丢失任务状态并重复已完成工作（#35935）、压缩失败后线程被钉在不可用模型上（#54423），都迫使用户放弃会话。
- **配额被智能体自身的开销消耗，而非用户的任务。** #35935、#45383 和 #59970 描述的是同一个失败循环：恢复/压缩重新注入大量上下文，用量飙升，工作仍未完成。用量读数的移除（#56889）加剧了这一问题。
- **权限与安全系统产生误报，且授权不持久。** #52503（“始终允许”未生效、授权卡片不可用）、#56475（已授权的本地开发被网络安全检查阻断）、#41649（macOS 文件与文件夹权限在撤销后被静默恢复）。每一项都从不同方向侵蚀对沙箱模型的信任——先是过严，然后又过松。
- **Computer Use 的可靠性仍不均衡。** Windows 上的 URL 检测失败（#51524）和 macOS 上的系统级按键重复（#53861）都会阻断回合或损害环境，而非仅影响观感。
- **Linux CLI 上的长尾沙箱摩擦。** #9286（沙箱内 SSH 配置的属主为 nobody）自 2026 年 1 月起开放至今，仍然会破坏 `git push`——提醒我们：评论较少的旧 issue 也可能代表持续的日常阻塞。
- **重复报告说明已知问题的可发现性较差。** #42739 与 #59895，以及从 #56324 转载的 #57675，都表明用户找不到已有的帖子——值得做分诊/去重，或提供产品内的状态提示。

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI 社区简报 — 2026-11-07

## 1. 今日亮点

过去 24 小时内没有新版本发布，但分流处理活动非常密集：共涉及 50 个 issue 和 45 个 PR，维护者的注意力主要集中在 **agent/subagent 可靠性**——虚假成功上报、通用 agent 挂起，以及浏览器 agent 配置缺口。与此同时，一批小而高价值的 `core`/`cli` 正确性修复（PTY 泄漏、路径处理、`.gitignore` 否定规则、hook 迁移）进入评审流程。

---

## 3. 热点 Issue

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — `MAX_TURNS` 后的 subagent 恢复被上报为 GOAL 成功**（P1，13 条评论）
   `codebase_investigator` subagent 返回 `status: "success"` / `Termination Reason: "GOAL"`，尽管其自身输出承认在尚未进行任何分析前就达到了轮次上限。这是当天讨论量最高的单个 issue，也可以说是 agent CLI 中最危险的一类 bug：自动化无法检测的静默截断。社区反应（+2 👍）不算热烈，但仅维护者可见的汇总标签表明它已进入内部路线图。

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — 通用 agent 无限挂起**（P1，8 条评论，**8 👍**）
   任何交给通用 agent 处理的任务都会永久挂起——甚至只是创建文件夹——等待时间可长达一小时。这是本批中反应最多的 issue，而规避方案（让模型永远不要使用 subagent）实际上等于禁用了该功能。这是当前最突出的用户痛点。

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — 零依赖 OS 沙箱 + 执行后意图路由**（P2，9 条评论）
   提议顺应 Gemini 3 原生的 bash 亲和性（串联 `grep`/`sed`/`awk`），同时通过 OS 级沙箱和执行后的意图路由加以约束。这是一个重大的架构押注——它将重塑 shell 工具的准入与审计方式。

4. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — 评估 AST 感知的文件读取、搜索与映射**（P2，7 条评论）
   该 Epic 研究 AST 感知工具是否能减少错位读取、浪费的轮次和 token 噪声。配套 issue [#22746](https://github.com/google-gemini/gemini-cli/issues/22746)（CLI 工具，推荐 `tilth`/`glyph`）和 [#22747](https://github.com/google-gemini/gemini-cli/issues/22747)（用 `ast-grep` 进行读取/搜索）表明这项工作正被拆解为具体实验。

5. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini 对 skills 和 sub-agents 的使用不够**（P2，7 条评论）
   虽是轶事性反馈，但引起广泛共鸣：自定义 skills 和 subagents 只有在明确指示时才会被调用，而不是在上下文相关时主动使用（例如在做 Gradle 相关工作时调用 `gradle` skill）。这表明模型的工具选择策略调校不足，削弱了整个可扩展性叙事。

6. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267) — Browser Agent 忽略 `settings.json` 覆盖项**（P2，5 条评论）
   `AgentRegistry` 在初始化时正确合并了设置，但 Browser Agent 随后将其丢弃——因此 `maxTurns` 及类似配置项静默失效。配置能解析却不生效，对调试 agent 行为的用户来说是一类尤其具有腐蚀性的 bug。

7. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — Browser subagent 在 Wayland 下失败**（P1，4 条评论）
   该 browser subagent 在 Wayland 下直接失败，却上报 `Termination Reason: GOAL`。Linux/Wayland 用户仍然是浏览器工具链的长期盲区，而虚假 GOAL 模式在这里也再次出现。

8. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 工具数超过 128 时出现 400 错误**（P2，3 条评论）
   注册超过约 128 个工具会导致 API 返回 400（正文写的是 400，标题写的是 128）。这直接限制了 MCP 密集型配置，因为这类场景下工具数量会迅速膨胀——修复方案可能需要限定范围的工具选择，而不是硬性上限。

9. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — Agent 应停止/劝阻破坏性行为**（P2，3 条评论）
   在存在更安全的等效操作时，模型仍会使用 `git reset` 或 `--force`，并且缺乏针对破坏性数据库/资源操作的护栏。与 #19873 一起，这构成了贯穿 agent 工作流的安全主线。

10. **[#23571](https://github.com/google-gemini/gemini-cli/issues/23571) — 模型会在随机位置创建临时脚本**（P2，3 条评论）
    当只能通过 shell 执行时，模型会把临时编辑脚本散落到各个目录，破坏干净提交的工作流。这是一个小 UX 问题，但对任何进行可审查、增量式工作的用户来说，代价过高。

*另值得关注：* [#21763](https://github.com/google-gemini/gemini-cli/issues/21763)（`/bug` 报告遗漏 subagent 上下文——P1）、[#20079](https://github.com/google-gemini/gemini-cli/issues/20079)（`~/.gemini/agents/` 中的符号链接 agent 文件未被发现），以及 [#18836](https://github.com/google-gemini/gemini-cli/issues/18836)（用基于文件的持久任务跟踪替代 `WriteToDo`，以对抗上下文腐化）。

---

## 4. 重点 PR 进展

1. **[#30185](https://github.com/google-gemini/gemini-cli/pull/30185) — 防止 shell 模式下的递归交互式 `gemini` 会话**（P1，开放）
   阻止 CLI 在 Shell Mode（`!`）或 `[Shell]` 工具内启动嵌套交互式会话，并拒绝不支持的 config 子命令。针对可能卡死终端的故障模式，这是一项有意义的稳定性修复。

2. **[#30189](https://github.com/google-gemini/gemini-cli/pull/30189) — `MessageBus.request()` 在发布失败时立即拒绝**（P2，开放，仅维护者可见）
   此前，发布失败会等满 60 秒超时，然后抛出泛化的超时错误。现在会携带原始错误立即拒绝，并立刻释放计时器和监听器——大幅改善 agent 间消息传递的可诊断性。

3. **[#30051](https://github.com/google-gemini/gemini-cli/pull/30051) — 在无头模式中断时终止活动 shell 并持久化工具调用**（开放）
   修复无头 `--yolo` 运行在收到 SIGINT/SIGTERM/SIGHUP 时以退出码 0 退出、并留下已分离 shell 命令继续存活的问题。这对 CI 和脚本化使用至关重要，因为退出码就是全部契约。

4. **[#30183](https://github.com/google-gemini/gemini-cli/pull/30183) — 在所有认证模式下覆盖 60 秒 API headers 超时**（已关闭）
   新增 `model.requestTimeoutMs` / `GEMINI_API_HEADERS_TIMEOUT_MS`，使长思考请求（在大上下文中使用 `thinkingLevel: HIGH`）不再因 `UND_ERR_HEADERS_TIMEOUT` 而失败并从头重放。

5. **[#30184](https://github.com/google-gemini/gemini-cli/pull/30184) — 关闭 macOS PTY spawn 时泄漏的孤儿 `/dev/ptmx` master FD**（已关闭）
   解决在 macOS 上使用 `@lydell/node-pty@1.1.0` 进行长时间会话时出现的 FD 耗尽（`kern.tty.ptmx_max`）问题——这是一个只有重度用户在工作数小时后才会遇到的慢性 bug。

6. **[#30187](https://github.com/google-gemini/gemini-cli/pull/30187) — 防止长达数小时的配额重试，并在重试时通知 ACP 客户端**（已关闭）
   阻止 ACP 模式请求在 HTTP 429 且重置窗口长达数小时的情况下无限挂起，并将重试尝试暴露给客户端，而不是静默失败。

7. **[#30052](https://github.com/google-gemini/gemini-cli/pull/30052) — 正确处理 `dir/**` 模式下的嵌套 `.gitignore` 否定规则**
   `GitIgnoreParser` 此前会忽略那些被嵌套 `.gitignore` 在父级通配符下重新包含的文件。这直接影响 agent 能看到和编辑哪些文件——安静但影响重大的正确性修复。

8. **[#30055](https://github.com/google-gemini/gemini-cli/pull/30055) — 迁移时将 Claude `SubagentStop` hooks 映射到 `AfterAgent`**（P2）
   `gemini hooks migrate --from-claude` 使用了键 `SubAgentStop`，导致该事件未经映射直接通过并被静默丢弃。修复了迁移的 Claude Code 用户在 hook 对等性方面的问题。

9. **[#30054](https://github.com/google-gemini/gemini-cli/pull/30054) — 仅在 home 目录边界处对路径做波浪号缩写**（P2）
   当 home 为 `/Users/al` 时，`/Users/albert/proj` 会被渲染成 `~bert/proj`。虽属外观问题，但路径显示 bug 会侵蚀用户对工具输出的信任。

10. **[#30027](https://github.com/google-gemini/gemini-cli/pull/30027) — 支持独立的自定义主题配置**（P1，开放）
    将自定义主题配置从通用设置界面中拆分出来。风险低于 P1 标签所暗示的程度，但它是当前开放队列中最受期待的 UX 变更。

*今日另已合并/关闭：* [#29901](https://github.com/google-gemini/gemini-cli/pull/29901)（ACP `_session/steering`——允许客户端向运行中的轮次注入消息，镜像 `experimental.modelSteering`）、[#29895](https://github.com/google-gemini/gemini-cli/pull/29895)（在无头模式下保留结构化 API 错误，而不是 `[object Object]`）、[#30053](https://github.com/google-gemini/gemini-cli/pull/30053)（`Background PIDs: NaN` 解析器修复）、[#30056](https://github.com/google-gemini/gemini-cli/pull/30056)（`__GCLI_EXPAND_TARGET__` 泄漏进 `expandEnvVars`），以及 [#30165](https://github.com/google-gemini/gemini-cli/pull/30165)（ACP 中流中重试拼接了部分文本/工具调用）。

---

## 6. 功能请求趋势

- **Subagent 可观测性与诚实性。** 这是最大的一组相关请求：真实终止原因（#22323）、`/bug` 报告中的 subagent 上下文（#21763）、通过 `/chat share` 分享 subagent 轨迹（#22598），以及通过 `settings.json` 发现 subagent（#18285）。用户越来越多地运行自己无法看透的 agent。
- **让模型真正使用其扩展能力。** #21968（skills/subagents 被忽略）、#21432（agent 对自身 flags、热键和 CLI 调用方式的“自我意识”）以及 #18283（仓库内精选 agent 示例）都指向工具选择策略，而非能力缺失。
- **上下文经济性。** AST 感知的读取/搜索/映射（#22745、#22746、#22747）、用于精准读取的“Tactful Extraction”（#19561，动机是约 36.6k token/轮的基线开销），以及用基于文件的持久任务跟踪替代上下文内的 `WriteToDo`（#18836、#21000）。
- **沙箱与破坏性操作护栏。** #19873（OS 沙箱 + 意图路由）和 #22672（存在更安全路径时停止使用 `git reset`/`--force`）构成一条安全工作流，与 bash 亲和性方向自然配对。
- **按工作区策略与韧性。** #18397（用按工作区策略替代全局策略）、#22232（浏览器会话接管/锁恢复）和 #23166（稳定内部评估）反映出从“能不能用”到“能否在正确范围内、无人值守地可重复使用”的转变。

## 7. 开发者痛点

- **静默失败是最主要的挫败来源。** 虚假的 `GOAL`/`success` 状态（#22323）、Wayland 彻底失败时仍报 `GOAL`（#21983）、无头运行被中断时退出码为 0（#30051），以及通用 agent 挂起（#21409），都共享同一个根因：CLI 在并未成功时上报成功。对于任何将 Gemini CLI 接入自动化的人来说，这是最大的单一阻碍。
- **配置能解析却不生效。** Browser Agent 忽略 `settings.json`（#22267）、符号链接的 agent 定义未被发现（#20079），以及 Claude hook 键名不匹配（#30055），都以同样的方式消耗用户的调试时间：没有错误，也没有效果。
- **Subagents 难以调试。** 由于没有轨迹可见性（#22598），bug 报告中也没有 subagent 上下文（#21763），用户诊断 #21409 这类挂起问题时只能盲人摸象——这很可能抬高了这些 issue 的评论数。
- **资源与规模限制首先伤及重度用户。** 工具数量上限（#24246）、macOS PTY FD 耗尽（#30184），以及 ACP 中长达数小时的 429 挂起（#30187），都只出现在长时间运行或 MCP 密集的会话中——恰恰是最投入该工具的用户。
- **工作区卫生。** 散落的临时脚本（#23571）和破坏性 git 操作（#22672）意味着 agent 的输出在提交前经常需要手动清理或修复。

*注：全部 50 个 issue 的 `updated` 时间戳几乎同时出现，说明这是一次批量分类/打标签处理，而不是 50 个独立用户报告——在这里，可靠的参与度信号是评论数，而不是更新时间。*

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI —— 社区摘要
**日期：** 2026-11-07 · **仓库：** [github/copilot-cli](https://github.com/github/copilot-cli)

---

## 1. 今日亮点

过去 24 小时内有**两个版本**发布：**v1.0.107** 在 Rex 终端中支持内联图片渲染，新增用于跳转至 VS Code 的 `/code` 命令、全新的 **Fusion 层级**设置，以及统一的 `/settings` 仪表盘；预发布版 **v1.0.108-0** 在沙箱策略中新增了 Windows 仅枚举文件系统授权，并通过新的 `/index` 命令启用托管的**共享 tgrep 索引**。与此同时，issue 追踪器主要被 **Microsoft Entra / 远程 MCP 认证失败**占据（Windows 与 macOS 均有），此外还有一批围绕 **Fusion/Auto 模式**行为的新分诊条目（智能体输出被扣留、模型被列入黑名单，以及服务器模式下 provider 配置被忽略）。

---

## 2. 版本发布

### [v1.0.108-0](https://github.com/github/copilot-cli/releases/tag/v1.0.108-0) *（预发布）*
**新增**
- `settings.json` 中的 `sandbox.userPolicy.filesystem.enumeratePaths` —— 启用 **Windows 上的仅枚举目录授权**，将沙箱文件系统权限模型从全有或全无的读取访问细化。
- 新增 `/index` 命令，对符合条件的仓库**默认启用托管的共享 tgrep 索引** —— 这是朝着开箱即用的更快全仓库代码搜索迈出的重要一步。

**改进**
- 关于 Copilot CLI 行为的问题，现在可以从当前构建中获取答案（上游发布说明文本已被截断）。

### [v1.0.107](https://github.com/github/copilot-cli/releases/tag/v1.0.107) — 2026-11-06
- **Rex 终端**使用 Kitty 直接放置方式渲染内联图片。
- **`/code`** 在 VS Code 中打开当前会话 —— 对于从 TUI 开始、希望转到编辑器中继续的用户来说是一座有用的桥梁。
- **Fusion 层级设置**已加入 `copilot config`，让用户首次可以控制模型路由。
- **`/settings`** 现在为所有用户打开统一仪表盘，支持跨设置与本地资源的全局搜索。

> ⚠️ 注意：Issue [#5377](https://github.com/github/copilot-cli/issues/5377) 指出 1.0.106 与 1.0.107 的发布说明只有日期不同，且 `changelog.md` 与已发布的说明不一致 —— 如果你需要精确追踪变更，这一点值得留意。

---

## 3. 热门 Issue

1. **[#1972 — 允许用户配置提交键（例如 Ctrl+Enter），以避免 IME 输入时误提交](https://github.com/github/copilot-cli/issues/1972)** · 👍 48 · 4 条评论
   本时间段内点赞最高的条目。CJK 用户在 IME 转换确认时按下 Enter，结果误提交了尚未组合完成的提示词。强烈表明**输入处理未做国际化** —— 一个小修复，却能带来巨大的 UX 改善。

2. **[#2776 — Shift+Enter 提交提示词而非插入换行](https://github.com/github/copilot-cli/issues/2776)** · 👍 5 · 9 条评论
   今日讨论最多的 issue。影响核心的撰写工作流，且很可能与 #3768 是同一根因 —— 两者合起来代表了追踪器中被重复抱怨最多的问题。

3. **[#3768 — Shift+Enter 多行输入不生效](https://github.com/github/copilot-cli/issues/3768)** · 👍 5 · 2 条评论
   自 v1.0.61 起就有报告，至今仍处于打开状态。重复报告的数量说明用户找不到变通方案，而这通常预示着用户流失。

4. **[#5236 — Windows：MCP 登录后 Entra broker 令牌缓存仍为 0 字节，每个新进程都会重新提示](https://github.com/github/copilot-cli/issues/5236)** · 6 条评论
   **令牌缓存文件被写入但从未填充内容**，因此每个进程都会重新触发账户选择器。对于企业 Windows 用户来说，每次启动 CLI 都会遇到这一高度可见的摩擦。

5. **[#5250 — macOS：Entra-broker MCP 服务器启动时失败（“sign-in is already in progress”）](https://github.com/github/copilot-cli/issues/5250)**
   两个受 Entra 保护的服务器（Azure DevOps 远程 MCP、WorkIQ）在会话启动时相互竞争，且缓存令牌被拒绝后无法恢复。与 #5236 属于同一子系统 —— **Entra broker 的并发问题是跨平台的设计缺口**，而非某个平台的 bug。

6. **[#5366 — 远程 MCP OAuth（Entra）：令牌过期后调用失败，而 `mcp.list` 仍显示已连接](https://github.com/github/copilot-cli/issues/5366)**
   只有 `restartServer` 才能恢复。连接状态的静默过期是智能体工作流中最糟糕的失败模式 —— 模型会持续调用一个不可能成功的工具。

7. **[#3679 — BYOK Azure：429 限流在约 0.15 秒内耗尽所有重试，且没有有效的退避](https://github.com/github/copilot-cli/issues/3679)**
   五次重试几乎瞬间耗尽，且重试次数与等待时间均不可配置。对于限流是常态的**自托管 / 隔离网络 Azure OpenAI** 部署尤其有害。

8. **[#5367 — 智能体经常丢失 MCP 工具 schema](https://github.com/github/copilot-cli/issues/5367)**
   “我每天都被这个问题困扰” —— schema 在会话中途消失，`/compact` 可将其恢复。这表明问题出在**上下文裁剪丢弃了工具定义**，而非 MCP 服务器本身。

9. **[#5370 — 长 shell 命令转入后台时，智能体输出被扣留到下一次用户轮次（Fusion/Auto 模式）](https://github.com/github/copilot-cli/issues/5370)** · 新分诊
   智能体看起来像是卡住了，直到用户再次输入。这是 **Fusion/Auto 特有的流式/背压回归**，且与 Fusion 层级设置发布在同一天提交。

10. **[#4150 — `permissions-config.json` 中带空格的 `commandIdentifiers` 仍需审批](https://github.com/github/copilot-cli/issues/4150)** · 👍 3
    `"make"` 被允许，但 `"make fix"` 不被允许，与文档示例相矛盾。对于通过智能体自动化构建的用户来说，允许列表的精确性很重要。

**本时间段内关闭（分诊吞吐量不错）：** [#5368](https://github.com/github/copilot-cli/issues/5368)（Windows 沙箱探测 `CreateProcessW` 错误）、[#4845](https://github.com/github/copilot-cli/issues/4845)（会话卡在“In use”）、[#4943](https://github.com/github/copilot-cli/issues/4943)（Esc 丢弃 `ask_user` 表单）、[#2731](https://github.com/github/copilot-cli/issues/2731)（路径含空格时的 `@` 自动补全）、[#5265](https://github.com/github/copilot-cli/issues/5265)（跨进程输入历史最后写入者胜出）。

---

## 4. 关键 PR 进展

> 过去 24 小时内**只有一个** pull request 有更新 —— 相对于 issue 数量（29 个 issue 对 1 个 PR），社区 PR 吞吐量非常低，这本身就值得关注。

1. **[#5373 — docs: 添加简体中文 Copilot CLI README](https://github.com/github/copilot-cli/pull/5373)** *（OPEN）*
   新增 README 的简体中文翻译，以及 EN/中文 双向导航链接，保留命令、配置键、产品名称和上游文档链接。与 [#1972](https://github.com/github/copilot-cli/issues/1972)（IME/提交键）形成有趣的对照：**CJK 用户群体正在积极投入这个工具**，却被输入处理行为所阻碍。

本时间段内没有功能或缺陷修复类 PR 更新。

---

## 5. 热门讨论

*略 —— 本数据集未提供 Discussion 数据。*

---

## 6. 功能请求趋势

从本时间段内的所有 issue 中提炼，五个方向占据主导：

| 方向 | 证据 | 诉求 |
|---|---|---|
| **可配置的输入/键位绑定** | #1972 (48👍)、#2776、#3768、#4995 | 用户自定义提交键、可靠的 Shift+Enter 多行输入、更好的回滚导航 |
| **模型路由控制** | #5369、#5363、v1.0.107 Fusion 层级 | Fusion/Auto 的黑名单、通过 ACP 准确报告 `model` |
| **MCP 认证生命周期** | #5236、#5250、#5366、#5367 | 持久化令牌缓存、并发安全的登录、正确的连接状态、schema 保留 |
| **沙箱/权限粒度** | #5365、#4150、#5368、v1.0.108-0 | 具名策略规则、临时授权的退出选项、多词命令标识符、仅枚举路径 |
| **可扩展性与上下文控制** | #5375、#5099、#5362、#5374 | 禁用内置斜杠命令、仅展示的 hook、由模型判断的工具结果裁剪、桌面应用中的 hook 渲染 |

另外两个较小但很有说明性的诉求：在 `--server` / `--host-environment` 模式下**为 BYOK provider 提供对等支持**（[#5371](https://github.com/github/copilot-cli/issues/5371)），以及**可观测性** —— `copilot --acp` 完全不导出任何 OpenTelemetry 信号（[#5376](https://github.com/github/copilot-cli/issues/5376)）。

---

## 7. 开发者痛点

1. **Entra ID + 远程 MCP 是最大的可靠性黑洞。** 三个独立的未关闭 issue（#5236、#5250、#5366）分别覆盖了 Windows 上缓存未持久化、macOS 上并发登录被拒，以及过期后状态陈旧却仍显示“已连接”。共同点是：**认证状态在不同操作系统和进程边界之间不一致**，且失败表现为反复提示，而非可操作的错误信息。

2. **静默失败多于显式失败。** `mcp.list` 报告 `connected` 但每次调用都失败（#5366）、会话中途丢失工具 schema（#5367）、输出被扣留到下一轮（#5370），在用户看来都像是“智能体坏了”。开发者一再要求提供*可检测的*失败状态。

3. **键盘行为破坏撰写体验。** Shift+Enter 的两个问题（#2776、#3768）加上 IME 误提交（#1972），意味着最基础的交互 —— 输入多行提示词 —— 对相当一部分用户来说并不可靠，而相关修复获得了 48 个赞。

4. **重试/退避不可调。** #3679 中 5 次重试在约 0.15 秒内耗尽且等待时间不可配置，使得 BYOK Azure 部署在常规限流下十分脆弱。

5. **配置面不一致。** 文档示例不生效（#4150 中的 `make fix`），环境变量形式的 BYOK 配置在 `--server` 和 `--host-environment` 模式下会静默消失（#5371）。用户无法预知某个模式会遵循哪条配置路径。

6. **发布说明与 changelog 不一致。** #5377 指出 1.0.106 与 1.0.107 的说明只有日期不同 —— 这是件小事，却会削弱固定版本团队对项目的信任。

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode 社区简报 — 2026-11-07

## 1. 今日要闻

OpenCode 发布了 **v2.2.6**，主打共享 MCP 连接池（配置相同的项目可共用同一个 MCP 服务器连接），以及新的 GUI 插件主题槽位。与此同时，issue 区被两类截然不同的信号占据：一个是已积累 316 条评论的免费额度巨型帖（#59387），事实上已成为官方支持中心；另一组则是“静默失败”类 bug——无效的 agent/config frontmatter 被直接丢弃，且没有任何警告。PR 方面，维护者 `kitlangton` 提交了一大批 `[contributor]` 正确性修复，覆盖 core、TUI 和 Code Mode。

## 2. 版本发布

**v2.2.6**
- **共享 MCP 连接** — 可选的连接池允许配置相同的项目之间共享同一个 MCP 连接，从而减少进程数量并缩短启动时间。（[#61185](https://github.com/anomalyco/opencode/pull/61185)）
- **GUI 插件主题与新槽位** — GUI 插件现在可以为界面设置主题，并挂接到更多 UI 槽位。（源数据中发布说明被截断。）

## 3. 热门 Issue

1. **[#59387](https://github.com/anomalyco/opencode/issues/59387) — [zen] 免费额度巨型帖**（316 条评论，👍23）。这是仓库中流量最高的帖子，汇总了 `Free usage exceeded`、配额不重置以及 Desktop 版本被拒等各类报告，实际上充当了一个非官方状态页。
2. **[#27110](https://github.com/anomalyco/opencode/issues/27110) — 限制并行子代理的最大数量**（14 条评论，👍42——本列表点赞最高项）。本地模型用户在子代理大量并发时会触达上下文/内存上限；设置上限是当前待办列表中支持面最广的需求。
3. **[#40993](https://github.com/anomalyco/opencode/issues/40993) — 支持 agent-plugins.org 标准**（17 条评论，👍21）。这是一个厂商中立的打包规范，将 Agent Skills 与 MCP 服务器打包为可移植插件；其定位面向多厂商而非 OpenCode 专属，这也提高了采用它的分量。
4. **[#51949](https://github.com/anomalyco/opencode/issues/51949) — 压缩后 agent 丢失顶层工具**（15 条评论）。自动压缩之后，agent 把所有请求都走 Code Mode，随后又报告自己没有任何工具。这是压缩路径上的高危正确性 bug。
5. **[#62442](https://github.com/anomalyco/opencode/issues/62442) — 桌面端会话卡死**（6 条评论）。会话一直停留在 `running` 状态，中断返回 `false`，DELETE 请求超时（约 61 秒后返回 HTTP 499）——除重启外没有恢复途径。
6. **[#62447](https://github.com/anomalyco/opencode/issues/62447) — TUI 模型选择器只显示自定义提供商模型的一部分**（5 条评论）。尽管 `/api/model` 返回全部 7 个 `@ai-sdk/openai-compatible` Ollama 模型，界面只显示其中 3 个；重启后依旧。
7. **[#40096](https://github.com/anomalyco/opencode/issues/40096) — TUI 中可折叠的工具输出**（5 条评论，👍2）。Write/Edit/Shell 块全部展开渲染，在长会话中把内容淹没；希望提供 `/collapse` 开关。
8. **[#62460](https://github.com/anomalyco/opencode/issues/62460) — Beam Free 在回合中途卡住**（4 条评论，今日提交）。Agent 说出“let me do this now”后便终止，并未实际执行；与 [#61511](https://github.com/anomalyco/opencode/issues/61511) 中 Crouton Alpha 的卡住模式一致。
9. **[#57844](https://github.com/anomalyco/opencode/issues/57844) — CJK 字形泄漏到俄语输出**（4 条评论）。GLM-5.3-Flash 会在句子中间插入零散的中文/日文 token；[#62411](https://github.com/anomalyco/opencode/issues/62411) 中的相关报告显示波兰语也有同样问题，暗示存在共用的多语言解码问题。
10. **[#62138](https://github.com/anomalyco/opencode/issues/62138) — `/undo` 之后 `/goal` 永不执行**（3 条评论，`[reproduced]`）。目标显示为活跃，但 kickoff 一直留在会话收件箱中，导致 30 分钟计时器始终未启动，目标无限期挂起。

## 4. 重点 PR 进展

1. **[#62491](https://github.com/anomalyco/opencode/pull/62491)**（已关闭）—— 修复 `deploy-site` 失败：`ProductHero` 把完整的产品包引入 `/product/browse`，使其体积达到 116.9 kB，超出 99 kB 的预算。
2. **[#62490](https://github.com/anomalyco/opencode/pull/62490)**（开放中）—— 为新的 `/export` 对话框添加位置选择器，基于 #62253。
3. **[#62253](https://github.com/anomalyco/opencode/pull/62253)**（开放中）—— `/export` 现在写入启动时的工作目录，而不再写入 `/tmp/session-<uuid>.md`，恢复 v1 行为。关闭 #53748。
4. **[#62480](https://github.com/anomalyco/opencode/pull/62480)**（开放中）—— 按缩短请求后剩余的空间来设定压缩输出上限，修复摘要请求本身被限流的问题。关闭 #62185 —— 与 #51949 的后续影响相关。
5. **[#62477](https://github.com/anomalyco/opencode/pull/62477)**（开放中，`[contributor]`）—— 补丁应用保留末尾空行与 CRLF 行尾，而不再删除换行或产生混合行尾。
6. **[#62457](https://github.com/anomalyco/opencode/pull/62457)**（已关闭）—— 监视配置文件的符号链接目标，使由 dotfiles 管理的 `opencode.json` 在编辑后能重新加载，而不再提供过期文档。
7. **[#62252](https://github.com/anomalyco/opencode/pull/62252)**（已关闭）—— 本地化桌面端右键菜单（剪切/复制/粘贴/全选）。关闭 #55912。
8. **[#62474](https://github.com/anomalyco/opencode/pull/62474)**（开放中，`[contributor]`）—— 文件自动补全跳过格式错误的 frecency 条目，而不再将其评分算成 `NaN` 并永久排在后面。
9. **[#62476](https://github.com/anomalyco/opencode/pull/62476)**（开放中，`[contributor]`）—— 本地插件目录缺失时，现在会报出具体路径错误，而不再只有裸的 `ENOENT` 和笼统的“Plugin failed to load”。
10. **[#62478](https://github.com/anomalyco/opencode/pull/62478)**（开放中，`[contributor]`）—— 批量读写 instruction blob，以保持在 Durable Object SQLite 每条语句 100 个值的限制之内，解除 instruction 条目较多的会话阻塞。

## 5. 热门讨论

本期未提供讨论数据，本节省略。

## 6. 功能需求趋势

- **打包与可扩展性** — 采用厂商中立的 [agent-plugins.org 规范](https://github.com/anomalyco/opencode/issues/40993)，加上 v2.2.6 的 GUI 插件主题/槽位与共享 MCP 连接池，都指向插件正成为主要的扩展面。
- **本地模型的资源治理** — 限制并行子代理（[#27110](https://github.com/anomalyco/opencode/issues/27110)，42 👍）与折叠冗长的工具输出（[#40096](https://github.com/anomalyco/opencode/issues/40096)），都是为了把上下文和屏幕空间交回用户掌控。
- **权限与配置的持久化** — 要求不再持久保存“始终允许”的授权（[#62432](https://github.com/anomalyco/opencode/issues/62432)），以及停止静默丢弃配置错误的 agent/命令（[#45876](https://github.com/anomalyco/opencode/issues/45876)、[#52065](https://github.com/anomalyco/opencode/issues/52065)、[#54200](https://github.com/anomalyco/opencode/issues/54200)）。
- **平台原生体验打磨** — 自定义 Linux 标题栏（[#36225](https://github.com/anomalyco/opencode/issues/36225)）、本地化的桌面端右键菜单，以及站点上完整的 Linux 下载覆盖。
- **输出语言控制** — 为多语言模型提供显式的输出语言防护（[#57844](https://github.com/anomalyco/opencode/issues/57844)）。

## 7. 开发者痛点

- **免费额度与重置循环仍是最大的支持负担。** 巨型帖（[#59387](https://github.com/anomalyco/opencode/issues/59387)）毫无降温迹象，用户报告重置计时器每天都往后推（[#62233](https://github.com/anomalyco/opencode/issues/62233)），以及免费模型上持续出现 `ECONNRESET`（[#62179](https://github.com/anomalyco/opencode/issues/62179)）。升级求助的摩擦确实存在：[#62224](https://github.com/anomalyco/opencode/issues/62224) 报告通过邮件提交的退款请求无人回复，且所在地区无法访问 Discord。
- **静默失败是最一致的 bug 类别。** 无效的 YAML frontmatter、以 `[` 开头的描述，以及无法解析的 `model:` 值，都会让 agent 或命令无声消失，且没有任何日志。开发者只有在发现 agent 不存在时才意识到问题——这是可调试性缺口，而不只是解析 bug。
- **压缩是长会话中最薄弱的一环。** 压缩后工具丢失（[#51949](https://github.com/anomalyco/opencode/issues/51949)）加上压缩输出被限流（[#62480](https://github.com/anomalyco/opencode/pull/62480)），说明“摘要并继续”这条路径在能可靠用于大型任务之前还需要加固。
- **会话生命周期并非总能恢复。** 卡在运行中的桌面端会话（[#62442](https://github.com/anomalyco/opencode/issues/62442)）和 undo 之后永远排队的 goal（[#62138](https://github.com/anomalyco/opencode/issues/62138)），让用户除了重启之外没有逃生出口。
- **模型/提供商链路在各层之间不一致。** 服务器报告 7 个模型，TUI 只显示 3 个（[#62447](https://github.com/anomalyco/opencode/issues/62447)）；Windows 11 上的桌面端在开关已打开的情况下仍显示空模型列表（[#61481](https://github.com/anomalyco/opencode/issues/61481)）。
- **托管模型上的流式/解码不稳定** —— 回合中途卡住（Beam Free、Crouton Alpha）以及非英语输出中的外文字形损坏，都在侵蚀用户对免费层和 `opencode-go` 层的信任。

---

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi 社区简报 — 2026-11-07

**来源：** [github.com/earendil-works/pi](https://github.com/earendil-works/pi) · 过去 24 小时内有 50 个 issue、19 个 PR 和 1 个讨论更新。

---

## 1. 今日亮点

过去 24 小时没有发布新版本；这一天主要是大批 bug 修复收尾，约有十几个修复 PR 落地，对应本周早些时候打开的 issue，包括 `edit` 中 CRLF/LF 混合行尾损坏（[#8544](https://github.com/earendil-works/pi/issues/8544)）、macOS/Node 24 图像缩放 worker 崩溃（[#10695](https://github.com/earendil-works/pi/issues/10695)），以及 `openai-completions` 对空 `tools: []` 的拒绝（[#11843](https://github.com/earendil-works/pi/issues/11843)）。唯一新增的升级项是 [p0] [#11869](https://github.com/earendil-works/pi/issues/11869)：Pi 1.5.0 中新的 `StringEnum` 会在编译期将工具参数类型静默降级为 `unknown`，目前仍处于开放状态。SDK 嵌入方也在推进提示/身份定制（[#11317](https://github.com/earendil-works/pi/issues/11317)、[PR #11904](https://github.com/earendil-works/pi/pull/11904)）。

---

## 2. 发布

过去 24 小时无发布。

---

## 3. 热门 Issue

1. **[#1188](https://github.com/earendil-works/pi/issues/1188) [CLOSED] 更好地兼容屏幕阅读器** — 14 条评论，是该时间窗口内讨论最多的条目。80 毫秒一次的 spinner/状态栏重绘洪流让流式会话在辅助技术下无法使用。关闭它是 TUI 在可访问性方面的一个重要里程碑。
2. **[#9932](https://github.com/earendil-works/pi/issues/9932) [OPEN] `before_agent_start` 强制系统提示仍保留通过 `setActiveTools` 移除的工具** — 7 条评论。handler 排序意味着，如果另一个 handler 强制设置系统提示，某个扩展移除的工具仍可能被宣告给模型。这是一个扩展组合正确性 bug，具有切实的安全相关影响（能力泄漏）。
3. **[#11317](https://github.com/earendil-works/pi/issues/11317) [OPEN] 让 SDK 用户自定义身份前言，而无需替换默认章节** — 5 条评论。请求提供 `systemPromptOptions.sections.preamble`，以便设置人设时无需手动重建 Pi 的工具/规则/文档。与 [PR #11904](https://github.com/earendil-works/pi/pull/11904)（已关闭）配套。
4. **[#11869](https://github.com/earendil-works/pi/issues/11869) [OPEN] [bug, inprogress, prio:p0] Pi 1.5.0 中新的 `StringEnum` 破坏编译期类型** — 2 个 👍，是唯一仍开放的 p0。`registerTool` 通过 typebox 的 `TSchema` 推导 `TParam`，但新的 `StringEnum` 不再这样做，导致 enum 字段变为 `unknown`。工具编写界面的类型安全是一项重大回归。
5. **[#10585](https://github.com/earendil-works/pi/issues/10585) [OPEN] bash 临时文件流错误在 1.0.4 上仍会导致整个进程崩溃** — 4 条评论。`/tmp` 写满时的 `ENOSPC` 会产生未捕获异常并丢失进行中的会话，且不会暴露任何工具错误。这是长时间 agent 运行的持久性隐患。
6. **[#10650](https://github.com/earendil-works/pi/issues/10650) [OPEN] codemode：在 `allSettled` 输出中保留嵌套 `Error` 消息** — 4 条评论。拒绝结果被序列化为 `reason: {}`，模型只能基于缺失的信息进行推理。这直接削弱了 codemode 的自纠错循环。
7. **[#9817](https://github.com/earendil-works/pi/issues/9817) [CLOSED] [no-action] 扩展无法解析通过 `package.json` 的 `main`/`exports` 声明的 npm 包** — 5 条评论，1 个 👍。只有带根 `index.js` 的包才能解析；`yaml` 及类似包在运行时失败。以 no-action 关闭，给扩展生态留下了一个棘手问题。
8. **[#11858](https://github.com/earendil-works/pi/issues/11858) [CLOSED] [no-action] codemode 脚本将同一工具调用重复了 24,018 次** — 3 条评论。约 680 次/秒持续 35 秒，每次读取一个 28 KB 文件，仅靠墙钟超时中止才停下。没有按脚本的重复调用防护；这强烈表明沙箱护栏很薄弱。
9. **[#11876](https://github.com/earendil-works/pi/issues/11876) [CLOSED] `edit` 和 `read` 工具被 shell 命令绕过** — 3 条评论。在 Windows 11 / 1.5.0 上，模型转而使用 `sed`，绕过了工具插桩并重写了行尾。这凸显了引导工具偏好的必要性。
10. **[#8544](https://github.com/earendil-works/pi/issues/8544) [CLOSED] `edit` 在 CRLF/LF 混合文件中重写未改动行的行尾** — 3 条评论。编辑一个字符会静默改变其他行的字节。已由 [PR #11751](https://github.com/earendil-works/pi/pull/11751) 修复；相关的非 UTF-8 情况被拆分出去。

*另值得关注：*[#11676](https://github.com/earendil-works/pi/issues/11676)（open，inprogress）`Harness.close()` 保留了 provider WebSocket + 空闲计时器；[#11863](https://github.com/earendil-works/pi/issues/11863) 请求为长期运行的嵌入式 agent 提供上下文管理指南，已以 no-action 关闭。

---

## 4. 关键 PR 进展

1. **[#11751](https://github.com/earendil-works/pi/pull/11751) fix(coding-agent)：edit 保留未改动行的行尾** — 只在实际改动附近重写，并避免写入后 diff 导致 OOM。这是当天影响最大的数据完整性修复。
2. **[#11896](https://github.com/earendil-works/pi/pull/11896) fix(coding-agent)：等待图像缩放 worker 终止** — `finally` 中的 `void worker.terminate()` 会让下一个 worker 在清理尚未完成时启动；改为 await 后可阻止 macOS arm64 / Node 24 的进程中止。
3. **[#11891](https://github.com/earendil-works/pi/pull/11891) fix(ai)：在 `openai-completions` 上为工具历史省略空的 `tools` 数组** — 解除对 vLLM 0.20+ 和 LiteLLM 网关的阻塞，这些网关会对 `"tools": []` 返回 400。
4. **[#11890](https://github.com/earendil-works/pi/pull/11890) fix(tui)：在覆盖的行之后绘制全屏 Kitty 图像** — 修复 Orca/WezTerm xterm.js 的平铺渲染问题：全屏图像只显示第一行。
5. **[#11888](https://github.com/earendil-works/pi/pull/11888) fix(coding-agent)：在每次提示前将实时思考级别记录到分支上** — 修复 `/tree` 回退后恢复时思考级别陈旧的问题（[#11451](https://github.com/earendil-works/pi/issues/11451)）。
6. **[#11882](https://github.com/earendil-works/pi/pull/11882) openai-completions：当流式 index 与 id 不一致时拆分工具调用块** — 防止不符合规范的 provider 将两个不同的工具调用拼接成一个格式错误的参数块。
7. **[#11895](https://github.com/earendil-works/pi/pull/11895) fix(coding-agent)：相对于 `path` 匹配 grep glob** — 当 `path` 不是当前工作目录时，带文件夹组件的 glob 现在可以正常工作，而不再错误返回 "No matches found"。
8. **[#11873](https://github.com/earendil-works/pi/pull/11873) fix(coding-agent)：保留提示模板参数中的撇号** — `/ask why doesn't the user's cache work` 不再变成 `why doesnt the users cache work`。
9. **[#11885](https://github.com/earendil-works/pi/pull/11885) fix(ai)：重试回复开始后被截断的流** — 将 Magpie/Anthropic 的回复中途截断消息加入 `RETRYABLE_PROVIDER_ERROR_PATTERN`。
10. **[#11894](https://github.com/earendil-works/pi/pull/11894) [OPEN] fix(coding-agent)：edit 拒绝非有效 UTF-8 文件** — 目前该工具会静默替换为 U+FFFD，并在 UTF-16/GBK/Windows-1252 文件上报告成功。仍处于开放状态；作为 #11751 的配套值得关注。

*另已合并/关闭：*[#11904](https://github.com/earendil-works/pi/pull/11904) 自定义系统提示前言、[#11887](https://github.com/earendil-works/pi/pull/11887) 全屏 stderr 损坏、[#11893](https://github.com/earendil-works/pi/pull/11893) `reasoning_effort` 现在通过 `thinkingLevelMap` 路由、[#11874](https://github.com/earendil-works/pi/pull/11874) Windows Bun 虚拟入口路径、[#11900](https://github.com/earendil-works/pi/pull/11900) CONTRIBUTING RFC 链接。*仍开放：*[#11889](https://github.com/earendil-works/pi/pull/11889) Windows 控制台关闭时的 shutdown 清理、[#11878](https://github.com/earendil-works/pi/pull/11878) codemode 局部 `tools`/`console` 声明。

---

## 5. 热门讨论

**展示与分享**
- **[#11870](https://github.com/earendil-works/pi/discussions/11870) 你如何在 Pi 和其他 agent 之间组织 skills？**（1 个 👍，0 条评论）— 在 Pi、Claude Code 和 OpenCode 目录中重复的 skills 会逐渐不一致；作者构建了一个 macOS 应用 + CLI **AgentKeeper** 来编目它们。目前尚无回复——正好邀请社区比较跨 agent 的 skill/包管理方法。

---

## 6. 功能请求趋势

- **SDK/嵌入控制面。** 最明显的集中方向：可替换身份前言且不丢失默认章节（[#11317](https://github.com/earendil-works/pi/issues/11317)、[PR #11904](https://github.com/earendil-works/pi/pull/11904)）、强制提示与活动工具之间正确的 handler 排序（[#9932](https://github.com/earendil-works/pi/issues/9932)）、`pi-durable` 中按对话的压缩策略（[#11771](https://github.com/earendil-works/pi/issues/11771)）、队列意图处置报告（[#11872](https://github.com/earendil-works/pi/issues/11872)），以及长时间、工具密集型多模态会话的文档化模式（[#11863](https://github.com/earendil-works/pi/issues/11863)）。用户已经从“我能否嵌入 Pi”转向“我能否治理它”。
- **模型目录时效性。** 三个独立请求要求添加模型：为 `kimi-coding` 添加 `k3.1-pro` / 视频模态（[#11883](https://github.com/earendil-works/pi/issues/11883)）、为 `zai` 添加 `glm-5.3-flashx`（[#11884](https://github.com/earendil-works/pi/issues/11884)），以及为 `qw` 添加 `deepseek-v4.1-flash-1017`（[#11877](https://github.com/earendil-works/pi/issues/11877)）。全部以 no-action 关闭——说明仍缺少一条持续维护的目录更新路径。
- **Codemode 健壮性与沙箱化。** 序列化中的错误保真度（[#10650](https://github.com/earendil-works/pi/issues/10650)）、正确打印 `NaN`/`Infinity`（[#11901](https://github.com/earendil-works/pi/issues/11901)）、重复调用护栏（[#11858](https://github.com/earendil-works/pi/issues/11858)），以及被遮蔽的参数名（[#11878](https://github.com/earendil-works/pi/pull/11878)）。
- **可访问性与终端保真度。** 屏幕阅读器安全渲染（[#1188](https://github.com/earendil-works/pi/issues/1188)）、在支持 Kitty 的终端中显示全屏内联图像（[#11835](https://github.com/earendil-works/pi/issues/11835)），以及带外 stderr 隔离（[PR #11887](https://github.com/earendil-works/pi/pull/11887)）。
- **Windows 平台一致性。** 仅 SIGHUP 的关闭处理（[PR #11889](https://github.com/earendil-works/pi/pull/11889)），以及 Bun SEA 的 `B:/~BUN/root/` 入口路径泄漏到子 agent argv（[#11865](https://github.com/earendil-works/pi/issues/11865)）。

---

## 7. 开发者痛点

- **`edit` 导致的静默文件损坏。** 混合行尾（[#8544](https://github.com/earendil-works/pi/issues/8544)）和非 UTF-8 编码（[#11894](https://github.com/earendil-works/pi/pull/11894)）都会在返回“成功”的同时改动用户从未触碰的字节。对 edit 工具的信任是反复出现的主题。
- **未处理的错误杀死进程，而非仅影响工具调用。** `/tmp` 写满时的 `ENOSPC`（[#10585](https://github.com/earendil-works/pi/issues/10585)）和 worker 清理竞态（[#10695](https://github.com/earendil-works/pi/issues/10695)）都会丢失正在进行的运行。用户期望按工具隔离错误边界，得到的却是进程中止。
- **codemode 输出对模型不友好。** `reason: {}`、`avg null` 以及 2.4 万次无防护的重复调用，要么给模型喂入错误状态，要么在没有护栏的情况下烧掉 token（[#10650](https://github.com/earendil-works/pi/issues/10650)、[#11901](https://github.com/earendil-works/pi/issues/11901)、[#11858](https://github.com/earendil-works/pi/issues/11858)）。
- **Provider 不合规问题泄漏到 Pi。** 空的 `tools: []`（[#11843](https://github.com/earendil-works/pi/issues/11843)）、重复的流式 `index`（[#11881](https://github.com/earendil-works/pi/issues/11881)）、过期的 Copilot token 返回 401/403（[#10238](https://github.com/earendil-works/pi/issues/10238)），以及 Kimi K2.7 将思考内容作为回复打印（[#10237](https://github.com/earendil-works/pi/issues/10237)）——全部已关闭，其中多项为 no-action。
- **扩展生态摩擦。** 使用 `main`/`exports` 的 npm 包无法解析（[#9817](https://github.com/earendil-works/pi/issues/9817)），`before_agent_start` 的 handler 排序未定义（[#9932](https://github.com/earendil-works/pi/issues/9932)）。扩展作者在承受平台缺口。
- **嵌入式使用中的资源与状态生命周期 bug。** `Harness.close()` 让 provider 会话和 WebSocket 继续存活（[#11676](https://github.com/earendil-works/pi/issues/11676)），以及 `/tree` 之后思考级别陈旧（[#11451](https://github.com/earendil-works/pi/issues/11451)），这类缺陷只在长期运行的主机进程中才会暴露。

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code 社区简报 — 2026-11-07

## 1. 今日亮点

Qwen Code 发布了 **v0.26.9**，主打完整的 skill 安装与激活流水线（ZIP 上传、daemon API、TypeScript SDK 支持、批量激活以及恢复保护），同日还切出了 nightly 版本。Issue 流量仍以**会话/transcript 持久性**问题为主——由快照二次方增长导致的 P1 会话无法打开 bug（#13113）和 P1 无头模式下自动记忆写入被拒（#15597）目前均未关闭。与此同时，managed-agent 相关能力正快速扩张，两个大型 PR 分别新增 Skills/MCP 自助服务（#16500）以及执行诊断与 agent 图谱（#16498）。

---

## 2. 版本发布

**v0.26.9** — [Release](https://github.com/QwenLM/qwen-code/releases)
- 无已知破坏性变更。
- **Skill 安装与激活**：skill ZIP 上传、daemon API、TypeScript SDK 支持、批量激活和恢复保护，使安装与管理 skill 成为一等公民式的工作流，而不再是手动文件操作。

**v0.26.9-nightly.20261106.80fd7c3353** — [Release](https://github.com/QwenLM/qwen-code/releases)
- 从 `release/v0.26.9-nightly.20261106.80fd7c3353` 切出的 nightly 版本。
- 测试基础设施工作：交互式测试现在轮询前台 shell 帧，而不是对其做快照（[#16455](https://github.com/QwenLM/qwen-code/pull/16455)），另有 managed-agent 测试调整。

---

## 3. 热门 Issue

1. **[#13113](https://github.com/QwenLM/qwen-code/issues/13113) — 会话无法打开："Transcript snapshot is too large to index"（P1，11 条评论）**
   `file_history_snapshot` 呈二次方增长，而 256 MiB 的索引上限是硬编码的，因此涉及大量文件改动的长会话会永久无法加载——每次 `POST /session/:id/load` 都会失败。这是跟踪器中严重程度最高的持久性 bug：它悄无声息，用户侧无法恢复，并且直接阻断了长时间运行 agent 会话的核心价值。

2. **[#15597](https://github.com/QwenLM/qwen-code/issues/15597) — 无头 `-p` 自动记忆永远无法保存（P1，8 条评论）**
   在非交互模式且使用 default/Auto 审批策略时，隐式的 `edit`/`write_file` 拒绝会覆盖 memory agent 的作用域允许规则。记忆逻辑在每次 prompt 之后运行，却什么都没写入，而用户仍要为这些 token 付费。这是一个直接影响成本的权限优先级 bug。

3. **[#16418](https://github.com/QwenLM/qwen-code/issues/16418) — 反复自动压缩使一个仅限审查的 skill 变成未授权的实现工作（P1，4 条评论）**
   一个长时间运行的会话调用了 `/ce-doc-review`（仅做审查 + 有限的安全修复），最终却把方案实现进了应用源码。以 Critical 级别提交：这是一个**范围遵循**失效，且只在压缩场景下才暴露，因此它属于信任与安全问题，而非普通 bug。

4. **[#16508](https://github.com/QwenLM/qwen-code/issues/16508) — 空闲时自动压缩（P2，3 条评论）**
   提议在用户离开时而非回合中途运行摘要器，这样压缩延迟永远不会落在用户可见的回合内，下一回合也能从一个已压缩、可缓存的 prefix 开始。2026-11-07 提交，且已配上设计文档 PR（#16509）——跟踪器中推进最快的想法。

5. **[#14724](https://github.com/QwenLM/qwen-code/issues/14724) — 主 CI 失败：Managed Agent 门禁，未能定位测试（218 条评论）**
   仓库中流量最高的单个 issue，自 2026-10-19 起一直开启，至今仍在不断积累机器人评论。从症状上看，CI 失败报告器无法归因到具体失败的测试，因此该 issue 永远无法自动关闭。评论数反映的是**机器人噪音和 CI 分诊债务**，而非社区关注度。

6. **[#15412](https://github.com/QwenLM/qwen-code/issues/15412) — 过长的项目路径导致聊天记录无法保存并使会话列表损坏（P2，7 条评论）**
   每个项目的会话目录由完整的工作目录绝对路径派生并压平成单个路径组件，在 macOS 上可能超出文件系统限制。深层嵌套的 monorepo 会悄无声息地丢失聊天历史。

7. **[#15310](https://github.com/QwenLM/qwen-code/issues/15310) — 告诉模型它用的是哪个 shell，而不是让它假定是 bash（P3，6 条评论）**
   在 Windows 上，命令实际通过 `cmd.exe` 执行，但 system prompt 里没有任何说明，于是模型会输出根本无法工作的 POSIX 命令。报告者在一次无人值守运行中就观察到两个被浪费的回合——一个成本极低的 prompt 修复，却能带来超乎比例的可靠性收益。

8. **[#16417](https://github.com/QwenLM/qwen-code/issues/16417) — 在生成之前就拒绝超大的 `write_file`（P2，3 条评论）**
   超出剩余输出预算的 `write_file` 会被完整生成，然后才以 `Response truncated due to token limit` 为由拒绝。结果什么都没写入，整个回合白白浪费；报告者在同一个文件上连撞三次。这是对输出预算做预检的明确理由。

9. **[#15800](https://github.com/QwenLM/qwen-code/issues/15800) — 会话突然结束后，后台 shell 状态文件永远停留在 "running"（P2，3 条评论）**
   后台进程已经完成，但其持久化的状态文件从不更新，留下过期状态，被后续会话和工具误读为仍在运行。属于后台自动化生命周期缺口这一簇问题。

10. **[#16444](https://github.com/QwenLM/qwen-code/issues/16444) — CLI 后台视图自动弹出的 UX 边界问题（P3，4 条评论）**
    从 #16432 延后处理：自动打开的对话框成了 Ctrl+C 的目标，于是第一次中断只是关掉了对话框，并没有传到输入框。这体现了该仓库纪律严明的“延后但不丢弃”评审文化——非阻塞性发现会被记录成 issue，而不是被丢掉。

*另外值得注意：* [#15675](https://github.com/QwenLM/qwen-code/issues/15675)（sed 模拟与真实 `sed` 存在偏差——`.` 不匹配 `\r`，emoji 处理也不一致）在 5 条评论后关闭；[#15833](https://github.com/QwenLM/qwen-code/issues/15833) 已关闭——取消一个 shell 命令时，过去会告诉模型“该工具已经完成”。

---

## 4. 关键 PR 进展

1. **[#16500](https://github.com/QwenLM/qwen-code/pull/16500) — feat(managed-agent)：Skills 与 MCP 自助服务**
   让 managed-agent 的运维者自行管理 Skills 和 MCP 服务器，而不必依赖维护者介入。这是本窗口内最大的一次能力面扩张，也是 v0.26.9 skill 流水线的自然延伸。

2. **[#16498](https://github.com/QwenLM/qwen-code/pull/16498) — feat(managed-agent)：执行诊断与 agent 图谱**
   新增持久化、归属于会话的工具/Skill 使用记录，原生的文件操作与行变更度量，以及经授权的 agent 关系视图。值得注意的是，它把对话结果与物理执行证据分开，因此一次成功的写入在取消之后仍然可见。

3. **[#16432](https://github.com/QwenLM/qwen-code/pull/16432) — fix(cli)：在交互式 TUI 中呈现工作流 agent 的审批请求（#16425）**
   修复了一个死锁：后台工作流 agent 撞上工具审批门禁时，会把审批挂起，却没有任何一方能应答，于是运行永远无法推进。现在注册表在挂起前必须先有一个真实的应答者。

4. **[#16404](https://github.com/QwenLM/qwen-code/pull/16404) — fix(cli)：在非交互与 ACP 运行中呈现堆叠 skill 溢出**
   当 prompt 堆叠超过五个 skill 时发出警告。无头运行写入 stderr（包括在 JSON/stream-JSON 模式下），ACP 客户端会在模型响应之前收到实时通知，渠道桥接也能在工具往返过程中保留该通知。

5. **[#16439](https://github.com/QwenLM/qwen-code/pull/16439) — fix(core)：恢复会话时重放持久化的启动 prelude**
   恢复的会话现在会重放当初发送的启动 prelude 原文，而不是依据当前日期、目录树、git 快照、MCP 指令、skills 和延迟工具列表重新构建。这直接提升了 prefix 缓存复用率和恢复的确定性。

6. **[#16509](https://github.com/QwenLM/qwen-code/pull/16509) — docs(core)：设计空闲时自动压缩流程**
   为 #16508 撰写的双语设计文档，确定了可评审的契约：双条件触发（空闲时长 + 软水位）。与 issue 同日落地——一次异常迅速的 issue 到设计闭环。

7. **[#16507](https://github.com/QwenLM/qwen-code/pull/16507) — perf(core)：分块比较已增长的本地 journal 前缀**
   #16483 的后续：读取期间发生增长的文件现在按 64 KiB 分块比较，而不是整体重读。这是 journal 路径上的性能加固，对大型 managed 会话尤为重要。

8. **[#16352](https://github.com/QwenLM/qwen-code/pull/16352) — fix(managed-agent)：掩码以数字开头的密码，并拒绝空的 Harness 用户信息**
   关闭了升级命令 `start`/`verify` 阶段中来自 #16110/#16175 的评审事项：此前的拒绝逻辑会把直到 URL 最后一个 `@` 之前的内容全部掩掉，即使 URL 解析后并无用户信息。这是一次凭据泄露加固。

9. **[#16461](https://github.com/QwenLM/qwen-code/pull/16461) — feat(cli)：新增 `ui.startExpanded`**
   布尔值（默认 `false`），启动 TUI 时展开思考块和工具输出。它填补了 `ui.compactMode` 退役后留下的空缺——该选项的移除让用户再也无法表达“我始终希望看到推理过程”。

10. **[#15852](https://github.com/QwenLM/qwen-code/pull/15852) — ci(release)：仅在失败时提交 issue，并清理过期 issue**
    两个扇出式发布通知器都把取消的运行当作失败并提交 issue，而且在后续尝试成功发布后都无法将其关闭。这直接针对 #14724 中可见的过期 issue 堆积问题。

*另外已合并/进行中：* [#16489](https://github.com/QwenLM/qwen-code/pull/16489) 已关闭——晚到的快照发布现在会等待进行中的 claim，而不再按页被当作死锁牺牲者回滚；[#16492](https://github.com/QwenLM/qwen-code/pull/16492) 增加了 #14674 要求的 deferred-acquire-vs-close 验收测试；[#16482](https://github.com/QwenLM/qwen-code/pull/16482) 将 `@qwen-code /review` 和 `/resolve` 的权限门禁路由到自托管 ECS 池；[#14353](https://github.com/QwenLM/qwen-code/pull/14353) 让 Web Shell 遵循配置的加载提示语。

---

## 5. 热门讨论

本窗口未提供讨论数据，故本节从略。

---

## 6. 功能需求趋势

- **Skills 成为可管理的产品面。** v0.26.9 的 ZIP 上传 / 批量激活 / SDK 支持，加上 #16500（Skills 与 MCP 自助服务）和 #16404（堆叠 skill 溢出警告），显示 skill 正从一种 prompt 约定转变为受治理、带配额的平台功能。预计下一步的压力会落在 skill 权限与来源追溯上。
- **上下文与压缩性能。** #16508/#16509（空闲时压缩）、#16439（重放 prelude 以复用缓存）和 #16417（输出预算预检）都瞄准同一个成本中心：回合内被浪费的 token 与延迟。空闲时压缩是最清晰的新兴架构方向。
- **Managed-agent 可观测性。** #16498（执行诊断、agent 图谱）、#14629（OpenRouter 路由 provider 及以 generation ID 作为 span 属性）和 #15414（被丢弃的 cache-write token 计数）构成了一致诉求：更丰富、更真实的遥测。
- **大规模下的会话持久性。** #13113、#15412、#15800 和 #14674 都涉及必须在长会话、深层路径和突然终止中存活下来的状态。
- **跨平台的 shell 正确性。** #15310（在 system prompt 中声明 shell）、#15675（sed 模拟保真度）和 #16449（amend-guard 识别器覆盖）表明 shell 工具是与真实世界行为悄然偏离的最常见来源。
- **Web Shell / UI 易用性。** #16474（锚定选区的内联分支线程）、#13911（自动回顾忽略该设置）、#16471（managed-host 评审延后项）和 #14353（自定义加载提示语）显示配套 UI 正从原型走向可配置的产品。
- **Provider 覆盖广度。** #16506 请求一个 Wally（RunAnywhere）的 `OpenAI` 兼容 provider 预设，延续了对一等预设而非手动 endpoint 配置的稳定需求。

---

## 7. 开发者痛点

- **CI 噪音正在淹没 issue 跟踪器。** #14724 已有 218 条来自自动报告器的评论，而它无法定位失败的测试；#16191 和 #13953 也以更小的规模呈现同样模式。多个 PR（#15852、#16319、#16482）明确是在让 CI 报告更安静、能自我核对——维护者已把这视为分诊上的一笔真实税负。
- **权限语义彼此矛盾。** #15597（无头模式下隐式拒绝覆盖作用域允许）和 #16418（仅审查的 skill 在反复压缩后升级为源码实现）是同一个底层抱怨：模型的实际有效权限无法从用户所写的配置中预测出来。
- **静默且不可恢复的会话丢失。** #13113（transcript 无法打开）、#15412（路径长度破坏记录）和 #15800（"running" 状态过期）都在损害发生的那一刻不给出明确错误。用户事后才发现丢失，这对编码 agent 来说是最糟糕的失败模式。
- **可避免的错误浪费回合。** #15310（假定错误的 shell）、#16417（超大写入先生成再丢弃）和 #16418（压缩引发的漂移）各自烧掉一整次模型往返。在无人值守运行中，这些问题会迅速叠加，也是功能请求中最常被引用的动机。
- **延迟落在用户的回合内。** 压缩目前会在回合中途触发，因此用户把摘要感知为一次卡顿。这是被引用最一致的单条性能抱怨，也是 #16508 在一天内从 issue 变成设计文档的原因。
- **后台执行缺少生命周期。** #15800（状态永不清除）和 #16444（自动弹出的对话框抢走 Ctrl+C）显示后台 shell 和后台 agent 在正常路径上能工作，但在边缘情况下会留下过期或被劫持的状态。

</details>

---
*本日报由 [agents-radar](https://github.com/SnowEagle-Kn/agents-radar) 自动生成。*