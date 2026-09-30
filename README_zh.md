# skill-principle

*[English](./README.md)*

一套 Agent Skill 的最佳实践原则库，配两条使用路径：**建设**——写新 skill 或迭代已有 skill 时，边写边按原则做结构决策，收尾强制自审；**审查**——对已有 skill 通读一遍，把"感觉哪里不对"变成一份可执行的 P0/P1/P2 分级报告。两条路径共用同一份原则，不会各说各话。它始终站在「真正要用这个 skill 干活的 Agent」视角读文件，把触发失灵、悬空引用、隐式逻辑漏洞这些坑，挡在它们真正搞砸一次生产运行之前。

> 非目标：本技能不代写整份 skill 的骨架或脚手架（那是 skill-creator / darwin-skill 之类构建工具的职责）——建设路径给的是原则指导、结构决策与自审。

## 特性

- **27 条原则，一份文档双写**：每条都写清「原则是什么 / 建设时怎么做 / 审查时怎么判、什么级别」，建设路径读前两段、审查路径读第一段与第三段，避免建设指南与审查清单长期漂移
- **两条路径，自动分流**：用户说要写/改 skill 走建设路径（意图与非目标 → frontmatter 与 description → 结构与披露层次 → 内容写作 → 完工自审），说要 review 走审查路径（确认目标 → 盘点引用图 → 逐条判定 → 分级报告 → 可选深度模式）；只给目录没说意图时会先问清楚
- **原则分三层**：
  - **机械层（1.1–1.4）**：frontmatter 合规（字段白名单、name 规则）、description 触发质量（关键词堆砌阈值、排除子句）、Markdown 结构完整性、安全底线（破坏性命令、硬编码凭证、allowed-tools 过度授权）
  - **通用原则（2.1–2.18）**：只留使用信息不留工程信息、流程与工具完备性（含决策判据/完成判据与脚本样例实跑比对）、前后文一致、git 版本管理与资产迁移、表述精简与体积阈值、无孤立文档与引用深度、不违背常识、案例覆盖、异常场景说明、正例优先与反例范围准确、隐式逻辑显式化、Runtime 中立性、目标/非目标清晰、reference 读取条件、结构一致性（术语漂移/近似重复/结构断裂）、渐进披露与注意力控制（分层管理/场景分割/任务拆解/逐步执行/单点负荷阈值）、事实归属单一（一个事实只有一个家，跨文件与跨 skill 只留指针）
  - **依赖与安装原则（3.1–3.6）**：独立安装指导、环境隔离、状态/凭证 JSON 分离（凭证必须 gitignore）、失败驱动安装、部署内聚性（自带 CLI/脚本与部署文档必须放在 skill 根目录内，不得与其平级）、跨平台且安装位置可确定（优先安装到 skill 根目录内的专属子目录）
- **建设收尾强制自审**：新写或改完的 skill 必须按原则跑一遍，P0/P1 当场修；「对话约束沉淀」子项在此必查——用户在建设对话中随口提过、却没落进 skill 的约束，是最常见的遗漏
- **分级报告**：每条问题落到「位置（文件:行）+ 问题 + 建议」，附通过项、不适用项与可选的 8 维成熟度评分卡
- **用户判决机制**：孤立文档、疑似违背机制、禁区补充、反例收窄等问题先暂存，审查完统一向用户提问，不打断式逐条问
- **大技能拆分审查**：文件数 > 20 时按分支并行派发子代理，全局原则（跨文件冲突、孤立文档、版本管理、结构一致性、渐进披露、部署内聚性、跨平台安装）保留在主会话
- **三个可选深度模式**（默认不启用，审查或自审完成后询问；模型剪裁也可直接要求）：
  - **增强 Review**：联网对照领域现状，评估过时 / 不一致 / 遗漏 / 过度限制
  - **实测验证**：触发率正负样本抽测、RED/GREEN/REFACTOR 行为压力测试、meta 反馈
  - **模型剪裁**：由目标模型自己把 skill 剪成只保留「它的默认行为与作者要求之差」的变体（外部事实一律原样保留），再由考官模型（用户指定的更强模型，或目标模型本身）出题，目标模型的子代理分原版、变体、无 skill（可选）三组盲考，按失分回补直到达标；原版不动，变体只对该模型有效

## 安装

方式一：软链（推荐，便于跟随仓库更新）

```bash
git clone <本仓库> ~/projects/skills/skill-principle
ln -s ~/projects/skills/skill-principle/skill ~/.claude（or codex etc..）/skills/skill-principle
```

方式二：使用 `dist/` 下的 `.skill` 打包文件（zip 格式），解压到对应 runtime 的 skills 目录下的 `skill-principle/`。

> 上面的 `~/.claude/skills/` 是 Claude Code 的目录约定，仅作示例；其他支持 Agent Skills 的 runtime 请替换为其对应的 skills 目录。

