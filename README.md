# Loom+ User Guide

Loom+ is an agent collaboration workspace for teams that coordinate people, agents, projects, wiki knowledge, meetings, access, and operational logs in one place.

This GitBook is the public user guide for Loom+. The web app links here through the GitBook constants in `src/lib/gitbook.ts`.

## What Loom+ Helps Teams Do

- Turn coordination requests into accountable issues and project missions.
- Keep agent and human work visible through statuses, owners, deadlines, and logs.
- Store reusable team knowledge in private wiki knowledge bases.
- Connect Google Meeting and Fireflies notes to searchable team memory.
- Expose team operations to agents through MCP and CLI tools.
- Configure Telegram Coordinator Bot entrypoints and deployment settings.
- Separate user, admin, owner, and system admin permissions clearly.

## Main App Areas

- Collaboration: coordination issues, decision records, dispatch, and follow-up.
- Projects: project boards, missions, assignees, priorities, deadlines, and mission logs.
- Wiki: knowledge bases, documents, and Ask Wiki workflows.
- Schedule: team calendar and meeting workflows.
- Agents: Coordinator Bot links, deployment targets, and bot environment setup.
- Admin: members, invitations, access tokens, models, and logs.
- Owner: team settings and Google Meeting setup.
- System Admin: platform-level users, teams, and system controls.

## First Steps

1. Open your Loom+ invite link.
2. Sign in with the invited Google account.
3. Select or create your team.
4. Bind Telegram from Profile if your team uses Telegram agent workflows.
5. Open Collaboration, Projects, Wiki, and Schedule for daily work.
6. If you are an Owner, complete Google Meeting and Fireflies setup.

## Source And Publishing

The intended GitHub source repository is:

[0xLazAI/loom-plus-gitbook](https://github.com/0xLazAI/loom-plus-gitbook)

For this workspace, the GitBook source files live in `docs/gitbook/`, with `.gitbook.yaml` pointing GitBook Sync at that directory.
