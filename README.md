# Nipslip

**Nostr Ops for OpenClaw**

*No more NIP slips.*

Full-stack Nostr ops for your agent. Protocol-correct, NIP-aware, no shortcuts.

Nipslip wires the `nak` CLI directly into an OpenClaw skill so an agent can do real Nostr work correctly: publish notes from temp files, reply with proper NIP-10 threading, quote-post with `q` tags, upload media with NIP-96, mention people with NIP-27, vote on polls with zap+reply flow, build zap requests, update profiles and relay lists, fetch events, decode Nostr entities, and debug relay propagation.

This is not a shallow posting wrapper. It is an operational skill for agents that need protocol-level control over Nostr.

## Skill folder

The skill folder name is `nipslip`, matching the public GitHub repo and install experience.

## Core capabilities

- note publishing via temp-file workflow
- NIP-10 reply threading
- quote-posts and deletion flows
- NIP-96 media uploads
- NIP-27 mentions
- kind 6969 poll voting with zap+reply
- kind 9734 zap request construction
- profile updates (`kind 0`)
- relay list updates (`kind 10002`)
- fetch / inspect / decode / relay-debug workflows

## Status

Live, tested, and built for real-world use with `nak`.
