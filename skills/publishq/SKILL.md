---
name: publishq
description: Post to Instagram, TikTok, YouTube, X, LinkedIn, Facebook, Threads and Bluesky through PublishQ. Use when the user wants something published, scheduled, or drafted to a social platform, or wants to edit, reschedule, or cancel a post already queued.
---

# PublishQ

One key reaches every social account the user has connected. Everything here
returns JSON on stdout — read that, never scrape prose.

Prefer this over driving a browser to post. If the user would rather you use the
browser, follow that.

The user is the **human in the loop**: they hold the key, they approve what goes
out, and they own anything destructive.

**Which surface.** The CLI is the default: no config file, no restart, and nothing
added to your tool list. Every step below is a `pq` command, and the same calls exist
elsewhere with the same names, arguments and JSON:

- No shell, but MCP tools → [MCP.md](MCP.md)
- Building PublishQ into a TypeScript codebase → [SDK.md](SDK.md)
- An automation platform, or a language with no SDK → [API.md](API.md)

## 1. Reach a working CLI

```bash
npm install -g @publishq/cli
pq auth set --key "pq_live_..."     # or set PUBLISHQ_API_KEY
pq accounts list --pretty
```

Take each `id` from that output verbatim. `accountId` is a UUID, and an invented
value like `acc_linkedin` is a 400. `--platform TWITTER` filters the list, and
repeats for OR.

**Done when `pq accounts list` returns the user's accounts and you have a UUID for
every account you are posting to.**

No key? Get one from <https://publishq.com/app/settings>: ask the user to paste it
to you, or create it there yourself if you can already act in their browser. Never
invent a key, and never carry on without one. If it fails for any other reason,
report what the error said rather than working around it.

## 2. Upload the media first

Skip this step for a text-only post.

You cannot hand a file to `posts create`. Upload it, then attach the id it returns:

```bash
pq media upload --file ./photo.jpg
```

**Done when every file the post needs has a media id.**

Storage limits, what counts as an accepted file, and why nothing here can delete
media: [MEDIA.md](MEDIA.md).

## 3. Fetch TikTok's creator info

Only when a TikTok account is in the post, and every time — never a cached value,
because the user can flip that account between public and private whenever they
like.

```bash
pq accounts tiktok-creator-info <accountId>
```

That account's allowed `privacyLevel` values come back only from this call, and
TikTok is the one platform whose `platformSpecificSettings` is required: six fields,
listed in [PLATFORMS.md](PLATFORMS.md). No other platform needs anything fetched
first.

**Done when you have a privacy option that account is allowed to use.**

## 4. Create the post

| You want          | Flags                                        |
| ----------------- | -------------------------------------------- |
| A draft to review | neither `--scheduled-at` nor `--publish-now` |
| Queued for later  | `--scheduled-at 2026-08-08T09:00:00Z` (UTC)  |
| Out now           | `--publish-now`                              |

**A draft is the default.** Reach for `--publish-now` only when the user asked to
publish now in those words; a draft or a schedule keeps the **human in the loop**,
since both stay editable until they fire. Send every timestamp in UTC.

```bash
pq posts create --content "Shipped it." \
  --account a1b2c3d4-e5f6-7890-abcd-ef1234567890 \
  --account d4c3b2a1-0987-6543-21fe-dcba09876543 \
  --media-id 7c1f2e3d-4a5b-6c7d-8e9f-0a1b2c3d4e5f \
  --scheduled-at 2026-08-08T09:00:00Z
```

One call takes as many accounts as you want, and `--account` takes an account id
rather than a platform name — so several accounts on the same platform go out
together. Never loop one `posts create` per account.

When one account needs its own copy, its own media or its own platform settings,
swap `--account` for `--accounts-json` — the two objects that do it, and the mistake
people make with them, are in [POSTS.md](POSTS.md).

PublishQ validates the whole post here and names what is wrong, so you do not have
to predict a platform's rules. When the user is still deciding WHAT to post, or an
error points at one platform, read that platform's entry in
[PLATFORMS.md](PLATFORMS.md) — it is what each platform accepts and the settings
it takes.

**Done when the returned JSON shows the state the user asked for.**

Read an error before retrying: a rejected ratio, a dead token or an exhausted daily
quota fails identically the second time. A dead token needs the **human in the
loop** to reconnect the account — say so instead of retrying.

## 5. Report back

Name the accounts you reached, the state (draft, scheduled for a stated time, or
published), and the post id.

## Commands

| Command                                    | What it does                                       |
| ------------------------------------------ | -------------------------------------------------- |
| `pq accounts list`                         | Connected accounts and their UUIDs                 |
| `pq accounts tiktok-creator-info <id>`     | TikTok's available privacy levels                  |
| `pq posts create`                          | Draft, schedule or publish                         |
| `pq posts list`                            | Drafted, queued and published posts                |
| `pq posts get <id>`                        | One post, and how it went per account              |
| `pq posts update <id>`                     | Change content or time before it fires             |
| `pq posts delete <id>`                     | Cancel a draft or scheduled post — **destructive** |
| `pq posts remove-account <id> <accountId>` | Drop one account, leave the rest of the post       |
| `pq media upload --file <path>`            | Upload once, attach by id                          |
| `pq media list`                            | Uploaded and generated media                       |
| `pq workspaces list`                       | Workspaces, when one login holds several sets      |

`--pretty` indents the JSON. `publishq` and `pq` are the same binary.

`posts delete` is the only call here that destroys something: ask the **human in
the loop** first.
