# MCP/CLI Tools

Loom+ exposes the same operational toolset through MCP clients and `loomcli`.
Use these tools when an agent or terminal workflow needs to create projects,
dispatch missions, write wiki memory, manage meetings, inspect logs, or resolve
Loom+ web links.

## Access Model

Create or copy an access token from:

```text
Admin -> Access Token
```

The token scopes tool access to the authenticated Loom+ user and team. Some
tools require Owner or Admin permissions.

## MCP Connection

Use this MCP server config in Cursor, Claude, or any compatible MCP client:

```json
{
  "mcpServers": {
    "loom+": {
      "url": "https://YOUR_DOMAIN/api/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_ACCESS_TOKEN"
      }
    }
  }
}
```

## CLI Connection

Install and authenticate `loomcli`:

```bash
git clone https://github.com/0xLazAI/loomcli.git
cd loomcli
npm install
npm link
loom login --server https://YOUR_DOMAIN --token YOUR_ACCESS_TOKEN
```

Useful CLI commands:

```bash
loom list
loom help create_mission
loom run create_project --json '{"name":"Agent Ops"}'
loom run list_projects --json '{}'
loom whoami
loom logout
```

CLI API endpoints:

| Endpoint | Purpose |
| --- | --- |
| `POST /api/cli/login` | Validate an access token and return user/team context. |
| `GET /api/cli/tools` | Return the available tool definitions and input schemas. |
| `POST /api/cli/run` | Run a tool with `{ "tool": "...", "args": { ... } }`. |

## Common Input Rules

| Concept | Accepted values |
| --- | --- |
| `platform` | `telegram`, `wechat`, `slack`, `discord`, `twitter`, `facebook`, `instagram`, `whatsapp`, `linkedin`, `reddit`, `youtube`, `tiktok`, `threads`, `line`, `signal`. Aliases accepted by the API: `tg`, `wx`, `x`, `fb`, `ig`, `wa`, `li`. |
| Mission status | `UNASSIGNED`, `IN_PROGRESS`, `REVIEW`, `DONE`, `CANCELLED`. |
| Project status | `UNASSIGNED`, `IN_PROGRESS`, `REVIEW`, `DONE`, `CANCELLED`. |
| Mission priority | `P0`, `P1`, `P2`, `P3`, `UNSET`. |
| Coordination issue status | `OPEN`, `IN_PROGRESS`, `DONE`, `CANCELLED`. |
| Coordination issue type | `COORDINATION_START`, `TASK_DISPATCH`, `STATUS_REQUEST`, `STATUS_UPDATE`, `MEETING_FOLLOWUP`, `DECISION_BROADCAST`, `BRIEF_GENERATION`, `REMINDER_TRIGGER`, `OTHER`. |
| Dates | Use ISO datetime, for example `2026-05-11T10:00:00`, or `YYYY-MM-DD` where the tool says a deadline day is accepted. |
| Nullable fields | Some update tools accept `null` to clear fields, for example `deadline`, `description`, `assigneeId`, `projectId`, and `ownerUserId`. |

## Knowledge Tools

### `list_knowledge_bases`

Lists active knowledge bases available to the access token.

Parameters: none.

Example:

```bash
loom run list_knowledge_bases --json '{}'
```

### `create_knowledge_base`

Creates a knowledge base.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `name` | No | string | Knowledge base name. Defaults to `New knowledge base` when omitted. |

Example:

```bash
loom run create_knowledge_base --json '{"name":"Meeting Notes"}'
```

### `get_knowledge_base_id`

Resolves a knowledge base id by name, case-insensitive.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `name` | Yes | string | Existing knowledge base name. |

Use this before `upsert_document` when the user gives a KB name instead of an id.

### `upsert_document`

Inserts or updates a document in a knowledge base.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `kbId` | Yes | string | Knowledge base id. |
| `content` | Yes | string | Document text content. |
| `documentId` | No | string | Existing document id. If present, updates that document. |
| `source` | No | string | Source label, file name, meeting title, or external reference. |
| `platform` | No | string | External platform for identity binding. |
| `platformId` | No | string | User id on the external platform. |

Example:

```bash
loom run upsert_document --json '{"kbId":"kb_123","content":"Decision: use weekly agent review.","source":"agent-review.md"}'
```

### `list_documents`

Lists documents inside a knowledge base.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `kbId` | Yes | string | Knowledge base id. |
| `page` | No | number | Page number, starting at 1. |
| `pageSize` | No | number | Page size. |
| `query` | No | string | Filter by document id, content, or source. |

### `delete_document`

