---
name: npm built-in shadow packages
description: Old/legacy Node.js repos sometimes declare Node built-in module names (fs, crypto, os, path, child_process, readline, stream, util, vm) as npm dependencies in package.json.
---

Some abandoned or malicious npm packages squat on the exact names of Node.js
built-in modules (e.g. `fs@0.0.1-security`, `crypto@1.0.1`). Registries/security
policies (including Replit's package firewall) block installing these — `npm
install` fails with 403/malware errors.

**Why:** These packages provide nothing; the code just does `require('fs')`
etc., which resolves to the real Node built-in whether or not the npm package
is installed. Removing them from `package.json` is a no-op for runtime
behavior.

**How to apply:** When `npm install` fails on a legacy project with a 403 or
"blocked by Security Policy" / "Known malware" error, check whether the
blocked package name matches a Node built-in module. If so, delete that
dependency line from `package.json` (don't try to find an alternate version)
and reinstall. Also double check for genuinely-missing dependencies actually
`require()`'d in the code but absent from package.json (e.g. `file-type` was
used via `require('file-type')` but never declared) — add those.
