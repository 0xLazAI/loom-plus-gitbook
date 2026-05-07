# Troubleshooting

Use this page when setup or agent operations do not behave as expected.

## Google Authorization Fails

Check:

- Google Calendar API is enabled in the Google Cloud project.
- OAuth consent screen is configured.
- The redirect URI in Google Cloud exactly matches the callback URL shown in Loom+.
- The OAuth app is published or the current account is allowed as a test user.
- The Team Owner saved Client ID and Client Secret before authorizing.

## Meetings Do Not Appear

Check:

- The team is authorized in `Owner -> Google Meeting`.
- You are viewing the correct team.
- The Google account has access to the calendar event.
- The event was created through Loom+ or the connected Google Calendar integration.

## Fireflies Ingestion Fails

Check:

- Fireflies API key is valid.
- Fireflies webhook URL matches the URL shown in Loom+.
- Fireflies webhook secret matches the value saved in Loom+.
- Target knowledge base exists and is active.
- Open `Admin -> Logs -> Fireflies logs` for the failure reason.

## MCP Or CLI Calls Fail

Check:

- Access token is active and copied completely.
- Request includes `Authorization: Bearer YOUR_ACCESS_TOKEN`.
- Server URL points to the correct Loom+ deployment.
- The token belongs to the same team you are operating in.
- Open `Admin -> Logs -> MCP/CLI logs` for request details.

## Wrong Sidebar Items

Check:

- You are in the correct team.
- Your role is correct for that team.
- Admin tools require Admin or Owner.
- Owner tools require Owner.
- System Admin tools require platform-level System Admin permission.