## 使用

对支持 Agent Skills 的 Agent 直接说：

建设路径：

- 「我想写个 skill，把会议纪要转成任务清单」
- 「帮我改进这个 skill 的结构」
- 「我的 skill 一直触发不了，description 该怎么写」

审查路径：

- 「帮我 review 一下 ~/projects/skills/foo」
- 「审查这个 skill 的质量」
- 「对这个 SKILL.md 给点反馈意见」

只有在用户要求修改时，审查路径才会动手改被审 skill；建设路径的自审例外——它审的就是本次对话刚写出来的内容，P0/P1 会当场修掉。

## 目录结构

```
skill-principle/
├── skill/                          # runtime 技能包（软链目标）
│   ├── SKILL.md                    # 路径判定 + 建设路径 + 审查路径
│   └── references/
│       ├── principles.md           # 27 条原则（原则 / 建设 / 审查 三段式）
│       ├── report-template.md      # 分级报告模板 + 评分卡
│       ├── enhanced-review.md      # 增强 Review 模式
│       ├── behavior-testing.md     # 实测验证模式
│       └── model-tailoring.md      # 模型剪裁模式
├── dist/                           # 打包产物（.skill）
└── README.md
```

## 版本

版本同时标在 git tag 与 SKILL.md frontmatter 的 `metadata.version` 上，当前 `v0.11.0`。

- `v0.1.0` — 初始版本：三层清单、拆分审查、判决机制、增强 Review
- `v0.2.0` — 用自身对自身做了一轮完整自审（含子代理拆分实战），修复拆分职责对齐等 3 个 P1；新增机械层校验；表述改为工具中立
- `v0.3.0` — 博采众长：吸收 darwin-skill、obra/superpowers、philschmid、skill-validator、verdict/skillscore 的做法，新增实测验证模式、安全扫描、Runtime 中立性/非目标/读取条件检查、评分卡
- `v0.4.0` — 逻辑一致性强化：面向"被多个 LLM/Agent 来回修改"的 skill，新增 2.16 结构一致性（术语漂移/近似重复/结构断裂）；2.2 补决策判据与完成判据检查；2.12 隐式逻辑走查放开为对所有 skill 执行；评分卡新增"逻辑一致性"维度（8 维）
- `v0.5.1` — 部署内聚性：新增 3.5（自带 CLI/脚本与部署文档必须在 skill 根目录内，平级放置即 P0）；盘点父目录平级资产；第三节执行条件放宽到"自带 CLI/脚本"
- `v0.6.0` — 注意力工程：新增 2.17 渐进披露与注意力控制（分层管理、场景分割、任务拆解、逐步执行、单点注意力负荷阈值、披露时机错配、上下文卸载）；评分卡"引用设计"维度扩展为"引用设计与渐进披露"
- `v0.7.0` — 跨平台安装：新增 3.6，要求覆盖支持的平台、明确安装位置，并优先将依赖安装到 skill 根目录内的专属子目录
- `v0.9.0` — 事实归属：新增 2.18 事实归属单一（一个事实只有一个家，别处只写名字加指针；查多家并存、越界展开、有家不指、有指针没有家、归属表缺失），建设路径 Step B3 增加归属与路由表决策；按该条对本 skill 自身做归属整改：两份标注清单与「核心姿态」各归一处、SKILL.md 新增归属表
- `v0.10.0` — 模型剪裁：新增第三个深度模式，按指定模型剪裁 skill（外部事实强制保留），并以原版/变体/无 skill 三组盲考量化剪裁损失、按失分回补
- `v0.11.0` — 版本写进 frontmatter：2.4 要求每次发版同时更新 git tag 与 `metadata.version`（安装副本通常不带 `.git`，Agent 与随附脚本从这里读版本），缺失或与 tag 不一致按 P1 报告；本 skill 自身补上 `metadata.version`
- `v0.8.0` — 更名 `skill-reviewer` → `skill-principle`，从审查扩展到建设：审查清单重构为「原则 / 建设 / 审查」三段式的原则库（`references/principles.md`），SKILL.md 新增路径判定与建设路径（Step B1–B5，收尾强制自审），审查流程保持不变（Step R1–R5）

## 设计参考

- [darwin-skill](https://github.com/alchaincyf/darwin-skill)— Review 维度、runtime 卫生、评分卡
- [obra/superpowers](https://github.com/obra/superpowers) — RED/GREEN/REFACTOR 子代理行为测试法
- [Testing Claude Skills](https://www.philschmid.de/testing-skills) — 触发率正负样本评测
- [skill-validator](https://github.com/agent-ecosystem/skill-validator) — 分级 token 阈值、关键词堆砌检测
- [Anthropic Agent Skills Best Practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) — 渐进式披露、time-sensitive 信息规避
