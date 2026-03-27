# Poll Voting

Read this file for kind 6969 discovery, zap-plus-reply voting, and poll failure
recovery.

## Discover The Poll

```bash
nak req -k 6969 -e <thread-root-id> wss://nos.lol wss://premium.primal.net

nak req -i <poll-event-id> wss://nos.lol wss://premium.primal.net
```

Inspect these tags on the poll event:

- `poll_option`
- `closed_at`
- `value_minimum` and `value_maximum`
- `e` with the `root` marker

## Check Closed At Before Doing Any Other Work

```python
import time
closed_at = int(poll_event_tags['closed_at'])
if time.time() >= closed_at:
    print("POLL CLOSED - do not proceed")
```

Abort if the poll is expired. Do this before research, vote copy, or invoice
work.

## Build The Zap Request

1. Identify the poll creator and use the creator's `lud16`, not the
   contestant's.
2. Fetch the creator's `lud16`:

```bash
nak profile <creator-pubkey> wss://nos.lol | grep '^lud16:'
```

To resolve an LNURL and get an invoice directly from a lightning address:

```bash
npx @getalby/cli -c ~/.alby-cli/connection-secret.key \
  request-invoice-from-lightning-address <user@domain> --amount <sats>
```

For the full zap flow (with nostr metadata), continue with the manual
resolution: `https://<domain>/.well-known/lnurlp/<user>`

3. Build the kind 9734 request:

```bash
nak event -k 9734 --sec $NSEC \
  -t 'relays=["wss://nos.lol","wss://relay.damus.io","wss://premium.primal.net"]' \
  -t 'amount=<msats>' \
  -t 'lnurl=<lnurl-endpoint>' \
  -t 'p=<creator-pubkey>' \
  -t 'e=<poll-event-id>' \
  -t 'poll_option=<0-or-1>' \
  -c @/tmp/zap_msg.txt
```

4. Get the invoice from the LNURL callback with the zap request in the `nostr`
   query parameter.
5. Pay the invoice via Alby CLI:

```bash
npx @getalby/cli -c ~/.alby-cli/connection-secret.key pay-invoice <bolt11>
```

If the CLI is unavailable, fall back to the direct API:

```bash
TOKEN=$(curl -s -X POST http://localhost:8080/api/start \
  -H 'Content-Type: application/json' \
  -d '{"unlockPassword":"<1password-alby-hub-password>"}' | python3 -c 'import sys,json;print(json.load(sys.stdin)["token"])')

curl -s -X POST "http://localhost:8080/api/payments/<bolt11>" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json"
```

## Post The Threaded Vote Rationale

```bash
nak event -k 1 --sec $NSEC -c @/tmp/reply.txt \
  -t "e=<thread-root>;;root" \
  -t "e=<poll-event-id>;;reply" \
  -t "p=<creator-pubkey>" \
  -t "p=<contestant-pubkey>" \
  wss://nos.lol wss://relay.damus.io wss://premium.primal.net
```

## Preserve The Failure Modes

- Zap the poll creator, not the contestant.
- A contestant's LNURL can return `403` and still be irrelevant if the
  creator's `lud16` is correct.
- Do not use `nak wallet nutzap`; the NIP-61 path can panic on missing keysets.
- Missing `closed_at` is a real failure mode. Abort if the window is already
  closed.
- For multi-matchup rounds, do matchup #1, show Brad the result, wait for
  approval, then continue one matchup at a time.

## Verify

- `closed_at` was checked before any other work.
- `poll_option` matches the intended choice.
- The zap targeted the creator and used the creator's `lud16`.
- The invoice was paid successfully before the reply was posted.
- The reply tags point at the thread root and the poll event.
