# Quote Posts

Read this file when the task involves quoting or referencing another Nostr note
inline (not replying in a thread, but embedding a reference).

## Decode The Target First

```bash
nak decode <nevent1...>
```

Extract the event id (and optionally the relay hint) from the decoded output.

## Publish A Quote Post

Use a `q` tag plus a `nostr:nevent` reference in the content body:

```bash
echo "Your commentary here

nostr:nevent1qqs..." > /tmp/quote.txt

nak event -k 1 --sec $NSEC -c @/tmp/quote.txt \
  -t "q=<target-event-id>" \
  -e <target-event-id> \
  wss://nos.lol wss://relay.damus.io wss://premium.primal.net
```

## Why Both q Tag And nostr:nevent

- The `q` tag tells clients "this note quotes another note" so they can render
  an embedded preview.
- The `nostr:nevent` inline reference gives clients enough metadata (event id +
  relay hints) to actually fetch and display the quoted note.
- Without both, some clients show "mentioned note not found" or render the
  quote as a plain reply instead of an embedded reference.

## Quote vs Reply

- **Reply** (threading.md): your note appears in the thread below the original.
  Uses `root` and `reply` markers on `e` tags.
- **Quote**: your note stands alone in your feed with the original embedded
  visually. Uses `q` tag.
- You can combine both — quote a note AND thread it as a reply — by including
  the `q` tag alongside NIP-10 `root`/`reply` tags.

## Verify

- `/tmp/quote.txt` contains `nostr:nevent1...` referencing the correct event.
- The `q` tag value matches the target event id.
- At least one primary relay confirms the quote post.
- Check the post in a client — the quoted note should render as an embedded
  preview, not a bare link.
