# Lean Change Spec（一页）— 更名 skill-principle 并扩展建设路径

- **Issue / 链接**：N/A
- **Owner**：weidwonder
- **Status**：Done
- **Triage 复核**：AI/LLM 行为？否（borderline：本仓库交付物本身是给 Agent 读的提示词资产，但改动不引入任何运行时模型推理链路、无代码、无线上发布，验证方式为文档自查 + 用本 skill 自审，沿用 v0.4.0–v0.7.0 同类改动的判定口径）；鉴权/安全/设备？否；跨服务/迁移/不可逆？否（GitHub 更名由平台保留旧名重定向，可回改）；指标/规则/对外承诺？否。

## 改什么（现状 → 期望）

现状：仓库 / GitHub 项目 / skill 名均为 `skill-reviewer`，定位只覆盖「审查已写好的 skill」，24 条标准以审查检查项的口径单向表述，建设 skill 的人拿不到正面指导。

期望：整体更名为 `skill-principle`，定位升级为「skill 最佳实践原则库 + 两条使用路径」：建设路径（写新 skill / 迭代已有 skill 时按原则边建边审）与审查路径（保持现有分级报告能力）。标准文档改写为「原则 + 建设怎么做 + 审查怎么判」的双写形式，两条路径共用同一份原则，杜绝长期漂移。

## 为什么

问题在建设阶段就已产生，事后审查只能补救。把同一套原则前移到建设期，能少走返工；而单一原则库双写，避免「建设指南」和「审查清单」变成两套互相打架的标准。

## 具体改动点

- `skill/references/review-checklist.md` → `git mv` 为 `skill/references/principles.md`：24 条逐条改写为「原则陈述 / 建设时怎么做 / 审查时怎么判 + 级别 + 执行归属」，保留原编号（1.1–1.4、2.1–2.17、3.1–3.6）与全部既有判定信息，不删标准。
- `skill/SKILL.md`：`name` 改为 `skill-principle`；description 重写为覆盖建设与审查两类触发语、并写清新的排除边界；正文加「路径判定」小节，保留现有审查流程（Step 1–5），新增建设流程（意图与边界 → frontmatter 与 description → 结构与渐进披露 → 内容写作 → 完工自审）；案例区补一条建设路径案例。
- `skill/references/report-template.md`、`enhanced-review.md`、`behavior-testing.md`：同步更名后的文件引用与措辞。
- `README.md`、`README_zh.md`：改写标题、定位、安装示例、目录结构、版本历史（新增 v0.8.0）。
- `dist/`：新增 `skill-principle-v0.8.0.skill`；历史包文件名保持原样不动。
- 仓库层：本地目录 `skill-reviewer` → `skill-principle`、GitHub 仓库更名、`origin` remote 更新、`~/.claude/skills/skill-reviewer` symlink 重建为 `skill-principle`。

## 影响范围 / 不做什么

- 影响：本仓库全部文档、skill 安装名与 GitHub 项目地址。
- **不做**：不接管从零生成 skill 骨架/脚手架（建设路径只给原则指导与自审，不代写整份 skill）；不删除或降级任何现有审查标准；不改 `dist/` 下历史版本包的文件名；不修改仓库外其他 skill 中对 `skill-reviewer` 的引用（合并后提示用户处理）。

## 验收标准（可判定，含边界/失败用例）

- [x] `skill/SKILL.md` frontmatter `name: skill-principle`，且与安装目录名一致；description 同时含建设类与审查类触发语，并保留排除子句。
- [x] 用户只说「帮我 review 某 skill」时，路径判定落到审查路径，流程与产物与 v0.7.0 一致（分级报告 + 判决项 + 可选深度模式）。
- [x] 用户说「我要写个 skill / 帮我改这个 skill」时，路径判定落到建设路径，且末步强制按原则自审并给出 P0/P1 修正。
- [x] 边界用例：用户意图不明（只给了一个 skill 目录、没说要干什么）时，SKILL.md 有明确的询问动作，不擅自选路径。
- [x] `skill/references/principles.md` 中 1.1–1.4、2.1–2.17、3.1–3.6 编号齐全，每条含原则陈述、建设指导、审查判定与级别；原「主会话执行 / 需用户判决」标注不丢失。
- [x] 仓库内（排除 dist 历史包与 .git）不再有 `skill-reviewer` 残留，除版本历史中说明更名的条目。
- [x] 失败用例：GitHub 更名若失败，本地内容改动仍自洽可用，README 安装指引不指向不存在的地址。

## 如何验证

- [x] `grep -rn "skill-reviewer" --exclude-dir=.git --exclude-dir=dist .` → 仅命中版本历史中的更名说明。
- [x] `grep -n "^### [123]\." skill/references/principles.md | wc -l` → 27 条，编号与索引表一致。
- [x] `grep -rn "review-checklist" skill README.md README_zh.md` → 无残留引用。
- [x] 用本 skill 的审查路径自审本次改动后的 skill 目录，P0 数为 0。
- [x] `unzip -l dist/skill-principle-v0.8.0.skill` → 含 SKILL.md 与 6 份 references。
- [ ] 合并后：`gh repo view` 显示新名，`git remote -v` 指向新地址，`ls -l ~/.claude/skills/skill-principle` 链接有效。
- [x] 忠实自查：逐条对照验收标准与「不做什么」，未擅自新增脚手架生成能力，未删减既有审查标准。

## 回滚方式

内容改动：`git revert` 本次合并提交。GitHub 更名：在仓库设置改回 `skill-reviewer`（旧名重定向仍在，期间地址不失效），本地目录与 symlink 一并改回。
