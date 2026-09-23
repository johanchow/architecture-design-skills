# Cursor Skills

本仓库聚合可复用的 Cursor Agent Skills，可直接拷贝/子模块安装到项目的 `.cursor/skills/`。

## Skills

| 目录 | 用途 |
|------|------|
| [`domain-driven-design/`](./domain-driven-design/) | 用 DDD 设计/评审复杂业务软件（限界上下文、聚合、CQRS、分层边界） |
| [`agent-design/`](./agent-design/) | 设计/评审可靠的 LLM Agent 系统（循环、工具、工作流、状态与记忆） |

## 安装

把本仓库内容放到项目的 `.cursor/skills/`（整库作为子模块，或按需复制单个 skill 目录均可）：

```bash
# 作为子模块挂到 .cursor/skills
git submodule add git@github.com:johanchow/architecture-design-skills.git .cursor/skills
```

或只拷贝某一个 skill：

```bash
cp -R domain-driven-design /path/to/project/.cursor/skills/
cp -R agent-design /path/to/project/.cursor/skills/
```

每个 skill 以目录内的 `SKILL.md` 为 Agent 入口。
