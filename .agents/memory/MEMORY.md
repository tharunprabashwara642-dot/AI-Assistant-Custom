# Memory Index

- [npm built-in shadow packages](npm-builtin-shadow-packages.md) — legacy Node projects sometimes list `fs`/`crypto`/`os`/etc as npm deps; these get blocked by the registry security policy and must be removed, not installed.
- [Baileys pairing code race](baileys-pairing-code-race.md) — calling `sock.requestPairingCode()` immediately after `makeWASocket()` can throw "Connection Closed"; add a short delay first.
