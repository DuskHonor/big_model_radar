# OpenClaw 生态日报 2026-10-13

> Issues: 500 | PRs: 500 | 覆盖项目: 12 个 | 生成时间: 2026-10-13 05:22 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [NanoBot](https://github.com/HKUDS/nanobot)
- [Zeroclaw](https://github.com/zeroclaw-labs/zeroclaw)
- [PicoClaw](https://github.com/sipeed/picoclaw)
- [NanoClaw](https://github.com/qwibitai/nanoclaw)
- [IronClaw](https://github.com/nearai/ironclaw)
- [LobsterAI](https://github.com/netease-youdao/LobsterAI)
- [TinyClaw](https://github.com/TinyAGI/tinyclaw)
- [Moltis](https://github.com/moltis-org/moltis)
- [CoPaw](https://github.com/agentscope-ai/CoPaw)
- [ZeptoClaw](https://github.com/qhkm/zeptoclaw)
- [EasyClaw](https://github.com/gaoyangz77/easyclaw)

---

## OpenClaw 项目深度报告

# OpenClaw 项目动态日报 (2026-10-13)

## 1. 今日速览
过去 24 小时内，OpenClaw 社区保持高度活跃，Issues 与 PR 更新量均触及数据上限（各 500 条），其中约 64% 的 Issues 为新开或活跃状态，显示核心用户群对近期版本稳定性问题的关注度高。项目今日密集发布了两个版本：稳定版 **v2026.10.2** 和 Beta 版 **v2026.10.5-beta.2**，主要聚焦于解决 Windows/macOS 下的更新挂起、内存泄漏及 SQLite 数据库完整性问题。尽管维护者（特别是 @steipete）提交了大量性能优化和架构重构 PR，但社区仍处于“高修复压力”阶段，P0 级别的崩溃循环（Crash-loop）和数据丢失问题依然是当前阻碍用户升级的主要痛点。

## 2. 版本发布
今日共发布 2 个版本，重点在于提升更新过程的鲁棒性和跨平台兼容性。

### v2026.10.2 (Stable)
*   **核心更新**：
    *   修复了从 2026.10.1 升级时的首次升级错误，确保原地更新（in-place updates）不再以错误结束。
    *   解决了 Windows、克隆文件系统（cloned-filesystem）及私有 Node 环境下的更新挂起或回滚问题 ([#168849](https://github.com/openclaw/openclaw/issues/168849), [#169031](https://github.com/openclaw/openclaw/issues/169031))。
    *   **迁移注意事项**：建议用户检查更新日志，确认 `update` 命令是否成功结算了之前遗留的 `update-recovery-pending` 状态。

### v2026.10.5-beta.2 (Beta)
*   **核心更新**：
    *   **更新恢复机制优化**：`openclaw update` 现在能更好地处理过期的 handoff leases、已完成的 receipts 以及 legacy 2026.9.8 恢复记录，避免在 `update-recovery-pending` 处停止。
    *   **资源管理**：保持服务堆内存限制（heap limit）和 webhook 监听器的稳定性。
    *   **Windows 兼容性**：处理了 Windows 短路径（short-path）相关的更新异常。

## 3. 项目进展
今日合并/关闭的 PR 集中在**性能优化**、**架构重构**和**CI/CD 稳定性**三个方面。

*   **性能与资源管理**：
    *   维护者提交了多项针对 Gateway 启动速度和会话观察写的优化 PR，例如 [PR #170271](https://github.com/openclaw/openclaw/pull/170271) 减少了会话观察写入请求，[PR #169649](https://github.com/openclaw/openclaw/pull/169649) 实现了干净关闭后数据库验证的复用，旨在降低重启开销。
    *   [PR #170673](https://github.com/openclaw/openclaw/pull/170673) 优化了 Agent 动态工具 Schema 的复用，减少了重复构建开销。
*   **架构与安全**：
    *   一系列重构 PR（如 [PR #170539](https://github.com/openclaw/openclaw/pull/170539) 和 [PR #170541](https://github.com/openclaw/openclaw/pull/170541)）致力于将跨存储仪表板写入移出主线程，并强化会话成员权限的权威性，提升系统并发处理能力和安全性。
    *   [PR #170583](https://github.com/openclaw/openclaw/pull/170583) 和 [PR #170632](https://github.com/openclaw/openclaw/pull/170632) 完善了认证凭据的发布机制，确保 SDK 和 Worker 写入路径与 Gateway 生命周期同步。
*   **前端体验**：
    *   修复了 Control UI 中聊天记录加载时的抖动问题，并优化了侧边栏刷新浏览器检查速度 ([PR #170671](https://github.com/openclaw/openclaw/pull/170671))。

## 4. 社区热点
以下 Issues 今日讨论最为活跃（按评论数排序），反映了用户对稳定性和功能完整性的强烈诉求：

1.  **[Gateway Memory Leak — RSS grows from 350MB to 15.5GB](https://github.com/openclaw/openclaw/issues/91588)** (39 评论)
    *   **状态**：Open (P0, Crash-loop)
    *   **热点分析**：这是当前最严重的稳定性问题。用户报告 Gateway 进程在 2-3 天内内存从 350MB 膨胀至 15.5GB，导致 OOM Kill 和反复重启。该 Issue 标记为 `needs-maintainer-review`，表明维护团队已知晓但尚未提供明确修复路径，用户焦虑度高。
2.  **[llama.cpp manager reports ready while embedding child exits](https://github.com/openclaw/openclaw/issues/159356)** (26 评论)
    *   **状态**：Open (P2, Session-state)
    *   **热点分析**：涉及本地模型嵌入服务的状态竞态条件。用户通过增加主机 RAM 实现了操作恢复，但历史 OOM 相关失败的根因仍未完全定位。
3.  **[WhatsApp: Backfill missed messages after reconnection](https://github.com/openclaw/openclaw/issues/50093)** (20 评论)
    *   **状态**：Open (P1, Message-loss)
    *   **热点分析**：功能性缺失。用户强烈希望能补全断连期间未收到的消息，目前 Gateway 重连后消息静默丢失，影响消息可靠性。
4.  **[Gateway blocks for minutes while capturing large external plugins](https://github.com/openclaw/openclaw/issues/160959)** (20 评论)
    *   **状态**：Open (P0, Regression)
    *   **热点分析**：2026.9.6 引入的回归问题。大型插件包导致事件循环阻塞数分钟，严重影响启动体验，需紧急修复。
5.  **[OpenClaw leaks unreaped hook/tool child processes](https://github.com/openclaw/openclaw/issues/97616)** (18 评论)
    *   **状态**：Open (P1, Zombie accumulation)
    *   **热点分析**：Hook/Tool 执行后子进程未被正确回收，导致僵尸进程累积，长期运行后性能退化。

## 5. Bug 与稳定性
今日报告的 P0/P1 级 Bug 主要集中在**内存泄漏**、**更新失败**和**数据完整性**三个领域：

*   **P0 严重 (Critical)**：
    *   **[内存泄漏导致 OOM 崩溃](https://github.com/openclaw/openclaw/issues/91588)**：无明确 Fix PR，处于 `needs-maintainer-review` 状态。这是当前最大的稳定性风险。
    *   **[Gateway 阻塞数分钟](https://github.com/openclaw/openclaw/issues/160959)**：2026.9.6 回归，无 Fix PR，需维护者介入。
    *   **[Windows 启动时间增加至 1030s](https://github.com/openclaw/openclaw/issues/168955)**：2026.10.1 回归，导致 Windows 上启动极慢，无 Fix PR。
    *   **[plugin-captures 临时目录未 GC](https://github.com/openclaw/openclaw/issues/158390)**：磁盘无限增长，无 Fix PR。
*   **P1 高 (High)**：
    *   **[Zombie 进程累积](https://github.com/openclaw/openclaw/issues/97616)**：无 Fix PR。
    *   **[Config 热重载失败导致插件不可用](https://github.com/openclaw/openclaw/issues/154891)**：回滚后状态不一致，无 Fix PR。
    *   **[Context Overflow 预检高估 Token](https://github.com/openclaw/openclaw/issues/101929)**：高估 2.3-2.6 倍，导致不必要的截断，无 Fix PR。
*   **已有进展/修复**：
    *   macOS App 就绪看门狗 SIGTERM 重启循环问题已在 [Issue #158936](https://github.com/openclaw/openclaw/issues/158936) 中关闭。
    *   更新激活 Doctor 失败问题已在 [Issue #168750](https://github.com/openclaw/openclaw/issues/168750) 中关闭。

## 6. 功能请求与路线图信号
*   **MCP 工具调用审批**：[Issue #78308](https://github.com/openclaw/openclaw/issues/78308) 提议允许 MCP 服务器使用与 Shell 执行相同的通道中介审批管道。由于涉及安全边界，标记为 `needs-security-review`，且已有高评论量，表明安全合规是重要路线图方向。
*   **SecretRef 支持**：[Issue #76493](https://github.com/openclaw/openclaw/issues/76493) 请求在 MCP 服务器环境配置中支持 SecretRef 对象，以实现密钥间接引用。此需求符合企业级安全最佳实践，被标记为 `needs-product-decision`。
*   **Context Length Fallback**：[Issue #9986](https://github.com/openclaw/openclaw/issues/9986) 已关闭，确认将在上下限超出时触发模型回退。相关性能优化 PR 正在推进中。

## 7. 用户反馈摘要
*   **痛点**：
    *   **稳定性**：用户普遍反映 Gateway 在长期运行后出现内存泄漏、僵尸进程和 SQLite 损坏，导致服务不可用。
    *   **更新体验**：Windows 和 macOS 用户抱怨更新过程挂起、启动时间过长或更新后配置丢失。
    *   **消息可靠性**：WhatsApp 和 Telegram 渠道的消息丢失问题（如断连期间未补全、回调静默丢弃）引发不满。
*   **使用场景**：
    *   用户多用于 7x24 小时运行的个人助理或企业网关，对长时间运行的内存效率和数据一致性极为敏感。
*   **满意度**：
    *   用户对 Beta 版本快速响应更新问题的态度表示认可，但核心稳定版本（Stable）的频繁回归（Regression）降低了信任度。

## 8. 待处理积压
以下长期未响应或高优先级积压项需维护者重点关注：

1.  **Gateway Memory Leak (#91588)**：创建于 2026-06-09，持续 4 个月，P0 级，39 评论。这是最严重的积压项，直接影响产品可用性。
2.  **WhatsApp Message Backfill (#50093)**：创建于 2026-03-19，P1 级，20 评论。功能缺失而非 Bug，但用户呼声高。
3.  **Zombie Process Accumulation (#97616)**：创建于 2026-06-29，P1 级，18 评论。长期资源泄漏问题。
4.  **Windows Startup Regression (#168955)**：创建于 2026-10-11，虽较新但为 P1 级回归，影响 Windows 用户体验。
5.  **MCP Approval Pipeline (#78308)**：创建于 2026-05-06，P2 级，18 评论。安全功能积压。

**建议**：维护团队应优先分配资源解决 #91588 内存泄漏问题，并审查 2026.9.6 至 2026.10.1 之间的回归测试覆盖范围，以防止后续版本再次出现类似 P0 级阻塞问题。

---

## 横向生态对比

# 个人 AI 助手/自主智能体开源生态横向对比分析报告 (2026-10-13)

## 1. 生态全景
2026年10月中旬，个人 AI 助手与自主智能体开源生态呈现出**“核心项目稳定性攻坚”与“长尾项目轻量化探索”**并行的双极分化态势。作为生态核心的 OpenClaw 正处于高频修复期，致力于解决内存泄漏与更新机制等 P0 级稳定性痛点，试图在保持功能迭代的同时重建用户信任；而 NanoBot 和 Zeroclaw 则分别深耕架构重构（SQLite 状态管理）与多智能体策略安全（权限边界）。与此同时，PicoClaw、NanoClaw、LobsterAI 等垂直或细分领域项目正快速补齐渠道适配（WhatsApp/Teams/Feishu）与多模态能力，生态整体正从单一聊天功能向具备持久记忆、细粒度权限控制及跨设备协同的复杂 Agent 宿主演进。

## 2. 各项目活跃度对比

| 项目名称 | 24h Issues | 24h PRs | 版本发布 | 核心聚焦 | 健康度评估 |
| :--- | :---: | :---: | :---: | :--- | :--- |
| **OpenClaw** | 500+ (封顶) | 500+ (封顶) | 2 (v2026.10.2/5b2) | 稳定性修复、性能优化 | **高风险/高压**：核心回归问题多，急需解决内存泄漏 |
| **NanoBot** | 1 | 31 | 无 | 架构重构、WebUI 组件化 | **良好**：处于底层架构优化密集期，核心解耦中 |
| **Zeroclaw** | 32 | 50 | 无 | 安全模型、数据一致性、性能 | **中等**：存在数据完整性 Bug，但功能迭代快 |
| **PicoClaw** | 少量 | 少量 | 无 | 渠道适配 (Telegram/OneBot) | **稳定**：处于维护与标准化过渡期，清理旧代码 |
| **NanoClaw** | 0 | 6 | 无 | 渠道稳定性 (Feishu/Teams) | **稳定**：常规迭代，解决特定环境兼容性问题 |
| **IronClaw** | 2 | 0 | 无 | 架构提案、基准测试 | **低活跃**：开发停滞，但社区有高质量提案 |
| **LobsterAI** | 2 | 12 | 无 | UI/UX、Computer Use | **积极**：功能打磨期，修复安全漏洞，体验优化 |
| **CoPaw** | 15 | 31 | 无 | 多设备同步、可配置性 | **活跃**：积极处理多端一致性与部署灵活性 |
| **TinyClaw** | 0 | 0 | 无 | - | **停滞** |
| **Moltis** | 1 | 2 | 无 | 多智能体策略、访问控制 | **中等**：核心开发聚焦于 Agent 行为一致性 |
| **ZeptoClaw** | 0 | 0 | 无 | - | **停滞** |
| **EasyClaw** | 0 | 0 | 无 | - | **停滞** |

## 3. OpenClaw 在生态中的定位
*   **绝对核心与枢纽角色**：OpenClaw 是目前该生态中唯一触及“数据上限”（500+ Issues/PRs）的项目，其体量远超其他垂直项目。它是大多数其他 Claw 衍生项目或集成方案的基础宿主，其他项目（如 PicoClaw, NanoClaw）往往在渠道适配或轻量级场景上对 OpenClaw 生态进行补充。
*   **技术路线差异**：与其他追求轻量或特定硬件（如 PicoClaw 的 ESP32 探索）的项目不同，OpenClaw 选择了**“重网关、高扩展性”**路线。它拥有复杂的 Gateway 启动机制、插件捕获（plugin-captures）和精细的更新恢复机制。这种复杂度带来了强大的功能上限，但也是其当前遭遇频繁 P0 级回归（内存泄漏、更新挂起）的根源。
*   **社区规模对比**：OpenClaw 拥有最庞大的用户基数和维护团队。@steipete 等核心维护者提交的 PR 量巨大，但同时也意味着其“破坏半径”最大——任何 Gateway 级 Bug 都会影响所有依赖其作为宿主的环境。相比之下，NanoBot 和 Zeroclaw 社区更聚焦于代码架构的纯净性，对性能与解耦的投入占比更高。

## 4. 共同关注的技术方向
多个项目同时涌现出的技术需求，反映了整个生态的共性挑战：

1.  **多渠道通信的可靠性与补齐 (Channel Reliability)**：
    *   **涉及项目**：OpenClaw, NanoBot, PicoClaw, NanoClaw。
    *   **具体诉求**：OpenClaw 用户强烈要求 WhatsApp 断连消息补齐；PicoClaw 讨论 OneBot 去重；NanoBot 推进 WhatsApp 官方接入。行业痛点在于 IM 渠道（尤其是非 API 官方支持的渠道）的“静默丢包”问题，各团队正在探索“状态持久化”或“补全机制”来增强消息可靠性。
2.  **细粒度访问控制与多智能体策略 (Access Control & Multi-Agent Security)**：
    *   **涉及项目**：OpenClaw, Moltis, Zeroclaw, NanoClaw。
    *   **具体诉求**：Moltis 正在开发 `spawn.allow` 限制子 Agent 生成范围；Zeroclaw 强化配对令牌与 Roster 用户绑定；OpenClaw 关注 MCP 工具调用的审批管道。这表明随着 Agent 获得越来越多的系统权限（文件系统、网络、Shell），生态正在从“默认信任”转向“最小权限原则”。
3.  **本地状态存储的性能与一致性 (Local State Consistency)**：
    *   **涉及项目**：NanoBot, Zeroclaw, OpenClaw。
    *   **具体诉求**：NanoBot 正在将核心状态管理从 JSONL 迁移至 SQLite 并引入有界线程池；Zeroclaw 修复了 SQLite 后端重写时间戳的 Bug。由于 Agent 需要跨会话记忆，非结构化或弱事务的文件存储正在被淘汰，项目集体转向强类型、支持事务的本地数据库（SQLite）以解决 OOM 和数据竞争问题。

## 5. 差异化定位分析

*   **功能侧重**：
    *   **全功能宿主**：**OpenClaw**，功能最全，包含复杂的更新器、插件系统、多模态输入。
    *   **架构纯净/高性能**：**NanoBot**，专注于底层解耦和 WebUI 组件化；**Zeroclaw**，侧重于 Provider 兼容性和成本计算。
    *   **垂直/细分场景**：**LobsterAI** 聚焦 Computer Use（桌面自动化）与多模态预览；**PicoClaw** 强调轻量化与特定渠道（QQ/WeChat/Telegram）的快速适配。
*   **目标用户**：
    *   OpenClaw 面向重度个人助理用户和企业网关部署者；NanoBot 面向追求代码可维护性的开发者；LobsterAI 面向需要“眼睛和手”（Computer Use）的自动化工程师。
*   **技术架构**：
    *   OpenClaw 和 Zeroclaw 倾向于 **Gateway 中心化** 架构，通过中间层解耦 UI 和 Agent 逻辑；PicoClaw 和 IronClaw 则更倾向于**轻量级进程模型**或声明式扩展，减少启动开销。

## 6. 社区热度与成熟度
*   **快速迭代/质量攻坚期 (High Velocity)**：
    *   **OpenClaw**：处于“修复回归”的密集期，用户信任度正在经受考验。
    *   **NanoBot**：处于“架构重构”期，基础组件正在被替换（JSONL -> SQLite），为下个版本的高可用性铺路。
    *   **CoPaw**：处于“功能补齐”期，多设备同步和可配置性正在快速完善。
*   **稳定巩固期 (Stable & Consolidating)**：
    *   **PicoClaw**：正在清理旧代码（Stale PRs），标准化渠道接口。
    *   **LobsterAI**：核心功能稳定，重点转向 UI/UX 细节打磨和安全漏洞（如 Skill 卸载）的修复。
*   **低活跃/探索期**：
    *   **IronClaw**：开发停滞，但社区提出了高质量的“声明式认证扩展”提案，显示出向模块化演进的潜力。

## 7. 值得关注的趋势信号
对 AI 智能体开发者而言，本次生态扫描揭示了以下行业趋势：

1.  **Agent 的“长尾可靠性”成为产品生死线**：随着 Agent 从聊天机器人演变为 7x24 小时运行的后台服务，**内存泄漏、僵尸进程和长期运行的数据一致性**（如 OpenClaw 的 #91588）不再是性能优化项，而是 P0 级阻断性问题。开发者必须引入更严格的资源回收机制和压力测试。
2.  **MCP 标准化的落地与合规化**：各项目（PicoClaw, IronClaw, OpenClaw）都在探讨 MCP 的集成，但焦点已从“能否调用”转向“安全审批”和“元数据规范”。**声明式配置（Declarative Auth）** 和**细粒度审计**将成为高级 Agent 框架的标配。
3.  **UI 与 Agent 的解耦**：NanoBot 正在建立 WebUI 组件库，LobsterAI 正在优化预览交互，表明前端不再是 Agent 的附属品，而是一个独立可插拔的渲染层。这使得 Agent 核心逻辑（LLM/Tool Loop）可以被更轻量或更高频地迭代，而不必担心破坏 UI 稳定性。
4.  **从“云端中心化”向“本地优先 (Local-First)”回潮**：Zeroclaw 和 NanoBot 对 SQLite、本地推理（llama.cpp）的强依赖，反映出在数据隐私和成本压力下，基于边缘设备的本地状态存储和轻量模型推理正在成为开源 Agent 开发者的新宠。

---

## 同赛道项目详细报告

<details>
<summary><strong>NanoBot</strong> — <a href="https://github.com/HKUDS/nanobot">HKUDS/nanobot</a></summary>

# NanoBot 项目动态日报
**日期**：2026-10-13
**数据来源**：GitHub @HKUDS/nanobot

## 1. 今日速览
过去 24 小时 NanoBot 项目表现出较高的代码活跃度，其中 Pull Requests (PR) 是主要活动区，共有 31 条更新记录（15 条待合并，16 条已合并/关闭），而 Issues 更新相对平稳，仅新增 1 条提案。
今日无新版本发布。社区开发重心明显偏向于**核心架构重构**（如 SQLite 状态管理、WebUI 组件化）以及**功能增强**（如更新流程整合、新的 Provider 支持）。
项目维护者在处理历史积压 PR（如 6 月前创建）与合并新贡献方面保持平衡。整体健康度良好，处于功能完善与底层架构优化的密集开发期。

## 2. 版本发布
**今日无新版本发布。**

## 3. 项目进展
今日共有 **16 条** PR 已合并或关闭，主要推进了以下方向：

*   **核心架构与状态管理重构**：
    *   关闭了 #6176 `feat(skills): explain nanobot usage from bundled documentation`，增加了内置文档技能，提升自我诊断能力。
    *   关闭了 #6172 `refactor(agent): compose image tools in application runtimes`，通过组件化重组图像工具，简化了 `AgentLoop` 的耦合，提升了代码可维护性。
*   **图像功能增强**：
    *   关闭了 #6160 `feat(images): reuse named connections and image request settings`，解决了图像配置中无法复用命名连接的问题，允许通过已保存的连接 ID 选择图像提供商，提升了用户体验的一致性。
*   **稳定性修复**：
    *   关闭了 #3588 `fix(transcription): convert non-WAV audio to 16kHz mono WAV before upload`，这是一个历史遗留的重要修复，解决了自托管 Whisper 后端对音频格式兼容性问题，确保了本地部署场景的转写稳定性。
*   **其他**：
    *   关闭了 #6180 `Add Sol AI to Open Source Partners`，扩展了合作伙伴生态展示。
    *   关闭了 #6178 `Feat/webui extensions`，作为相关扩展功能的合并基础（详细功能见开放 PR #6032）。

## 4. 社区热点
今日开放 PR 中，以下条目讨论最为活跃（注：数据中评论数标记为 undefined，但根据提交时间与标签权重判断）：

*   **[P1 优先级] 架构重构：Session 状态中心化至 SQLite**
    *   **链接**：[PR #5943](https://github.com/HKUDS/nanobot/pull/5943)
    *   **分析**：作者 @chengyongru 提出将 JSONL 替换为 SQLite 作为权威存储，并引入有界工作线程池处理存储 I/O。这是项目核心的稳定性与性能优化，标记为 P1 表明对当前架构解耦的重要性极高。
*   **[P2 优先级] 运行时契约隔离与导入边界强制执行**
    *   **链接**：[PR #6166](https://github.com/HKUDS/nanobot/pull/6166)
    *   **分析**：旨在解决配置直接触达具体工具的问题，通过拆分所有权并强制执行依赖边界，降低模块间耦合。这属于代码质量的长期投入。
*   **[P2 优先级] WebUI 组件库与展示分离**
    *   **链接**：[PR #6179](https://github.com/HKUDS/nanobot/pull/6179)
    *   **分析**：引入 `bun run gallery` 作为无网关的组件参考，覆盖 120 个示例族和 159 个源模块。这极大地降低了社区贡献前端代码的门槛，是 WebUI 生态成熟的标志。
*   **更新流程整合**
    *   **链接**：[PR #6168](https://github.com/HKUDS/nanobot/pull/6168) 和 [PR #6154](https://github.com/HKUDS/nanobot/pull/6154)
    *   **分析**：@Re-bin 正在推进“管理式安装”与“可恢复的应用内更新”。这两个 PR 涉及预构建的预览候选项，旨在让用户无需前端构建环境即可更新，是提升最终用户（End-user）体验的关键特性。

## 5. Bug 与稳定性
今日报告的 Bug 修复主要集中在数据完整性和特定提供商兼容性上：

1.  **[P2] PPTX 表格提取数据错位**
    *   **链接**：[PR #6181](https://github.com/HKUDS/nanobot/pull/6181) (开放)
    *   **问题**：PPTX 表格提取时，空单元格被丢弃导致列错位（例如 `Name/空白/100` 变成 `Alice\t100`）。
    *   **状态**：已提交修复 PR，保留所有单元格位置以跳过全空行，包含真实 PPTX 回归测试。
2.  **[P2] Codex 图像生成流式传输中断**
    *   **链接**：[PR #6011](https://github.com/HKUDS/nanobot/pull/6011) (开放，标记 Conflict)
    *   **问题**：使用 `buffered AsyncClient.post()` 导致在响应体未完全读取前断开连接，可能丢弃已生成的图像。
    *   **状态**：已有修复 PR，需解决代码冲突后合并。
3.  **[历史] 天气技能修复**
    *   **链接**：[PR #4145](https://github.com/HKUDS/nanobot/pull/4145) (开放，标记 Conflict)
    *   **问题**：解决 #3958 中天气技能的问题。
    *   **状态**：这是一个创建于 6 月的 PR，今日仍有更新，处于解决冲突阶段。

## 6. 功能请求与路线图信号
从开放 PR 中可以看出以下功能趋势：

*   **WhatsApp 官方接入**：
    *   **链接**：[PR #6152](https://github.com/HKUDS/nanobot/pull/6152)
    *   支持官方 `WhatsApp Agent Platform`，这是一个重要渠道扩展，预计将成为下个版本的高优先级特性。
*   **MCP 预设扩展**：
    *   **链接**：[PR #6068](https://github.com/HKUDS/nanobot/pull/6068)
    *   新增 `FXMacroData` MCP 预设，提供宏观经济数据（央行利率、CPI 等），展示了 MCP 工具在金融垂直领域的具体应用场景。
*   **WebUI 本地扩展表面**：
    *   **链接**：[PR #6032](https://github.com/HKUDS/nanobot/pull/6032)
    *   允许通过本地目录发现并加载带有 `extension.json` 的 WebUI 扩展，通过网关提供路由。这暗示了 NanoBot 正在构建一个更安全的插件化前端生态。
*   **新模型提供商支持**：
    *   **CoreWeave Inference**：[PR #6103](https://github.com/HKUDS/nanobot/pull/6103) 增加文档示例。
    *   **OpenRouter Native Image API**：[PR #5718](https://github.com/HKUDS/nanobot/pull/5718) 支持 OpenRouter 的原生图像生成 API，扩展图像生成模型池。
    *   **Telegram 自定义 API**：[PR #4919](https://github.com/HKUDS/nanobot/pull/4919) 支持自托管 Bot API 服务器，增加企业级部署的灵活性。

## 7. 用户反馈摘要
*基于今日唯一新增 Issue #6182 的分析：*

*   **提案**：[Issue #6182](https://github.com/HKUDS/nanobot/issues/6182)
*   **场景**：用户 @cairn-agent 提议通过受限的 MCP 工具展示“Plazmi”世界（一个家居世界）的“追赶”示例。
*   **痛点/需求**：用户希望展示现有 Agent 如何重新访问具有持续真实 UTC 时钟的世界（跨会话状态保持）。这反映了用户对**MCP 工具在状态管理**（Session State）方面的具体应用场景（如游戏世界、持续模拟）的需求，而不仅仅是单次问答。
*   **反馈**：该 Issue 尚无前评论，但属于高质量的文档/示例增强提案，有助于丰富 MCP 配置指南。

## 8. 待处理积压
以下 PR 虽然今日有更新，但创建时间较早或存在长期冲突，需要维护者重点关注：

1.  **[冲突] 图像生成流式传输修复**：[PR #6011](https://github.com/HKUDS/nanobot/pull/6011) 和 [PR #5718](https://github.com/HKUDS/nanobot/pull/5718)。这两个 PR 均涉及图像功能，且标记为 Conflict，可能阻碍图像功能的统一更新。
2.  **[长期开放] 天气技能修复**：[PR #4145](https://github.com/HKUDS/nanobot/pull/4145) 创建于 2026-06-01，至今仍开放且存在冲突，属于典型的低优先级但长期积压项。
3.  **[复杂重构] 运行时契约隔离**：[PR #6166](https://github.com/HKUDS/nanobot/pull/6166) 虽然较新，但涉及 `package initializers` 和 `runtime Pydantic rebuild` 等核心底层逻辑，合并风险和复杂度较高。

</details>

<details>
<summary><strong>Zeroclaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# Zeroclaw 项目动态日报 (2026-10-13)

## 1. 今日速览
*   **活跃度评估**：高。过去 24 小时内共有 **36** 条 Issues 更新（32 新开/活跃）和 **50** 条 PRs 更新（47 待合并，3 已处理），且未发布新版本，表明当前处于密集的开发迭代与代码审查阶段。
*   **整体状态**：项目焦点集中在 **Provider 兼容性修复**（解决 OpenRouter 成本计算错误、Gemini 隐藏 token 计数丢失）、**安全模型强化**（网关配对令牌与 Roster 用户绑定）以及 **零代码（ZeroCode）性能优化**（修复 Prompt 缓存失效问题）。
*   **稳定性预警**：虽然 CI 门禁通过，但今日新增多起涉及 SQLite 会话后端时间戳重写、Web 仪表盘成本显示混淆等数据一致性问题，需重点关注回归测试。

## 2. 版本发布
*   无新版本发布。

## 3. 项目进展
**合并与关闭概况**：
过去 24 小时共合并/关闭 **3** 条 PRs。

**重要合并 PR**：
*   **[CLOSED] #11108: [Bug]: Preserve browser and search tool semantics instead of rewriting calls to shell**
    *   **进展**：修复了 `map_tool_name_alias` 错误将 `browser_open` 和 `web_search` 重定向到 `shell` 的问题，恢复了专用工具的执行语义，提升了插件调用的准确性。
    *   **链接**: https://github.com/zeroclaw-labs/zeroclaw/pull/11108
*   **[CLOSED] #10996: [Feature]: Seed channel instance configuration and grants during plugin installation**
    *   **进展**：完善了插件安装时的频道实例配置播种逻辑，解决了权限授予（grants）缺失的问题。
    *   **链接**: https://github.com/zeroclaw-labs/zeroclaw/pull/10996
*   **[CLOSED] #10331: bug(runtime): recover terminal settlement intents abandoned by a dead worker**
    *   **进展**：修复了后台委派任务中，当 worker 异常终止时，终端结算意图（terminal settlement intents）无法恢复的可靠性问题。
    *   **链接**: https://github.com/zeroclaw-labs/zeroclaw/pull/10331

## 4. 社区热点
今日讨论最活跃（评论数最多）的 Issues 主要涉及架构决策与长期追踪，显示社区与核心维护者正在对齐未来的技术路线。

*   **#8692 [Tracker]: Maintainer decision queue for RFCs and design issues** (15 评论)
    *   **诉求分析**：这是一个决策漏斗，用户与维护者正在密集讨论大量已接受（Accepted）但未完成的 RFC。评论活跃度高表明这些架构变更（如 Gateway 分离、运行时重构）已进入关键实施阶段，社区密切关注实施进度。
    *   **链接**: https://github.com/zeroclaw-labs/zeroclaw/issues/8692
*   **#8891 [Tracker]: Persistent memory - wire the curation, relevance, and operability planes to parity** (11 评论)
    *   **诉求分析**：长期记忆系统的多 PR 协同工作流追踪。评论集中在如何确保跨会话记忆与成熟 Agent 运行时的功能对等（Parity）。
    *   **链接**: https://github.com/zeroclaw-labs/zeroclaw/issues/8891
*   **#11254 RFC: A2A protocol crate (zeroclaw-a2a)** (9 评论)
    *   **诉求分析**：关于 Agent-to-Agent (A2A) 协议新 Crate 的架构评审。社区关注该新模块如何与现有的发现机制（#7763/#8274）集成，以及所有权边界的定义。
    *   **链接**: https://github.com/zeroclaw-labs/zeroclaw/issues/11254
*   **#11420 [Bug]: SQLite session backend rewrites created_at of every message on each turn** (8 评论)
    *   **诉求分析**：用户发现 SQLite 后端在每轮对话中重写时间戳，导致历史数据失去时间参考价值。这是阻碍日志审计和历史回放功能的关键 Bug。
    *   **链接**: https://github.com/zeroclaw-labs/zeroclaw/issues/11420

## 5. Bug 与稳定性
按严重程度排列，今日重点关注数据一致性与 UI 展示异常。

**S1 - 工作流阻塞 / 高严重度**
*   **#11648 [Bug]: Web dashboard pairing input still limited to 6 digits**
    *   **现象**：后端已升级为 32 位字母数字配对码，但 Web 前端仍限制输入为 6 位，导致无法完成配对。
    *   **Fix 状态**：无明确 PR，需前端紧急修复。
    *   **链接**: https://github.com/zeroclaw-labs/zeroclaw/issues/11648
*   **#11606 [Bug]: model_routing_config upsert_agent rewrites entire config**
    *   **现象**：尝试更新单 Agent 路由时，整个 config.toml 被重写，导致风险配置被重置、字段丢失。这是严重的数据完整性 Bug。
    *   **Fix 状态**：状态标记为 `in-progress`，有修复进行中。
    *   **链接**: https://github.com/zeroclaw-labs/zeroclaw/issues/11606

**S2 - 降级行为 / 中严重度**
*   **#11204 [Bug]: OpenRouter spend shows $0.00 and all tokens classified "free tok"**
    *   **现象**：`usage.cost` 未被正确摄入，导致仪表盘显示成本为零，所有 Token 被视为免费。
    *   **Fix 状态**：已有 PR **#11670** 正在修复 Provider Token 计数保留问题。
    *   **链接**: https://github.com/zeroclaw-labs/zeroclaw/issues/11204 | [PR #11670](https://github.com/zeroclaw-labs/zeroclaw/pull/11670)
*   **#11696 [Bug]: perf(runtime): system prompt is rebuilt from disk every turn**
    *   **现象**：每轮对话都从磁盘重新读取 System Prompt，导致任何小修改都破坏 Prompt 缓存，显著增加 API 成本和延迟。
    *   **Fix 状态**：今日新开，需优化缓存策略。
    *   **链接**: https://github.com/zeroclaw-labs/zeroclaw/issues/11696
*   **#11655 [Bug]: Core conflict supersede hides unrelated memories**
    *   **现象**：在无向量分数的默认配置下，Core 记忆冲突解决逻辑错误地隐藏了不相关的旧记忆。
    *   **链接**: https://github.com/zeroclaw-labs/zeroclaw/issues/11655

**S3 - 次要问题 / 低严重度**
*   **#11675 [Bug]: ACP session store re-stamps created_at**
    *   **现象**：ACP 会话存储在修剪转录时重写了所有行的时间戳（类似 #11420 的问题但在 ACP 层）。
    *   **链接**: https://github.com/zeroclaw-labs/zeroclaw/issues/11675
*   **#11179 [Bug]: Flaky CI test: pending_park_retry...**
    *   **现象**：测试跨越秒边界时偶尔失败，导致 CI 红灯，虽不影响生产但阻碍开发流程。
    *   **链接**: https://github.com/zeroclaw-labs/zeroclaw/issues/11179

## 6. 功能请求与路线图信号
结合今日活跃的 PRs，以下功能正处于实施或待实施阶段：

*   **安全加固：网关配对令牌绑定**
    *   **状态**：功能开发中。
    *   **详情**：将网关配对令牌与 Roster 用户绑定，而非共享操作员身份，实现细粒度权限控制。PR **#11286** 正在开发中，且依赖的 Issue **#10573** 已标记为 Accepted 且基础依赖已合并。
    *   **链接**: [PR #11286](https://github.com/zeroclaw-labs/zeroclaw/pull/11286) | [Issue #10573](https://github.com/zeroclaw-labs/zeroclaw/issues/10573)
*   **可观测性：操作员覆盖成本限制**
    *   **状态**：功能开发中。
    *   **详情**：允许操作员在成本超限时通过 `cost.allow_override` 手动覆盖，避免必须重启 Daemon。PR **#11589** 正在实施。
    *   **链接**: [PR #11589](https://github.com/zeroclaw-labs/zeroclaw/pull/11589)
*   **工具性能：延迟加载工具 (tool_search)**
    *   **状态**：功能开发中。
    *   **详情**：通过 `tool_search` 延迟加载内置工具 Schema，减少 System Prompt 体积。但需警惕 Issue **#11703** 提到的缓存失效问题。PR **#11473** 正在实施。
    *   **链接**: [PR #11473](https://github.com/zeroclaw-labs/zeroclaw/pull/11473) | [Issue #11703](https://github.com/zeroclaw-labs/zeroclaw/issues/11703)
*   **搜索路由：基于提示的 Provider 路由**
    *   **状态**：已接受，待实施。
    *   **详情**：允许为 `web_search_tool` 配置不同的 Provider 路由（如主源 vs 独立佐证）。
    *   **链接**: [Issue #11074](https://github.com/zeroclaw-labs/zeroclaw/issues/11074)

## 7. 用户反馈摘要
基于 Issues 评论和 PR 描述，提炼出以下痛点：

*   **成本计算透明度危机**：多个用户（如 @alperyilmaz, @Audacity88）抱怨成本仪表盘数据不准确（$0.00, 分类错误, UTC/本地时间混淆）。这是目前用户满意度最大的负面来源，影响信任度。
*   **配置数据持久性担忧**：用户发现配置更新（如 #11606）或会话操作（如 #11420, #11675）会意外修改或重写原有数据（时间戳、风险配置）。用户对"读多写少"的预期未被满足。
*   **Prompt 缓存效率**：随着多 Agent 和长上下文场景增多，用户开始关注 API 成本效率。System Prompt 和工具 Schema 的频繁变动导致缓存失效，增加了显式的使用成本。
*   **插件生态稳定性**：Discord 等渠道插件的安装和配置仍存在问题（#10999），WASM 插件在调用失败后的状态清理不彻底（#11402），表明插件生命周期管理是当前的薄弱环节。

## 8. 待处理积压
提醒维护者关注以下长期未响应或高优先级积压项：

*   **#11420 & #11675 (SQLite/ACP 时间戳重写)**：这两个 Issue 都指向会话存储的时间戳管理缺陷，且 #11420 自 10 月 2 日起持续活跃，涉及数据完整性，建议作为下一个修复周期的优先项。
*   **#11606 (Config 重写 Bug)**：标记为 `in-progress` 但涉及高风险配置破坏，需确保修复 PR 尽快合并，防止用户数据损坏。
*   **#11204 & #11613 (成本计算错误)**：虽已有 PR，但涉及 Provider 解析逻辑，需确保覆盖所有兼容 Provider（OpenAI, Gemini, OpenRouter 等），避免修复一个却破坏另一个。
*   **#8692 (RFC 决策队列)**：维护者决策积压。虽然这是 Tracker，但 15 条最新评论表明大量 RFC 等待最终裁决，建议安排定期（如每周）的架构评审会清理此队列，防止技术债积累。

</details>

<details>
<summary><strong>PicoClaw</strong> — <a href="https://github.com/sipeed/picoclaw">sipeed/picoclaw</a></summary>

# PicoClaw 项目动态日报 (2026-10-13)

### 1. 今日速览
过去 24 小时内，PicoClaw 项目维持了**中等活跃度**。虽然没有新版本发布，但代码清理工作显著加速，维护者集中关闭了 6 个长期未响应的 Pull Request，旨在降低维护积压。新增 Issue 集中在渠道接口适配（Telegram/OneBot）和外部集成（MCP/Plazmi）两个方向。整体项目健康状况良好，处于从功能扩展向**稳定性治理与标准化**过渡的阶段。

### 2. 版本发布
无

### 3. 项目进展
今日主要进展体现在**代码库的清理与维护**上。维护者集中处理了 6 个长期停滞的 PR，关闭了多个 3 月份提交的增强与修复请求（如日志格式化、SSL 证书兼容等），这通常意味着这些功能可能已被合并入主干、通过其他方式实现或不再符合项目优先级。
*   **[feishu] 频道支持 Thread 隔离**：[#1676](https://github.com/sipeed/picoclaw/pull/1676) 被标记关闭，此前实现了飞书群聊话题线程的上下文隔离功能。
*   **[logger] 配置化**：[#1500](https://github.com/sipeed/picoclaw/pull/1500) 关闭，该 PR 旨在通过 `config.json` 自定义日志时间格式。
*   **[openai_compat] 兼容性修复**：关闭了 2 个长期修复 PR，包括 Termux 环境下的 SSL 证书加载 ([#1397](https://github.com/sipeed/picoclaw/pull/1397)) 以及移除 Google 提供工具调用中的额外内容 ([#1509](https://github.com/sipeed/picoclaw/pull/1509))。
*   **[telegram] 性能与格式优化**：[#1651](https://github.com/sipeed/picoclaw/pull/1651) 关闭，优化了 HTML 消息分块逻辑，防止长消息截断破坏单词边界。

*注：大量 PR 的集中关闭表明维护者正在对 Stale（过时）分支进行大规模清理。*

### 4. 社区热点
今日讨论最活跃的热点集中在**OpenAI 接口标准化**与**OneBot 渠道交互体验**上。

*   **OpenAI Responses API 迁移**：[#3381](https://github.com/sipeed/picoclaw/pull/3381)
    开发者 @XenonR 提交的 PR 将 OpenAI 提供商从传统 API 切换至新的 Responses API，该 PR 关联了 Bug [#3392](https://github.com/sipeed/picoclaw/issues/3392)（CLA 签名检测失败），显示出该项目在追求前沿接口标准化的同时也面临着 CI/CLA 流程的阻碍。
*   **OneBot 自动回复去重/可配置化**：[#3395](https://github.com/sipeed/picoclaw/issues/3395)
    用户 @ycsqwan 指出 OneBot 渠道的自动 emoji 确认逻辑是硬编码的，无法关闭，导致群聊体验不佳。对应的功能 PR [#3396](https://github.com/sipeed/picoclaw/pull/3396) 虽然被关闭，但 Issue 本身引发了关于“渠道默认行为是否应可配置化”的讨论。

### 5. Bug 与稳定性
今日报告的 Bug 主要集中在渠道消息解析的边界条件处理上。

*   **[High] Telegram Markdown 转义缺陷**：[#3426](https://github.com/sipeed/picoclaw/issues/3426)
    *   **状态**：OPEN，无关联 Fix PR。
    *   **详情**：启用 MarkdownV2 时，链接标签或引用块中的保留字符未被正确转义，导致 Telegram API 拒收消息，进而触发纯文本降级，损失了格式。这影响了对排版要求较高的用户使用场景。

### 6. 功能请求与路线图信号
*   **MCP 低依赖示例集成**：[#3427](https://github.com/sipeed/picoclaw/issues/3427)
    提案引入 Plazmi (MCP/CLI) 作为持久化 Agent 会话的示例。这表明 PicoClaw 正在探索**外部生态系统集成**（如虚拟世界交互），试图将 PicoClaw 从一个单纯的聊天助手扩展为具备持久状态（Pulse/Digest）的 Agent 宿主。
*   **QQ 渠道接口更新**：[#3394](https://github.com/sipeed/picoclaw/issues/3394)
    由于 QQ 机器人接口变动，底层库已更新，但 PicoClaw 的聊天通道未同步，导致功能失效。维护者若希望维持国内主流 IM 渠道的完整性，此问题需尽快排期修复。

### 7. 用户反馈摘要
*   **渠道行为的可控性**：用户（如 @ycsqwan）强烈抱怨底层行为“硬编码”且不可控（如自动回复），反映出用户群体正从“开箱即用”向“精细化定制”需求演进。
*   **跨平台环境痛点**：从关闭的 PRs 可以看出，用户关注 Android/Termux 等非标准环境下的兼容性（如 SSL 证书问题），这是移动端 AI Agent 落地的常见痛点。

### 8. 待处理积压
*   **Stale 分支清理**：尽管今日关闭了多个 PR，但仍有长期未响应的 Stale 标签 Issue（如 [#3392](https://github.com/sipeed/picoclaw/issues/3392)）需要 CLA 协助。
*   **OneBot 可配置性**：虽然 PR [#3396](https://github.com/sipeed/picoclaw/pull/3396) 被关闭，但对应的 Issue [#3395](https://github.com/sipeed/picoclaw/issues/3395) 依然开放，维护者需要明确是拒绝该功能提议还是计划重写，以避免社区误解。
*   **QA 接口同步**：[#3394](https://github.com/sipeed/picoclaw/issues/3394) 涉及接口更新的同步，若无专人跟进，将导致 QQ 渠道功能不可用。

</details>

<details>
<summary><strong>NanoClaw</strong> — <a href="https://github.com/qwibitai/nanoclaw">qwibitai/nanoclaw</a></summary>

# NanoClaw 项目动态日报

**日期**：2026-10-13
**数据来源**：GitHub (nanoClaw) 过去 24 小时活动

## 1. 今日速览
过去 24 小时，NanoClaw 项目保持稳定的中等活跃度，共记录到 6 条 Pull Request 动态，其中 4 条处于开放状态，2 条已关闭或合并。
今日无新版本发布，也无新的 Issue 提交。
开发重点集中在修复 OneCLI 安装流程、优化飞书渠道的消息稳定性以及解决 Teams 文件交付的兼容性问题。
从 PR 标签来看，维护者目前正积极处理 `bug` 和 `skills` 领域的遗留问题，项目处于常规迭代维护期。

## 2. 版本发布
*今日无新版本发布。*

## 3. 项目进展
今日有 2 个重要的 Pull Request 处于已关闭/合并状态，推动了核心功能的稳定性与安全性：

*   **修复飞书进度卡片误撤回与语音模型读取问题**：
    *   [PR #4079](https://github.com/nanocoai/nanoclaw/pull/4079) `[CLOSED]`：修复了飞书渠道中因工具执行进度卡片在折叠后被视为无可见过程而被误删除的 Bug。同时优化了 `VOICE_SUMMARY_MODEL` 的环境变量读取机制，确保其从 `.env` 文件动态读取。
*   **修复审批卡片在聊天线程中的投递位置**：
    *   [PR #4080](https://github.com/nanocoai/nanoclaw/pull/4080) `[CLOSED]`：解决了安全审批卡片（Approval Card）在发起者 DM 中投递时，未能正确关联到特定线程（Thread）而是投递到顶层的问题，改善了用户体验。

## 4. 社区热点
*注：今日所有列出的 PR 评论数及点赞数均显示为 undefined 或 0，社区讨论热度相对较低，主要聚焦于技术修复而非广泛的功能辩论。*

*   **OneCLI 集成修复**：
    *   [PR #4082](https://github.com/nanocoai/nanoclaw/pull/4082) `[OPEN]`：社区正在关注 OneCLI 从 1.42.0 过渡到 1.45.0 的稳定性。该 PR 旨在解决新版本 API 变更导致的 `410 Gone` 错误，并采用新的 Agent Grants API。
*   **Teams 渠道文件交付**：
    *   [PR #3674](https://github.com/nanocoai/nanoclaw/pull/3674) `[OPEN]`：这是一个持续了较长时间的待处理项，今日有更新。旨在为外发文件携带 MIME 类型，以解决 Microsoft Teams 渠道拒绝接收文件的问题。

## 5. Bug 与稳定性
今日处理或涉及的稳定性问题主要集中在渠道兼容性、安装脚本和环境变量处理上：

*   **[严重] OneCLI 安装与技能更新错误**：
    *   描述：OneCLI 1.43+ 版本对 `agents set-secrets` 等命令返回 `410 Gone`，导致安装和技能刷新失败。
    *   状态：已有 [Fix PR #4082](https://github.com/nanocoai/nanoclaw/pull/4082) 正在处理。
*   **[中等] 测试环境隔离失效**：
    *   描述：`resolveRegistryRemote` 在端到端测试中未正确清理调用者的环境变量，导致 `update-skills` 测试失败。
    *   状态：已有 [Fix PR #4081](https://github.com/nanocoai/nanoclaw/pull/4081) 正在处理。
*   **[低] 飞书进度卡片误删**：
    *   描述：纯思考无工具执行的起手卡片被误判为无可见过程而删除。
    *   状态：已通过 [PR #4079](https://github.com/nanocoai/nanoclaw/pull/4079) 修复并关闭。

## 6. 功能请求与路线图信号
*   **社区生态丰富**：
    *   [PR #4083](https://github.com/nanocoai/nanoclaw/pull/4083) `[OPEN]`：提议将“Sol AI”添加到社区部分，展示了开发者对构建个人 AI 代理博客和实际工作流的兴趣，可能成为未来文档或社区推荐的一部分。
*   **多模态与渠道增强**：
    *   结合已关闭的飞书语音模型读取 PR (#4079) 和 Teams 文件修复 (#3674)，路线图信号显示项目正在加强多模态数据处理（语音摘要）以及企业级渠道（Teams, Feishu）的文件交互能力。

## 7. 用户反馈摘要
*由于过去 24 小时内没有新的 Issue 提交且现有 PR 评论数据不可用，暂无基于用户直接文字评论的反馈摘要。*

## 8. 待处理积压
*   **Microsoft Teams 文件交付修复**：
    *   [PR #3674](https://github.com/nanocoai/nanoclaw/pull/3674) 创建于 2026-08-30，已搁置超过 1.5 个月。虽然今日有更新，但仍处于 OPEN 状态。作为影响企业用户使用的关键功能，维护者应关注此 PR 的合并进度，以解决 Teams 渠道的文件互操作问题。
*   **OneCLI 版本跳跃适配**：
    *   [PR #4082](https://github.com/nanocoai/nanoclaw/pull/4082) 涉及跨多个小版本（1.42 到 1.45）的 API 适配。鉴于 OneCLI 是常用工具，该 PR 的合并对于保持安装脚本的健壮性至关重要，需防止长期积压导致的安装失败率上升。

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

# IronClaw 项目动态日报 (2026-10-13)

## 1. 今日速览
今日 IronClaw 项目整体处于**低活跃度**状态，过去 24 小时内仅记录了 2 条新的 Issues 活动，无 Pull Request 合并或关闭，亦无新版本发布。项目核心开发迭代暂时停滞，社区关注点主要集中在架构扩展提案与基准测试失败分析上。尽管开发侧静默，但社区通过 Issue 提交的提案质量较高，显示出用户对模块化扩展和稳定性诊断的持续关注。

## 2. 版本发布
今日无新版本发布。

## 3. 项目进展
过去 24 小时内无 Pull Request 被合并或关闭，项目核心功能代码库无变更，未推进新的功能落地或修复。

## 4. 社区热点
今日讨论最活跃的为两个新开 Issues，虽无评论互动，但内容涉及关键架构方向：

*   **#8136 [OPEN] Proposal: optional hosted-MCP Plazmi extension using declarative auth and egress**
    *   **链接**: [View Issue #8136](https://github.com/nearai/ironclaw/issues/8136)
    *   **作者**: @cairn-agent
    *   **分析**: 该提案建议利用 IronClaw 现有的“Reborn”扩展机制，通过声明式认证和出口（egress）配置来实现可选的 Plazmi 托管 MCP 扩展。这反映了社区希望避免将特定供应商逻辑硬编码到宿主中，倾向于保持核心架构的通用性和模块化。
*   **#8135 [OPEN] Daily ironclaw failure taxonomy — 2026-10-12**
    *   **链接**: [View Issue #8135](https://github.com/nearai/ironclaw/issues/8135)
    *   **作者**: @pranavraja99
    *   **分析**: 该 Issue 提供了 2026-10-12 日期的失败分类报告，重点分析了 officeqa 套件中 52 个非通过任务。报告指出这些失败主要归因于模型质量错误（如 DeepSeek-V4-Flash 的表现），而非框架缺陷。这显示社区正在建立更精细的故障归因机制，以区分模型能力瓶颈与系统稳定性问题。

## 5. Bug 与稳定性
*   **无新增崩溃或回归报告**: 今日未收到明确的代码级 Bug 报告。
*   **基准测试稳定性关注**: 尽管 [Issue #8135](https://github.com/nearai/ironclaw/issues/8135) 将 officeqa 的失败归类为模型质量错误，但这仍提示了在使用特定模型后端时存在非确定性输出或准确率不足的问题。目前**无针对此问题的 Fix PR**，主要依赖模型侧优化或调整基准测试期望值。

## 6. 功能请求与路线图信号
*   **扩展架构演进信号**: [Issue #8136](https://github.com/nearai/ironclaw/issues/8136) 提出的“声明式认证与出口”扩展方案，强烈暗示 IronClaw 路线图可能正朝着**更解耦的插件化架构**发展，旨在支持更多第三方托管服务（如 Plazmi）而无需修改核心代码。若维护者采纳此思路，下一版本可能会增强扩展接口的标准化程度。
*   **缺乏短期落地计划**: 目前无相关 PR 处于待合并状态，表明该功能请求尚未进入开发阶段，仅为社区提议。

## 7. 用户反馈摘要
*   **痛点**: 用户在集成特定 AI 模型（如 DeepSeek-V4-Flash）时，面临模型推理质量导致的任务失败，且需要清晰的失败分类来区分是代码 Bug 还是模型能力不足。
*   **使用场景**: 开发者正在使用 IronClaw 进行 officeqa 等基准测试，并尝试将托管 MCP 服务（Plazmi）作为扩展集成，以丰富 Agent 的能力边界。
*   **满意/不满意**: 用户对现有的“Reborn”扩展机制表示认可（认为其是一个良好的切入点），但对直接添加供应商逻辑到宿主代码持谨慎态度，偏好声明式配置。

## 8. 待处理积压
*   **长期积压提醒**: 今日新增的 2 个 Issue 均为 OPEN 状态且无评论，尚未进入维护者响应流程。
*   **维护者关注建议**: 建议维护者尽快评审 [Issue #8136](https://github.com/nearai/ironclaw/issues/8136) 的架构提案，以明确扩展机制的未来方向；同时确认 [Issue #8135](https://github.com/nearai/ironclaw/issues/8135) 中的失败分类是否符合项目当前的基准测试标准，避免无效的 Bug 追踪。目前无其他长期未响应的历史 Issue 特别突出（基于今日数据快照）。

</details>

<details>
<summary><strong>LobsterAI</strong> — <a href="https://github.com/netease-youdao/LobsterAI">netease-youdao/LobsterAI</a></summary>

# LobsterAI 项目动态日报 (2026-10-13)

## 1. 今日速览
过去 24 小时内，LobsterAI 项目呈现出**高开发活跃度、低发布频率**的健康状态。虽然过去 24 小时没有新版本发布（Release），但代码合并非常密集，共有 12 条 PR 更新（9 条已合并/关闭，3 条待合并），主要集中在桌面端 UI 优化、Computer Use 功能完善及多平台兼容性修复上。社区方面，新增 Issue 活跃度较低（仅 2 条），其中 1 条为安全性相关的严重漏洞报告（已关闭），另 1 条为第三方评测收录邀请。整体来看，团队正专注于**功能打磨与稳定性修复**，为后续版本积累基础。

## 2. 版本发布
*无新版本发布。*

## 3. 项目进展
今日合并/关闭的 PR 显示了项目在 **Agent 体验优化** 和 **底层稳定性** 方面的显著进步：

*   **Computer Use 能力扩展与修复**：
    *   支持了 Intel Mac 平台，新增了 `mac-x64` 运行时，解决了 Intel Mac 用户无法使用 Computer Use 套件的问题 ([PR #2844](https://github.com/netease-youdao/LobsterAI/pull/2844))。
    *   修复了 MCP 桥接在标准输入流 (stdin) 关闭时未能正确退出的 Bug，提升了服务进程的稳定性 ([PR #2845](https://github.com/netease-youdao/LobsterAI/pull/2845))。
*   **UI/UX 细节打磨**：
    *   优化了 Agent 头部显示，移除了过时的 emoji 头像路径，并支持双击重命名任务 ([PR #2840](https://github.com/netease-youdao/LobsterAI/pull/2840))。
    *   修复了图像、SVG 和 Mermaid 预览在缩放后无法拖动查看边缘的问题，改进了交互体验 ([PR #2841](https://github.com/netease-youdao/LobsterAI/pull/2841))。
    *   暂时隐藏了每轮积分显示芯片，因当前计算逻辑未包含图片生成成本，避免误导用户 ([PR #2842](https://github.com/netease-youdao/LobsterAI/pull/2842))。
*   **会话管理智能化**：
    *   实现了新桌面会话的自动标题生成功能，基于用户首条提问异步生成简短标题，且具备完善的失败降级机制（保留原标题，不阻塞对话） ([PR #2846](https://github.com/netease-youdao/LobsterAI/pull/2846))。
*   **其他**：
    *   版本分支 `Release/2026.9.24` 的合并 ([PR #2843](https://github.com/netease-youdao/LobsterAI/pull/2843))。

## 4. 社区热点
*   **第三方评测收录邀请**：
    *   **议题**：Nautilus 组织邀请 LobsterAI 加入其独立的 Agent 评测榜单，提供免费的 L1 收录位，强调判据预注册和结果可复现 ([Issue #2801](https://github.com/netease-youdao/LobsterAI/issues/2801))。
    *   **分析**：这表明 LobsterAI 在开源 Agent 领域的关注度正在提升，第三方机构已将其作为“国产代表位”之一纳入评测视野。虽然目前评论数较少（2 条），但此类外部认可对提升项目社区影响力有潜在价值。
*   **专家广场功能提案**：
    *   **议题**：开发者提出添加“专家广场”（Expert Plaza），允许用户发现并召唤特定任务的 Agent，内容通过 CDN 动态加载 ([PR #2849](https://github.com/netease-youdao/LobsterAI/pull/2849))。
    *   **分析**：这是一个较大的功能架构变更，旨在增强 Agent 的灵活性和发现机制，目前处于待合并状态，值得社区关注其实现细节。

## 5. Bug 与稳定性
*   **[高] Skill 元数据导致任意目录删除漏洞**：
    *   **描述**：已安装的 Skill 在卸载时，其可控的元数据可能导致任意目录被删除。该漏洞存在于 `main` 分支，但最新标签版本 `v0.2.4` 中不存在（因为 `openclawSourceDir` 字段和 `ipcHandlers/skills/handlers.ts` 模块在 v0.2.4 之后才引入） ([Issue #2793](https://github.com/netease-youdao/LobsterAI/issues/2793))。
    *   **状态**：该 Issue 已关闭，意味着问题已修复或已被标记为仅影响开发分支且不在发布版本中。需确认修复代码是否已合并至 `main`。
    *   **备注**：这是典型的供应链/沙箱逃逸风险，需确保后续版本加强 Skill 卸载时的权限校验。
*   **[中] IM 后台累加器销毁异常**：
    *   **描述**：`IMCoworkHandler.destroy()` 在清理后台投递累加器时抛出异常，因为该累加器有意没有 Promise 拒绝回调 ([PR #2850](https://github.com/netease-youdao/LobsterAI/pull/2850))。
    *   **状态**：已有修复 PR 待合并，使用可选调用（optional invocation）解决。

## 6. 功能请求与路线图信号
*   **专家广场 (Expert Plaza)**：PR #2849 引入了该概念，暗示未来版本将支持更动态的 Agent 发现和管理机制，内容解耦于桌面客户端。
*   **会话标题自动化**：PR #2846 表明项目正在提升用户体验的细节，通过自动化生成会话标题来减轻用户负担。
*   **多平台支持**：PR #2844 补充了 Intel Mac 支持，显示团队正在完善 Computer Use 功能的跨平台覆盖，未来可能进一步扩展至其他架构。

## 7. 用户反馈摘要
*   **图像预览交互痛点**：用户反馈在预览面板中放大图像（如 160%）后，无法拖动查看图像左侧部分，原因是 `transform: scale()` 不扩展滚动容器边界。此问题已修复 ([PR #2841](https://github.com/netease-youdao/LobsterAI/pull/2841))。
*   **积分显示准确性**：用户可能因当前积分显示未包含图片生成成本而产生困惑或不满，团队选择暂时隐藏该功能直至重构完成 ([PR #2842](https://github.com/netease-youdao/LobsterAI/pull/2842))。
*   **会话管理便捷性**：用户希望新会话能自动获得有意义的标题，而非手动命名，团队已实现此功能并做了严格的隐私保护（仅发送前 1000 字符，排除文件路径等敏感信息） ([PR #2846](https://github.com/netease-youdao/LobsterAI/pull/2846))。

## 8. 待处理积压
*   **依赖更新**：Mermaid 依赖版本从 10.9.8 升级到 12.1.0 的 PR 自 2026-09-14 创建以来一直处于 OPEN 状态，已停滞近一个月 ([PR #2672](https://github.com/netease-youdao/LobsterAI/pull/2672))。建议维护者评估大版本升级的风险与收益，尽快处理。
*   **待合并功能 PR**：PR #2849（专家广场）和 PR #2850（IM 修复）尚未合并，需确认代码审查进度。

**总体健康度评估**：
项目处于**积极迭代期**。核心功能（Computer Use、Agent UI）持续完善，关键安全漏洞已得到处理。主要风险在于部分长周期 PR（如依赖升级）的积压，以及新引入功能（如专家广场）的稳定性。建议关注下一版本的发布计划，以验证这些改进的用户体验。

</details>

<details>
<summary><strong>TinyClaw</strong> — <a href="https://github.com/TinyAGI/tinyclaw">TinyAGI/tinyclaw</a></summary>

过去24小时无活动。

</details>

<details>
<summary><strong>Moltis</strong> — <a href="https://github.com/moltis-org/moltis">moltis-org/moltis</a></summary>

# Moltis 项目日报 (2026-10-13)

**数据来源**：Moltis GitHub Repository
**报告周期**：过去 24 小时

## 1. 今日速览
过去 24 小时内，Moltis 项目整体处于**中等活跃**状态，主要聚焦于多智能体（Multi-Agent）系统的策略增强与安全合规性。
社区提交了 1 个高优先级的安全相关 Issue 和 2 个关于智能体行为一致性与访问控制的 PR，显示出维护者正致力于完善 `spawn_agent` 机制及其权限边界。
目前暂无新版本发布，且 2 个 PR 均处于待合并（Open）状态，表明代码集成工作尚未完成。
核心开发精力集中在解决聊天交互与子智能体生成路径之间的行为偏差，以及强化预设（Preset）层面的细粒度访问控制。

## 2. 版本发布
*无新版本发布。*

## 3. 项目进展
今日无 PR 合并或关闭。当前处于开发周期中，以下 PR 代表项目近期正在推进的核心功能：

*   **统一聊天与子智能体行为路径**：
    *   **[PR #1301] Chat ↔ sub-agent parity: persona, system_prompt_suffix, delegate_only, preset model**
    *   该 PR 旨在解决直接聊天（Direct Chat）与 `spawn_agent` 子运行（Sub-runs）之间的一致性问题。目前，某些预设字段（如 persona, system_prompt_suffix）仅影响子智能体，而不影响直接聊天。此更新将统一两条路径，确保协调器/工作者（Coordinator/Worker）架构下，智能体“以自身身份对话”的行为与生成子智能体时的行为保持一致。
    *   链接: https://github.com/moltis-org/moltis/pull/1301

*   **增强 spawn_agent 的访问控制能力**：
    *   **[PR #1302] Preset spawn.allow: allowlist for spawn_agent targets**
    *   此 PR 引入了可选的 `agents.presets.<id>.spawn.allow` 配置项。通过允许列表（Allowlist），调用者可以限制 `spawn_agent` 只能生成特定的预设智能体。这一功能强化了组织架构（Org-chart）和协调器图（Coordinator Graphs）的安全性，在执行层面（`resolve_preset`）进行强制执行，并更新工具 Schema 以仅显示允许的生成目标。
    *   链接: https://github.com/moltis-org/moltis/pull/1302

## 4. 社区热点
今日最活跃的议题集中在多智能体交互的安全与策略执行上。

*   **高关注度 Issue：Web UI 通道会话绕过访问策略**
    *   **[Issue #1303] Web turns on a channel-bound session are delivered to the channel, bypassing the account's chat access policy**
    *   作者 `@texxronn` 指出，当非网关回合（如 Web UI、Cron、Webhook）在“通道绑定”（Channel-bound）会话中运行时，Moltis 不仅将上下文降级为公共（无工具/无私有上下文），还意外地将回复投递到该通道聊天中。这导致了一个**策略绕过漏洞**：用户本意可能是通过 Web UI 进行受限交互，但消息却泄露到了公共或受控的频道中，且无 Web UI 警告。
    *   当前该 Issue 评论数为 0，但其涉及访问控制核心逻辑，需重点关注。
    *   链接: https://github.com/moltis-org/moltis/issues/1303

    *分析*：背后诉求在于强化**上下文隔离**与**消息投递策略**的解耦。用户期望在非网关入口（Web UI）触发时，应严格遵守该入口的访问权限策略，而非默认继承底层通道的投递行为。

## 5. Bug 与稳定性
今日报告的主要 Bug 集中于策略执行层面，而非崩溃或回归错误。

*   **[中] 通道绑定会话的访问策略绕过**
    *   **描述**：在通道绑定会话中执行 Web/Cron/Webhook 回合时，回复被错误地投递至通道，绕过账户级别的聊天访问策略。
    *   **状态**：已报告 (Issue #1303)，暂无关联的 Fix PR 直接指向此特定投递逻辑问题（PR #1301 和 #1302 侧重于生成控制，而非投递策略）。
    *   **链接**：https://github.com/moltis-org/moltis/issues/1303

## 6. 功能请求与路线图信号
基于今日 PR 和 Issue，以下功能极有可能纳入近期路线图：

1.  **细粒度多智能体权限控制**：
    *   `spawn.allow` 允许列表功能 (PR #1302) 表明 Moltis 正从简单的“可生成任意智能体”向“基于策略生成”演进，支持更复杂的组织架构图。
    *   *信号*：高。已开发完成，等待合并。

2.  **智能体行为一致性强化**：
    *   统一 `spawn_agent` 与直接聊天的系统提示、Persona 和委托行为 (PR #1301)。
    *   *信号*：高。解决现有架构中的行为偏差，是提升开发者体验的关键。

3.  **会话隔离与投递策略解耦**：
    *   修复 Issue #1303 暗示项目将重新审视“通道绑定”与“非网关入口”之间的策略继承逻辑，可能需要引入更精细的“投递目标”配置。

## 7. 用户反馈摘要
*   **痛点**：`@texxronn` 反馈在 Web UI 中操作通道绑定会话时，缺乏对消息去向的明确控制或警告，导致潜在的策略泄露风险。
*   **场景**：协调器/工作者（Coordinator/Worker）架构下，用户发现“直接与智能体对话”与“让主智能体生成该智能体”行为不一致，影响了工作流的可预测性。
*   **满意度**：当前社区对多智能体扩展性的支持持积极态度，但对安全边界（Access Policy）的严格性存在顾虑。

## 8. 待处理积压
*   **PR 积压**：2 个重要 PR (#1301, #1302) 均处于 Open 状态超过 24 小时（#1302 创建于 10-12，#1301 更新于 10-13）。建议维护者尽快审查合并，以解锁多智能体策略增强的后续开发。
*   **安全 Issue**：Issue #1303 涉及访问控制绕过，虽无新评论，但因其安全敏感性，应优先于普通功能请求进行修复。

</details>

<details>
<summary><strong>CoPaw</strong> — <a href="https://github.com/agentscope-ai/CoPaw">agentscope-ai/CoPaw</a></summary>

2026-10-13 CoPaw (QwenPaw) 项目动态日报

### 1. 今日速览
过去 24 小时，CoPaw 项目保持活跃但无新版本发布。过去 24 小时 Issues 更新 15 条（新开/活跃: 13，已关闭: 2），PR 更新 31 条（待合并: 23，已合并/关闭: 8）。社区热点集中在多设备消息同步、Web 搜索提供商的可配置性以及前端渲染性能优化。多个严重的 Bug 修复 PR 已进入审核或待合并状态，表明项目维护团队正在积极处理稳定性问题。

### 2. 版本发布
无。过去 24 小时没有新版本发布。

### 3. 项目进展
*   **修复审批流程竞态条件**: [PR #8188](https://github.com/agentscope-ai/QwenPaw/pull/8188) 修复了导致工具审批失效的 Bug（Issue #8105），该项目在 24 小时内已被关闭，显著提升了工具调用的可用性。
*   **支持可配置的 Console BaseName**: [PR #8197](https://github.com/agentscope-ai/QwenPaw/pull/8197) 解决了在反向代理子路径部署时无法访问控制台的问题（Issue #8177），优化了自托管部署的灵活性。
*   **修复 GitHub 导入二进制损坏问题**: [PR #8198](https://github.com/agentscope-ai/QwenPaw/pull/8198) 解决了从 GitHub 安装 Skill 时非 UTF-8 字节被替换为 U+FFFD 导致的图片/字体损坏问题（Issue #8179），确保了插件生态的完整性。

### 4. 社区热点
*   **多设备消息同步延迟**: [Issue #8202](https://github.com/agentscope-ai/QwenPaw/issues/8202) 指出在一个设备上发送的消息在另一个设备上必须等待整个回合结束才可见，目前已有相关修复 PR [PR #8203](https://github.com/agentscope-ai/QwenPaw/pull/8203) 正在处理。
*   **Web 搜索提供商的可配置性**: [Issue #8174](https://github.com/agentscope-ai/QwenPaw/issues/8174) 讨论了内置 tavily provider 无法配置付费 API key 的问题，目前已有针对此问题的修复 PR [PR #8204](https://github.com/agentscope-ai/QwenPaw/pull/8204)；同时，[Issue #8199](https://github.com/agentscope-ai/QwenPaw/issues/8199) 提出了将 Firecrawl 作为新的 Web 搜索提供商的诉求。
*   **前端 GPU 性能优化**: [Issue #8135](https://github.com/agentscope-ai/QwenPaw/issues/8135) 指出控制台的 UI 效果（如大面积 backdrop-filter）导致 GPU 持续占用，用户呼吁提供官方的“降低特效”选项。

### 5. Bug 与稳定性
*   **[严重] Windows 平台删除项目失败**: [Issue #8206](https://github.com/agentscope-ai/QwenPaw/issues/8206) 指出在 Windows 上删除带有已写入日志文件的项目时，由于重命名包含打开的日志文件的目录导致失败（WinError 5），目前暂无修复 PR。
*   **[高] 控制台间歇性静默空完成**: [Issue #8172](https://github.com/agentscope-ai/QwenPaw/issues/8172) 报告在 2.2.0 版本的 Docker 部署中，Web 控制台聊天会间歇性静默结束，模型未被调用且无前端回复，目前暂无修复 PR。
*   **[中] 桌面端启动失败 (Windows)**: [Issue #8194](https://github.com/agentscope-ai/QwenPaw/issues/8194) 报告在 Windows 上遇到过时的 PID 会导致 `os.kill` 抛出未被捕获的 `SystemError`，导致插件无法启动，修复 PR [PR #8196](https://github.com/agentscope-ai/QwenPaw/pull/8196) 已提交等待合并。

### 6. 功能请求与路线图信号
*   **增加 Skill 目录分类支持**: [PR #8178](https://github.com/agentscope-ai/QwenPaw/pull/8178) 提出支持嵌套/分类的 skill 目录（如 `<root>/<category>/<skill>/SKILL.md`），同时保持扁平结构兼容，有望提升 Skill 管理的可扩展性。
*   **新增决策模型配置与提供商**: [PR #8205](https://github.com/agentscope-ai/QwenPaw/pull/8205) 增加了结构化的决策模型配置（引入 `model_type` 属性），为后续的路由功能奠定基础。
*   **Skill 下载后台任务化**: [Issue #8126](https://github.com/agentscope-ai/QwenPaw/issues/8126) 呼吁将大型 Skill 的下载转为带进度和取消按钮的后台任务，以解决目前的超时问题。
*   **硬件接入探索**: [Issue #8189](https://github.com/agentscope-ai/QwenPaw/issues/8189) 提出了将 ESP32 等硬件类似 Meta Muse 接入 QwenPaw 的设想。

### 7. 用户反馈摘要
*   **痛点 1 - 交互体验不直观**: 用户在多设备同步和消息可见性方面感到困惑，消息只有在整个任务结束时才会持久化，导致跨端体验割裂。
*   **痛点 2 - 性能与资源开销**: 用户指出控制台的 UI 动画在现代设备（特别是集成显卡 iGPU 上）造成了不必要的 GPU 占用，影响了基础性能。
*   **痛点 3 - 自托管灵活性不足**: 用户在进行自托管部署时，遇到反向代理配置和跨局域网设备访问的问题，期望框架能提供更灵活的 basename 和路由配置。

### 8. 待处理积压
*   **[长期未响应] Langfuse 可观测性工具输出缺失**: [PR #7964](https://github.com/agentscope-ai/QwenPaw/pull/7964) 修复了 Langfuse `tool.*` 观察从未记录输出内容的 Bug。该 PR 自 2026-09-24 创建，至今超过 20 天未合并，可能影响系统监控能力。
*   **[长期未合并] 核心架构重构（Channels 提取）** : [PR #8187](https://github.com/agentscope-ai/QwenPaw/pull/8187) 是一项 XXL 级别的重构，旨在将 channels 提取为带有子进程隔离的插件。作为影响底层的巨大改动，需要维护者投入大量时间进行 code review 和风险评估。
*   **[长期未响应] AskUserQuestion 工具集成**: [PR #6384](https://github.com/agentscope-ai/QwenPaw/pull/6384) 增加了 `AskUserQuestion` 内置工具，允许 agent 阻塞并询问用户。该 PR 自 2026-07-23 创建，停留时间极长。

</details>

<details>
<summary><strong>ZeptoClaw</strong> — <a href="https://github.com/qhkm/zeptoclaw">qhkm/zeptoclaw</a></summary>

过去24小时无活动。

</details>

<details>
<summary><strong>EasyClaw</strong> — <a href="https://github.com/gaoyangz77/easyclaw">gaoyangz77/easyclaw</a></summary>

过去24小时无活动。

</details>

---
*本日报由 [Big Model Radar](https://github.com/DuskHonor/big_model_radar) 自动生成。*