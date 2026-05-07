# Fireflies Setup

This guide is for Team Owners who want Loom+ to ingest Fireflies meeting notes into the team wiki.

## Goal

Loom+ receives Fireflies `Transcription complete` webhooks, fetches meeting notes, and writes normalized meeting-note documents into the selected team knowledge base.

## 1. Open Google Meeting Settings

In Loom+, go to `Owner -> Google Meeting` and find the Fireflies meeting notes section.

## 2. Save Fireflies Configuration

Fill these fields:

- `API Key`: Fireflies API key for the organizer account.
- `Webhook Secret`: shared secret used to verify webhook delivery.
- `Target Knowledge Base`: where meeting notes should be written.

Notes:

- On first successful save, Loom+ creates a default knowledge base named `会议纪要`.
- The Team Owner can rename the default knowledge base.
- The Team Owner can switch ingestion to another active team knowledge base.
- Organizer Email is derived from the current Team Owner email.

## 3. Test Connection

Select `Test connection` before or after saving credentials.

This confirms Loom+ can call the Fireflies API with the configured key. It does not prove the webhook is configured.

## 4. Configure Fireflies Webhook

In Fireflies Developer Settings:

1. Copy the webhook URL shown in Loom+.
2. Paste it into Fireflies webhook configuration.
3. Use the same Webhook Secret value saved in Loom+.
4. Keep the webhook enabled for the same organizer account.

## 5. Understand Ingestion

1. Loom+ creates or tracks the meeting in the team calendar.
2. Fireflies sends a `Transcription complete` webhook after notes are ready.
3. Loom+ verifies the webhook secret.
4. Loom+ fetches transcript and summary from Fireflies.
5. Loom+ writes the note into the selected knowledge base.
6. Loom+ writes a Fireflies log entry for success or failure.

## 6. Logs And Retry

Open `Admin -> Logs -> Fireflies logs`.

If ingestion fails, open the detail and retry after fixing the underlying issue.
