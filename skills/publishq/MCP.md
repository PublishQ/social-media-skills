# The same steps as MCP tools

Read this only if you cannot run a shell. The CLI in [SKILL.md](SKILL.md) is the
default because it needs no config file, no restart, and adds nothing to your tool
list.

## Register the server

Three facts are the whole of it: the command is `npx -y @publishq/mcp`, the
transport is **stdio**, and `PUBLISHQ_API_KEY` goes in that server's environment.
There is no HTTP endpoint and no `Authorization` header. The server refuses to start
without the key.

Most clients take this block, in a JSON file they name themselves:

```json
{
  "mcpServers": {
    "publishq": {
      "command": "npx",
      "args": ["-y", "@publishq/mcp"],
      "env": { "PUBLISHQ_API_KEY": "pq_live_..." }
    }
  }
}
```

Some take an `mcp add` subcommand instead, and Codex wants the same four values as
TOML. Whichever your client uses, it is those four values: name, command, args, env.

Then restart the client, or reload its MCP servers, or the tools do not appear.

## Tool per step

Every tool takes the same arguments as the flag of the same name, and returns the
same JSON.

| Step                   | CLI                               | Tool                               |
| ---------------------- | --------------------------------- | ---------------------------------- |
| 1. Accounts and ids    | `pq accounts list`                | `publishq_list_accounts`           |
| 2. Upload media        | `pq media upload --file <path>`   | `publishq_upload_media`            |
| 3. TikTok creator info | `pq accounts tiktok-creator-info` | `publishq_get_tiktok_creator_info` |
| 4. Create the post     | `pq posts create`                 | `publishq_create_post`             |
| Read one post          | `pq posts get <id>`               | `publishq_get_post`                |
| List posts             | `pq posts list`                   | `publishq_list_posts`              |
| Edit before it fires   | `pq posts update <id>`            | `publishq_update_post`             |
| Cancel a post          | `pq posts delete <id>`            | `publishq_delete_post`             |
| Drop one account       | `pq posts remove-account`         | `publishq_remove_post_account`     |
| List media             | `pq media list`                   | `publishq_list_media`              |
| Read one media item    | `pq media get <id>`               | `publishq_get_media`               |
| Workspaces             | `pq workspaces list`              | `publishq_list_workspaces`         |

`publishq_get_workspace` and `publishq_create_workspace` exist too, for a login
holding several sets of accounts.

`publishq_create_post` takes `mediaIds`, and takes every account in one call — the
fan-out rule in step 4 of [SKILL.md](SKILL.md) is the same here. `publishq_delete_post`
is the one destructive tool: ask the **human in the loop** first, and leave it out
of your allowlist if the user wants it unavailable.
