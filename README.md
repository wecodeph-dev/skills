# wecodeph-dev/skills

Agent skills from [wecodeph.dev](https://wecodeph.dev), in the open [Agent Skills](https://agentskills.io) format. They work with any agent that supports skills: Claude Code, Codex, Cursor, Copilot, OpenCode, Zed and others.

## Install

```console
npx skills@latest add wecodeph-dev/skills          # into the current project
npx skills@latest add wecodeph-dev/skills -g       # globally, for every project
npx skills@latest add wecodeph-dev/skills -s weplan   # just one skill
```

## Skills

| Skill | What it teaches the agent |
|---|---|
| [`weplan`](skills/weplan/SKILL.md) | Publish self-contained HTML (reports, dashboards, prototypes) as a shareable link with [`npx weplan`](https://www.npmjs.com/package/weplan), and update the page in place when the user asks for changes. |

## License

MIT
