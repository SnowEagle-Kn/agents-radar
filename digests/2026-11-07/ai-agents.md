# OpenClaw 生态日报 2026-11-07

> Issues: 365 | PRs: 500 | 覆盖项目: 5 个 | 生成时间: 2026-11-07 02:20 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw 项目深度报告

# OpenClaw 项目摘要 — 2026-11-07

---

## 1. 今日概览

OpenClaw 仍处于高频、高竞争的维护阶段：过去 24 小时内有 **365 个 issue** 和 **500 个 PR** 被更新，其中 **160 个 issue 已关闭**（占被触及 issue 的 43.8%），**123 个 PR 已合并/关闭**（约占被触及 PR 的 24.6%）。今日没有发布新版本，跟踪器中评论最多的 issue 是一个 **P0 崩溃循环回归**（[#160959](https://github.com/openclaw/openclaw/issues/160959)，80 条评论），自 9 月下旬起一直处于开启状态，这表明部分阻碍发布的缺陷已经跨越了多个发布周期仍未解决。PR 队列是当前主要的健康隐患：**377 个 PR 仍处于开启状态**，而今日关闭的只有 123 个；在评论最多的 PR 中，有相当一部分是草稿、占位符或“需要补充证明”的候选，而非可合并的工作。活跃度很强，但吞吐量偏向于接收与分诊，而不是落地修复——这与一个承受高用户负载、并依赖激进自动化分诊的项目相符（几乎每个 issue 都带有 `clawsweeper:*` 标签）。

---

## 2. 发布情况

**今日无新版本发布。** Latest Releases 区块为空。

从 issue 流量中可以看出：当前稳定线似乎是 **2026.10.10**，预发布版 **2026.11.5-beta.1** 在 [#141474](https://github.com/openclaw/openclaw/issues/141474) 和 [#193962](https://github.com/openclaw/openclaw/issues/193962) 中被提及。值得注意的是，[#193962](https://github.com/openclaw/openclaw/issues/193962) 报告称，2026.10.10 上的受影响用户**无法通过 `openclaw update` 获取 2026.11.5-beta.1 中的修复**，因为已安装的更新器的 10 秒 SQLite 头部探测会超时——这是一个分发渠道层面的阻塞问题，值得在下一次稳定版发布前优先处理。

---

## 3. 项目进展

**今日合并/关闭的 PR（部分）：**

| PR | 变更 |
|---|---|
| [#194384](https://github.com/openclaw/openclaw/pull/194384) | `fix(cron)`：一次性任务的收尾以及启用/禁用状态切换现在可在 PostgreSQL 16 上正常工作（`function json(unknown) does not exist`）；同时修复了 PostgreSQL 18 缓存一致性断言 |
| [#194399](https://github.com/openclaw/openclaw/pull/194399) | `fix(media)`：被中断的附件下载现在会被取消，而不是一直运行到进程退出 |
| [#194407](https://github.com/openclaw/openclaw/pull/194407) | `fix(ci)`：在 Vitest 元数据增长时保留编译 worker 的复用 |
| [#194405](https://github.com/openclaw/openclaw/pull/194405) | `test(ui)`：修复不稳定的已配置 provider 图标测试（定时运行 42319656353） |
| [#194411](https://github.com/openclaw/openclaw/pull/194411) | `fix(plugins)`：恢复缺失的 OpenClaw ID Access 素材（解除素材检查阻塞） |
| [#194190](https://github.com/openclaw/openclaw/pull/194190) | `fix(ui)`：在紧凑行中，当日时间戳仅显示时间 |
| [#194152](https://github.com/openclaw/openclaw/pull/194152) | `ui`：对话框关闭与幽灵图标操作统一改用 `IconButton` |

**今日关闭的 issue（部分）：** [#166017](https://github.com/openclaw/openclaw/issues/166017)（MCP 会话空闲 TTL 默认值未生效 → 256 子进程 spawn-broker 耗尽）、[#178291](https://github.com/openclaw/openclaw/issues/178291)（P0：2026.10.4→10.5 聊天更新在激活后 Doctor 检查中失败）、[#164923](https://github.com/openclaw/openclaw/issues/164923)（某工作区短期记忆晋升 0/512）、[#155457](https://github.com/openclaw/openclaw/issues/155457)（Anthropic 流式 JSON 参数格式错误）、[#168794](https://github.com/openclaw/openclaw/issues/168794)（Codex 中继 >32 MiB 图片 → 502）、[#165433](https://github.com/openclaw/openclaw/issues/165433)（重连信封被拒绝）、[#164690](https://github.com/openclaw/openclaw/issues/164690)（`claude-cli` 下 stdio MCP 重复生成）。

**评估：** 当日落地的工作主要是**正确性与 CI/测试卫生**，而非面向用户的功能。影响最大的关闭项是 [#166017](https://github.com/openclaw/openclaw/issues/166017)——一个导致所有 exec/read/MCP 启动瘫痪的硬上限耗尽缺陷——但该修复尚未确认已在稳定版中发布。

---

## 4. 社区热点

**#160959 — [P0] Gateway 在捕获大型外部插件时阻塞数分钟（2026.9.6 回归）** · 80 条评论 · [链接](https://github.com/openclaw/openclaw/issues/160959)
这是项目中讨论最热烈的线程。自插件生成捕获功能落地（#144252）以来，拥有大型外部/工作区插件依赖树的 Gateway 会在启动时以及每次重新发布 prepared-runtime 时阻塞其事件循环**数分钟**。被标记为 `impact:crash-loop`、`P0`、`clawsweeper:no-new-fix-pr`。其根本需求是架构性的：插件捕获必须移出 V8 主线程，或改为增量/缓存方式。相关的活性证据见 [#166641](https://github.com/openclaw/openclaw/issues/166641)（`web_fetch` 回退路径同步捕获插件）。

**#164294 — Gateway 状态数据库的 WAL 自动检查点永不完成** · 39 条评论 · [链接](https://github.com/openclaw/openclaw/issues/164294)
一个 PASSIVE 检查点被进程内的读取器钉住（`checkpointedFrames=866`、`logFrames=17091`、`consecutiveBlocked` 持续攀升）。用户实际上只能眼看着 WAL 无界增长而无任何补救途径。需要对 `state/openclaw.sqlite` 进行长生命周期读取器审计。

**#177656 — [P1] 原生主机 exec 返回共享状态完整性准入错误** · 30 条评论 · [链接](https://github.com/openclaw/openclaw/issues/177656)
在稳定版 2026.10.5 上出现 ≥28 次失败，子进程副作用未证实，无修复。错误信息：`Direct shared-state reader requires current integrity admission`。这是没有关联修复 PR 的最高严重度 issue。

**#150635 — Dreaming 深度阶段永不晋升（短期回忆被驱逐）** · 25 条评论 · [链接](https://github.com/openclaw/openclaw/issues/150635)
在 512 条目上限处，夜间摄取会暂存数百条零召回条目，将真正被召回的条目驱逐出去，因此晋升永远不会发生。结合今日关闭的 [#164923](https://github.com/openclaw/openclaw/issues/164923)，这指向一个系统性的**记忆层级容量/驱逐策略**问题，而非两个孤立的 bug。

**#191944 — `gateway stop` / `stop --force` 在 macOS 上失败** · 23 条评论 · [链接](https://github.com/openclaw/openclaw/issues/191944)
`ServiceInspectionError: Process ancestry could not be fully inspected`。一个基础的生命周期操作在一流桌面平台上失效——实现复杂度低，但 UX 影响面大。

**PR 侧：** 最值得关注的开放候选是 [#194376](https://github.com/openclaw/openclaw/pull/194376)（`feat(gateway)`：为 `sessions_send` 唤醒启动添加准入边界——解决并发发送者绕过批处理/速率限制的问题）、[#182383](https://github.com/openclaw/openclaw/pull/182383)（`feat(plugin-sdk)`：访问策略修订缓存，安全敏感）以及 [#194133](https://github.com/openclaw/openclaw/pull/194133)（UI 菜单行结构规范化）。队列中还包含明确的非工作项——[#191509](https://github.com/openclaw/openclaw/pull/191509)（“空的 iOS 交接占位符——请勿使用”）和 [#188726](https://github.com/openclaw/openclaw/pull/188726)（调查占位符）——它们抬高了开放 PR 的计数。

---

## 5. 缺陷与稳定性

按严重度排序。“修复 PR”表示数据中存在已关联/已合并的候选修复。

| 严重度 | Issue | 症状 | 修复 PR？ |
|---|---|---|---|
| **P0** | [#160959](https://github.com/openclaw/openclaw/issues/160959) | 插件捕获导致 Gateway 事件循环阻塞数分钟；崩溃循环 | 无 |
| **P1** | [#177656](https://github.com/openclaw/openclaw/issues/177656) | 原生主机 exec：共享状态完整性准入错误，出现 28+ 次 | 无 |
| **P1** | [#191847](https://github.com/openclaw/openclaw/issues/191847) | 2026.10.10 回归：大会话反复使 512 MB 的 SQLite worker OOM；**每一**轮都因 “admission is closed” 失败 | 无 |
| **P1** | [#192959](https://github.com/openclaw/openclaw/issues/192959) | K8s Pod 重启后 Gateway 拒绝启动（fsGroup 重新挂载导致客户端凭据目录失效） | 无 |
| **P1** | [#185656](https://github.com/openclaw/openclaw/issues/185656) | 大型 zstd 会话记录归档在媒体持久化迁移中超出 Node 字符串长度上限——**尽管修复 #170853 已合并，#170847 仍回归**；阻塞更新 | 已合并但无效 |
| **P1** | [#182647](https://github.com/openclaw/openclaw/issues/182647) | 云端 worker 轮次在推理前失败：“Session capability metadata requires worker preparation” | `queueable-fix` |
| **P1** | [#166641](https://github.com/openclaw/openclaw/issues/166641) | `web_fetch` 冷回退在 Gateway 主线程上同步捕获插件 → 活性停滞（已捕获 V8 profile） | 无 |
| **P1** | [#155563](https://github.com/openclaw/openclaw/issues/155563) | 2026.9.5 回归：成功完成一轮后 `service-child-relay`/`group-anchor`/MCP 分组仍被保留 | 无 |
| **P1** | [#126950](https://github.com/openclaw/openclaw/issues/126950) | Telegram 的 `tgcb1:` 不透明回调载荷对非插件调用方被静默丢弃（消息丢失） | 已关联 PR 开放 |
| **P1** | [#157182](https://github.com/openclaw/openclaw/issues/157182) | 当环境主会话不可见时，Control UI 强制跳转 `/chat/main` 并无限重试（已标记安全问题） | 无 |
| **P1** | [#162087](https://github.com/openclaw/openclaw/issues/162087) | 9.7：对 9.7 之前的账户模式 cron 任务，定时 `announce` 失败（缺少 `ownerOrigin`） | 无 |
| **P2** | [#164294](https://github.com/openclaw/openclaw/issues/164294) | WAL 检查点被永久阻塞 | 无 |
| **P2** | [#141474](https://github.com/openclaw/openclaw/issues/141474) | `sessions_yield` 使 `agents_wait` 永久悬挂；`outputSchema` 在 `claude-cli` 上无效——**修复仅在 2026.11.5-beta.1 中，不在稳定版** | 预发布中 |
| **P2** | [#193962](https://github.com/openclaw/openclaw/issues/193962) | SQLite 头部探测超时修复（#187197）无法从 2026.10.10 通过 `openclaw update` 获取 | 修复已存在，分发受阻 |
| **P2** | [#182891](https://github.com/openclaw/openclaw/issues/182891) | 提示缓存从未被复用——每个请求都出现 `cache read dropped N -> 0` | 无 |
| **P2** | [#191944](https://github.com/openclaw/openclaw/issues/191944) | macOS 上 gateway stop 失败（进程祖先检查） | 无 |
| **P2** | [#158127](https://github.com/openclaw/openclaw/issues/158127) | 2026.9.6 多智能体运行时发布失败 + Codex 启动超时 | 无 |
| **P2** | [#167609](https://github.com/openclaw/openclaw/issues/167609) | Code Mode 将 guest 的 TypeError 报告为 `internal_error`（在 main `e29577b7d7d` 上可实时复现） | `fix-shape-clear` |
| **P2** | [#123792](https://github.com/openclaw/openclaw/issues/123792) | 使用 `claude-cli` 后端时助手轮次被重复持久化/渲染 | 无 |

**稳定性解读：** 最主要的故障簇是 **Gateway 主线程阻塞与 SQLite worker 压力**（#160959、#166641、#191847、#164294、#185656）。第二簇是**运行时生命周期泄漏**（#155563、现已关闭的 #166017、已关闭的 #164690）。第三簇是**更新/安装完整性**（#185656、#193962、#178291）——这令人担忧，因为它可能把用户困在已知有问题的版本上。有两个 P1 回归（#185656、#191847）已确认存在于当前稳定线中。

---

## 6. 功能请求与路线图信号

**成本与可观测性**
- [#13219](https://github.com/openclaw/openclaw/issues/13219) — 按模型的使用量日志，用于成本跟踪（自 2026 年 2 月开启，10 条评论，👍1）。[#143017](https://github.com/openclaw/openclaw/issues/143017)（实测 2130 万缓存写入 token）和 [#130257](https://github.com/openclaw/openclaw/issues/130257)（委派的 ACP 子运行花费 $14.14，而父级为 $0.04，且无法归因）反复强化了这一需求。**近期路线图的强候选**：三个独立的成本可见性 issue，且都附有生产环境数据。
- [#178373](https://github.com/openclaw/openclaw/issues/178373) — 缺少运行级别的轮次/token 预算；工具循环检测为可选（默认关闭）；保留的历史每轮都被重新计费。

**智能体/会话管理**
- [#166678](https://github.com/openclaw/openclaw/issues/166678) — 在选择器中隐藏智能体，但仍保持其可被生成（WebUI/macOS/iOS），👍1。
- [#158800](https://github.com/openclaw/openclaw/issues/158800) — 在 Control UI 中 `/new` 之后可浏览/分叉此前的频道聊天片段。
- [#103067](https://github.com/openclaw/openclaw/issues/103067) — 集中管理聊天会话命名；定义子智能体会话生命周期与跨频道持久化。

**频道与集成**
- [#136271](https://github.com/openclaw/openclaw/issues/136271) — msteams：为频道上传动态解析团队的 SharePoint 站点。
- [#162087](https://github.com/openclaw/openclaw/issues/162087) — cron `announce` 的账户模式归属（是 bug，但会驱动投递模型决策）。

**UX 与配置**
- [#163739](https://github.com/openclaw/openclaw/issues/163739) — 让 Ctrl+Shift+A（归档）可配置；它与浏览器快捷键冲突，👍1。工作量小，收益明确。
- [#79223](https://github.com/openclaw/openclaw/issues/79223) — 可配置的 Dream Diary 语言（`memory-core` 中硬编码英文提示词），👍2——非英文工作区反复提出的抱怨。
- [#164620](https://github.com/openclaw/openclaw/issues/164620) — 提供禁用托管插件目录源的设置（`clawhub.ai` 为硬编码）。
- [#130247](https://github.com/openclaw/openclaw/issues/130247) — cron 任务无法结束：没有 `--until`/`--max-runs`，也没有累计运行计数器。

**预测：** 下一周期最有可能落地的条目是**成本/用量聚合（#13219 系列）**、**cron 生命周期边界（#130247）**和**快捷键可配置（#163739）**——它们都范围窄、定义清晰，且有反复出现的报告支撑。记忆/dreaming 相关工作（#150635、#79223）在实现前很可能需要一次设计决策（`clawsweeper:needs-product-decision`）。

---

## 7. 用户反馈总结

**痛点，按信号频次排序：**

1. **启动/升级脆弱。** 从 2026.9.6→2026.10.10 的用户报告 Gateway 阻塞数分钟、迁移在大型归档上失败、以及无法通过更新摆脱问题版本（#160959、#185656、#193962、#178291）。这是不满情绪的最大单一来源。
2. **记忆功能令人失望。** “dreaming”/短期回忆系统被广泛报告为未按宣传方式工作——某个工作区晋升 0/512，而同类工作区达到 50–90%（#164923、#150635）。用户自己在做细致的对比调试，这说明文档/可观测性存在缺口。
3. **成本不透明。** 多位用户量化了无法归因的大额支出（#143017：2130 万缓存写入 token；#130257：父子成本相差 100 倍）。提示缓存被报告为从未复用（#182891），直接转化为计费 token。
4. **平台相关的生命周期故障。** macOS 的 `gateway stop`（#191944）、Kubernetes fsGroup 重启（#192959）、Android 每 0.8 秒轮询一个未知智能体（#167316，已关闭）。桌面/容器用户感觉相对于 Linux systemd 路径是二等公民。
5. **多智能体复杂度。** 运行时发布被取代错误、Codex 启动超时、重复的 MCP 生成、每个 stdio 子进程 145 MB 的包装器开销（#158127、#164690、#164017 系列）。运行大量智能体的运维者报告资源增长不可预测。

**满意信号：** 报告始终详细、可复现，并标明版本——用户群体技术素养高且投入度高。今日的若干关闭项（#166017、#164923、#178291）都源自持续数周的实质性讨论，说明用户确实会跟踪修复直至解决。主要不满在于 **P0/P1 项的修复时间**，而非对报告的响应速度。

---

## 8. 积压观察

**长期未回应的高价值 issue：**

| 时长 | 条目 | 重要性 |
|---|---|---|
| 约 9 个月 | [#13219](https://github.com/openclaw/openclaw/issues/13219) 按模型的使用量日志（2026 年 2 月） | 对最强烈的成本抱怨而言成本最低的答案；仍处于 `needs-product-decision` |
| 约 6 个月 | [#79223](https://github.com/openclaw/openclaw/issues/79223) Dream Diary 语言（2026 年 5 月） | `memory-core` 中硬编码提示词；修复极其简单，却阻塞国际用户 |
| 约 4 个月 | [#112638](https://github.com/openclaw/openclaw/issues/112638) `session.maintenance` 强制模式无法约束存储 | 磁盘静默无界增长；`maxEntries`/`maxDiskBytes` 实际上只是建议性的 |
| 约 4 个月 | [#103067](https://github.com/openclaw/openclaw/issues/103067) 集中管理聊天会话命名 / 子智能体生命周期 | 多智能体 UX 的基础；不断产生重复报告 |
| 约 3 个月 | [#126950](https://github.com/openclaw/openclaw/issues/126950) Telegram 不透明回调被丢弃 | 主要频道上的消息丢失；已关联 PR 开放但停滞 |
| 约 3 个月 | [#130247](https://github.com/openclaw/openclaw/issues/130247) / [#130257](https://github.com/openclaw/openclaw/issues/130257) cron 没有结束条件；ACP 运行成本无法归因 | 均由同一运维者提交；调度器缺少基本的生命周期语义 |
| 约 2.5 个月 | [#123792](https://github.com/openclaw/openclaw/issues/123792) 助手轮次被重复渲染（`claude-cli`） | Control UI + Android 上可见的数据完整性/UX 缺陷 |

**需要维护者关注的停滞 PR：**

- [#75225](https://github.com/openclaw/openclaw/pull/75225) — `feat(agents)`：在 `agents_list` 中暴露已配置的描述。自 **2026-04-30** 开放，`proof: sufficient`，`status: ⏳ waiting on maintainer`。超过六个月且证明包已充分，这是流程失败，而非技术问题。
- [#125969](https://github.com/openclaw/openclaw/pull/125969) — `fix(gateway)`：限定图片能力发现的适用范围。自 2026-08-18 开放，`ready for maintainer look`。
- [#130231](https://github.com/openclaw/openclaw/pull/130231) — `ci(i18n)`：非发布型语言区域刷新。自 2026-08-26 开放，被标记 `merge-risk: 🚨 security-boundary`、`needs proof`。
- [#182383](https://github.com/openclaw/openclaw/pull/182383) — `feat(plugin-sdk)`：访问策略修订缓存（XL，安全敏感）。需要安全审查者，而不是作者继续迭代。
- [#161362](https://github.com/openclaw/openclaw/pull/161362) — `fix(agents)`：停止警告“有文档记载的核心工具组需要插件”。`security-review-required`，自 2026-09-29 起等待作者。

**建议：** 开放 PR 积压（377）现在已经大到足以掩盖真正的工作——多个排名靠前的 PR 明确是不可合并的占位符（[#191509](https://github.com/openclaw/openclaw/pull/191509)、[#188726](https://github.com/openclaw/openclaw/pull/188726)）。关闭它们或将其转为草稿 issue，并优先处理两个没有修复 PR 的 P0/P1 回归（[#160959](https://github.com/openclaw/openclaw/issues/160959)、[#177656](https://github.com/openclaw/openclaw/issues/177656)），将实质性改善项目的观感与实际健康状况。

---

## 横向生态对比

⚠️ 横向对比生成失败。

---

## 同赛道项目详细报告

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — 项目摘要，2026-11-07

## 1. 今日概览

活动量很高，且明显以维护工作为主：过去 24 小时内有 50 个议题和 50 个 PR 更新，关闭了 5 个议题，合并/关闭了 13 个 PR，但**没有发布新版本**。当天的工作集中在三条线上：npm→pnpm 工作区切换后的清理与后续事项（#149746 相关），`alt-glitch` 提交的一大组更新路径加固 PR（由 #150383 跟踪），以及网关/会话存储可扩展性和“会话权威”切换（#145171）方面的持续压力。讨论最多的条目 #139818（42 条评论）今日关闭，此前围绕远程后端 Desktop 会话无法恢复展开了长讨论——这表明会话状态正确性是项目的主要痛点。整体健康度可读为**活跃但回归问题偏多**：近期基础设施变更（pnpm、状态存储迁移、更新交换机制）正在产生一串可见的后续缺陷，其中包括一个未关闭的 **Windows 上的 P0 数据完整性问题**（#147938）。

---

## 3. 项目进展

展示的 20 个评论最多的 PR 仍全部处于开启状态；13 个已合并/关闭的 PR 未在样本中逐项列出，因此已合并功能的归因仅限于明显仍在进行中的内容。

**进行中的高影响工作：**

| 领域 | PR | 状态 |
|---|---|---|
| 平台抽离 | [#150210](https://github.com/NousResearch/hermes-agent/pull/150210) — Yuanbao 从核心移出，成为 Hermes Labs 插件，并为现有用户提供自动迁移 | 开启，CI 已审查 |
| 会话读取 API | [#150102](https://github.com/NousResearch/hermes-agent/pull/150102) — `session.transcript`：无需挂接即可读取已存储会话（选择器字段、转录页、压缩记录、子代理转录） | 开启 |
| 状态后端 | [#150421](https://github.com/NousResearch/hermes-agent/pull/150421) — 可选的对话状态 PostgreSQL 后端（阶段 B），叠加在 #150332 上 | 开启 |
| 更新路径 | [#150385](https://github.com/NousResearch/hermes-agent/pull/150385)、[#150386](https://github.com/NousResearch/hermes-agent/pull/150386)、[#150387](https://github.com/NousResearch/hermes-agent/pull/150387)、[#150405](https://github.com/NousResearch/hermes-agent/pull/150405)、[#150406](https://github.com/NousResearch/hermes-agent/pull/150406)、[#150407](https://github.com/NousResearch/hermes-agent/pull/150407)、[#150408](https://github.com/NousResearch/hermes-agent/pull/150408) | 开启 |
| 测试/CI 加固 | [#150411](https://github.com/NousResearch/hermes-agent/pull/150411)（升级测试套件从真实发布版本启动）、[#150418](https://github.com/NousResearch/hermes-agent/pull/150418)（在更新路径 PR 上运行升级 E2E 分片）、[#150195](https://github.com/NousResearch/hermes-agent/pull/150195)（消除拒绝标记测试的偶发失败） | 开启 |
| pnpm 后续事项 | [#150430](https://github.com/NousResearch/hermes-agent/pull/150430)（缓存输入、未声明依赖、锁文件属性）、[#150419](https://github.com/NousResearch/hermes-agent/pull/150419)（为 linux-arm64-bionic/Termux 固定 android-arm64） | 开启 |
| 重构 | [#150388](https://github.com/NousResearch/hermes-agent/pull/150388) — 从核心移除 CJK FTS5 分词器；[#150401](https://github.com/NousResearch/hermes-agent/pull/150401) — 按包限定的日志器路由 | 开启 |

**今日关闭（议题侧）：** #139818（远程后端 Desktop 恢复）、#106960（systemd 仪表盘被盘点为手动 serve）、#149861（pnpm 切换后 macOS Desktop 构建失败）、#150035 和 #150036（pnpm npmrc 读取器 / 版本固定整合）。

值得注意的方向：多个 PR 明确要求在状态存储目录迁移**之前**发布（#150407：“必须在迁移前达到稳定版本”），这表明这是一次协调、分阶段的迁移，而非临时打补丁。

---

## 4. 社区热点

| 条目 | 信号 | 底层需求 |
|---|---|---|
| [#139818](https://github.com/NousResearch/hermes-agent/issues/139818) — 远程后端 Desktop 会话无法恢复（`session.resume` → not_found） | **42 条评论**，3 个 👍，已关闭 | 用户在远程主机上运行 `hermes serve`，并期望 Desktop 对话在进程重启后仍能保留。长讨论串反映出用户对会话持久化位置及其归属感到困惑。 |
| [#146643](https://github.com/NousResearch/hermes-agent/issues/146643) — 网关在每次变更时重新读取完整会话历史；CPU 和 Desktop 超时随存储规模增长 | 17 条评论，P2 性能 | **可扩展性上限**：在几千个会话的存储上，网关会消耗 1.5–2.5 个核心，Desktop 请求会触发 30 秒超时。这是重度用户面临的首要结构性风险。 |
| [#106960](https://github.com/NousResearch/hermes-agent/issues/106960) — 更新后 systemd 仪表盘被盘点为手动 serve | 14 条评论，已关闭 | 使用 systemd 单元的自托管用户希望获得一等公民式的服务发现，而不是回退启发式判断。 |
| [#139347](https://github.com/NousResearch/hermes-agent/issues/139347) — 有界托管房间转录会丢弃仍被结算引用的用户 | 9 条评论，1 个 👍 | 多参与者/托管房间的正确性：驱逐逻辑与校验不变量冲突。 |
| [#147938](https://github.com/NousResearch/hermes-agent/issues/147938) — state.db 校验把活跃锁误判为损坏 | 7 条评论，**P0** | 对更新器的信任。用户需要“不要动我的数据”的保证。 |
| [#105163](https://github.com/NousResearch/hermes-agent/issues/105163) — 仅支持英语的停滞防护意图检测器 | 7 条评论 | 非英语操作者正在无声地失去防停滞保护。 |

**解读：** 社区重心已从功能请求转向**运营信任**——会话持久性、更新安全性和规模化性能。42 条评论的 #139818 讨论串是最清晰的信号，表明会话权威（#145171）是正确的架构押注。

---

## 5. 缺陷与稳定性

按严重程度排序。“修复 PR”指今日样本中可见的 PR。

**P0 — 数据完整性**
- [#147938](https://github.com/NousResearch/hermes-agent/issues/147938) *（开启，Windows）* — `state.db` 校验在更新暂停窗口内无法区分活跃锁与损坏；它会把数月前的陈旧快照恢复到健康数据之上，随后又把成功的恢复报告为“未完成”。今日风险最高的未关闭议题。样本中无关联修复 PR。

**高 — 数据丢失 / 安全边界**
- [#150436](https://github.com/NousResearch/hermes-agent/issues/150436) *（开启，v0.23.0 Docker）* — 任何配置保存（例如一次“始终允许”批准）都会删除那些同时设置在受管 `/etc/hermes/config.yaml` 作用域中的键的用户值。静默、用户不可见的配置丢失。无修复 PR。
- [#147720](https://github.com/NousResearch/hermes-agent/issues/147720) *（开启，P3 安全）* — 按配置文件路由的子代理会继承**父级**的工具集，而不是目标配置文件的工具集；禁用了终端的目标配置文件在被委派时仍可运行 shell 命令。这是权限边界违规；标签低估了影响。
- [#137723](https://github.com/NousResearch/hermes-agent/issues/137723) *（开启，P2）* — Codex 配额恢复在内存中将凭据标记为 `ok`，但持久化为 `exhausted`，因此池重载会让过期配额错误复活。无修复 PR。

**中 — 功能故障**
- [#148329](https://github.com/NousResearch/hermes-agent/issues/148329) *（开启，P2）* — 在 git 工作树内锁定技能会阻塞该仓库中的**所有** git 写入，且代理侧没有补救办法。保护功能变成了工作阻塞项。
- [#146643](https://github.com/NousResearch/hermes-agent/issues/146643) *（开启，P2 性能）* — 见热点议题；CPU/超时随存储规模增长。
- [#133006](https://github.com/NousResearch/hermes-agent/issues/133006) *（开启，P2，macOS）* — Desktop 表单填写：浏览器目标陈旧/为空，加上未经验证的代理恢复循环。
- [#146280](https://github.com/NousResearch/hermes-agent/issues/146280) *（开启，P2）* — Anthropic 用量：`utilization <= 1%` 因百分比字段上的分数缩放而被报告为 100%。看似表面问题，但会侵蚀计费信任。
- [#148277](https://github.com/NousResearch/hermes-agent/issues/148277) *（开启，P2）* — 已落地的 Desktop 消息还会被恢复到输入框中；重新发送会以新的提交 ID 再运行一次。
- [#150397](https://github.com/NousResearch/hermes-agent/issues/150397) *（开启）* — 多路复用网关（11 个配置文件）中，本地终端后端会用进程 cwd 覆盖被路由配置文件显式设置的 `terminal.cwd`。
- [#150056](https://github.com/NousResearch/hermes-agent/issues/150056) *（开启）* — `message_agent` 投递在压缩后数小时内以 `admission_conflict` 被拒绝；**原因未确定**，先前的假设已被重放推翻。

**pnpm 切换引发的回归集群（#149746）**
- [#149861](https://github.com/NousResearch/hermes-agent/issues/149861) *（已关闭）* — pnpm 12.15.0 静默丢弃 `get-windows` 可选依赖；所有 macOS Desktop 构建均失败。
- [#150146](https://github.com/NousResearch/hermes-agent/issues/150146) *（开启）* — 同一故障，在 `main`（v0.23.0+209）上仍可复现。两个文件对缺失可选依赖是否致命存在分歧。**修复 PR：** [#150430](https://github.com/NousResearch/hermes-agent/pull/150430)、[#150419](https://github.com/NousResearch/hermes-agent/pull/150419)。

**更新路径正确性（开启，附带修复 PR）**
- [#150398](https://github.com/NousResearch/hermes-agent/issues/150398) → [#150408](https://github.com/NousResearch/hermes-agent/pull/150408)
- [#150399](https://github.com/NousResearch/hermes-agent/issues/150399) → [#150407](https://github.com/NousResearch/hermes-agent/pull/150407)
- [#150403](https://github.com/NousResearch/hermes-agent/issues/150403) → [#150406](https://github.com/NousResearch/hermes-agent/pull/150406)
- [#150402](https://github.com/NousResearch/hermes-agent/issues/150402) → [#150405](https://github.com/NousResearch/hermes-agent/pull/150405)

**低**
- [#105163](https://github.com/NousResearch/hermes-agent/issues/105163)（i18n 停滞防护）、[#79836](https://github.com/NousResearch/hermes-agent/issues/79836)（Desktop 侧边栏缺少平台分区）、[#150369](https://github.com/NousResearch/hermes-agent/issues/150369)（iPhone Safari 麦克风需要双击；AudioContext 手势门控）、[#150439](https://github.com/NousResearch/hermes-agent/issues/150439)（在危险命令提示中明确选择“拒绝”后的重新授权路径不清晰）。

---

## 6. 功能请求与路线图信号

**用户请求：**
- [#150431](https://github.com/NousResearch/hermes-agent/issues/150431) — 受管作用域应能**锁定** `mcp_servers`（映射级锁定，而非叶子合并）。这是企业/管理员部署的信号。
- [#148307](https://github.com/NousResearch/hermes-agent/issues/148307) — 当用户在 Desktop 上活跃时，抑制常规移动推送通知。跨设备在线状态感知。
- [#145171](https://github.com/NousResearch/hermes-agent/issues/145171) — 跟踪议题：将 `serve`、远程 Desktop 和 Web 切换至网关会话权威。

**下一版本可能包含（高置信度）：**
1. **pnpm 稳定化**——修复 macOS Desktop 构建，并进行锁文件/版本固定整合（#150146、#150430、#150419、#150041 跟踪）。
2. **更新路径加固包**——#150383 系列（#150385–#150408、#150411、#150418）有明确排序，部分以在状态存储迁移前落地为前提。
3. **`session.transcript` 读取 API**（#150102）——为第三方前端（提及 hermes-tern）解除阻塞，并且是可扩展会话选择器的前提条件。

**有可能但并非迫在眉睫：** PostgreSQL 状态后端阶段 B（#150421，叠加且需主动启用）；Yuanbao 从核心到插件的抽离（#150210）；为 `wally`（#150438）和 Yandex Mail（#150433）增加目录项。

**值得标记的结构性押注：** 状态存储迁移（`src/hermes_agent/`），加上 #150388 的 FTS5 CJK 移除和 #150421 的 Postgres 接缝，共同表明会话存储正在进行重构。预计落地时会有迁移说明和次版本号提升。

---

## 7. 用户反馈总结

**痛点，按强烈程度排序：**

1. **更新安全性主导了焦虑。** 用户不是在要求功能；他们在报告更新可能让 macOS 构建搁浅（#149861、#150146）、把活跃数据库锁误诊为损坏并回滚到陈旧数据（#147938），或静默丢弃用户配置（#150436）。`alt-glitch` 的更新路径系列读起来像是对累积信任侵蚀的直接回应。
2. **重启后的会话持久性。** #139818 的 42 条评论和 #146643 的超时都描述了同一种真实体验：当网关/serve 进程轮转时，对话会“消失”或变得不可达。远程后端和多配置文件自托管用户是受影响最大的群体。
3. **规模。** 几千个会话就足以打满核心并破坏 Desktop 用户体验（#146643）。重度用户正在触及 SQLite 后端设计的实际上限。
4. **多配置文件 / 多租户运营。** #150397（一个网关中有 11 个配置文件）、#147720（配置文件工具集泄漏）、#150431（管理员锁定）都来自将 Hermes 作为共享基础设施而非单用户助手来运行的运营者。
5. **客户端功能对等差距。** Desktop 侧边栏遗漏已启用平台（#79836）、iPhone Safari 需要双击（#150369）、移动通知重复 Desktop 活动（#148307），以及发送内容可能被复制到输入框（#148277）。

**满意信号：** 深思熟虑、可复现的报告附带版本/提交固定信息，甚至包含自我纠正（#150056 在重放失败后明确撤回自己的假设），表明用户群参与度高且技术成熟。最热议题上的 3 个 👍 以及 #139347/#148307 各 1 个，绝对数字很低——这个社区通过详细评论而非表情反应来表达认同。

**不满信号：** 反复出现的“代理侧没有补救办法”（#148329）、“原因未确定”（#150056）以及“成功恢复被报告为未完成”（#147938）等措辞，指向一种认知：故障模式不透明，自我诊断不可靠。

---

## 8. 待办积压观察

具有一定存续时间且没有可见修复 PR 的议题：

| 议题 | 存续时间（截至 2026-11-07） | 重要性 |
|---|---|---|
| [#79836](https://github.com/NousResearch/hermes-agent/issues/79836) — Desktop 消息侧边栏缺少 `raft` 和其他已启用平台 | ~93 天 | 平台适配器已发布，但在 UI 中不可见；这是多平台用户长期存在的功能对等差距。6 条评论，无修复 PR。 |
| [#105163](https://github.com/NousResearch/hermes-agent/issues/105163) — 仅支持英语的停滞防护意图检测器 | ~61 天 | 会静默禁用所有非英语代理运行的防停滞功能。修复范围小且明确（`_TRAILING_CONTINUE_INTENT_RE`），投资回报清晰。 |
| [#133006](https://github.com/NousResearch/hermes-agent/issues/133006) — macOS Desktop 表单填写失败，浏览器目标陈旧 | ~33 天 | 标记为 `needs-repro`；阻塞 macOS 上的旗舰级 computer-use 用例。 |
| [#139347](https://github.com/NousResearch/hermes-agent/issues/139347) — 有界转录驱逐破坏结算引用 | ~23 天 | `DiscussionValidationError` 会让房间卡住；这是托管房间中的正确性缺陷，1 个 👍，无修复 PR。 |
| [#145171](https://github.com/NousResearch/hermes-agent/issues/145171) — 入口点切换跟踪议题 | ~12 天 | 元议题；需要明确排序，以避免 #139818 一类缺陷再次出现。 |

**需要维护者关注的 PR：** [#150418](https://github.com/NousResearch/hermes-agent/pull/150418) 是一个明确的**草稿，等待 CI 负责人做出成本决策**（会给更新路径 PR 增加运行器成本），并叠加在 #150411 之后。[#150421](https://github.com/NousResearch/hermes-agent/pull/150421)（Postgres 阶段 B）叠加在 #150332 上，如果阶段 A 未被审查就会停滞。[#150210](https://github.com/NousResearch/hermes-agent/pull/150210)（Yuanbao 抽离）是一次跨仓库迁移，在所有开放 PR 中协调成本最高。

---

*数据基础：2026-11-07 更新了 50 个议题和 50 个 PR；议题/PR 列表分别按评论数截断至前 30 和前 20，因此已合并 PR 的归因和零评论条目代表性不足。所有链接均指向 `github.com/NousResearch/hermes-agent`。*

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

过去24小时无活动。

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
*本日报由 [agents-radar](https://github.com/SnowEagle-Kn/agents-radar) 自动生成。*