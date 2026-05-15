# Agent Bot Setup

The Agents page lets a team Owner connect bot entrypoints to a Loom+ team and
prepare a deployable Coordinator Bot. Team members can open saved bot and group
links, but only the team Owner can save configuration or create deployment
tokens.

## What The Agents Page Supports

| Agent | Status | Purpose |
| --- | --- | --- |
| Coordinator Bot | Available | Telegram assistant for chat-based task dispatch, status updates, and agent coordination workflows. |
| Meeting Bot | Coming soon | Placeholder for future scheduling and meeting workflows. |

Current supported platform:

| Platform | Status |
| --- | --- |
| Telegram | Available |
| Discord | Coming soon |

## Permission Model

| Role | Capability |
| --- | --- |
| Team Owner | Configure bot links, generate a Loom+ access token, and open deploy links. |
| Admin | View saved agent entrypoints. |
| User | View saved agent entrypoints. |

Loom+ stores only bot and group links. Secret values are entered in the deploy
platform and are not stored in Loom+.

## Before You Start

You need:

- Team Owner access in Loom+.
- A Telegram bot created with BotFather.
- A Telegram group where the Coordinator Bot should operate.
- A Loom+ access token for the bot.
- An OpenAI API key for the deployed OpenClaw agent.
- Access to a deployment provider, currently DigitalOcean or Render.

If the Agents page shows `Database setup required`, run the latest Prisma
migration before saving agent links. The required table is `team_agents`.

## Step 1: Create A Telegram Bot

1. Open Telegram and start a chat with `@BotFather`.
2. Send `/newbot`.
3. Choose a display name, for example `Loom Coordinator`.
4. Choose a bot username, for example `LoomCoordinatorBot`.
5. Copy the BotFather token. It looks like `123456:ABC...`.
6. Keep the token private. You will paste it into the deploy platform as
   `CLAWCHEF_VAR_COORDINATOR_TELEGRAM_BOT_KEY`.

## Step 2: Add The Bot To Your Telegram Group

1. Open your Telegram group.
2. Add the new bot as a member.
3. Give the bot permissions needed for your workflow.
4. Copy the group link.

Accepted group link formats:

| Input format | Example |
| --- | --- |
| Public username | `@LoomTeam` |
| Public t.me link | `https://t.me/LoomTeam` |
| Private invite link | `https://t.me/+InviteHash123` |
| Legacy joinchat link | `https://t.me/joinchat/InviteHash123` |

## Step 3: Save Bot Links In Loom+

Open:

```text
Workspace -> Agents
```

In `Coordinator Bot setup`, fill:

| Field | Required | Notes |
| --- | --- | --- |
| Display name | Yes | Human-readable name shown in Loom+. Defaults to `Coordinator Bot`. |
| Telegram bot link | Yes | Accepts `@CoordinatorBot` or `https://t.me/CoordinatorBot`. |
| Telegram group link | No | Accepts public usernames, t.me links, invite links, or joinchat links. |
| Deploy provider | No | Selects the one-click deploy target. Supported values are DigitalOcean and Render. |

Click `Save links`.

After saving, team members can open:

- `Open bot`: saved Telegram bot link.
- `Open group`: saved Telegram group link, if configured.

## Step 4: Generate LOOM_TOKEN

In the Agents page environment config table, click the key button on the
`LOOM_TOKEN` row.

This creates a team access token through the same Access Token system used by
MCP and CLI. The token is copied to your clipboard. Paste it into the deploy
platform as `LOOM_TOKEN`.

If you need to revoke it later, open:

```text
Admin -> Access Token
```

## Step 5: Deploy The Coordinator Bot

The Agents page provides one-click deploy links.

| Provider | Use case | Notes |
| --- | --- | --- |
| DigitalOcean | Recommended | Reads `.do/deploy.template.yaml` from the bot repo and prompts for env vars. |
| Render | Secondary option | Uses the repo deployment flow and prompts for env vars. |

Deployment source:

```text
https://github.com/0xLazAI/deepflow-loomplus-templete
```

Branch:

```text
main
```

