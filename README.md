# Weekly Reply Email Monitor Agent

An n8n workflow that reads the last week of two Gmail inboxes, a personal one and a work one, finds the emails that are actually waiting on a personal reply, and writes a reply draft for each one inside the original Gmail thread. Nothing is ever sent to anyone else: the drafts wait in Gmail until you review and send them yourself, and you receive one summary email listing everything the workflow read and what it decided. The project demonstrates a single AI step automation in a self hosted n8n: Gmail integration across two accounts, structured output from a language model, cost control through filtering before the model, and a report on every run so the automation never fails silently.

---

## How it works

Every Monday at 07:00 (or whenever you click Test Run), the workflow moves through the same steps:

1. **Settings.** One node holds both inbox addresses, the summary recipient and how many days to look back.
2. **Read both inboxes.** Gmail fetches the last 7 days from the personal and the work inbox, leaving out sent mail, drafts, promotions and social updates.
3. **Keep emails from people.** A Code node drops automated senders, mailing lists, security alerts and receipts, keeps only the latest message in each thread, removes quoted text and trims long bodies, so the AI only sees emails a person might expect an answer to.
4. **Triage and draft.** One AI call per remaining email (OpenAI `gpt-5-mini` through n8n's Information Extractor) returns a structured answer: does this need a reply, how urgent is it, why, which language, and the reply itself. The AI is given today's date and the inbox name, so it skips invitations that have already passed and writes a little more formally for work.
5. **Save drafts.** Each reply is saved as a Gmail draft in the same thread, in the inbox the email arrived in, addressed to the sender.
6. **Summary.** A summary email lists the drafts by priority, then every email that was read with the AI's one line reason, then the counts. It is sent on every run, including weeks where nothing needs a reply.

---

## How to use

1. Run a self hosted n8n (built and tested on n8n 2.34.6) and open it at `http://localhost:5678`.
2. Create a Google Cloud OAuth client for Gmail: create a project, enable the Gmail API, set the consent screen to External and Testing, add both of your Gmail addresses as Test users, and add `http://localhost:5678/rest/oauth2-credential/callback` as an authorized redirect URI.
3. In n8n, import `weekly-reply-email-monitor-agent.json` (Import from File).
4. Create two Gmail OAuth2 credentials, one signed in as each inbox. Use an Incognito window or "Use another account" when signing in, because Google's account chooser tends to pick the account already active in your browser.
5. Attach the personal credential to *Get Personal Inbox*, *Draft Reply (Personal)* and *Send Summary*, and the work credential to *Get Work Inbox* and *Draft Reply (Work)*.
6. Add an OpenAI credential to the *OpenAI Model* node.
7. Fill in the **Settings** node, set `days_back` to 2 for a small first test, and click Execute workflow. Check your Drafts folders and the summary email, then set `days_back` back to 7 and publish the workflow.

A self hosted n8n only runs schedules while it is running, so the computer needs to be awake with n8n started on Monday morning.

---

## Make it your own

Everything you need to change lives in two places. The **Settings** node holds the addresses, the summary recipient, your name and the number of days to look back. The system prompt in *Triage and Draft Reply* holds the reply style: replace `[Your Name]` with your name, and adjust the tone, the languages (it currently handles English and Indonesian) and what counts as needing a reply. The schedule and the workflow timezone (Asia/Jakarta) can be changed in the trigger and in the workflow settings. To use only one inbox, disable the two work nodes; the Code node already removes the duplicate emails that a disabled node passes through.

---

## Files in this repository

**Workflow**
`weekly-reply-email-monitor-agent.json`: the complete n8n workflow, ready to import, with no credentials and placeholder addresses.

**Author's note**
The sticky note at the top of the canvas explains in plain words what the workflow does, what it never does, how to set it up and what it costs.

---

## What's intentionally not finished

Running the workflow twice on the same week creates duplicate drafts, because it does not check for drafts it already made. It reads plain text bodies only, so attachments and images are ignored. It handles English and Indonesian replies; other languages will work but are not tuned. There is no automatic retry if the computer was asleep at the scheduled time.

---

Built with Claude Code.
