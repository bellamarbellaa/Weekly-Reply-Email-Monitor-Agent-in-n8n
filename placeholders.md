# Placeholders

Everything you need to fill in before the first run, in one list. Nothing else in the workflow is tied to a specific person.

---

## In n8n: the Settings node

| Field | Replace with | Example |
|---|---|---|
| `personal_email` | The personal Gmail address to read | `your.personal@gmail.com` |
| `work_email` | The work Gmail address to read | `your.work@gmail.com` |
| `summary_to` | Where the summary email goes (separate several addresses with commas) | `your.personal@gmail.com` |
| `my_name` | Your first name | `Your Name` |
| `days_back` | How many days to look back | `7` (use `2` for a first test) |

---

## In n8n: the Triage and Draft Reply node

| Where | Replace | With |
|---|---|---|
| Options, System Prompt Template | `[Your Name]` (appears twice, in both sign offs) | Your name as you sign emails |

See `system-prompt.md` for the full prompt and how to edit it.

---

## In n8n: credentials (selected on each node, never stored in the JSON)

| Credential | Nodes |
|---|---|
| Gmail OAuth2 signed in as your personal inbox | Gmail: Get Personal Inbox, Gmail: Draft Reply (Personal), Gmail: Send Summary |
| Gmail OAuth2 signed in as your work inbox | Gmail: Get Work Inbox, Gmail: Draft Reply (Work) |
| OpenAI API key | OpenAI Model |

---

## In Google Cloud: the OAuth client

| Setting | Value |
|---|---|
| Authorized redirect URI | `<your-n8n-url>/rest/oauth2-credential/callback`. Copy the exact value from the **OAuth Redirect URL** field in n8n's Gmail credential window, for example `http://localhost:5678/rest/oauth2-credential/callback` when n8n runs on your own computer |
| Test users | Both Gmail addresses from the Settings node |

---

## Optional

| Where | Default | Change if |
|---|---|---|
| Workflow settings, Timezone | `Asia/Jakarta` | You live in another timezone |
| Every Monday 07:00 (Schedule Trigger) | Monday, 07:00 | You want another day or time |
| OpenAI Model | `gpt-5-mini` | You prefer `gpt-5-nano` (cheaper, if listed) or another model |
