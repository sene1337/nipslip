# Media Uploads and Recovery

Read this file for image, video, or audio posts, or when fixing a text-only
note that should have contained media.

## Upload Through Nostr.Build

Use `nak curl`, not plain `curl`, because `nostr.build` requires NIP-98 auth.

- NIP-96 descriptor: `https://nostr.build/.well-known/nostr/nip96.json`
- Upload endpoint: `https://nostr.build/api/v2/nip96/upload`

```bash
FILE=/absolute/path/to/video.mp4

nak curl --sec "$NSEC" -X POST \
  -F "file=@${FILE};type=video/mp4" \
  -F "caption=short description" \
  https://nostr.build/api/v2/nip96/upload
```

Expected result: JSON with a public `url` and a `nip94_event` block.

## Publish The Returned URL As A Normal Note

1. Put the final note text and the returned media URL in `/tmp/post.txt`.
2. Publish it with the standard kind 1 note flow from
   [posting.md](posting.md).
3. Check client rendering after relay success.

## Recover From A Bad Text-Only Media Note

1. Upload the file through the NIP-96 flow above.
2. Publish the corrected note with the returned media URL.
3. Delete the bad note:

```bash
nak event -k 5 --sec $NSEC -e <event-id> \
  wss://nos.lol wss://relay.damus.io wss://premium.primal.net
```

## Verify

- The upload used `nak curl --sec "$NSEC"`.
- The published note body contains the actual returned media URL.
- Any bad text-only note was deleted after the corrected post went live.
