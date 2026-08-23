# Game-Dev Skill

A [Claude Code skill](https://docs.claude.com/en/docs/claude-code/skills) for building 2D, browser-based games (HTML5/Canvas/WebGL via JS or TS). It drives a strict discovery-interview → system-by-system build-and-test loop, verifying every system in a real browser before moving on to the next.

## Installation

### Option A — plugin marketplace (recommended)

This repo is set up as a Claude Code plugin marketplace, so you don't need to clone or copy anything by hand. From inside any project, in a Claude Code session, run:

```
/plugin marketplace add Sudhanshu5669/Html5-Gamedev-Skill
/plugin install game-dev@html5-gamedev
```

Pick an install scope when prompted (user scope installs it for you across all projects). If the install summary says `Run /reload-plugins to activate.`, run:

```
/reload-plugins
```

### Option B — copy into a project

Copy `.claude/skills/game-dev/` into the `.claude/skills/` directory of the project where you want to build a game:

```bash
mkdir -p /path/to/your/project/.claude/skills
cp -r .claude/skills/game-dev /path/to/your/project/.claude/skills/
```

Claude Code auto-discovers skills under `.claude/skills/` — no further setup is required.

## Usage

From inside your project (with Claude Code running), just ask for a game and describe what you want:

```
make a platformer
build a top-down shooter
let's build a 2D word game
```

Claude will invoke the skill automatically based on the request. You can also trigger it explicitly with a prompt describing the game:

- **Plugin install (Option A):** skills from plugins are namespaced by plugin name, so run `/game-dev:game-dev`, then describe the game — e.g. `/game-dev:game-dev a stickman ragdoll platformer with physics-driven combat`.
- **Copied into a project (Option B):** run `/game-dev`, then describe the game the same way.

### What happens next

1. **Resume check** — if `GAME_SPEC.md` already exists in the project, this is treated as a resumed build; Claude reads `GAME_SPEC.md`/`SYSTEMS.md` and continues where it left off instead of re-running the interview.
2. **Discovery interview** — for a new project, Claude asks about genre, target platform, art approach, and scope, plus genre-specific follow-ups. Answers are written to `GAME_SPEC.md`, which becomes the source of truth for the build.
3. **Tech stack selection** — Claude picks the smallest stack that satisfies the spec (engine, physics, audio, bundler) from a curated library reference.
4. **Scaffold** — project is scaffolded, a persistent **dev level** (a sandbox scene for testing systems in isolation) is built, and `SYSTEMS.md` is written as the build checklist.
5. **Build loop** — systems are built **one at a time**, in order, each one tested in-browser and committed to git immediately after it passes. No system is marked done on code inspection alone.
6. **Assembly & playtest** — once every system passes individually, they're wired into real levels/content and playtested end-to-end, with findings logged in `PLAYTEST.md`.

### Generated files

Working through the skill produces these files in your project root:

| File | Purpose |
|---|---|
| `GAME_SPEC.md` | Genre, platform, art approach, scope, and core loop — the spec for the whole build |
| `SYSTEMS.md` | Build checklist: one row per system, with status and test notes |
| `PLAYTEST.md` | Findings from the full end-to-end playtest pass |

These are living documents — re-read them at the start of a later session rather than re-deriving context, and update them explicitly if scope changes.

## Repository layout

```
.claude-plugin/
├── marketplace.json                  # marketplace catalog — added via /plugin marketplace add
└── plugin.json                       # plugin manifest — the whole repo is the "game-dev" plugin

.claude/skills/game-dev/
├── SKILL.md                          # the skill definition (phases, rules, discipline)
└── references/
    ├── dev-level.md                  # how to build the persistent test sandbox
    ├── libraries.md                  # curated 2D web game library knowledge base
    ├── testing-protocol.md           # in-browser testing steps run after every system
    ├── game-spec.template.md         # template for GAME_SPEC.md
    └── systems.template.md           # template for SYSTEMS.md
```

`.claude/skills/game-dev/` is the single source of truth for the skill content — `plugin.json` points at it (`skills: ["./.claude/skills/"]`) for the marketplace install, and it's what you copy directly for Option B.

## Scope

This skill targets 2D, browser-based games only. For 3D, native (non-web) targets, or a specific engine (Unity/Unreal/Godot), it will say so and ask before proceeding with adapted guidance rather than forcing the request into the web mold.
