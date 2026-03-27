# Mailcow API Endpoint Reference

> Source: Official OpenAPI/Swagger docs
> Base URL: `https://{MAILCOW_HOST}/api/v1`
> Auth: `X-API-Key: {key}` header on all requests

---

## Domains

| Action | Method | Endpoint |
|--------|--------|----------|
| Get all / specific | GET | `/get/domain/all` or `/get/domain/{domain}` |
| Get by tags | GET | `/get/domain/all?tags=tag1,tag2` |
| Create | POST | `/add/domain` |
| Update | POST | `/edit/domain` |
| Update footer | POST | `/edit/domain/footer` |
| Delete | POST | `/delete/domain` |
| Delete tags | POST | `/delete/domain/tag/{domain}` |

**Create domain body:**
```json
{
  "active": "1", "aliases": "400", "backupmx": "0", "defquota": "3072",
  "description": "some description", "domain": "domain.tld",
  "mailboxes": "10", "maxquota": "10240", "quota": "10240",
  "relay_all_recipients": "0", "rl_frame": "s", "rl_value": "10",
  "restart_sogo": "10", "tags": ["tag1", "tag2"]
}
```

**Delete domain body:** (plain array, NOT items wrapper)
```json
["domain.tld", "domain2.tld"]
```

**Edit domain body:**
```json
{
  "attr": {
    "active": "1", "aliases": "400", "backupmx": "1", "defquota": "3072",
    "description": "text", "gal": "1", "mailboxes": "10",
    "maxquota": "10240", "quota": "10240", "relay_all_recipients": "0",
    "relayhost": "2", "tags": ["tag3", "tag4"]
  },
  "items": "domain.tld"
}
```

---

## Domain Antispam Policies

| Action | Method | Endpoint |
|--------|--------|----------|
| Get blacklist | GET | `/get/policy_bl_domain/{domain}` |
| Get whitelist | GET | `/get/policy_wl_domain/{domain}` |
| Create policy | POST | `/add/domain-policy` |
| Delete policy | POST | `/delete/domain-policy` |

**Create body:** `{"domain": "domain.tld", "object_from": "*@baddomain.tld", "object_list": "bl"}`
`object_list`: `"bl"` = blacklist, `"wl"` = whitelist

**Delete body:** (array of prefid IDs) `["1", "2"]`

---

## Mailboxes

| Action | Method | Endpoint |
|--------|--------|----------|
| Get all | GET | `/get/mailbox/all` |
| Get specific | GET | `/get/mailbox/{email}` |
| Get all for domain | GET | `/get/mailbox/all/{domain}` |
| Get spam score | GET | `/get/spam-score/{mailbox}` |
| Create | POST | `/add/mailbox` |
| Update | POST | `/edit/mailbox` |
| Update spam score | POST | `/edit/spam-score/` |
| Update custom attrs | POST | `/edit/mailbox/custom-attribute` |
| Update ACL | POST | `/edit/user-acl` |
| Update Pushover | POST | `/edit/pushover` |
| Update quarantine notif | POST | `/edit/quarantine_notification` |
| Delete | POST | `/delete/mailbox` |
| Delete tags | POST | `/delete/mailbox/tag/{mailbox}` |

**Create mailbox body:**
```json
{
  "active": "1", "domain": "domain.tld", "local_part": "info",
  "name": "Full Name", "authsource": "mailcow",
  "password": "password", "password2": "password",
  "quota": "3072", "force_pw_update": "1",
  "tls_enforce_in": "1", "tls_enforce_out": "1",
  "tags": ["tag1", "tag2"]
}
```

**Delete mailbox body:** (plain array)
```json
["info@domain.tld", "sales@domain.tld"]
```

**Edit mailbox body:**
```json
{
  "attr": {
    "active": "1", "force_pw_update": "0", "name": "Full Name",
    "password": "", "password2": "", "quota": "3072",
    "sender_acl": ["default", "info@domain2.tld", "*"],
    "sogo_access": "1", "tags": ["tag3", "tag4"]
  },
  "items": ["info@domain.tld"]
}
```

**Edit spam score body:**
```json
[{"items": ["info@domain.tld"], "attr": {"spam_score": "8,15"}}]
```

---

## Aliases

| Action | Method | Endpoint |
|--------|--------|----------|
| Get all / specific | GET | `/get/alias/all` or `/get/alias/{id}` |
| Get time-limited | GET | `/get/time_limited_aliases/{mailbox}` |
| Create | POST | `/add/alias` |
| Create time-limited | POST | `/add/time_limited_alias` |
| Update | POST | `/edit/alias` |
| Delete | POST | `/delete/alias` |

**Create body:** `{"active": "1", "address": "alias@domain.tld", "goto": "dest@domain.tld"}`
Note: `goto` can be comma-separated. Use `"goto_spam": 1` to mark as spam instead.

