# AI Lead Qualifier (n8n)

An automation that scores incoming property leads with AI, replies to each lead within a minute, and alerts the owner. Built with n8n, Google Gemini, Airtable and Gmail.

![Workflow canvas](screenshots/01-canvas.png)

## What it does
1. A lead fills a public form (name, email, phone, intent, budget, area, timeline, message).
2. Invalid emails are saved as "Needs Review" and the owner is alerted. No AI call is wasted.
3. Gemini scores the lead 1-10 and classifies it Hot / Warm / Cold, returning structured JSON.
4. The lead is saved to Airtable.
5. Routing:
   - Hot: personalized reply + instant owner alert
   - Warm: personalized reply + follow-up email after 2 days
   - Cold: generic thank-you
6. Airtable is updated with Reply Sent and Replied At.
7. If the AI fails after 3 retries, the lead is saved as "Needs Review" and the owner is alerted instead of sending a bad email.

## Design decisions
- Lead text is treated as untrusted data (prompt-injection guard).
- The AI is forbidden from inventing listings, prices or addresses.
- Budget and timeline are free text, any currency and any wording.
- Replies are written in the language the lead used.

## Screenshots
| Airtable | Reply email |
|---|---|
| ![Airtable](screenshots/02-airtable.png) | ![Reply email](screenshots/03-reply-email.png) |

## Setup (about 10 minutes)
1. Import `ai-lead-qualifier.json` into n8n.
2. Create an Airtable base "Lead Qualifier", table "Leads", with fields: Name, Email, Phone, Intent, Budget, Area, Timeline, Message, AI Summary, AI Reason, Score, Category (Hot/Warm/Cold/Needs Review), Reply Sent (checkbox), Submitted At, Replied At.
3. Add your own credentials: Airtable token, Gmail (OAuth), Gemini API key.
4. In each Airtable node, select your own base and table.
5. Replace `your-email@example.com` in the 3 alert nodes with your address.
6. Publish the workflow and share the production form URL.

## Known limitations
- Replies from a new Gmail sender can land in Spam. Use a business domain with SPF/DKIM for production.
- The Warm follow-up sends even if the lead already replied.
- Alerts use Gmail; swap the node for Telegram or Slack if preferred.
- The bad-email and AI-failure branches are built but were not live-tested.
- Tested live with Hot leads; the screenshots above are the proof (the n8n trial has since ended).

## Stack
n8n, Google Gemini, Airtable, Gmail this one
