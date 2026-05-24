# mongodb-compass-skills

Make MongoDB Compass actually usable against a **tunneled remote DB on Linux/Wayland**, packaged as an installable Claude Code skill.

## What it covers

One skill, `mongodb-compass`, with progressive-disclosure reference files:

- **connection-and-tunnels** — the `directConnection=true` gotcha (silent replica-set failure), favorite vs recent persistence, connection JSON location, SSH-tunnel troubleshooting (ECONNREFUSED, zombie tunnels, `nc` ≠ MongoDB responding).
- **password-persistence** — the Wayland safeStorage bug and the launcher-wrapper that re-injects passwords into the connection JSONs on every launch.

## Install

```
/plugin marketplace add milojarow/mongodb-compass-skills
/plugin install mongodb-compass-skills
```

## Active-skill marker

While `mongodb-compass` is engaged, replies are prefixed with 🧭.

## License

MIT