The deploy URL does not contain secrets. It only passes the list of environment
variable names and, when available, your current Loom+ origin.

## Environment Variables

Configure these variables in the deployment provider.

| Variable | Required | Secret | Description |
| --- | --- | --- | --- |
| `CLAWCHEF_VAR_COORDINATOR_TELEGRAM_BOT_KEY` | Yes | Yes | Telegram BotFather token used by the Coordinator agent channel. |
| `LOOM_TOKEN` | Yes | Yes | Loom+ team access token used by `loomcli` inside the deployed agent. |
| `CLAWCHEF_VAR_OPENAI_API_KEY` | Yes | Yes | OpenAI API key used by OpenClaw to run the Coordinator agent model. |
| `CLAWCHEF_VAR_ALLOWED_ORIGIN` | Yes | No | Your Loom+ app URL. Also used as the `loomcli` server URL. |
| `DOCS_AUTH_TOKEN` | Yes | Yes | Password for Deepflow docs pages and fallback local CLI token inside the deployment. |

Example values:

```text
CLAWCHEF_VAR_COORDINATOR_TELEGRAM_BOT_KEY=123456:telegram-bot-token
LOOM_TOKEN=loom_access_token_from_agents_page
CLAWCHEF_VAR_OPENAI_API_KEY=sk-...
CLAWCHEF_VAR_ALLOWED_ORIGIN=https://YOUR_LOOM_DOMAIN
DOCS_AUTH_TOKEN=choose-a-random-password
```

Do not commit these values to GitHub.

## How The Coordinator Bot Uses Loom+

The deployed bot should use `LOOM_TOKEN` to call Loom+ tools through `loomcli`
or the CLI API.

Recommended checks after deployment:

```bash
loom whoami
loom list
loom run list_projects --json '{}'
```

Common bot workflows:

| Workflow | Loom+ tools commonly used |
| --- | --- |
| Create project work from chat | `create_project`, `create_mission`, `resolve_links` |
| Assign work by Telegram user | `bind_user_info`, `get_user_email_by_platform_id`, `create_mission` |
| Check assigned tasks | `list_assigned_missions_by_platform_id` |
| Move work through review | `update_mission`, `get_mission_logs` |
| Store group knowledge | `get_knowledge_base_id`, `upsert_document`, `ask_knowledge_base` |

See also: [MCP/CLI Tools](mcp-cli-tools.md).

## User Binding For Telegram Workflows

If the bot needs to map Telegram users to Loom+ users:

1. The user opens `Profile` in Loom+.
2. The user copies their binding code.
3. The user sends this command to the configured Telegram agent:

```text
/bindloomid YOUR_CODE
```

The agent should call `bind_user_info` with:

```json
{
  "bindingCode": "YOUR_CODE",
  "platform": "telegram",
  "platformId": "TELEGRAM_USER_ID"
}
```

After binding, agent tools can resolve assignees by Telegram user id.

## Troubleshooting

| Problem | Check |
| --- | --- |
| `Only team owner can manage agents` | Sign in as the team Owner. Admins and Users can view links but cannot save configuration. |
| `Enter a valid Telegram bot username or t.me link` | Use `@BotName` or `https://t.me/BotName`. Bot usernames must be 5 to 32 characters and use letters, numbers, or underscores. |
| `Enter a valid Telegram group username, t.me link, or invite link` | Use `@GroupName`, `https://t.me/GroupName`, `https://t.me/+InviteHash`, or `https://t.me/joinchat/InviteHash`. |
| Bot cannot call Loom+ tools | Verify `LOOM_TOKEN` is set, not revoked, and belongs to the correct team. |
| Bot cannot reach Loom+ | Verify `CLAWCHEF_VAR_ALLOWED_ORIGIN` matches the public Loom+ URL. |
| Bot does not receive Telegram messages | Verify the Telegram bot token, group membership, bot permissions, and deploy logs. |
| OpenAI calls fail | Verify `CLAWCHEF_VAR_OPENAI_API_KEY` in the deploy provider. |
| Agents page cannot save | Run the latest Prisma migration for `team_agents` and `groupUrl`. |
