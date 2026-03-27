# Threading and Replies

Read this file for NIP-10 replies, `nevent` links, or reply-target debugging.

## Decode Shared Links First

```bash
nak decode <nevent1...>
```

Decode first whenever the input is an `nevent` link rather than a raw event id.

## Build Replies With NIP-10 Markers

Use `-t` tags with `root` and `reply` markers. Never use bare `-e` for a live
reply.

```bash
nak event -k 1 --sec $NSEC -c @/tmp/reply.txt \
  -t "e=<root-event-id>;;root" \
  -t "e=<event-being-replied-to>;;reply" \
  -p <author-pubkey-of-reply-target> \
  wss://nos.lol wss://relay.damus.io wss://premium.primal.net
```

Apply these rules:

- `root` is the original post that started the thread.
- `reply` is the specific event being answered right now.
- If replying directly to the root post, use the same event id for both tags.
- Inspect the reply target's existing tags before posting so the true thread
  root is correct.

## Combine With Other Flows

- For replies that tag someone in the body, also read
  [mentions.md](mentions.md).
- For poll-vote rationale replies, also read [polls.md](polls.md).

## Verify

- `/tmp/reply.txt` contains only the final reply body.
- `root` and `reply` tags point to the intended events.
- `-p` matches the author of the reply target.
- At least one primary relay confirms the reply.
