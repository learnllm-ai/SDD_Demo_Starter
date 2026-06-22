# SDD Starter

[LearnLLM.AI](https://learnllm.ai) 的 [SDD](https://learnllm.ai/courses/sdd) 课程的配套代码。

这份 starter 帮你用 SDD（Spec-Driven Development）流程做一个项目。流程的核心是**先锁定目标再写代码，每个阶段小而独立、可验证、可合并**，做完一轮再来一轮。

按你的项目状态选入口：

- **新项目**（从零开始）→ [第一部分：新项目如何引入](#第一部分新项目如何引入)
- **已有项目**（代码已经存在）→ [第二部分：已有项目如何引入](#第二部分已有项目如何引入)

---

## 第一部分：新项目如何引入

从零开始走完整套流程：准备 → 写Constituion → 写 feature spec → 实现 → 验证 → 收尾 → replanning → 进入下一轮。

### 0. 准备

1. 把 `prd.md` 和 `templates/` 复制到你的新项目目录。
2. 打开 `prd.md`，把你的项目想法写进去——格式不拘，几段话、几条要点都可以，关键是让 AI 有足够的上下文开始提问。
3. 如果项目还没有 git，`git init`。

### 1. 创建项目Constituion

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

### 2. 开始 feature spec

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

### 3. 实现 feature

```text
按照最新的 `specs/YYYY-MM-DD-feature-name/plan.md` 中的 task group 实现这个 feature。

实现时要和 `requirements.md`、`validation.md` 保持一致。
如果实现过程中发现本 feature 的 spec 有缺失或错误，同步更新这三份文件——但不碰 roadmap、changelog 等独立步骤会处理的文件。
```

### 4. 验证 feature

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

### 5. 收尾

```text
直接根据当前分支的 git 提交记录生成或更新 `CHANGELOG.md`：
- 不存在就创建，存在就在顶部补充
- 每个提交用一条简短 bullet 概括
- 保持日期分组，最新在前

然后在 `specs/roadmap.md` 里把这个 phase 标记为已完成。
提交、切回 main、合并分支、删除 feature 分支。
```

### 6. Replanning

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

### 7. 进入下一轮

回到步骤 2——找下一个未完成 phase、开分支、写 spec、实现、验证、收尾。每个 phase 独立可交付，每一步都有 spec 和验证兜底。

---

## 第二部分：已有项目如何引入 SDD

老项目走四步：反向工程出Constituion → 挑一个独立 feature → 跑 feature loop → 立刻 replanning。完成后回到 [第一部分：新项目如何引入](#第一部分新项目如何引入) 的步骤 7 循环。

### 1. 反向工程，从代码生成Constituion

**准备工作：**

1. **不用**复制 `prd.md`，但要复制 `templates/` 到项目根目录。
2. 项目已经在 git 仓库里就跳过 `git init`。

**分四轮对话和 AI 做，每一轮的产出都过一遍再进下一轮：**

**① 让 AI 探索代码库，建立全局认知**

```text
探索这个项目的代码库——读 README、依赖文件、配置文件，浏览目录结构，识别核心业务逻辑的位置。

完成后给我一份发现摘要：
- 项目大致是什么类型（Web 应用 / CLI / API / 库）
- 主要技术栈（先列依赖文件里写明的，不要猜）
- 目录组织方式，核心业务逻辑在哪
- 哪些地方让你看不懂或拿不准

不要写任何 spec 文件，先做发现。
```

看完它的摘要，纠正它理解错的地方（比如把 `utils/` 当核心业务、把注释掉的旧代码当现役功能）。

**② 反向生成 `mission.md`**

```text
基于你对代码库的了解，起草 `specs/mission.md`——这个项目解决什么问题、为谁解决、核心价值是什么、不做什么。

每一条都要标注：
- [事实] —— 从代码里能明确看出来的
- [推断] —— 你猜的，需要我确认

写作时参考 `templates/mission.md`。
```

重点 review `[推断]` 那些条目——商业目标、目标用户、"不做什么"的边界，这些代码里看不出来，必须你来定。

**③ 反向生成 `tech-stack.md`**

```text
基于依赖文件和实际代码，起草 `specs/tech-stack.md`——语言、框架、数据库、关键第三方依赖、部署相关信息，标注开发依赖和生产依赖。

写作时参考 `templates/tech-stack.md`。
```

这一步最准，依赖文件不会骗人。你要做的是**判断"该不该用"**——把已经不维护的库、只在一个文件里用过一次的依赖、公司禁用的库挑出来，让 AI 在 tech-stack 里标记"待清理"。

**④ 反向生成 `roadmap.md`**

```text
基于 Git 提交历史、issue、CHANGELOG、代码里的 TODO/FIXME，起草 `specs/roadmap.md`：
- 把已完成的功能按时间分成几个 phase，标 ✅
- 把未完成事项（TODO / issues / 我口头补充）整理成后续 phase，拆得尽量细
- 已完成 phase 里能从 commit 推断出关键决策的，简短记一下

写作时参考 `templates/roadmap.md`。
```

已完成部分 AI 能帮你还原，**未完成部分需要你来排优先级**——AI 只能看到代码里有什么，看不到接下来该往哪走。

### 2. 挑第一个 feature——独立性强、耦合度低

第一个 feature **不是为了交付重要功能，是为了验证 SDD 在这个项目里跑得通**。挑错了 feature，会卡在老代码的泥潭里出不来。

**优先选**（跟其他模块关系越少越好）：

- 加一个**新页面 / 新视图**——只影响路由和模板
- 加一个**新 CLI 命令**——只影响命令注册
- 加一个**新 API 接口**——只影响路由和响应
- 修一个 TODO / issue 里记着的、范围清晰的小 bug

**避开**：

- 核心业务逻辑重构
- 数据库结构变更
- 替换认证 / 权限系统
- 跨多个模块的依赖升级

如果 roadmap 的下一个 phase 是上面那种"避开"的类型，**先回 roadmap 把它拆开**，挑出一个独立性强的子任务做第一轮。

### 3. 跑 feature loop——多两样东西

挑好之后按主流程 **步骤 2 → 3 → 4 → 5** 跑，但要在 prompt 里加两段——这是老项目和新项目最关键的区别。

**步骤 2 写 feature spec 时**，在原 prompt 末尾加上：

```text
这是已有项目，请额外做两件事：

1. 在 `requirements.md` 里加一节「兼容性约束」——这个 feature 涉及哪些现有模块、必须保持哪些已有行为不变、有哪些不能破坏的接口契约。

2. 在 `plan.md` 里加一类「适配任务」——为了支持这个新功能，需要怎样调整哪些已有接口/数据结构/配置；和"新增任务"分开列。

写之前先实际读一遍涉及到的现有代码，不要凭推测写约束。
```

**步骤 4 验证时**，除了 `validation.md` 的清单，额外做一件事：

- **跑一遍项目原有的测试 / 启动流程**，确认新 feature 没把老功能弄坏。老项目最常见的翻车场景是"新东西做对了，老东西被改坏了"。
- 如果项目原本没有测试，至少**手动走一遍核心老功能**（登录、主页面、关键操作流），确认行为没变。

### 4. 第一个 feature 跑完，立刻 replanning

新项目里步骤 6 是"按需"，老项目里**第一轮 replanning 是必做**——反推出来的Constituion第一版一定有偏差，跑完一个 feature 才看得出来要修哪儿。

步骤 5 收尾之后直接走 **步骤 6**，把这一轮发现的所有问题同步回 mission / tech-stack / roadmap。完成后回到主线 **步骤 7**，进入下一轮，和新项目完全一样。

> 渐进式原则：老代码**不主动改造**，新功能按 SDD 跑。老模块的 spec 不追求全补——只补**经常被改动的**和**新人 / 新 Agent 看不懂的**那些，每次改老代码顺手补一份，日积月累。

老项目跑顺后，后续 feature 都按 [第一部分：新项目如何引入](#第一部分新项目如何引入) 的步骤 2→7 循环。
