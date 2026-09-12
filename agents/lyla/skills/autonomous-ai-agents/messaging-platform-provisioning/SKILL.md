---
name: messaging-platform-provisioning
description: "Use when provisioning messaging bots. Verify official paths."
version: 0.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [messaging, bots, provisioning, discord, security]
    related_skills: []
---

# Messaging-Platform Provisioning Skill

Provision messaging integrations by separating platform-account creation from Hermes configuration. Prefer supported APIs and documented setup flows; never disguise an undocumented web endpoint as a stable tool.

## When to Use

- Creating or connecting a Discord, Telegram, Slack, or similar bot.
- Deciding whether a requested bot-creation workflow can be implemented as an API tool.
- Automating the post-creation configuration of an existing bot.

## Procedure

1. **Check the platform's official API surface first.** Confirm whether an authenticated API exposes resource creation, credential rotation, permission/intents configuration, and installation/invite operations. Treat the developer portal as a separate control plane when the API only manages an existing application.
2. **Separate provisioning phases.** Mark each step as application creation, bot credential issuance, platform permissions/intents, server installation, or Hermes gateway configuration. Automate only phases supported by documented APIs and local Hermes commands.
3. **Use a supported interactive path for account-bound steps.** If creation or credential issuance requires the owner's web session, login, 2FA, CAPTCHA, or consent, stop at that boundary and require the owner to complete it; do not use private endpoints, stolen user tokens, or session cookies to bypass it.
4. **Keep secrets local.** Have the owner place bot tokens in the profile's `.env` or use the platform's setup wizard. Never request, print, or type tokens into chat, logs, screenshots, or generated source files.
5. **Configure Hermes only after credentials exist.** Use profile-scoped commands such as `hermes -p <profile> gateway setup`, then validate with `hermes -p <profile> config check`, gateway status, and a real message round trip.
6. **Report the boundary precisely.** State which phases were automated, which require owner action, and the exact verified next command; do not claim a bot was created when only a Hermes profile was prepared.

## Pitfalls

- **Do not infer an application-creation endpoint from application-management endpoints.** Discord's public API exposes operations for an existing application, not a documented `POST /applications` creation route; portal creation remains an owner-authenticated step.
- **Do not automate secrets through browser control.** A browser can navigate setup pages, but revealing or copying a bot token into an agent context defeats the secret boundary and grants full bot control to anyone who obtains the transcript.
- **Do not clone a configured profile for a new bot unless credential reuse is intentional.** Cloning environment files can silently attach the new agent to the old bot; create an isolated profile and configure its credentials separately.
- **Do not treat an online bot as a working integration.** Verify intents, authorization, gateway connectivity, allowed users/channels, and an actual reply because login success alone does not prove message handling.

## Verification

- Confirm the official API specification or documentation for every automated platform-side operation.
- Confirm profile isolation and that no token appears in command output or tracked files.
- Run the profile configuration check and gateway status.
- Send a test DM or mention and verify the bot replies.

## References

- Discord-specific creation and Hermes setup notes: `references/discord-provisioning.md`.
