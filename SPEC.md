# BeaconWick — Project-Specific Rule Overrides

**SPEC.md > AI.md > global CLAUDE.md. Rules here override AI.md where they conflict.**

---

## Notification Providers (extends AI.md PART 18)

AI.md PART 18 defines email/SMTP only. BeaconWick extends this with the following
additional notification provider categories and implementations.

### Email providers
- SMTP (direct — AI.md PART 18 default)
- SendGrid
- Brevo
- Resend

### SMS and voice
- Twilio (SMS + voice call)
- Telnyx (SMS)
- 46elks, AliyunSms, Cellsynt, ClickSendSMS, EgoSMS, FreeMobile, GtxMessaging,
  Octopush, PromoSMS, SevenIO, SerwerSMS, SMSC, SMSEagle, SMSIr, SMSManager,
  SMSPartner, SMSPlanet, Teltonika

### Team chat
- Slack
- Discord
- Microsoft Teams
- Mattermost
- Google Chat
- Rocket.Chat
- Pumble
- Stackfield
- Zoho Cliq
- Kook
- Bitrix24
- Nextcloud Talk
- YZJ (Yunzhijia)

### Messaging apps
- Telegram
- WhatsApp (via Whapi, WAHA, Evolution, 360messenger, Onesender, CallMeBot)
- Signal
- Line
- Matrix
- Nostr
- DingTalk (DingDing)
- Feishu / Lark
- WeCom (WeChat Work)
- VK
- VKTeams
- Bale
- Threema
- OneChat
- OneBot (QQ)
- Fluxer

### Mobile push
- Pushover
- Pushbullet
- PushDeer
- PushPlus
- Pushy
- Bark (iOS)
- Gorush
- Ntfy (self-hosted)
- Gotify (self-hosted)
- LunaSea
- TechulusPush
- Wpush
- SpugPush
- ServerChan
- Max (Line Max)

### Browser push
- Web Push (browser native, no app required)

### On-call and incident management
- PagerDuty
- OpsGenie
- Splunk On-Call (VictorOps)
- Squadcast
- PagerTree
- FlashDuty
- GoAlert
- SIGNL4
- HeiiOnCall
- AlertNow
- Alerta
- Grafana OnCall

### Ticketing and ITSM
- Jira Service Management
- HaloPSA
- Zendesk
- xMatters
- Zapier

### Webhooks and automation
- Generic HTTP POST webhook (Uptime Kuma outgoing schema compatible)
- Google Sheets
- Keep
- Notifery
- Home Assistant

### Meta-provider
- Apprise — provides access to 78+ additional notification services through a
  single integration; use as a catch-all for any provider not listed above

---

## Registration Mode Default (overrides AI.md PART 34)

AI.md defaults `registration.mode` to `open`. BeaconWick defaults to `admin_only`.
Recommended mode per deployment profile:

| Deployment | Recommended mode | Reason |
|---|---|---|
| Self-hosted / SMB | `admin_only` (default) | Admin controls every account |
| Enterprise | `invite` | Controlled onboarding; audit trail of who invited whom |
| PaaS | `open` | Self-serve signup for customers |



- Each provider is implemented as a standalone notification driver
- Drivers are registered at startup; missing credentials disable that driver only
- All drivers must respect the same alert lifecycle: DOWN, UP, certificate expiry,
  maintenance start, maintenance end
- Webhook payload format must match the Uptime Kuma outgoing schema exactly for
  migration compatibility (see IDEA.md compatibility requirements)
