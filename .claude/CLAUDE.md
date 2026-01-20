# Needs-Based AI Game Jam

Virtual game jam competition system where multiple AI agent teams build games in parallel from a shared brief.

## Project Structure

```
.claude/
├── agents/           # Specialized agent definitions
├── skills/           # Orchestration skills
├── context/          # Project conventions
├── templates/        # Brief and GDD templates
└── jams/             # Per-jam data and logs
```

## Available Skills

### `/game-jam-manager`
Orchestrate a full game jam competition with multiple AI teams.

**Usage:**
```
/game-jam-manager                           # 3 teams, interactive brief
/game-jam-manager --teams 2                 # 2 teams
/game-jam-manager --brief path/to/brief.md  # Use existing brief
```

**What it does:**
1. Creates or uses a jam brief
2. Spawns N teams as parallel claude-yolo processes
3. Monitors team progress via status files
4. Collects submissions when all teams complete
5. Spawns Judge agent to evaluate and rank
6. Reports final results with repo links

### `/team-project-manager`
Manage a single team through the development workflow. Used internally by game-jam-manager.

**Usage:**
```
/team-project-manager --jam .claude/jams/<id> --team alpha
```

**What it does:**
1. Reads brief and creates game repository
2. Spawns Designer to create GDD
3. Architect + Plan Reviewer loop (max 3 iterations)
4. Senior Dev + Code Reviewer loop (max 3 iterations)
5. Release Engineer commits and pushes
6. Writes submission.md

## Agents

| Agent | Role | Model |
|-------|------|-------|
| designer | Create GDD from brief | Sonnet |
| architect | Design implementation plan | Sonnet |
| plan-reviewer | Review implementation plan | Sonnet |
| senior-developer | Implement the game | Sonnet |
| code-reviewer | Review game code | Sonnet |
| release-engineer | Commit and push | Haiku |
| judge | Evaluate all submissions | Opus |

## Quick Start

1. **Run a game jam:**
   ```
   /game-jam-manager --teams 3
   ```

2. **Follow the prompts to create a brief** (or provide one)

3. **Wait for teams to complete** (monitor shows progress)

4. **Review results** in `.claude/jams/<id>/judging/results.md`

## Game Repositories

Teams create repos at: `$GAME_REPOS_DIR/<jam-name>-<team>/` (see `.claude/local/config.md` for paths)

## Configuration

### Local Paths Setup

Copy `.claude/local/config.md.example` to `.claude/local/config.md` and set your paths:
- **JAM_MASTER_DIR** - Absolute path to this project
- **GAME_REPOS_DIR** - Parent directory where game repos are created

This file is gitignored to keep machine-specific paths out of the repo.

### Claude-Yolo Setup

The game jam manager spawns team processes using a separate Claude config at `~/.claude-yolo`. This is pre-configured with:

```
~/.claude-yolo/
├── .credentials.json    # Symlink to ~/.claude/.credentials.json
└── settings.json        # Permissive settings for autonomous operation
```

**Important:** Symlink credentials to share OAuth tokens:
```bash
ln -sf ~/.claude/.credentials.json ~/.claude-yolo/.credentials.json
```

**settings.json contents:**
```json
{
  "permissions": {
    "allow": ["Read", "Edit", "Write", "Bash(*)", "WebSearch", "WebFetch", "mcp__*"],
    "deny": []
  }
}
```

Team processes are spawned with:
```bash
cd $JAM_MASTER_DIR && \
env -u ANTHROPIC_API_KEY CLAUDE_CONFIG_DIR=~/.claude-yolo claude --dangerously-skip-permissions \
  -p "/team-project-manager --jam <jam-dir> --team <name>"
```

**Important:** The `env -u ANTHROPIC_API_KEY` is required if you have an API key set in your environment. Without it, Claude Code will use API credits instead of your Max subscription OAuth credentials.

The `--dangerously-skip-permissions` flag combined with the permissive settings allows fully autonomous operation without user prompts.

## Read First

Before contributing or modifying:
- `.claude/context/game-jam-conventions.md` - Full system conventions
- `.claude/templates/` - Brief and GDD formats
