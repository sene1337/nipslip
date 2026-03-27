# Mentions and Tagging

Read this file when a note needs both a visible inline mention and client
notification.

## Build The Inline Reference

```bash
nak encode nprofile \
  --pubkey <hex-pubkey> \
  --relay wss://nos.lol \
  --relay wss://relay.damus.io
```

## Pair The Inline Reference With A Matching P Tag

```bash
cat > /tmp/post.txt <<'EOF'
Hey nostr:nprofile1...

Quick question for you...
EOF

nak event -k 1 --sec "$NSEC" -c @/tmp/post.txt \
  -t "p=<hex-pubkey>" \
  wss://nos.lol wss://relay.damus.io wss://premium.primal.net
```

Use both pieces together:

- `nostr:nprofile1...` in `.content` for visible client rendering
- matching `p` tag for notification and indexing

## Avoid Weaker Patterns

- `p` tag alone may notify or index but may not render as a visible mention.
- Raw `nostr:npub...` may render as plain text.
- `#[0]` is an older style. For fresh authored notes, prefer explicit NIP-27
  inline references plus a `p` tag.
- Do not improvise mention syntax from memory on live posts.

## Verify

1. Generate the `nprofile` from the target pubkey.
2. Include the matching `p` tag.
3. If the pattern is new or high-visibility, do one minimal test and confirm
   rendering in the target clients before posting the real note.
