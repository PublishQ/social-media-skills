# Platforms

Context on each platform, so you can shape a post that fits before you send it, and
explain a rejection when one comes back.

Nothing here is a check you have to run first. PublishQ validates the whole post
when you create it and the error names what is wrong, so read a platform's entry
when the user is deciding WHAT to post ("this one only takes four images, so pick
four"), or when an error points at that platform. TikTok is the one exception, and
it is step 3 of [SKILL.md](SKILL.md): it needs a live call whose result you pass
back.

Media is prepared for you — format converted, oversized scaled down, heavy files
compressed, per platform, transparency kept. Never edit a file first; see
[MEDIA.md](MEDIA.md). Aspect ratio is the exception, because cropping is the user's
call.

**`platformSpecificSettings`** goes on the account entry in `--accounts-json`
(see [POSTS.md](POSTS.md)) and holds the options below. It is optional on every
platform except TikTok. Anything not listed for a platform is not a valid key
there, and the object is strict — a misspelled key is a 400, not a silent ignore.

## Instagram

**Requires a Business or Creator account.** The publishing API refuses personal
accounts, and this is the single most common first-post failure. Only the user can
switch it, in the Instagram app under Settings → Account type.

- Feed and carousel images must sit between 9:16 and 16:9. **Ratio is what gets
  rejected, not resolution** — a wide screenshot fails however sharp it is.
- A carousel is 2–10 items, and the first item's ratio governs all of them.
- GIFs are not supported. Convert to MP4.
- Caption caps at 2,200 characters; 100 API posts per rolling 24 hours.

Settings:

- `contentType`: `"story"` or `"reels"`. Omit for a feed post or carousel. A lone
  video already publishes as a Reel, so there is no Shorts-style endpoint to hunt
  for; pass `"reels"` only to force it.
- `shareToFeed` (boolean): a Reel appears in the Feed tab as well as Reels. Neither
  value guarantees placement — Instagram decides.
- `trialParams: {"graduationStrategy":"MANUAL"|"SS_PERFORMANCE"}`: publishes a Reel
  to non-followers first. Needs a public account with 200+ followers, and Instagram
  rejects the post otherwise, so confirm both before offering it.
- `audioName` (string): names the audio track on a Reel.

## Facebook

**Publishes to a Page the user administers, never a personal profile.** Aiming at
a profile is rejected outright.

- A multi-photo album cannot contain a video.

Settings:

- `contentType`: `"story"` is the only accepted value. There is no reel value —
  a lone video becomes a Reel by itself. Omit the whole object for a normal post.

## TikTok

**The only platform where `platformSpecificSettings` is required.** Call
`pq accounts tiktok-creator-info <accountId>` first, every time, and take
`privacyLevel` from what it returns — the allowed values depend on the creator's
own account and they can change it whenever they like.

Six fields are required on every TikTok post, video or photo:

```json
{
  "privacyLevel": "PUBLIC_TO_EVERYONE",
  "disableComment": false,
  "disableDuet": false,
  "disableStitch": false,
  "brandContentToggle": false,
  "brandOrganicToggle": false
}
```

`privacyLevel` is one of `PUBLIC_TO_EVERYONE`, `FOLLOWER_OF_CREATOR`,
`MUTUAL_FOLLOW_FRIENDS`, `SELF_ONLY`. The creator-info call is what tells you which
of those that account may use.

All six are required either way, but **`disableDuet` and `disableStitch` only do
anything on a video** — TikTok ignores them on a photo carousel, and on a private
account. Send them regardless; leaving one out is a 400.

The two brand toggles are TikTok's branded-content disclosure. Default both to
`false` and only set one when the user says the post is an ad or a paid
partnership.

Then one optional field per kind:

- Photo carousel: `autoAddMusic` (boolean) lets TikTok add background music, which
  the user can change later in the app.
- Video: `videoMadeWithAi` (boolean) declares AI-generated content.

Also:

- One video **or** photos, never both in the same post — separate endpoints.
- TikTok itself takes JPEG and WebP for photo posts; a PNG is converted for you,
  transparency intact.

## YouTube

**Needs a video file.** There is no image path on YouTube at all.

- A vertical video under three minutes is treated as a Short automatically.
- Titles cap at 100 characters, and neither title nor description may contain
  `<` or `>`.

Settings:

- `description` (up to 5,000 characters): the watch-page description. The post
  content becomes the title, so this is where the body text goes.
- `privacyStatus`: `"public"`, `"unlisted"` or `"private"`.
- `tags` (array of strings).
- `categoryId` (string of digits): YouTube's category. The accepted set is in the
  docs below.
- `madeForKids` (boolean): required by law where it applies, and it turns off
  comments and notifications.
- `containsSyntheticMedia` (boolean): declares AI-generated content.
- `embeddable` (boolean), `playlistId` (string).

## X

- Characters are counted by weight: a URL always costs 23.
- Photos (up to four), one video, one GIF, and a poll are mutually exclusive
  modes. You cannot combine them.

Settings:

- `poll: {"options":["A","B"],"durationMinutes":1440}`: a poll instead of media.
- `replySettings`: `"following"`, `"mentionedUsers"`, `"subscribers"` or
  `"verified"` — who may reply.
- `replyToTweetId` (digits): posts this as a reply to that one post.
- `quoteTweetId` (digits): quotes that one post.
- `communityId` (digits): posts into a Community the user belongs to.
  `shareWithFollowers` (boolean) applies only alongside it.
- `forSuperFollowersOnly` (boolean).

## LinkedIn

Posts to a personal profile or a company Page through the same call, so you do
not need to know which one you are aiming at.

- Up to 20 images, or one MP4. Images and video cannot be mixed.

Settings:

- `visibility`: `"PUBLIC"` (the default) or `"CONNECTIONS"`. Personal profiles
  only — a company Page is always public.

## Threads

- Text is counted in UTF-8 **bytes**, so emoji-heavy copy runs out of room
  earlier than a character count suggests.
- The one platform here that allows photos and video in the same carousel.
- Only GIPHY GIFs work; an uploaded GIF file does not.
- 250 published posts per rolling 24 hours.

Settings:

- `topicTag` (1–50 characters, no `.` or `&`): one topic for the post, which
  overrides a topic auto-detected from inline hashtags.

## Bluesky

Connected with a handle plus an app password, generated in Bluesky's own
settings — so nothing the user gives PublishQ can lock them out of the account.

- Up to four images, or one MP4. Not both.
- An animated GIF renders as a single static frame.
- Mentions, links and hashtags become rich-text facets, worked out from your text.
  Write plain prose; there is no field for byte offsets and no arithmetic to do.

Settings: none. Bluesky takes no platform-specific options.

## Full numbers

Every field, every accepted value and every response shape is in the docs, which
are the source of truth for all of the above:

- <https://publishq.com/docs> — the CLI, MCP and SDK references
- <https://publishq.com/docs/platforms/instagram-api> and its siblings
  (`facebook-api`, `tiktok-api`, `youtube-api`, `twitter-api`, `linkedin-api`,
  `threads-api`, `bluesky-api`) — per-platform limits and behaviour
- <https://publishq.com/openapi.yaml> — the machine-readable contract
