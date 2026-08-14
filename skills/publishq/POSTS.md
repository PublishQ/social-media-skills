# One post, many accounts

Read this when the post is not identical everywhere: different copy, different
media, or different settings per account.

A post is ONE object with an `accounts[]` array, and one create call covers every
account. Never loop one create per account — that produces several unrelated posts
the user then has to manage separately.

## Per-account overrides

`--account` sends the same content everywhere. Swap it for `--accounts-json` when
one account needs its own (the two flags conflict):

```bash
pq posts create --content "Shipped it." --accounts-json '[
  {"accountId":"a1b2c3d4-e5f6-7890-abcd-ef1234567890"},

  {"accountId":"d4c3b2a1-0987-6543-21fe-dcba09876543",
   "postOverrides":{"content":"A longer version for LinkedIn, with the context that audience wants."}},

  {"accountId":"11111111-2222-3333-4444-555555555555",
   "postOverrides":{"mediaIds":["7c1f2e3d-4a5b-6c7d-8e9f-0a1b2c3d4e5f"]},
   "platformSpecificSettings":{"contentType":"story"}}
]'
```

Two different objects, and mixing them up is the usual mistake:

| Object                     | Scope                | Holds                                                      |
| -------------------------- | -------------------- | ---------------------------------------------------------- |
| `postOverrides`            | same on any platform | `content`, `mediaIds`, `thumbnailTimestamp`                |
| `platformSpecificSettings` | one platform only    | that platform's own options — [PLATFORMS.md](PLATFORMS.md) |

`postOverrides` fields, all optional:

- **`content`** — replaces the post-level text for this account only. This is how
  one call sends a short version to X and a long one to LinkedIn.
- **`mediaIds`** — replaces the post-level media for this account only. This is how
  one call sends a picture aimed at one audience to X and a different one to
  Instagram. Upload each file first and put its id here.
- **`thumbnailTimestamp`** — seconds into the video to grab the cover frame from.
  YouTube and TikTok.

Anything omitted falls back to the post-level value, so an account entry with only
an `accountId` gets the shared content and media.

## Writing per-platform copy

When the user gives you one piece of text, send it everywhere and do not invent
variants. When they ask for it tailored, `postOverrides.content` is the mechanism —
and the per-platform character limits in [PLATFORMS.md](PLATFORMS.md) are what
decide how much room you have.
