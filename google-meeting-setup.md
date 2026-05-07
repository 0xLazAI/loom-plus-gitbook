# Google Meeting Setup

This guide is for Team Owners. Google Meeting settings are team-scoped and live in `Owner -> Google Meeting`.

## 1. Create Or Select A Google Cloud Project

1. Open [Google Cloud Console](https://console.cloud.google.com/).
2. Use the Google account that should create and manage team meetings.
3. Select an existing project, or create a new project for Loom+.

## 2. Enable Google Calendar API

1. Open [API Library](https://console.cloud.google.com/apis/library).
2. Search for `Google Calendar API`.
3. Open the Calendar API page and select `Enable`.

## 3. Configure OAuth Consent

1. Open [OAuth consent screen](https://console.cloud.google.com/apis/credentials/consent).
2. Choose the correct user type for your organization.
3. Fill in app name, support email, and developer contact email.
4. Publish the app if Google requires it. Unpublished apps can produce a 403 authorization page.

## 4. Create OAuth Client

1. Open [Credentials](https://console.cloud.google.com/apis/credentials).
2. Select `Create credentials -> OAuth client ID`.
3. Choose `Web application`.
4. Copy the callback URL shown in Loom+ `Google Meeting`.
5. Add it to `Authorized redirect URIs`.
6. Create the client and copy the Client ID and Client Secret.

## 5. Save And Authorize In Loom+

1. Paste Client ID and Client Secret into Loom+.
2. Select `Save credentials`.
3. Select `Authorize Google Calendar`.
4. Complete the Google OAuth flow.
5. Confirm Loom+ shows the account as authorized.

After authorization, Loom+ can create, update, delete, and list meetings through the web app, MCP, CLI, and scheduling bot workflows.
