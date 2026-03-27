# Posting and Recovery

Read this file for new notes, sanitization, or delete-plus-repost cleanup.

## Publish From A Clean Temp File

1. Draft anywhere if needed, but copy only the final note body to
   `/tmp/post.txt`.
2. Remove drafting cruft before publishing:
   - framework notes
   - alternate hooks
   - open questions
   - appendix or sourcing notes
3. Sanitize before posting:
   - remove real LAN IPs
   - remove real usernames and hostnames unless intentionally public
   - generalize local paths where possible
   - keep setup details only at the level needed to teach the lesson
4. Publish with the standard kind 1 command:

```bash
echo "post content here" > /tmp/post.txt
nak event -k 1 --sec $NSEC -c @/tmp/post.txt \
  wss://nos.lol wss://relay.damus.io wss://premium.primal.net
```

5. Treat success on `wss://nos.lol` plus `wss://premium.primal.net` as live.
   Note a `wss://relay.damus.io` timeout without blocking unless the task
   explicitly needs a retry.

## Recover From A Bad Post

Once the bad event id is known, delete it with the standard kind 5 flow:

```bash
nak event -k 5 --sec $NSEC -e <event-id> \
  wss://nos.lol wss://relay.damus.io wss://premium.primal.net
```

Then:

1. Build a new clean `/tmp/post.txt`.
2. Repost with the standard kind 1 command.
3. Confirm which relays succeeded for the delete and the repost.

## Preserve The Learned Constraints

- Do not post the drafting document directly.
- Do not inline note content in shell commands; bullets, quotes, and special
  characters break escaping.
- Do not use `nak event --content -` (stdin pipe). It is unreliable — use
  `-c @/tmp/post.txt` (file reference) or `-c "$NOTE"` (variable) instead.
- Prefer readable plain formatting over markdown theater.

## Verify

- `/tmp/post.txt` contains only the final post body.
- Sensitive local details are removed unless intentionally public.
- Relay results were checked and the success policy was applied correctly.
- If a malformed post escaped, the delete-plus-repost path completed.
