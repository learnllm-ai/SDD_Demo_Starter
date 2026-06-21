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

`validation.md` 默认是**给人照着跑**的清单——实现 feature 的 AI 同时是被验证方，让它自验容易出现"自己给自己打勾"。推荐两种做法：

**方式 A（推荐）：自己照着 `validation.md` 过一遍。**
你是最终用户，最清楚什么算"通过"。把每条场景验证手动跑一次，记下不符合预期的地方，再交给 AI 修。

**方式 B：开一个新的 agent 当独立验证者。**
新开一个干净上下文的对话/会话（不要复用实现 feature 的那个 AI），把下面这段发给它：

```text
你是这个 feature 的独立验证者，不要参与实现，也不要修代码。

读取最新的 `specs/YYYY-MM-DD-feature-name/validation.md`，逐条执行里面的验证步骤：
- 自动化检查：跑命令，对照期望结果
- 场景验证：按输入和步骤实际操作，记录真实输出
- 输出文本检查：检查可见文本的语气和一致性

完成后输出一份验证报告：
- 每条验证：通过 / 失败 / 跳过（说明原因）
- 失败项写清楚：期望是什么、实际是什么、复现步骤
- 不要尝试修复，只如实报告

报告完后由我决定怎么处理。
```

拿到报告后再回到实现 feature 的 AI（或新开一轮）去修问题，修完重新验证，直到清单全绿。

## 5. 收尾

```text
直接根据当前分支的 git 提交记录生成或更新 `CHANGELOG.md`：
- 不存在就创建，存在就在顶部补充
- 每个提交用一条简短 bullet 概括
- 保持日期分组，最新在前

然后在 `specs/roadmap.md` 里把这个 phase 标记为已完成。
提交、切回 main、合并分支、删除 feature 分支。
```

## 6. Replanning（按需）

跑完一两个 phase，原来的 roadmap 大概率会偏——可能冒出新的全局要求（加测试框架、改响应式、定品牌色…），也可能发现剩下的 phase 划分不再合理。这时候**先停下来更新 spec，不要急着改代码**。

```text
我们有一个新的变化要同步进项目：<写下新决策，或"基于当前进展重排 roadmap 剩余阶段">。

读取 `specs/` 下的现有产物和已完成 phase 的成果，按这个顺序更新 spec（先不要碰代码）：
1. 先改最顶层受影响的产物（mission / tech-stack / roadmap）
2. 再把变更传导到受影响的 feature spec（requirements / plan / validation）

改之前先告诉我你打算动哪些文件、改什么，确认后再写。
```

spec 更新完之后，**代码的改动回到正常的 feature 循环**——把它当成一个新 phase 或新 feature，走步骤 2→3→4→5。这样你能在 spec 改完后停下来 review，也避免一个超长对话里 spec 和代码搅在一起。

> 如果这次改动很小（比如就改个文案、调个颜色），也可以在同一轮对话里顺手把代码一起改了——前提是你能在一次 review 里看清所有 diff。改动一旦上规模，就老老实实拆成新 phase。

## 7. 进入下一轮

回到步骤 2——找下一个未完成 phase、开分支、写 spec、实现、验证、收尾。每个 phase 独立可交付，每一步都有 spec 和验证兜底。