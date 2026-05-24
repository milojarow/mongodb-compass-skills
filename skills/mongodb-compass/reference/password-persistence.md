# Password persistence on Wayland

## The problem

Compass relies on Electron's **safeStorage** (backed by gnome-keyring) to store connection passwords. On **Wayland this is broken** — Compass loses saved passwords every time it exits, and worse, it **overwrites** the connection JSON's stored secret on exit (clearing it, because safeStorage failed). You end up re-typing credentials on every launch, and a one-time manual JSON edit doesn't survive.

## The fix: a launcher wrapper that re-injects on every start

Wrap the real Compass binary in a launcher script that, on each start, writes the passwords back into the connection JSONs *just before* execing the real binary — so the launcher wins the race against Compass clearing them:

1. Store passwords as **environment variables** (e.g. in a secrets file sourced into the session), never in the repo or dotfiles.
2. The launcher holds a `USERS` map: each MongoDB user → the env var name holding its password.
3. On launch, for each user, the script reads the env var and writes the password into the matching `~/.config/MongoDB Compass/Connections/<uuid>.json`.
4. The launcher then `exec`s the real binary (e.g. `/usr/sbin/mongodb-compass`).
5. Put the launcher's directory **earlier in `PATH`** than the real binary, so typing `mongodb-compass` runs the wrapper, not the original.

## URL-encoding

Passwords are injected into a `mongodb://USER:PASS@...` URI, so special characters must be **URL-encoded** (`+` → `%2B`, etc.). Do the encoding **inside the launcher** so the stored env var stays the raw password.

## Why re-inject every launch (not edit once)

Because Compass clears the secret on exit when safeStorage fails, a one-time edit is gone by the next start. Re-injecting on every launch is the only thing that makes credentials stick — you're not fixing the keyring, you're routing around it.