**Delete body:** (array of numeric IDs) `["6", "9"]`

**Edit body:**
```json
{
  "attr": {"active": "1", "address": "alias@domain.tld", "goto": "dest@domain.tld", "private_comment": "", "public_comment": ""},
  "items": ["6"]
}
```

**Time-limited alias body:** `{"username": "info@domain.tld", "domain": "domain.tld"}` (Mailcow generates address)

---

## DKIM

| Action | Method | Endpoint |
|--------|--------|----------|
| Get | GET | `/get/dkim/{domain}` |
| Generate | POST | `/add/dkim` |
| Duplicate | POST | `/add/dkim_duplicate` |
| Delete | POST | `/delete/dkim` |

**Generate:** `{"dkim_selector": "dkim", "domains": "mailcow.tld", "key_size": "2048"}`
**Duplicate:** `{"from_domain": "mailcow.tld", "to_domain": "awesomecow.tld"}`
**Delete:** `[["mailcow.tld"]]` (array of arrays!)

---

## Quarantine

| Action | Method | Endpoint |
|--------|--------|----------|
| Get all | GET | `/get/quarantine/all` |
| Release / learn ham | POST | `/edit/qitem` |
| Delete | POST | `/delete/qitem` |

**Release body:** `{"items": ["33", "34"], "attr": {"action": "release"}}`
**Delete body:** `["33"]`

---

## Mail Queue

| Action | Method | Endpoint |
|--------|--------|----------|
| Get queue | GET | `/get/mailq/all` |
| Flush (retry all) | POST | `/edit/mailq` |
| Delete all | POST | `/delete/mailq` |

**Flush:** `{"action": "flush"}`
**Delete all:** `{"action": "super_delete"}`

---

## Logs

| Type | Endpoint |
|------|----------|
| ACME / Let's Encrypt | `/get/logs/acme/{count}` |
| API | `/get/logs/api/{count}` |
| Autodiscover | `/get/logs/autodiscover/{count}` |
| Dovecot (IMAP) | `/get/logs/dovecot/{count}` |
| Netfilter | `/get/logs/netfilter/{count}` |
| Postfix (SMTP) | `/get/logs/postfix/{count}` |
| Rate limited | `/get/logs/ratelimited/{count}` |
| Rspamd history | `/get/logs/rspamd-history/{count}` |
| SOGo | `/get/logs/sogo/{count}` |
| Watchdog | `/get/logs/watchdog/{count}` |

All GET. `{count}` = number of entries, e.g. `100`

---

## Status

| Action | Endpoint |
|--------|----------|
| Container status | GET `/get/status/containers` |
| vmail disk usage | GET `/get/status/vmail` |
| Version | GET `/get/status/version` |

---

## Fail2Ban

| Action | Endpoint |
|--------|----------|
| Get config | GET `/get/fail2ban` |
| Edit config | POST `/edit/fail2ban` |

**Edit body:**
```json
{
  "attr": {
    "ban_time": "86400", "ban_time_increment": "1",
    "blacklist": "10.100.6.5/32,10.100.8.4/32",
    "max_attempts": "5", "max_ban_time": "86400",
    "netban_ipv4": "24", "netban_ipv6": "64",
    "retry_window": "600", "whitelist": "mailcow.tld"
  },
  "items": "none"
}
```

---

## Rate Limits

| Action | Endpoint |
|--------|----------|
| Get mailbox rl | GET `/get/rl-mbox/{mailbox}` (or `all`) |
| Get domain rl | GET `/get/rl-domain/{domain}` (or `all`) |
| Edit mailbox rl | POST `/edit/rl-mbox/` |
| Edit domain rl | POST `/edit/rl-domain/` |

**Edit body:** `{"attr": {"rl_value": "10", "rl_frame": "h"}, "items": ["info@domain.tld"]}`
`rl_frame`: `"s"` = per second, `"m"` = per minute, `"h"` = per hour

---

## Domain Admins

| Action | Endpoint |
|--------|----------|
| Get all | GET `/get/domain-admin/all` |
| Create | POST `/add/domain-admin` |
| Edit | POST `/edit/domain-admin` |
| Edit ACL | POST `/edit/da-acl` |
| Delete | POST `/delete/domain-admin` |
| Issue SSO token | POST `/add/sso/domain-admin` |

**Create:** `{"active": "1", "domains": "mailcow.tld", "password": "pw", "password2": "pw", "username": "testadmin"}`
**Delete:** `["testadmin"]`
**SSO token:** `{"username": "testadmin"}` -> returns `{"token": "..."}`, valid 30s

---

## App Passwords

