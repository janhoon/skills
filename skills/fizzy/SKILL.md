---
name: fizzy
description: Manage Fizzy kanban boards, cards, columns, and tags via the fizzy CLI. Includes the Ralph loop protocol — agent ownership tagging, Ralphing column transitions, and done state management. Use when creating or working tickets that will be executed by a Ralph agent.
metadata:
  openclaw:
    emoji: "🗂️"
  version: "1.0"
---

# Fizzy Skill

Use this skill to operate Fizzy boards via the `fizzy` CLI — and to follow the **Ralph loop protocol** when tickets are assigned to an agent for execution.

## Setup

Config lives at `~/.config/fizzy/config.yaml`.

```bash
fizzy board list          # verify access
fizzy tag list            # list available tags
```

Key environment:
- **API Token:** set in config or `--token`
- **Account:** `0000001` (default, or pass `--account`)

## Boards

```bash
fizzy board list
fizzy column list --board <BOARD_ID>
```

Known boards (from TOOLS.md):
- Veldix: `03fhhw1fqtd1qks4gavlbjtys`
- Virtual Office / Speke: `03fie7qrkwk1eanf67qz8pnjy`
- Ace Observability: `03fimjg5jiqgre47682zq0g24`
- Billing: `03fmd7ent8ecmoosyk3swomno`
- Walkr: `03fo20q0mxh1og43yu5111c0o`
- Lesaka Insights: `03fohwbgi6ghfmj38ovate7u8`
- Kiep: (check board list)
- whoami: `03fj8qfgkl0d7816qorjghi3x`

## Cards

```bash
# List cards on a board
fizzy card list --board <BOARD_ID>

# Create card
fizzy card create --board <BOARD_ID> --title "Title" --description "<p>Details</p>"

# Show card
fizzy card show <CARD_NUMBER>

# Move to a column
fizzy card column <CARD_NUMBER> --column <COLUMN_ID>

# Tag a card
fizzy card tag <CARD_NUMBER> --tag "agent:ralph"

# Close (done)
fizzy card close <CARD_NUMBER>
```

## Columns

Every board has a standard set of columns including **Ralphing** (purple, for in-progress agent work) and **Done**.

```bash
# Get column IDs for a board
fizzy column list --board <BOARD_ID>
```

---

## Ralph Loop Protocol

The Ralph loop is the agent execution workflow. All tickets intended for agent execution **must** follow this protocol exactly.

### 1. Ticket Creation — Tag with agent name

When a ticket is created for Ralph to execute, immediately tag it with the agent name:

```bash
# Create the card
fizzy card create --board <BOARD_ID> --title "Feature: X" --description "<p>...</p>"
# Returns: card number e.g. 42

# Tag with agent name
fizzy card tag 42 --tag "ralph"
```

**Rules:**
- Only the tagged agent is allowed to pick up and execute this ticket.
- If multiple agents exist (ralph-1, ralph-2, etc.), tag with the specific agent name.
- Never start work on a ticket tagged for a different agent.

### 2. Work Starts — Move to Ralphing

When an agent begins working on a ticket, move it to the **Ralphing** column immediately:

```bash
# Get Ralphing column ID
RALPHING_ID=$(fizzy column list --board <BOARD_ID> | python3 -c "
import json,sys
cols=json.load(sys.stdin).get('data',[])
col=next((c for c in cols if c['name'].lower()=='ralphing'),None)
print(col['id'] if col else '')
")

fizzy card column <CARD_NUMBER> --column "$RALPHING_ID"
```

This signals the card is actively being worked on. No other agent should touch it.

### 3. Work Complete — Move to Done

When the agent finishes (PR merged, task verified), close the card:

```bash
fizzy card close <CARD_NUMBER>
```

If the board uses a named "Done" column instead of close:

```bash
DONE_ID=$(fizzy column list --board <BOARD_ID> | python3 -c "
import json,sys
cols=json.load(sys.stdin).get('data',[])
col=next((c for c in cols if c['name'].lower()=='done'),None)
print(col['id'] if col else '')
")

fizzy card column <CARD_NUMBER> --column "$DONE_ID"
```

### Full Ralph Loop Example

```bash
BOARD="03fie7qrkwk1eanf67qz8pnjy"

# 1. Create ticket
CARD=$(fizzy card create --board "$BOARD" \
  --title "Add dark mode toggle" \
  --description "<p>Add a dark/light theme toggle to settings.</p>" \
  | python3 -c "import json,sys; print(json.load(sys.stdin)['data']['number'])")

# 2. Tag for ralph
fizzy card tag "$CARD" --tag "ralph"

# 3. Ralph picks it up — move to Ralphing
RALPHING=$(fizzy column list --board "$BOARD" | python3 -c "
import json,sys
cols=json.load(sys.stdin).get('data',[])
col=next((c for c in cols if c['name'].lower()=='ralphing'),None)
print(col['id'] if col else '')
")
fizzy card column "$CARD" --column "$RALPHING"

# 4. Ralph finishes — close card
fizzy card close "$CARD"
```

### Protocol Rules Summary

| Step | Who | Action |
|------|-----|--------|
| Ticket created | Orchestrator (Clawd) | Create card + tag with agent name |
| Ticket pickup | Agent (Ralph) | Check tag matches own name, move to Ralphing |
| Work in progress | Agent (Ralph) | Card stays in Ralphing |
| Work complete | Agent (Ralph) | Close card / move to Done |
| Ownership violation | — | Agent must NOT touch cards tagged for another agent |

### Ownership Check (for agents)

Before starting any ticket, an agent must verify the ticket is tagged for them:

```bash
fizzy card show <CARD_NUMBER> | python3 -c "
import json,sys
card=json.load(sys.stdin).get('data',{})
tags=[t['name'] for t in card.get('tags',[])]
agent='ralph'  # this agent's name
if agent in tags:
    print('✅ Ticket is mine')
else:
    print(f'❌ Not mine — tagged for: {tags}')
    exit(1)
"
```

## Error Handling

- If `fizzy column list` returns no Ralphing column, the board hasn't been set up — create it with `fizzy column create --board <BOARD_ID> --name "Ralphing" --color "#7C3AED"`.
- If a card close fails, try moving to the Done column by ID instead.
- Never silently skip column transitions — they are the source of truth for work state.
