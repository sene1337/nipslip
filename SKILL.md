---
name: nostr-ops
description: 'Operate Nostr from the CLI with nak: publish new notes from temp files, reply with correct NIP-10 threading, quote-post with q tags, delete bad posts, upload media through NIP-96 and NIP-98, tag or mention people with NIP-27 plus p tags, vote on kind 6969 polls with zap plus reply, update profile (kind 0) and relay lists (kind 10002), fetch and inspect events, decode and encode nevent/npub/nprofile links, inspect relay behavior, and debug relay publish or fetch issues. Use for requests like "post to nostr", "reply on nostr", "quote nostr post", "delete nostr post", "nostr media upload", "vote on nostr poll", "tag someone on nostr", "nostr mention", "nostr threading", "update nostr profile", "nostr relay list", "fetch nostr event", "nostr relay debug", "check nostr replies", or any request to use nak for Nostr operations.'
---

# Nostr Ops

## Hold These Invariants

- Bootstrap `NSEC` from 1Password before any live action. Read
  [references/identity.md](references/identity.md).
- Publish note content from a clean temp file only. Never inline the body in a
  shell command.
- Use the standard relay trio unless the task explicitly requires something
  else: `wss://nos.lol wss://relay.damus.io wss://premium.primal.net`
- Treat success on `wss://nos.lol` plus `wss://premium.primal.net` as live.
  Note `wss://relay.damus.io` timeouts, but do not block on them unless a retry
  is explicitly required.
- Finish every operation with the verification checklist in this file and the
  operation-specific checks in the reference you used.

## When To Use nak

Use `nak` when you need exact control over Nostr events from CLI:
- publishing from a clean temp file with deterministic formatting
- replying with correct NIP-10 threading
- deleting a bad post and reposting
- inspecting relay success/failure directly
- uploading media through NIP-96

Do NOT default to `nak` when a casual manual post in a client is faster and no
auditability or control is needed.

## Pick The Right Reference

- Read [references/posting.md](references/posting.md) for new notes, temp-file
  publishing, sanitization, relay-success handling, and delete-plus-repost
  cleanup.
- Read [references/threading.md](references/threading.md) for NIP-10 replies,
  root and reply markers, `nevent` decoding, and reply-target reconstruction.
- Read [references/media.md](references/media.md) for NIP-96 uploads with
  `nak curl`, `nostr.build`, returned media URLs, and bad-media recovery.
- Read [references/mentions.md](references/mentions.md) for NIP-27 inline
  mentions, `nprofile` encoding, `p`-tag pairing, and mention verification.
- Read [references/quoting.md](references/quoting.md) for quote-posting with
  `q` tags, `nostr:nevent` inline references, and the difference between
  quoting and replying.
- Read [references/profile.md](references/profile.md) for kind 0 profile
  updates, kind 3 follow lists, and kind 10002 relay lists. Critical: kind 0
  replaces your entire profile.
- Read [references/fetching.md](references/fetching.md) for reading events from
  relays, inspecting profiles, decoding/encoding Nostr identifiers, and
  debugging relay propagation.
- Read [references/polls.md](references/polls.md) for kind 6969 discovery,
  `closed_at` pre-flight, zap-request construction, Alby Hub payment, failure
  modes, and the approval checkpoint pattern.
- Read [references/identity.md](references/identity.md) for 1Password retrieval,
  key-handling rules, relay defaults, and execution context.

Combine references when the task crosses categories:

- Reply plus mention: use
  [references/threading.md](references/threading.md) and
  [references/mentions.md](references/mentions.md).
- Poll vote plus threaded rationale: use
  [references/polls.md](references/polls.md) and
  [references/threading.md](references/threading.md).
- Media note plus cleanup of a bad earlier note: use
  [references/media.md](references/media.md) and
  [references/posting.md](references/posting.md).
- Quote plus mention: use
  [references/quoting.md](references/quoting.md) and
  [references/mentions.md](references/mentions.md).
- Profile update after inspecting current state: use
  [references/fetching.md](references/fetching.md) and
  [references/profile.md](references/profile.md).

## Verify Before Reporting Done

- Final content lives in a clean temp file, not a drafting doc.
- Real usernames, LAN IPs, hostnames, and unnecessary setup fingerprints are
  removed unless intentionally public.
- Replies use correct NIP-10 `root` and `reply` tags.
- Delete events target the correct event id.
- At least one primary relay confirms success.
- If a malformed post escaped, the delete-plus-repost path was executed.
- For poll votes, `closed_at` was checked first and the zap targeted the poll
  creator with the intended `poll_option`.
- For public mentions, the inline `nostr:nprofile...` reference and matching
  `p` tag were both included.
