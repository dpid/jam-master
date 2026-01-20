# Game Jam Conventions

This document describes the conventions and patterns used in the virtual game jam system.

## Directory Structure

```
.claude/
├── agents/           # Agent definitions
│   ├── designer.md   # GDD creation
│   ├── judge.md      # Submission evaluation
│   ├── architect.md  # Implementation planning
│   ├── plan-reviewer.md
│   ├── senior-developer.md
│   ├── code-reviewer.md
│   └── release-engineer.md
├── skills/
│   ├── game-jam-manager/    # Main orchestrator
│   │   └── SKILL.md
│   └── team-project-manager/ # Per-team orchestrator
│       └── SKILL.md
├── context/
│   └── game-jam-conventions.md  # This file
├── templates/
│   ├── brief-template.md    # Jam brief format
│   └── gdd-template.md      # Game design doc format
└── jams/
    └── <jam-id>/            # Per-jam data
        ├── brief.md         # Jam requirements
        ├── run-log.md       # Orchestrator log
        ├── teams/
        │   └── <team-name>/
        │       ├── pid.txt      # Process ID
        │       ├── status.txt   # Current phase
        │       ├── log.md       # Team activity log
        │       ├── gdd.md       # Copy of game design
        │       ├── implementation-plan.md
        │       ├── implementation-plan-review.md
        │       ├── code-review.md
        │       └── submission.md
        └── judging/
            ├── submissions.md   # All team submissions
            ├── scores.md        # Detailed scoring
            └── results.md       # Final rankings
```

## Game Repositories

Each team creates a separate game repository:
- Location: `$GAME_REPOS_DIR/<jam-name>-<team>/` (see `.claude/local/config.md` for path)
- Naming: `<jam-name>-alpha`, `<jam-name>-bravo`, etc. (jam name from brief's `## Name` section)
- Created fresh for each jam (delete old ones first if reusing team names)

## Team Names

Standard team names in order: alpha, bravo, charlie, delta, echo, foxtrot

## Status Values

Teams report status in `status.txt`:
- `pending` - Not started
- `initializing` - Reading brief, creating repo
- `designing` - Creating GDD
- `architecting` - Planning implementation
- `implementing` - Writing game code
- `reviewing` - Code review
- `submitting` - Final commit/push
- `completed` - Successfully finished
- `failed` - Error occurred

## Jam Workflow

1. **Manager creates brief** - Interactive or from file
2. **Manager spawns teams** - Parallel claude-yolo processes
3. **Each team runs independently:**
   - Read brief → Designer creates GDD
   - Architect + Plan Reviewer loop
   - Senior Dev + Code Reviewer loop
   - Release Engineer commits
4. **Manager monitors** - Polls status files
5. **Manager collects submissions** - From submission.md files
6. **Judge evaluates** - Clones repos, plays games, scores
7. **Manager reports results** - Final rankings

## Agent Communication

- Agents communicate via files in the team directory
- No direct inter-agent communication
- Project Manager (skill) orchestrates agent spawning
- Each agent reads inputs, writes outputs, reports completion

## Tech Stack Decisions

Teams choose based on jam requirements:
- **Vanilla Canvas + TypeScript** - Simple 2D, full control
- **Phaser 3** - Physics, complex 2D, tilemaps
- **Three.js** - 3D games
- **p5.js** - Rapid prototyping, creative coding

## Judging Criteria

Default weights (can be customized per jam):
| Criterion | Weight |
|-----------|--------|
| Theme Adherence | 20% |
| Gameplay | 25% |
| Polish | 20% |
| Creativity | 20% |
| Technical Merit | 15% |

Scores: 1-10 per criterion, weighted sum = final score (max 50)
Bonuses add to final score.

## File Formats

### brief.md
Must contain:
- `## Theme` - Creative theme
- `## Required Features` - Must-have features
- `## Judging Criteria` - Table with weights

### submission.md
Contains:
- Game title
- Repository URL
- Feature list
- Tech stack
- Notes for judges

## Claude-Yolo Environment

Team processes run with:
```bash
CLAUDE_CONFIG_DIR=~/.claude-yolo claude --dangerously-skip-permissions
```

This provides:
- Full file system access
- Unrestricted bash execution
- No permission prompts
- Isolated from main Claude config

## Time Considerations

- Jam timeout: 2 hours (configurable)
- Monitor interval: 30 seconds
- Max architecture iterations: 3
- Max implementation iterations: 3
