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

## What are Skills?

Skills are markdown files that give AI agents the knowledge and the workflow for a
specific job. Add this one and your agent stops guessing at publishing: it knows to
upload your media before the post rather than after, to ask TikTok what that account
is allowed to do, to leave you a draft instead of publishing over your head, and to
read an error rather than retry the same failing call. It reaches PublishQ through
whichever surface it already has, so the same instructions work from a terminal with
the [CLI](https://www.npmjs.com/package/@publishq/cli), from an editor over
[MCP](https://www.npmjs.com/package/@publishq/mcp), from your own code with the
[SDK](https://www.npmjs.com/package/@publishq/sdk), or from an automation over plain
HTTP.

## Skills

| Skill                           | What it does                                               |
| ------------------------------- | ---------------------------------------------------------- |
| [`publishq`](./skills/publishq) | Post, schedule, draft, reschedule and cancel social posts. |

## Installation

### Option 1: CLI install (recommended)

Use [npx skills](https://github.com/vercel-labs/skills):

```bash
# Install every skill in this repo
npx skills add PublishQ/social-media-skills

# Install one by name
npx skills add PublishQ/social-media-skills --skill publishq

# See what is in here first
npx skills add PublishQ/social-media-skills --list
```

The CLI detects which agents you have and asks where to put it. Claude Code gets
`.claude/skills/`, Codex and Cursor share `.agents/skills/`, OpenClaw gets
`~/.openclaw/skills/`.

> [!TIP]
> Running the command from **inside** an agent session (asking your agent to install
> it for you) makes the CLI non-interactive, and it may only write to the universal
> `.agents/skills/` directory, which Claude Code does not read. Name the agent
> explicitly:
>
> ```bash
> npx skills add PublishQ/social-media-skills --skill publishq -a claude-code
> ```

### Option 2: Clone and copy

```bash
git clone https://github.com/PublishQ/social-media-skills.git
cp -r social-media-skills/skills/* .agents/skills/
```

### Option 3: Git submodule

Pins a version and updates on your say-so, which is worth it when a team shares the
same instructions:

```bash
git submodule add https://github.com/PublishQ/social-media-skills.git .agents/social-media-skills
```

Then point your agent at `.agents/social-media-skills/skills/`, and
`git submodule update --remote` when you want the latest.

### Option 4: Fork and customise

Fork it, change the copy to match how your team posts, and install from your fork.
Everything here is markdown, so a fork stays readable and merges cleanly.

## Usage

Ask for what you want. The skill fires on its own:

```
"Post this on LinkedIn and X"
→ one call, both accounts, left as a draft for you

"Schedule the launch note for Tuesday 9am"
→ queued in UTC, editable until it fires

"Put this carousel on TikTok"
→ asks TikTok what that account allows, then sets the privacy it requires

"Different copy for LinkedIn, same picture"
→ one post, per-account overrides, one upload

"Cancel whatever is going out tomorrow"
→ finds it and cancels before it publishes
```

You can also name it: `use the publishq skill to schedule this for Friday`.

## What you need

An API key from [Settings → API Keys](https://publishq.com/app/settings), and the
social accounts you want to post to connected once in the dashboard. The agent never
sees a platform password, only a key you can revoke.

No developer account, no app review, and no tokens to keep alive per platform.

## Docs

- [publishq.com/docs](https://publishq.com/docs) for the API, CLI, MCP and SDK references
- [publishq.com/openapi.yaml](https://publishq.com/openapi.yaml) for the contract
- [publishq.com/agents](https://publishq.com/agents) for per-agent setup