| Action | Endpoint |
|--------|----------|
| Get for mailbox | GET `/get/app-passwd/all/{mailbox}` |
| Create | POST `/add/app-passwd` |
| Delete | POST `/delete/app-passwd` |

**Create body:**
```json
{
  "active": "1", "username": "info@domain.tld", "app_name": "myapp",
  "app_passwd": "password", "app_passwd2": "password",
  "protocols": ["imap_access", "dav_access", "smtp_access", "eas_access", "pop3_access", "sieve_access"]
}
```
**Delete:** `["1"]` (array of IDs)

---

## TLS Policy Maps

| Action | Endpoint |
|--------|----------|
| Get all / specific | GET `/get/tls-policy-map/all` or `/get/tls-policy-map/{id}` |
| Create | POST `/add/tls-policy-map` |
| Delete | POST `/delete/tls-policy-map` |

**Create:** `{"parameters": "", "active": "1", "dest": "mailcow.tld", "policy": "encrypt"}`
**Delete:** `["3"]`

---

## Routing

### Forwarding Hosts

| Action | Endpoint |
|--------|----------|
| Get all | GET `/get/fwdhost/all` |
| Add | POST `/add/fwdhost` |
| Delete | POST `/delete/fwdhost` |

**Add:** `{"filter_spam": "0", "hostname": "hosted.mailcow.de"}`
**Delete:** array of IPs (not hostnames!): `["5.1.76.202", "2a00:f820:417::202"]`

### Sender-Dependent Transports

| Action | Endpoint |
|--------|----------|
| Get all / specific | GET `/get/relayhost/all` or `/get/relayhost/{id}` |
| Create | POST `/add/relayhost` |
| Delete | POST `/delete/relayhost` |

**Create:** `{"hostname": "mailcow.tld:25", "password": "pw", "username": "testuser"}`

### Transport Maps

| Action | Endpoint |
|--------|----------|
| Get all / specific | GET `/get/transport/all` or `/get/transport/{id}` |
| Create | POST `/add/transport` |
| Delete | POST `/delete/transport` |

**Create:** `{"active": "1", "destination": "example.org", "nexthop": "host:25", "password": "pw", "username": "testuser"}`

---

## Address Rewriting

### BCC Maps

| Action | Endpoint |
|--------|----------|
| Get all / specific | GET `/get/bcc/all` or `/get/bcc/{id}` |
| Create | POST `/add/bcc` |
| Delete | POST `/delete/bcc` |

**Create:** `{"active": "1", "bcc_dest": "bcc@awesomecow.tld", "local_dest": "mailcow.tld", "type": "sender"}`
**Delete:** `["3"]`

### Recipient Maps

| Action | Endpoint |
|--------|----------|
| Get all / specific | GET `/get/recipient_map/all` or `/get/recipient_map/{id}` |
| Create | POST `/add/recipient_map` |
| Delete | POST `/delete/recipient_map` |

**Create:** `{"active": "1", "recipient_map_new": "target@mailcow.tld", "recipient_map_old": "recipient@mailcow.tld"}`

---

## Sync Jobs

| Action | Endpoint |
|--------|----------|
| Get all | GET `/get/syncjobs/all/no_log` |
| Create | POST `/add/syncjob` |
| Edit | POST `/edit/syncjob` |
| Delete | POST `/delete/syncjob` |

**Delete:** `["6", "9"]`

---

## Resources (Room/Equipment Booking)

| Action | Endpoint |
|--------|----------|
| Get all | GET `/get/resource/all` |
| Create | POST `/add/resource` |
| Delete | POST `/delete/resource` |

---

## OAuth2 Clients

| Action | Endpoint |
|--------|----------|
| Get all / specific | GET `/get/oauth2-client/all` or `/get/oauth2-client/{id}` |
| Create | POST `/add/oauth2-client` |
| Delete | POST `/delete/oauth2-client` |

**Create:** `{"redirect_uri": "https://mailcow.tld"}`

---

## CORS

**Edit:** POST `/edit/cors`
```json
{"attr": {"allowed_origins": ["*", "mail.mailcow.tld"], "allowed_methods": ["POST", "GET", "DELETE", "PUT"]}}
```

---

## Identity Provider (Keycloak/SSO)

**Edit:** POST `/edit/identity-provider`

---

## Key Patterns to Remember

- **DELETE endpoints:** always plain JSON array `["item"]`, never `{"items": [...]}`
- **EDIT endpoints:** always `{"attr": {...}, "items": [...]}` structure
- **Quota:** always in MB as string (`"3072"` = 3 GB)
- **Active:** `"1"` or `"0"` (strings, not booleans)
- **ID-based resources** (aliases, BCC, TLS policy maps): do a GET first to find numeric IDs
- **Forwarding hosts** delete by IP address, not hostname
- **DKIM delete** takes `[["domain.tld"]]` (array of arrays)
