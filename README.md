# Yoya MCP servers

[Yoya](https://yoya.ai) is an AI scheduling assistant. A host shares one booking link; Yoya chats with each guest, books a real open time on the host's calendar, and sends the host a brief on who is coming and why.

Yoya runs two remote MCP servers. Both are hosted by Yoya over streamable HTTP, so there is nothing to install or run locally. This repository holds their documentation and registry metadata.

| Server | URL | Sign-in | For |
|---|---|---|---|
| **Yoya** (host) | `https://yoya.ai/api/mcp` | OAuth 2.1 | A host's own agent running their Yoya account |
| **Yoya Booking** (guest) | `https://yoya.ai/api/mcp/book` | None | Any agent booking a meeting with a Yoya host |

Both are listed on the official MCP Registry as `io.github.makloai/yoya` and `io.github.makloai/yoya-booking`.

## Install the Yoya skill in Hermes Agent

```bash
hermes skills install makloai/mcp-server-yoya/yoya
```

To browse the repository as a Hermes Skills Hub tap:

```bash
hermes skills tap add makloai/mcp-server-yoya
hermes skills search yoya --source github
```

The [Yoya skill](skills/yoya/SKILL.md) is available under the [MIT license](skills/yoya/LICENSE).

## Connect

**Claude Code**

```bash
claude mcp add --transport http yoya https://yoya.ai/api/mcp
claude mcp add --transport http yoya-booking https://yoya.ai/api/mcp/book
```

**Cursor** (`~/.cursor/mcp.json`)

```json
{
  "mcpServers": {
    "yoya": { "url": "https://yoya.ai/api/mcp" },
    "yoya-booking": { "url": "https://yoya.ai/api/mcp/book" }
  }
}
```

**Claude, ChatGPT and other clients**: add a custom connector with the server URL. The host server opens a Yoya sign-in page the first time and asks which permissions to grant.

Step-by-step guides: [yoya.ai/help/mcp](https://yoya.ai/help/mcp), [Claude Code](https://yoya.ai/help/claude-code), [Cursor](https://yoya.ai/help/cursor).

## Host server: `https://yoya.ai/api/mcp`

For the person who owns the Yoya account. Sign-in is OAuth 2.1 with dynamic client registration. You choose which scopes to grant and can revoke access at any time from [Connected assistants](https://yoya.ai/mcp/connections).

### Scopes

| Scope | Allows |
|---|---|
| `pages:read` | View meeting types, sharing links, teams, plan and payment settings |
| `pages:write` | Create, edit and delete meeting types; manage teams, payment settings and your own AI keys |
| `availability:read` | View working hours and date overrides |
| `availability:write` | Change working hours and date overrides |
| `bookings:read` | Read bookings, guest details and meeting briefs |
| `bookings:write` | Cancel or reschedule bookings |

### Tools

**Meeting types and sharing**
`list_my_booking_pages`, `create_booking_page`, `update_booking_page`, `delete_booking_page`, `get_share_link`, `get_embed_code`, `set_meeting_format`

**Availability**
`get_availability`, `set_weekly_hours`, `set_date_overrides`

**Bookings and briefs**
`list_my_bookings`, `list_booking_history`, `get_meeting_brief`, `reschedule_my_booking`, `cancel_my_booking`

**Setup and connections**
`diagnose_setup`, `get_connection_status`, `connect_calendar`, `set_booking_calendar`, `get_host_profile`, `set_host_profile`, `clear_host_profile`

**Teams**
`list_my_teams`, `get_team`, `create_team`, `invite_team_member`, `accept_team_invitation`, `set_team_availability`, `configure_team_meeting`

**Payments, plan and AI keys**
`get_payment_status`, `set_payment_settings`, `connect_stripe`, `get_plan`, `upgrade_plan`, `buy_voice_minutes`, `manage_billing`, `change_team_seats`, `get_ai_keys`, `set_ai_key`, `test_ai_key`, `remove_ai_key`

Connecting a calendar, connecting Stripe and anything that spends money never happen silently. Those tools return a link to a confirmation step in the Yoya dashboard, and the host finishes it there.

### Example prompts

- "Create a 30-minute discovery call on Yoya, weekdays 9 to 5 Eastern, and ask guests for their company and what they want to cover."
- "Who am I meeting this week, and what does each guest want to talk about?"
- "Block December 24 through January 1 on all my meeting types."
- "Move tomorrow's call with Priya to Thursday afternoon."

## Booking server: `https://yoya.ai/api/mcp/book`

For a guest's agent. No account and no sign-in: it reads the same information as the host's public booking page.

| Tool | What it does |
|---|---|
| `get_booking_page` | Read a public Yoya booking link: meeting options and the details the host requires |
| `list_available_times` | Find open times, labeled in the guest's time zone |
| `preview_booking` | Validate guest details and the chosen time without booking or sending email |
| `book_meeting` | Confirm a reviewed booking, or return a payment link for a paid meeting |
| `get_booking` | Check a booking using its private manage token |
| `reschedule_booking` | Move a booking to a new open time |
| `cancel_booking` | Cancel a booking |

A host's "who isn't a fit" rules are checked at `preview_booking` and are never shown to the guest's agent.

### Example prompt

- "Book 30 minutes with Maya at yoya.ai/maya next week, any afternoon. I'm Sam Lee, sam@example.com, and I want to talk about onboarding."

## Privacy and support

- Privacy policy: [yoya.ai/privacy](https://yoya.ai/privacy)
- Terms: [yoya.ai/terms](https://yoya.ai/terms)
- Help: [yoya.ai/help](https://yoya.ai/help) or customer@yoya.ai

The server source lives in Yoya's private application repository. Issues and questions about the MCP servers are welcome here.
