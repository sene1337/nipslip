# Identity and Relays

Read this file before any live Nostr action.

## Bootstrap NSEC From 1Password

```bash
source ~/.zshrc 2>/dev/null
NSEC=$(op item get '<1password-item>' --vault <1password-vault> --field password --reveal)
```

- Keep `NSEC` in shell memory only.
- Parameterize `'<1password-item>'` and `<1password-vault>` when adapting the
  command. Do not hardcode real vault identifiers or item names into the skill.
- Re-run the bootstrap if `op` fails because the shell does not have the
  1Password token environment yet.

## Use The Standard Relay Set

```bash
wss://nos.lol wss://relay.damus.io wss://premium.primal.net
```

- Use this relay trio for publish, reply, delete, fetch, and poll work unless
  the task explicitly calls for something else.

## Run In The Right Place

- Run locally on the current host when OpenClaw is already on that machine.
- Use remote or node execution only when the target machine is actually remote.

## Handle Keys Conservatively

- Do not store `NSEC` in files, chat, or logs.
- Keep secrets out of temp files; temp files are for note content only.
- Reuse `$NSEC` for later `nak` commands in the same shell session.
