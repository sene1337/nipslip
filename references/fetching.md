# Fetching and Inspecting Events

Read this file for reading events from relays, inspecting profiles, debugging
relay behavior, or decoding Nostr identifiers.

## Fetch A Specific Event By ID

```bash
nak req -i <event-id> wss://nos.lol wss://premium.primal.net
```

## Fetch Someone's Profile

```bash
nak req -k 0 -a <pubkey-hex> -l 1 wss://nos.lol
```

The `content` field is a JSON string with name, about, nip05, lud16, picture,
banner, etc.

## Fetch Recent Posts By Author

```bash
nak req -k 1 -a <pubkey-hex> -l 10 wss://nos.lol wss://premium.primal.net
```

Add `--since <unix-timestamp>` to filter by time window.

## Fetch Replies To A Post

```bash
nak req -k 1 -e <event-id> -l 20 wss://nos.lol wss://premium.primal.net
```

## Decode Nostr Identifiers

```bash
# nevent → event id + relay hints
nak decode nevent1qqs...

# npub → hex pubkey
nak decode npub1...

# nprofile → hex pubkey + relay hints
nak decode nprofile1...

# note → hex event id (NIP-19 bare)
nak decode note1...
```

## Encode Identifiers

```bash
# hex pubkey → npub
nak encode npub <hex-pubkey>

# hex event id → nevent (with relay hint)
nak encode nevent <hex-event-id> --relay wss://nos.lol
```

## Check What Relays Have

When debugging "my post didn't show up" or "I can't find this event," query
each relay individually:

```bash
nak req -i <event-id> wss://nos.lol
nak req -i <event-id> wss://relay.damus.io
nak req -i <event-id> wss://premium.primal.net
```

Empty result from a specific relay means the event didn't propagate there.

## Verify

- Decoded identifiers match the expected hex values.
- Fetched events contain the expected content and tags.
- If debugging relay issues, at least one relay returns the event.