Deletes a document from a knowledge base.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `kbId` | Yes | string | Knowledge base id. |
| `documentId` | Yes | string | Document id. |

Permission: Owner or Admin.

### `update_knowledge_base_name`

Renames a knowledge base.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `kbId` | Yes | string | Knowledge base id. |
| `name` | Yes | string | New display name. |

Permission: Owner or Admin.

### `ask_knowledge_base`

Asks one knowledge base a question.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `kbId` | Yes | string | Knowledge base id. |
| `question` | Yes | string | Question text. |
| `modelId` | No | string | Model id from the Models page. Uses the default model when omitted. |

### `ask_all_knowledge_bases`

Asks across all active knowledge bases available to the token.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `question` | Yes | string | Question text. |
| `modelId` | No | string | Model id from the Models page. Uses the default model when omitted. |

## Identity And User Mapping Tools

### `bind_user_info`

Binds an external platform identity to a Loom+ user using the user's binding code
from Profile.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `bindingCode` | No | string | Preferred binding code from Profile. |
| `invitationCode` | No | string | Legacy alias for `bindingCode`. |
| `platform` | Yes | string | Canonical platform or supported alias. |
| `platformId` | Yes | string | User id on that platform, for example Telegram `from.id`. |

At least one of `bindingCode` or `invitationCode` must be provided.

Example:

```bash
loom run bind_user_info --json '{"bindingCode":"abc123","platform":"telegram","platformId":"987654"}'
```

### `get_user_email_by_platform_id`

Looks up a Loom+ user by external platform identity.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `platform` | Yes | string | Canonical platform or supported alias. |
| `platformId` | Yes | string | User id on that platform. |

Permission: Owner or Admin.

### `get_user_emails_by_ids`

Batch looks up Loom+ user emails by external platform ids.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `platform` | Yes | string | Canonical platform or supported alias. |
| `ids` | Yes | array | Platform user ids. |

Permission: Owner or Admin.

### `list_assigned_missions_by_platform_id`

Lists missions assigned to the Loom+ user bound to a platform account.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `platform` | Yes | string | Canonical platform or supported alias. |
| `platformId` | Yes | string | User id on that platform. |
| `status` | No | string | Mission status filter. |

## Meeting Tools

### `list_upcoming_meetings`

Lists upcoming calendar events.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `withinHours` | No | number | Look-ahead window. Default is 24. |
| `platform` | No | string | Filter by bound user platform. |
| `platformId` | No | string | Required when `platform` is provided. |

### `create_google_meeting`

Creates a Google Calendar event with a Google Meet link.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `summary` | Yes | string | Meeting title. |
| `startTime` | Yes | string | ISO 8601 start time. |
| `endTime` | Yes | string | ISO 8601 end time. |
| `attendeeEmails` | Yes | array | Email addresses to invite. |
| `timeZone` | No | string | IANA time zone, for example `America/Los_Angeles`. Default is UTC. |

Permission: Owner or Admin. The Owner must complete Google Meeting setup first.

Example:

```bash
loom run create_google_meeting --json '{"summary":"Agent weekly review","startTime":"2026-05-11T10:00:00","endTime":"2026-05-11T10:30:00","attendeeEmails":["ops@example.com"],"timeZone":"Asia/Shanghai"}'
```

### `update_google_meeting_by_link`

Updates a Google Calendar event by meeting link.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `meetingLink` | Yes | string | Google Meet link or stored calendar event link. |
| `summary` | No | string | New title. |
| `startTime` | No | string | New ISO start time. |
| `endTime` | No | string | New ISO end time. |
| `attendeeEmails` | No | array | Replacement attendee email list. |
| `timeZone` | No | string | IANA time zone for start and end. |

Permission: Owner or Admin.

### `delete_google_meeting_by_link`

Deletes a Google Calendar event by meeting link.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `meetingLink` | Yes | string | Google Meet link or stored calendar event link. |

Permission: Owner or Admin.

### `scheduling_create_session`

Creates a Telegram scheduling session. The organizer chooses candidate slots
first, attendees pick from those slots, then Loom+ creates a Google Meeting for
the selected time.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `topic` | Yes | string | Meeting topic. |
| `organizer` | Yes | object | `{ "tgId": "123", "name": "Alice" }`. |
| `attendees` | Yes | array | Array of `{ "tgId": "...", "name": "..." }`. |
| `chatId` | No | string | Telegram group chat id. Omit to DM the organizer. |
| `days` | No | number | Number of days to offer. Default is 3. |
| `duration` | No | number | Slot duration in minutes. Default is 60. |
| `teamId` | No | string | Loom+ team id. Defaults to the token's team. |

