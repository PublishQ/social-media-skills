<p align="center">
  <img src="https://publishq.com/favicon.svg" width="80" alt="PublishQ logo" />
</p>

<h1 align="center">social-media-skills</h1>

<p align="center">
  <strong>Social media publishing skills for AI agents. One key posts and schedules to every account you have connected.</strong>
</p>

Agent skills for [PublishQ](https://publishq.com). Drop one into your agent and it
can publish, schedule and draft to Instagram, TikTok, YouTube, X, LinkedIn,
Facebook, Threads and Bluesky, without a developer app or a review per platform.

A skill is plain markdown. Your agent reads it, follows the steps and calls the
PublishQ API through the [CLI](https://www.npmjs.com/package/@publishq/cli),
the [MCP server](https://www.npmjs.com/package/@publishq/mcp), the
[SDK](https://www.npmjs.com/package/@publishq/sdk) or plain HTTP. Nothing here is
code, so you can read every instruction your agent is about to follow.

## Skills

| Skill                    | What it does                                               |
| ------------------------ | ---------------------------------------------------------- |
| [`publishq`](./publishq) | Post, schedule, draft, reschedule and cancel social posts. |

## Install

```bash
npx skills add https://github.com/PublishQ/social-media-skills --skill publishq
```

Or clone it and point your agent's skills folder at the one you want:

```bash
git clone https://github.com/PublishQ/social-media-skills.git
```

**Claude Code** reads `~/.claude/skills/<name>` for yourself, or
`.claude/skills/<name>` to check it into a repo. Symlink it and Claude Code picks
up a change without a restart:

```bash
ln -s "$PWD/social-media-skills/publishq" ~/.claude/skills/publishq
```

**OpenClaw** installs from a path:

```bash
openclaw skills install ./social-media-skills/publishq --global
```

Any other agent that reads a skills directory takes the folder as it is. For an
agent with no skill mechanism at all, skip the skill and wire up the
[MCP server](https://www.npmjs.com/package/@publishq/mcp) or the
[CLI](https://www.npmjs.com/package/@publishq/cli) directly, or hand it
<https://publishq.com/agents> and let it set itself up.

## What you need

An API key from [Settings → API Keys](https://publishq.com/app/settings), and the
social accounts you want to post to connected once in the dashboard. The agent
never sees a platform password, only a key you can revoke.

## Docs

- [publishq.com/docs](https://publishq.com/docs) for the API, CLI, MCP and SDK references
- [publishq.com/openapi.yaml](https://publishq.com/openapi.yaml) for the contract
- [publishq.com/agents](https://publishq.com/agents) for per-agent setup
