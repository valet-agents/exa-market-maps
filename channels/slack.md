# Slack Message Received

The Slack event payload is appended directly after these
instructions in the user message. Parse it inline — do not fetch,
list, or search for the payload elsewhere. Do NOT use tools to
read the payload.

## Quick Filter — Exit Early If Not Relevant

Before doing anything else, check whether this message is worth
responding to. **Stop immediately and take no action** if ANY of
these are true:

- The message is from a bot (check for `bot_id` or
  `subtype: "bot_message"` in the payload).
- The message is from yourself.
- The message is a channel join/leave, topic change, pin, or other
  system event (any non-empty `subtype` that isn't a real user
  message).
- The message body, after stripping your @mention, is empty or
  just a greeting / thank-you / emoji.
- You're not @mentioned and the message isn't in a thread you
  already replied in.

If you are unsure whether the message is relevant, err on the side
of NOT responding.

## Scope

Extract the `channel` and `ts` (or `thread_ts`) from the payload.
All replies MUST go to this channel and thread. Do not read or
act on messages from other channels or threads.

## Steps

1. Extract `channel`, `ts`, `thread_ts` (if present), `user`, and
   `text` from the event payload.
2. Apply the Quick Filter above. If the message fails the filter,
   **stop here — do nothing**.
3. Strip your @mention token from `text` to get the raw question.
4. Classify the request per SOUL **Phase 1: Parse the request**:
   - **New map** — fresh thesis, build a new webset.
   - **Refresh / enrich** — references a prior map; resolve the
     target webset id from MEMORY.md (this user's recent maps).
   - **Question about a map** — answer from an existing webset;
     do not create a new one.
   - **Delete** — confirm-then-execute (one restated line, wait
     for 👍 / yes / delete it before acting).
   When ambiguous, ask one clarifying question instead of
   guessing.
5. Run the right `exa-websets-mcp` calls per SOUL **Phase 2**:
   create + define enrichments + search for new maps; lookup +
   add enrichments + refresh for follow-ups; lookup-only for
   questions about an existing map.
6. Compose the reply per SOUL **Phase 3**: one post, under 2,500
   characters, preview capped at 10 entities, every company name
   links to its Exa source URL, webset URL linked once for the
   full live map.
7. Post to the originating thread using `thread_ts` if present,
   otherwise `ts`. One reply per mention.
8. After posting a new map or refresh, append a line for this
   user under recent websets in MEMORY.md (slug, thesis, webset
   id, created-at) so the next follow-up resolves to the right
   webset.
