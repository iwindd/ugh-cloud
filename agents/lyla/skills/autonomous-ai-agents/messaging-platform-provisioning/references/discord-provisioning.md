# Discord Provisioning Reference

## Supported boundary

The official Discord API supports reading and updating an existing application (`/applications/@me`) and managing resources attached to an application. It does not document a public application-creation route. Create the Application and Bot in the Discord Developer Portal using the owner's Discord account.

## Owner steps

1. Create an Application with the desired name.
2. Add a Bot user.
3. Enable the intents required by the gateway integration, especially Message Content Intent and Server Members Intent when the integration needs message text and user authorization checks.
4. Generate an installation URL with the minimum required scopes and permissions.
5. Store the Bot Token locally; if it is lost, reset it in the portal rather than searching logs or chat history.

## Hermes steps

Use the target profile, not the active profile implicitly:

```text
hermes -p <profile> gateway setup
hermes -p <profile> config check
hermes -p <profile> gateway run
```

Use a profile-local `.env` for `DISCORD_BOT_TOKEN` and authorization settings such as `DISCORD_ALLOWED_USERS`. Do not copy the token from another profile unless sharing that bot is deliberate.

## Tool-design rule

A reusable tool may validate an existing token, write it to the correct local secret store, configure allowed users/channels, generate an invite URL from a known application ID, start the gateway, and perform a round-trip health check. It must not depend on undocumented Developer Portal endpoints or Discord user tokens to create applications.
