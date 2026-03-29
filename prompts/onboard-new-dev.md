<!--
  PROMPT: Generate CLAUDE.md and Copilot instructions for an existing project
  USE WHEN: Onboarding Claude Code or Copilot to an existing Android project
            so they apply the right patterns without repeated reminders.
  HOW TO USE:
    1. Open Claude Code in your Android project
    2. Paste this prompt
    3. Claude reads the project and generates config files
-->

# Generate AI assistant configuration for this project

## Goal

Read the existing codebase and generate two files:
1. `CLAUDE.md` — tells Claude Code which skills apply to this project
2. `.github/copilot-instructions.md` — same rules for GitHub Copilot

## What to read first

Before generating the files, read:
- `build.gradle.kts` (root and app module) to identify libraries in use
- Module structure (`settings.gradle.kts`) to understand module boundaries
- One ViewModel, one Repository, one Composable screen to infer current patterns
- Any existing `CLAUDE.md` or `.github/copilot-instructions.md`

## CLAUDE.md output format

```markdown
# CLAUDE.md

## Project type
[Android only | Android + KMP | Android + KMP + iOS]

## Always apply these skills
[list only skills that match libraries already in the project]

## Module rules
- Feature modules must not import other feature modules
- [any project-specific rules observed in the codebase]

## Naming conventions
- [inferred from existing files]

## Do not
- [anti-patterns already present that Claude should avoid making worse]
```

## .github/copilot-instructions.md output format

```markdown
# Copilot instructions

Follow these patterns for all code in this project:

## Architecture
[MVI / MVVM / other — inferred from codebase]

## Key patterns
[2–4 bullet points from the dominant skill patterns found]

## Libraries in use
[actual libraries found in build files]

## Do not suggest
[anti-patterns to avoid based on ANTI_PATTERNS.md]
```

## Rules for generation

- Only list skills that match libraries already present in `libs.versions.toml` or `build.gradle.kts`
- Do not invent patterns not observed in the code
- If the codebase mixes patterns (e.g. some MVI, some MVVM), note the inconsistency and recommend the dominant or intended pattern
- Keep both files under 60 lines — brevity means Claude applies them reliably
