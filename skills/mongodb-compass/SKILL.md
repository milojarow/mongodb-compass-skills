---
name: mongodb-compass
description: Use when connecting MongoDB Compass to a remote database through an SSH tunnel, especially on Linux/Wayland — when Compass loses saved passwords on exit, fails silently with replica-set/hostname errors, drops "recent" connections, or a tunneled connection times out or refuses. Covers the directConnection gotcha, favorite-connection persistence, the launcher-wrapper password re-injection pattern for Wayland (where safeStorage/gnome-keyring is broken), and SSH-tunnel troubleshooting. Not for writing MongoDB queries/aggregations or server/driver administration.
---

# mongodb-compass

Make MongoDB Compass actually usable against a tunneled remote DB on Linux/Wayland — where it fails silently to connect and forgets passwords on exit.

> **🧭 ACTIVE-SKILL MARKER:** Prefija tu reply con 🧭 **solo en turnos donde el trabajo toca el dominio de `mongodb-compass`** — Compass + SSH tunnels en Wayland — editar Connection JSONs, wiring del launcher wrapper, troubleshooting safeStorage. La **capa/proyecto da igual** (frontend, backend, n8n, script local — todos valen): lo que importa es si *este turno* toca el dominio. En turnos que NO lo tocan (typecheck, build, deploy, git ops, edición o curl de otros dominios), **omite 🧭** aunque la skill se haya cargado antes en la sesión. Si otras skills activas también aplican al mismo turno, **apila sus emojis** en el prefijo.

## Overview

Two non-obvious failures dominate Compass-over-SSH-tunnel on Linux:

- **It fails silently without `directConnection=true`.** Without it, Compass does replica-set discovery, resolves internal hostnames that don't exist on your machine, and just fails — no clear error. Always set it. See [reference/connection-and-tunnels.md](reference/connection-and-tunnels.md).
- **It forgets passwords on Wayland.** safeStorage via gnome-keyring is broken on Wayland, so Compass loses saved passwords on exit. The fix is a launcher wrapper that re-injects passwords into the connection JSONs on every launch. See [reference/password-persistence.md](reference/password-persistence.md).

## When to use

- Connecting Compass to a remote MongoDB through an SSH tunnel.
- Compass loses passwords every time you close it (Wayland).
- A connection fails silently, times out, or hangs.
- A saved connection disappeared (a "recent" connection got cleaned up).

**Not for:** writing MongoDB queries / aggregations, or server / driver administration — this is about the Compass GUI + tunnel plumbing.

## Where things live

| Task | Reference |
|---|---|
| directConnection gotcha, connection string, favorite vs recent, SSH-tunnel troubleshooting | [reference/connection-and-tunnels.md](reference/connection-and-tunnels.md) |
| Wayland password loss → launcher re-injection pattern, URL-encoding, PATH ordering | [reference/password-persistence.md](reference/password-persistence.md) |

## Quick reference

- **Connection string:** `mongodb://USER:PASS@localhost:PORT/DB?authSource=admin&directConnection=true`
- **Always** `directConnection=true` — skips replica-set discovery that resolves nonexistent internal hosts.
- **Set `savedConnectionType: "favorite"`** in the connection JSON — Compass periodically purges `"recent"` ones.
- **Connections live at** `~/.config/MongoDB Compass/Connections/<uuid>.json`.
- **Verify end-to-end:** `nc -zv 127.0.0.1 PORT` only tests TCP; confirm MongoDB actually answers via the wire protocol or Compass itself.

## Common mistakes

- Connecting without `directConnection=true` → silent failure resolving internal hostnames.
- Trusting `nc` success as "it works" → `nc` only proves the tunnel's TCP is open, not that MongoDB responds.
- Saving a connection as "recent" and finding it gone later → set it to `favorite`.
- Re-typing passwords every launch on Wayland → that's the safeStorage bug; use the launcher-reinjection pattern, don't fight the keyring.
