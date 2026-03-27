---
name: mailcow
description: >
  Interact with a Mailcow API to manage mailboxes, domains, aliases, DKIM, spam settings, logs, quarantine,
  mail queue, and system status. Use this skill whenever the user wants to do ANYTHING with their Mailcow
  mail server via API -- creating or deleting mailboxes, adding domains, configuring aliases, checking logs,
  viewing quarantine, flushing the mail queue, generating DKIM keys, adjusting rate limits, or checking
  container status. Also trigger when the user says things like "add a mailbox", "create an alias",
  "check the mail queue", "show me the logs", "get DKIM for a domain", or any other mail server admin task.
---

# Mailcow API Skill

## Setup

The user must provide:
- `MAILCOW_HOST`: e.g. `https://mail.example.com` (no trailing slash)
- `MAILCOW_API_KEY`: found in Mailcow UI under **Access > API** (read-write or read-only depending on task)

Ask the user for these if not already provided in the conversation. Never hardcode credentials.

All requests use:
```
GET/POST/PUT/DELETE https://{MAILCOW_HOST}/api/v1/{endpoint}
Headers:
  X-API-Key: {MAILCOW_API_KEY}
  Content-Type: application/json
```

Use `bash_tool` with `curl` to make all API calls. Always use `-s` (silent) and `-w "\nHTTP_STATUS:%{http_code}"` for clean output.

Example pattern:
```bash
curl -s -w "\nHTTP_STATUS:%{http_code}" \
  -H "X-API-Key: $API_KEY" \
  -H "Content-Type: application/json" \
  https://mail.example.com/api/v1/get/mailbox/all
```

---

## Endpoint Reference

See `references/endpoints.md` for the full endpoint list grouped by category.

Quick overview of categories:
- **Mailboxes** -- get, add, edit, delete
- **Domains** -- get, add, edit, delete
- **Aliases** -- get, add, edit, delete
- **DKIM** -- get, add, delete
- **Anti-Spam / Rspamd** -- get/set user policies, spamfilter
- **Quarantine** -- list, release, delete
- **Mail Queue** -- get, flush, delete
- **Logs** -- SMTP, IMAP, dovecot, rspamd, autodiscover, watchdog
- **Status** -- container status, system info
- **Fail2ban** -- list, edit
- **Rate limits** -- get, edit
- **App Passwords** -- get, add, delete
- **Domain Admins** -- get, add, edit, delete
- **TLS Policy** -- get, add, edit, delete
- **Routing** -- transport maps

---

## Workflow

1. Read the user's intent
2. Look up the relevant endpoint(s) in `references/endpoints.md`
3. Build the curl command -- ask the user for any missing parameters
4. Run via `bash_tool`
5. Parse the JSON response and present it clearly (table, bullet list, or prose -- whatever fits)
6. For destructive actions (delete, flush queue), confirm with the user first

## Output formatting

- For lists (mailboxes, domains, aliases): show as a clean markdown table
- For single items: show key fields in a readable summary
- For status checks: highlight anything that looks unhealthy (not running, high error counts)
- Always show the HTTP status code if it's not 200

## Error handling

- 401 = wrong/missing API key
- 403 = key is read-only but write was attempted
- 404 = resource not found
- 422 = validation error -- show the `type` and `msg` fields from the response
- 500 = server error -- suggest checking Mailcow logs

---

## Security notes

- Never log or output the API key in responses
- For read-only tasks (get logs, check status), a read-only API key is sufficient
- For write tasks (create/delete mailboxes, edit domains), a read-write key is required
- Remind the user to use IP-restricted API keys where possible
