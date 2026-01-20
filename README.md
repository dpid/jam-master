# Jam Master

Virtual game jam competition system where multiple AI agent teams build games in parallel from a shared brief.

## Overview

Jam Master orchestrates AI-powered game jams. You provide a theme and constraints, and multiple AI teams compete to build games simultaneously. A judge agent evaluates the submissions and declares a winner.

## Requirements

- [Claude Code](https://github.com/anthropics/claude-code) CLI
- GitHub CLI (`gh`)
- Node.js

## Setup

1. Clone this repo
2. Copy `.claude/local/config.md.example` to `.claude/local/config.md`
3. Edit `config.md` with your local paths:
   - `JAM_MASTER_DIR` - Path to this project
   - `GAME_REPOS_DIR` - Parent directory where game repos will be created
4. Set up the autonomous runner config (see below)

### Claude-Yolo Config

Team processes run autonomously using a separate Claude config at `~/.claude-yolo`:

```bash
mkdir -p ~/.claude-yolo
ln -sf ~/.claude/.credentials.json ~/.claude-yolo/.credentials.json
```

Create `~/.claude-yolo/settings.json`:
```json
{
  "permissions": {
    "allow": ["Read", "Edit", "Write", "Bash(*)", "WebSearch", "WebFetch", "mcp__*"],
    "deny": []
  }
}
```

## Usage

Run a game jam:

```
claude
> /game-jam-manager
```

You'll be asked about:
1. Theme
2. Required features
3. Technical requirements (packages, tech stack)
4. Bonus challenges
5. Constraints
6. Number of teams
7. Visibility (public/private/local)

After approving the brief, teams spawn and work autonomously. Results appear when all teams finish.

### Options

```
/game-jam-manager --teams 2              # Run with 2 teams
/game-jam-manager --brief path/to.md     # Use existing brief
/game-jam-manager --public               # Create public GitHub repos
/game-jam-manager --private              # Create private GitHub repos
/game-jam-manager --local                # Local only, no GitHub
```

## How It Works

```
┌─────────────────┐
│  Game Jam       │
│  Manager        │
└────────┬────────┘
         │ spawns
    ┌────┴────┬────────┐
    ▼         ▼        ▼
┌───────┐ ┌───────┐ ┌───────┐
│ Team  │ │ Team  │ │ Team  │
│ Alpha │ │ Bravo │ │Charlie│
└───┬───┘ └───┬───┘ └───┬───┘
    │         │        │
    └────┬────┴────────┘
         ▼
    ┌─────────┐
    │  Judge  │
    └─────────┘
```

Each team follows this workflow:

1. **Designer** - Creates Game Design Document from brief
2. **Architect** - Plans implementation
3. **Plan Reviewer** - Reviews and requests changes (up to 3 iterations)
4. **Senior Developer** - Implements the game
5. **Code Reviewer** - Reviews code (up to 3 iterations)
6. **Release Engineer** - Commits and pushes (if not local)

## Agents

| Agent | Role | Model |
|-------|------|-------|
| designer | Create GDD from brief | Sonnet |
| architect | Design implementation plan | Opus |
| plan-reviewer | Review implementation plan | Sonnet |
| senior-developer | Implement the game | Sonnet |
| code-reviewer | Review game code | Sonnet |
| release-engineer | Commit and push | Haiku |
| judge | Evaluate submissions | Opus |

## Project Structure

```
.claude/
├── agents/           # Agent definitions
├── skills/           # Orchestration skills
│   ├── game-jam-manager/
│   └── team-project-manager/
├── context/          # Project conventions
├── templates/        # Brief and GDD templates
├── local/            # Machine-specific config (gitignored)
└── jams/             # Per-jam data (gitignored)
```

## License

MIT
