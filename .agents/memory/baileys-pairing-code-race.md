---
name: Baileys pairing code race
description: WhatsApp bots using @whiskeysockets/baileys can silently fail to produce a pairing code due to a startup race condition.
---

Calling `sock.requestPairingCode(phoneNumber)` immediately after
`makeWASocket({...})` can throw `Connection Closed` (Boom, statusCode 428)
because the underlying WebSocket handshake hasn't completed yet.

**Why:** `requestPairingCode` sends a raw WS message right away; if the socket
isn't open yet, the send fails. In bots that wrap the whole connect flow in an
unhandled/fire-and-forget async function AND filter common connection error
strings (e.g. suppressing "Connection Closed", "Timed Out" from
console.error/stderr for noise reduction), this failure becomes completely
silent — the process just sits there with no pairing code and no visible
error.

**How to apply:** If a Baileys-based WhatsApp bot's pairing-code flow produces
no code and no visible error, add a short delay (~3s) between socket creation
and the `requestPairingCode` call, or wait for a `connection.update` event
with `connection === 'connecting'` first. Don't assume "no error" means
success — check for error-suppression logic (custom `unhandledRejection`
handlers, overridden `console.error`/`process.stderr.write`) that may be
hiding the real cause.
