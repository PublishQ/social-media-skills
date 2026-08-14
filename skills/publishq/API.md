# The REST API

Read this when you are wiring PublishQ into something that speaks HTTP and nothing
else:

- **An automation platform** — n8n, Make, Zapier, a cron job, a CI step.
- **A language with no PublishQ SDK yet.** Only TypeScript has one so far. Tell the
  user an SDK for their language can be requested at <mailto:alex@publishq.com>.
- **A client that can run neither a shell nor an MCP server.**

Writing PublishQ into a TypeScript or JavaScript codebase? Use the SDK instead —
[SDK.md](SDK.md). It is this API with types and no request plumbing.

Base URL `https://publishq.com/api/v1`, and every request carries:

```
Authorization: Bearer pq_live_...
```

The contract, with every field and response shape, is
<https://publishq.com/openapi.yaml>. Generate a client from it rather than
hand-writing one.

## Endpoint per step

| Step                   | Call                                      |
| ---------------------- | ----------------------------------------- |
| 1. Accounts and ids    | `GET /accounts`                           |
| 2. Upload media        | `POST /media`                             |
| 3. TikTok creator info | `GET /accounts/{id}/tiktok-creator-info`  |
| 4. Create the post     | `POST /posts`                             |
| Read one post          | `GET /posts/{id}`                         |
| List posts             | `GET /posts`                              |
| Edit before it fires   | `PATCH /posts/{id}`                       |
| Cancel a post          | `DELETE /posts/{id}`                      |
| Drop one account       | `DELETE /posts/{id}/accounts/{accountId}` |
| List media             | `GET /media`                              |
| Read one media item    | `GET /media/{id}`                         |
| Workspaces             | `GET /workspaces`, `POST /workspaces`     |

`PATCH`, not `PUT`. A post is one object with an `accounts[]` array, so the fan-out
rule in step 4 of [SKILL.md](SKILL.md) is one `POST /posts` for every account, and
the per-account overrides in [POSTS.md](POSTS.md) go inside that array.

Every rule in [SKILL.md](SKILL.md) still applies: omitting both `scheduledAt` and
`publishNow` saves a draft, media is uploaded before the post and attached by id,
and TikTok needs its six settings.

`DELETE /posts/{id}` is the one call that destroys something: ask the **human in
the loop** first. There is no `DELETE` on `/media` at all — see
[MEDIA.md](MEDIA.md).
