# Building PublishQ into an application (TypeScript)

Read this when the user is writing PublishQ into their own codebase rather than
posting once, and that codebase is TypeScript or JavaScript — Node, Next.js,
Express, a worker, anything on npm.

For any other language, there is no SDK yet: use the REST API directly
([API.md](API.md)). Tell the user that, and that an SDK for their language can be
requested at <mailto:hello@publishq.com> — it gets generated from the same contract.

## Install and construct

```bash
npm install @publishq/sdk
```

```ts
import PublishQ from '@publishq/sdk';

const publishq = new PublishQ({ apiKey: process.env.PUBLISHQ_API_KEY! });
```

Keep the key server-side. It carries the user's whole posting capability, so it
belongs in an environment variable on a server, never in a browser bundle, a mobile
app or a committed file.

## The four namespaces

Every method returns `{ data, error }` — check `error` rather than catching:

```ts
const { data, error } = await publishq.accounts.list();
if (error) return handle(error);
for (const account of data.accounts) {
  console.log(account.platform, account.accountName);
}
```

| Namespace    | Methods                                                                                               |
| ------------ | ----------------------------------------------------------------------------------------------------- |
| `posts`      | `list()`, `create(body)`, `get(id)`, `update(id, body)`, `delete(id)`, `removeAccount(id, accountId)` |
| `accounts`   | `list()`, `getTikTokCreatorInfo(id)`                                                                  |
| `media`      | `list()`, `upload(body)`, `get(id)`                                                                   |
| `workspaces` | `list()`, `create(body)`, `get(id)`                                                                   |

These map one-to-one onto the steps in [SKILL.md](SKILL.md): `media.upload` is step
2, `accounts.getTikTokCreatorInfo` is step 3, `posts.create` is step 4. Every rule
there still applies — a draft is the default, one `create` call takes every account,
and TikTok needs its six settings.

## Handle the error, not just the type

A call that compiles is not automatically a valid one — the API validates the whole
post and is strict about platform settings. Read `error` and act on what it names:

```ts
const { data, error } = await publishq.posts.create({
  /* ... */
});
if (error) return handle(error);
```

## Where the truth is

- <https://publishq.com/docs/sdk> — the SDK reference
- <https://publishq.com/openapi.yaml> — the contract the SDK is generated from
- [PLATFORMS.md](PLATFORMS.md) for per-platform settings, [POSTS.md](POSTS.md) for
  per-account overrides, [MEDIA.md](MEDIA.md) for uploads
