# Cursor Skills

Reusable Cursor Agent Skills. Copy a skill directory into a project's `.cursor/skills/`.

## Skills

| Directory | Purpose |
|-----------|---------|
| [`domain-driven-design/`](./domain-driven-design/) | Design or review complex business software with DDD (bounded contexts, aggregates, CQRS, and layer boundaries) |
| [`agent-design/`](./agent-design/) | Design or review reliable LLM agent systems (loops, tools, workflows, state, and memory) |

## Install

Copy the skill directories you need into the project's `.cursor/skills/`:

```bash
cp -R domain-driven-design /path/to/project/.cursor/skills/
cp -R agent-design /path/to/project/.cursor/skills/
```

Each skill uses the `SKILL.md` in its directory as the Agent entry point.
