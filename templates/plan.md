# Plan: <功能名>

每个 task group 在需求驱动下按实现顺序分组，命名用描述性的短句（从 requirements 里自然长出），不用固定的分类词。每个 group 独立可 review。把不相关的东西推到后续 phase。

Group 的数量和粒度跟随需求——一个 feature 可能 3 个 group 也可能是 6 个。但「测试」总是至少要有一个 group。

如果这个 feature 需要实现者跑一些轻量的自检（编译通过、服务能起、curl 返回正确），可以在最后加一个「验证」group——作为实现的一部分，确保东西能跑。注意这只覆盖实现者的快速自检，**正式的完整验收见 `validation.md`**，那是另一个独立步骤。

举个例子，如果一个 feature 是「给 CLI 加导出功能」，group 可能是这样：

```markdown
## Group 1 — JSON 读取与容错
  …（条目标记为可执行的动作）

## Group 2 — Markdown 导出逻辑
  …

## Group 3 — CLI 子命令 `export`
  …

## Group 4 — 测试
  …
```

---

## Group 1 — <描述性名称>

1.1 …
1.2 …

---

## Group 2 — <描述性名称>

2.1 …
2.2 …

---

…

---

## Group N — 测试

…

---

## 备注

- 从 `requirements.md` 出发。
- 遵守 `specs/tech-stack.md`。
- 未经批准不要新增依赖。