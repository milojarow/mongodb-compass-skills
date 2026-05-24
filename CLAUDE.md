# CLAUDE.md

This file provides guidance to Claude Code when working with this repository.

## Project Overview

This is the **mongodb-compass-skills** repository — how to make MongoDB Compass work against a tunneled remote database on Linux/Wayland, packaged as a Claude Code skill.

**Repository**: https://github.com/milojarow/mongodb-compass-skills

## Repository Structure

```
mongodb-compass-skills/
├── .claude-plugin/          # Claude Code plugin configuration
├── CLAUDE.md                # This file
├── README.md                # Project overview
├── LICENSE                  # MIT License
├── evaluations/             # Test scenarios for the skill
│   └── mongodb-compass/
└── skills/
    └── mongodb-compass/
        ├── SKILL.md         # Entry point: the two killer gotchas + pointers
        └── reference/       # Depth: connection-and-tunnels, password-persistence
```

## The skill

### mongodb-compass
Connecting MongoDB Compass to a remote DB over an SSH tunnel on Linux/Wayland: the `directConnection=true` gotcha (silent replica-set discovery failure), `savedConnectionType: "favorite"` persistence, the launcher-wrapper password re-injection pattern for Wayland (where Electron safeStorage/gnome-keyring is broken), and SSH-tunnel troubleshooting.

## Skill Activation

Activates when connecting Compass to a tunneled remote MongoDB, when Compass loses passwords on Wayland, or when a tunneled connection fails silently / times out / disappears.

## Updating this skill

After any session that discovers a new Compass/tunnel wall. Keep entries generic — patterns and examples, never real hosts, credentials, or operator-specific paths. The git log of this repo is the diary.