Requires scheduler bot configuration.

## Link Tools

### `resolve_links`

Resolves Loom+ web links from business resource ids.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `scenario` | Yes | string | One of `projectId`, `missionId`, `documentId`, `meetingLink`. |
| `id` | Yes | string | Resource id or meeting link. |
| `subType` | No | string | Optional route subtype. |
| `query` | No | string | Optional query string, for example `tab=logs`. |

Example:

```bash
loom run resolve_links --json '{"scenario":"missionId","id":"mission_123","query":"tab=logs"}'
```

## Project Tools

### `list_projects`

Lists projects in the task board.

Parameters: none.

### `get_project_id_by_name`

Resolves a project id by name, case-insensitive.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `name` | Yes | string | Project name. |

### `create_project`

Creates a project.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `name` | Yes | string | Project name. |
| `description` | No | string | Project description. |
| `creatorEmail` | No | string | Creator email. |
| `creatorPlatform` | No | string | Creator platform. |
| `creatorPlatformId` | No | string | Creator platform user id. |

If creator mapping is unbound, Loom+ still creates the project and stores an
external source snapshot.

### `update_project`

Updates project fields.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `projectId` | Yes | string | Project id. |
| `name` | No | string | New project name. |
| `description` | No | string or null | New description. Use `null` to clear. |
| `status` | No | string | Project status. |

### `update_project_status`

Updates only the project status.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `projectId` | Yes | string | Project id. |
| `status` | Yes | string | Project status. |

### `delete_project`

Deletes a project.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `projectId` | Yes | string | Project id. |

## Mission Tools

### `list_missions`

Lists missions in a project.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `projectId` | Yes | string | Project id. |

### `get_mission`

Gets one mission by id.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `missionId` | Yes | string | Mission id. |

### `list_project_members`

Lists assignees that already appeared in project missions.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `projectId` | Yes | string | Project id. |

### `create_mission`

Creates a mission in a project.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `projectId` | Yes | string | Project id. |
| `title` | Yes | string | Mission title. |
| `description` | No | string | Mission description. |
| `assigneeId` | No | string | Loom+ user id. |
| `assigneeEmail` | No | string | Loom+ user email. |
| `assigneePlatform` | No | string | External platform. |
| `assigneePlatformId` | No | string | External platform user id. |
| `creatorEmail` | No | string | Creator email. |
| `creatorPlatform` | No | string | Creator platform. |
| `creatorPlatformId` | No | string | Creator platform user id. |
| `deadline` | No | string | ISO datetime or `YYYY-MM-DD`. |
| `priority` | No | string | Mission priority. |

Assignee resolution order: `assigneeId`, then `assigneeEmail`, then
`assigneePlatform` plus `assigneePlatformId`.

Example:

```bash
loom run create_mission --json '{"projectId":"project_123","title":"Prepare agent handoff","assigneeEmail":"ops@example.com","deadline":"2026-05-15","priority":"P1"}'
```

### `create_mission_by_project_name`

Creates a mission by project name instead of project id.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `projectName` | Yes | string | Project name. |
| `title` | Yes | string | Mission title. |
| `description` | No | string | Mission description. |
| `assigneeId` | No | string | Loom+ user id. |
| `assigneeEmail` | No | string | Loom+ user email. |
| `assigneePlatform` | No | string | External platform. |
| `assigneePlatformId` | No | string | External platform user id. |
| `creatorEmail` | No | string | Creator email. |
| `creatorPlatform` | No | string | Creator platform. |
| `creatorPlatformId` | No | string | Creator platform user id. |
| `deadline` | No | string | ISO datetime or `YYYY-MM-DD`. |
| `priority` | No | string | Mission priority. |

### `list_missions_by_deadline`

Lists missions by deadline day across one or more projects.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `projectIds` | Yes | array | One or more project ids. |
| `deadline` | Yes | string | Deadline day, ISO datetime or `YYYY-MM-DD`. |
| `emails` | No | array | Optional assignee email filter. |

### `update_mission`

Updates mission fields.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `missionId` | Yes | string | Mission id. |
| `title` | No | string | Mission title. |
| `description` | No | string or null | Description. Use `null` to clear. |
| `status` | No | string | Mission status. |
| `assigneeId` | No | string or null | Loom+ user id. Use `null` to unassign. |
| `assigneeEmail` | No | string | Assignee email. |
| `deadline` | No | string or null | ISO datetime or `YYYY-MM-DD`. Use `null` to clear. |
| `reason` | No | string | Status change reason. Stored in mission logs. |
| `priority` | No | string or null | Mission priority. Use `null` to clear where supported. |

