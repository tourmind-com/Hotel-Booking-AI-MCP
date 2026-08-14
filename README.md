# Hotel Booking AI MCP Product Package

This repository contains the ToC product contract and the user-installable companion Skill for Hotel Booking AI MCP. It intentionally does not contain MCP server source code, deployment scripts, or runtime-specific implementation.

## Package contents

```text
Hotel-Booking-AI-MCP/
├── README.md
├── Hotel Booking AI MCP-FORMAT.md
└── skill/
    └── hotel-booking-ai/
        ├── SKILL.md
        └── references/
            └── parameter_guide.md
```

- `skill/hotel-booking-ai/` defines the Agent's hotel-search, live-rate, booking, payment, order-management, and update behavior.
- `Hotel Booking AI MCP-FORMAT.md` defines the public MCP connection and capability contract.

## MCP connection

Use the public production endpoint without connection headers:

```json
{
  "mcpServers": {
    "tourmind": {
      "url": "https://api.tourmind.com/mcp/toc",
      "type": "streamableHttp"
    }
  }
}
```

The MCP connection itself is public. Call `check_skill_update`, hotel search, hotel detail, live rates, and availability checks without `user_key`, even when a key is already stored. Booking, order lookup, cancellation, and payment tools receive `user_key` from the companion Skill only when those protected actions are requested.

## Product model

The ToC MCP and ToB MCP use the same hotel discovery, Google POI resolution, candidate verification, live-rate ranking, image, cancellation, tax, payment, and error-handling prompts wherever their product behavior is identical.

The ToC variant differs only where the product differs:

| Concern | ToC behavior |
|---|---|
| MCP endpoint | `https://api.tourmind.com/mcp/toc` |
| Connection authentication | Public connection; no headers |
| Discovery and availability | Public; no `user_key` required |
| Booking and order operations | Require `user_key` from `user_key.txt` |
| Read-only `web_url` | May be returned by public hotel search or room-rate queries; do not send `user_key` |

The MCP service provides stable hotel tools. The local Skill defines the current user workflow and can evolve independently as search, ranking, price verification, booking, and display strategies improve.

## Skill version and updates

The installed `SKILL.md` is the single source of truth for the Skill version:

```markdown
# Hotel Booking AI Skill (ToC MCP)

**Skill version:** `<current-version>`
```

The Agent sends this version only to `check_skill_update` when an update check is due. Business tools never receive a Skill version, and users never maintain it in the MCP connection configuration.

## Development handoff

The server implementation must satisfy `Hotel Booking AI MCP-FORMAT.md`, including all ten tools referenced by the companion Skill. Transport framework, hosting, deployment, internal forwarding, and release infrastructure are outside this package.

## TourMind hotel booking ecosystem

Choose the package that matches the audience and connection model:

| Audience | Integration | Authentication model | Repository |
|---|---|---|---|
| Consumer / ToC | Direct HTTP Skill | Public search and availability; `user_key` only for order operations | [Hotel Booking AI](https://github.com/tourmind-com/Hotel-Booking-AI) |
| Business / ToB | Direct HTTP Skill | Skill Token required for every API call | [TourMind Booking Skill](https://github.com/tourmind-com/Tourmind-Booking-Skillss) |
| Consumer / ToC | MCP package + companion Skill | Public MCP connection; `user_key` only for order operations | **[Hotel Booking AI MCP](https://github.com/tourmind-com/Hotel-Booking-AI-MCP)** |
| Business / ToB | MCP package + companion Skill | Bearer-authenticated MCP connection | [TourMind Booking MCP](https://github.com/tourmind-com/Tourmind-Booking-MCP) |
