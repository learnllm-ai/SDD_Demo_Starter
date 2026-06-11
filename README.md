# SDD Demo Starter

[LearnLLM.AI](https://learnllm.ai) 的 [SDD](https://learnllm.ai/courses/sdd) 课程的配套代码。

这份 starter 帮你用 SDD（Spec-Driven Development）流程做一个项目。流程的核心是**先锁定目标再写代码，每个阶段小而独立、可验证、可合并**，做完一轮再来一轮。


## 0. 准备

1. 把 `prd.md` 和 `templates/` 复制到你的新项目目录。
2. 打开 `prd.md`，把你的项目想法写进去——格式不拘，几段话、几条要点都可以，关键是让 AI 有足够的上下文开始提问。
3. 如果项目还没有 git，`git init`。

## 1. 创建项目宪法

把下面这段发给 AI：

```text
读取 `prd.md` 里的项目输入。

创建一个 `specs` 目录，里面包含：
- `mission.md`
- `tech-stack.md`
- `roadmap.md`

`roadmap.md` 要写成高层级实现顺序，并拆成多个小而独立可交付的阶段。

写文件前，先和我确认以下三点，不要自己猜——怎么问都行，分批、一次性、自然对话都可以：
- 范围 —— 这个阶段做什么、不做什么
- 决策 —— 需要拍板的关键选择
- 背景 —— 会影响 spec 的上下文和约束

写作时参考 `templates/mission.md`、`templates/tech-stack.md` 和 `templates/roadmap.md`。
确保覆盖要点，但不用拘泥格式——跟着项目类型自然生长。
```

这一步结束时你会有：`specs/mission.md`、`specs/tech-stack.md`、`specs/roadmap.md`。

## 2. 开始 feature spec

```text
读取 `specs/roadmap.md`，找到下一个未完成的 phase。

如果当前项目在 git 仓库里，先创建对应的 feature 分支：`phase-N-<kebab-name>`

然后为这个 phase 创建 feature spec 目录 `specs/YYYY-MM-DD-feature-name/`，包含：
- `requirements.md`（范围、边界、决策、上下文）
- `plan.md`（按需求拆分的 task group，按实现顺序排列）
- `validation.md`（验收清单——自动化检查、场景验证、完成条件）

写文件前，同样先和我确认范围、决策、背景——怎么问都行，不要自己猜。

写作时参考 `specs/mission.md`、`specs/tech-stack.md`，以及 `templates/requirements.md`、`templates/plan.md`、`templates/validation.md`。
```

## 3. 实现 feature

```text
按照最新的 `specs/YYYY-MM-DD-feature-name/plan.md` 中的 task group 实现这个 feature。

实现时要和 `requirements.md`、`validation.md` 保持一致。
如果实现过程中发现本 feature 的 spec 有缺失或错误，同步更新这三份文件——但不碰 roadmap、changelog 等独立步骤会处理的文件。
```

## 4. 验证 feature

```text
执行最新的 `specs/YYYY-MM-DD-feature-name/validation.md` 里的所有验证步骤。
修复发现的问题。
准确报告哪些通过了、哪些还需要处理。
```

## 5. 收尾

```text
直接根据当前分支的 git 提交记录生成或更新 `CHANGELOG.md`：
- 不存在就创建，存在就在顶部补充
- 每个提交用一条简短 bullet 概括
- 保持日期分组，最新在前

然后在 `specs/roadmap.md` 里把这个 phase 标记为已完成。
提交、切回 main、合并分支、删除 feature 分支。
```

## 6. 进入下一轮

回到步骤 2——找下一个未完成 phase、开分支、写 spec、实现、验证、收尾。每个 phase 独立可交付，每一步都有 spec 和验证兜底。