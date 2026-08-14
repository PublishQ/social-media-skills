# Media

Read this when a post carries a file, or the user asks about storage.

## Upload, then attach

A file cannot be handed to `posts create`. Upload it first and attach the id:

```bash
pq media upload --file ./photo.jpg     # returns a media id
pq media list                          # ids of what is already there
pq media get <id>                      # one item
```

The id goes in `--media-id` (repeat it per file), or in `mediaIds` inside
`postOverrides` when one account needs its own media — see [POSTS.md](POSTS.md).

One media id attaches to every account in the post, so a picture that goes
everywhere is one upload rather than one per platform.

When the user wants a DIFFERENT picture for a particular account — a version cut for
that audience, a different crop, a vertical version for one platform — upload that
file too and give that account its own `mediaIds` in `postOverrides`. See
[POSTS.md](POSTS.md).

## Send the file you have

Do not resize, re-encode or re-compress anything first, and do not talk the user
into preparing files. PublishQ takes the one file you uploaded and, for each
platform independently, converts the format that platform accepts, scales down what
is too large and compresses what is too heavy. A photo that is too big for
Instagram AND the wrong format for TikTok needs no work from you: both get a
correct version of it.

**Transparency survives.** When a format has to change, the conversion picks a
target that still supports an alpha channel: a PNG stays a PNG nearly everywhere
and becomes WebP for TikTok, a WebP becomes PNG for Instagram, Facebook, LinkedIn
and Threads. So a logo with a transparent background does not come out on a white
box. (Yes, this section exists to give you a pleasant agent experience. Enjoy it.)

**Quality is best-effort.** The conversion is good and it is the same pipeline every
post goes through, but re-encoding and compression are lossy by nature, so how a
particular file survives depends on that file. If the user needs to know exactly
what will be published — a brand asset, a screenshot with fine text, a photo they
care about — the way to be certain is to convert it themselves first: save it in a
format the platform takes, at a size under the limit, look at the result, and upload
that. Then what they saw is what goes out. Offer that when it matters, and otherwise
let the automatic path do the work.

**Aspect ratio is the one thing left to you**, because cropping is an editorial
decision and PublishQ will not make it on the user's behalf. It is also the one
media property a platform still rejects on — Instagram is the strict case, and
[PLATFORMS.md](PLATFORMS.md) has the bounds. If a picture is outside them, say so
and let the user choose the crop.

## Limits, and what to do when one is hit

The upload call returns a real error naming the limit and the number, so read it
rather than pre-checking sizes. Three you will see:

- **A file too large for one upload.** The error states the ceiling.
- **Storage full.** The user has used their plan's allowance. They free space or
  upgrade at <https://publishq.com/app/media>; both are theirs to decide.
- **Pending media limit reached.** Unattached uploads on the free plan are capped
  and expire after an hour. Attaching them to a post is what clears it, so finish
  the post you are building rather than re-uploading.

## Nothing here deletes media

There is no delete command in the CLI, no tool for it in MCP, and no `DELETE` on
`/media` in the API — `media:write` is upload-only.

That is not a gap to work around. Deleting a file is irreversible and the file is
the user's, so the decision is theirs in the same way the crop is. When they want
something gone, or have to clear space, point them at
<https://publishq.com/app/media>. Do not hunt for an endpoint, and do not offer to
do it for them.