Example:

```bash
loom run update_mission --json '{"missionId":"mission_123","status":"REVIEW","reason":"Implementation is ready for owner review."}'
```

### `delete_mission`

Deletes a mission.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `missionId` | Yes | string | Mission id. |

### `get_mission_logs`

Gets mission change logs.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `missionId` | Yes | string | Mission id. |

## Coordination Issue Tools

### `list_coordination_issues`

Lists coordination issues in the current team.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `teamId` | No | string | Optional team id. Defaults to token team. |
| `status` | No | string | Coordination issue status. |
| `type` | No | string | Coordination issue type. |
| `ownerUserId` | No | string | Owner user id. |
| `projectId` | No | string | Linked project id. |
| `q` | No | string | Free-text search. |

### `get_coordination_issue`

Gets one coordination issue by id.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `coordinationIssueId` | Yes | string | Coordination issue id. |

### `create_coordination_issue`

Creates a coordination issue, optionally with a new project and linked missions.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `title` | Yes | string | Issue title. |
| `teamId` | No | string | Optional team id. Defaults to token team. |
| `projectId` | No | string | Existing project id. |
| `summary` | No | string | Issue summary. |
| `type` | No | string | Coordination issue type. |
| `status` | No | string | Coordination issue status. |
| `sourceInput` | No | string | Original input text. |
| `content` | No | object | Structured content object. |
| `initiatorUserId` | No | string | Initiator user id. |
| `ownerUserId` | No | string | Owner user id. |
| `priority` | No | string | Issue priority: `P0`, `P1`, `P2`, `P3`, `UNSET`. |
| `externalRef` | No | string | External reference. |
| `tags` | No | array | Tag list. |
| `createProject` | No | object | `{ "name": "...", "description": "..." }`. |
| `createMissions` | No | array | Mission payloads. See below. |

`createMissions` item shape:

| Field | Required | Type | Notes |
| --- | --- | --- | --- |
| `title` | Yes | string | Mission title. |
| `description` | No | string | Mission description. |
| `assigneeId` | No | string | Assignee user id. |
| `deadline` | No | string | ISO datetime or `YYYY-MM-DD`. |
| `priority` | No | string | Mission priority. |

If `createMissions` is provided, pass either `projectId` or `createProject`.

Example:

```bash
loom run create_coordination_issue --json '{"title":"Prepare launch review","summary":"Agents need coordinated launch readiness checks.","type":"TASK_DISPATCH","createProject":{"name":"Launch readiness"},"createMissions":[{"title":"Check Fireflies setup","priority":"P1"},{"title":"Review MCP token access","priority":"P2"}]}'
```

### `update_coordination_issue`

Updates coordination issue fields.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `coordinationIssueId` | Yes | string | Coordination issue id. |
| `title` | No | string | Issue title. |
| `summary` | No | string or null | Issue summary. |
| `type` | No | string | Coordination issue type. |
| `status` | No | string | Coordination issue status. |
| `sourceInput` | No | string or null | Original input text. |
| `content` | No | object or null | Structured content object. |
| `ownerUserId` | No | string or null | Owner user id. |
| `priority` | No | string or null | Issue priority. |
| `externalRef` | No | string or null | External reference. |
| `tags` | No | array or null | Tag list. |
| `projectId` | No | string or null | Linked project id. |

### `delete_coordination_issue`

Deletes a coordination issue.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `coordinationIssueId` | Yes | string | Coordination issue id. |

Deleting an issue does not delete linked projects or missions.

### `get_coordination_issue_logs`

Gets the full audit trail for a coordination issue.

| Parameter | Required | Type | Notes |
| --- | --- | --- | --- |
| `coordinationIssueId` | Yes | string | Coordination issue id. |

## Recommended Agent Workflows

### Dispatch work from chat

1. Resolve users with `get_user_email_by_platform_id` or `get_user_emails_by_ids`.
2. Create a project with `create_project`, or resolve one with `get_project_id_by_name`.
3. Create missions with `create_mission`.
4. Use `resolve_links` to send users direct links back into Loom+.

### Store meeting intelligence

1. Create or resolve a KB with `create_knowledge_base` or `get_knowledge_base_id`.
2. Insert meeting notes with `upsert_document`.
3. Ask follow-up questions with `ask_knowledge_base`.
4. Link operational work with `create_coordination_issue` or `create_mission`.

### Run reviews

1. List current projects with `list_projects`.
2. List missions with `list_missions`.
3. Move ready work with `update_mission`.
4. Inspect `get_mission_logs` before closing a mission.
