# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This repo is a single Claude Code skill (`codex`) that delegates coding tasks to the Codex CLI. It consists of a SKILL.md and two shell wrapper scripts (bash + PowerShell) that handle output capture, session tracking, and progress streaming. The repo is also a Myco substrate managed by `_canon.yaml`.

## Skill invocation

The skill triggers when the user explicitly asks to use Codex (e.g., "用 codex 做", "让 codex 执行"). Never use it proactively — Claude handles routine coding directly; Codex is only for user-requested delegation.

## Common commands

Run the Codex wrapper to delegate a task:

```powershell
& ~/.claude/skills/codex/scripts/ask_codex.ps1 "task description" -f path/to/file.tsx
```

```bash
~/.claude/skills/codex/scripts/ask_codex.sh "task description" --file path/to/file.tsx
```

Resume a previous session:
```bash
~/.claude/skills/codex/scripts/ask_codex.sh "follow-up task" --session <session_id>
```

## Architecture

- **SKILL.md** — The authoritative skill definition loaded by Claude Code. It contains the script invocation instructions, options reference, and failure-handling guide. Any behavioral changes to the skill go here.
- **scripts/ask_codex.sh** — Bash wrapper for Linux/macOS. Handles PTY allocation (script(1) with fallback), JSON stream parsing, progress display, and multi-turn session resume.
- **scripts/ask_codex.ps1** — PowerShell wrapper for Windows. Mirrors the bash script's functionality using `System.Diagnostics.Process` for async I/O, with cmd.exe execution for .cmd/.ps1 wrappers.
- **_canon.yaml** — Myco substrate contract (schema v1, contract v0.6.13). Defines write surface, lint exit policy, and subsystem declarations. Never edit manually without a Myco workflow.
- **MYCO.md** — Agent-entry page for Myco. Read-only for humans; the boot-brief injector patches it.

## Script behavior

Both wrapper scripts:
1. Run `codex exec` (or `codex exec resume` for follow-up) with the user's task as stdin
2. Capture the JSON stream (new sessions) or plain text (resume mode) in real time
3. Extract agent messages, command executions, and file operations into a markdown output file
4. Print `session_id=<id>` and `output_path=<path>` on success
5. Write output to `.runtime/` (gitignored) by default

Key options: `--read-only` (analysis only), `--reasoning high|medium|low`, `--session <id>` (multi-turn), `--file` (entry-point hints, repeatable).