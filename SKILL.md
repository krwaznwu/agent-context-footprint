---
name: agent-context-footprint
description: "Use when an AI agent sends unrelated memory, project details, or instructions with each LLM request. Helps identify persistent context, separate global preferences from project-specific facts, move information to the narrowest useful scope, and verify that fresh sessions receive only relevant context without disabling useful tools."
version: 1.0.0
author: Community
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [context, memory, prompts, isolation, projects]
---

# Agent Context Footprint

## Purpose

Long-lived AI agents can accumulate a large persistent prefix: global memory, user preferences, skill descriptions, project instructions, tool schemas, and conversation history. Some of it may be sent to the model before the current request.

The main problem is not size alone. It is **scope mismatch**: information about one project is loaded while the agent is working on another.

This skill helps an agent:

1. identify what context is persistent;
2. classify information by its proper scope;
3. move project-specific facts out of global memory safely;
4. verify that unrelated sessions stay clean;
5. preserve useful capabilities and rollback paths.

## When to Use

Use this skill when:

- new sessions contain unrelated project details;
- global memory is becoming a project archive;
- different projects influence each other's answers;
- the user wants to understand what is sent with each LLM request;
- an agent needs clearer boundaries between global, project, session, and task context.

Do not use it to:

- delete useful information merely to make a prompt smaller;
- claim exact token cost from byte counts;
- disable interactive tools by default;
- publish raw memory, profiles, transcripts, or local paths;
- promise automatic isolation that the host agent does not support.

## Context Layers

Separate these layers before changing anything:

| Layer | Appropriate content |
|---|---|
| Global user profile | Stable preferences useful across nearly all work |
| Global memory | Cross-project facts and universal operating rules |
| Project context | One project's purpose, constraints, commands, and canonical files |
| Project status | Current checkpoint, next step, and changing state |
| Skill | A reusable procedure that applies to many projects |
| Session history | The current discussion and tool results |
| Current task | The immediate request and supplied material |
| Tool schemas | Descriptions of tools available to the model |

Conversation branches or separate sessions can isolate history, but they may still share global memory and profile data. Project files isolate project facts only when the host agent actually discovers and injects them.

## Workflow

### 1. Inspect before editing

Determine which persistent layers the agent sends to the model. Use built-in diagnostics when available.

For Hermes:

```bash
hermes prompt-size
```

Record categories and sizes, but do not copy private contents into public reports.

**Done when:** the persistent layers are identified without changing configuration or memory.

### 2. Classify every candidate

For each global entry under review, choose one destination:

- useful almost everywhere → keep globally;
- useful only in one project → project context;
- changing progress → project status or task tracker;
- reusable method → skill;
- recoverable from a canonical source → keep a pointer or remove after verification;
- stale or contradicted → remove after verification;
- secret or credential → secret storage, never prompt context.

**Done when:** every proposed removal has a destination or a clear deletion reason.

### 3. Create narrow project context

Use the context filename supported by the host agent. In Hermes this is commonly a project-root `HERMES.md`; verify against the installed version rather than assuming.

Keep it compact:

```markdown
# Example Project

## Scope
- What belongs to this project.
- What explicitly does not belong here.

## Canonical sources
- `README.md`
- `STATUS.md`

## Operating rules
- Project-specific constraints and verification commands.

## Handoff
- Read `STATUS.md` for changing progress.
```

Keep changing state in `STATUS.md` or a tracker, not in permanently injected instructions.

**Done when:** the project file contains only project-scoped information and points to canonical sources.

### 4. Back up before removal

Before changing global memory or profile data:

- save the original files;
- record their location and hashes;
- write exact restore instructions;
- do not include secrets or private content in a public migration report.

**Done when:** rollback can be performed without reconstructing text manually.

### 5. Verify the destination

Start or measure a session from the project directory and confirm that its project context is present. Then test a neutral directory or unrelated project and confirm that the same context is absent.

For Hermes, compare `hermes prompt-size` from the intended working directories and inspect the project-context category.

**Done when:** relevant context appears only in the intended scope.

### 6. Remove migrated global entries

Only after destination verification:

- remove or replace migrated entries;
- scan global memory for leftover project names and paths;
- keep the backup and migration record;
- use a fresh session for the final check because existing sessions may retain earlier context.

**Done when:** migrated facts are absent globally, still available in their project, and rollback remains possible.

### 7. Report honestly

Report separately:

- measured prompt bytes or characters;
- memory/profile reduction;
- project context added where needed;
- unchanged skill and tool-schema overhead;
- uncertainty from tokenization and provider caching.

Do not convert local byte reduction directly into a billing or quota claim.

## Tool Availability

Tool descriptions can occupy significant context, but available tools are not the same as invoked tools. Interactive work often needs an unexpected browser, terminal, GUI, or verification step.

Default policy:

- preserve the full tool surface for general interactive sessions;
- narrow tools only for deterministic, tested automation or explicit user requests;
- prefer script-only execution when no LLM reasoning is needed;
- treat tool schemas as a separate measurement category, not automatic waste.

## Safety Rules

- Never expose raw memory, profiles, transcripts, credentials, usernames, private project names, or personal absolute paths.
- Never delete global information before its destination is created and verified.
- Never assume a project file is injected merely because it exists.
- Never claim that this skill intercepts or rewrites LLM requests automatically.
- Never equate bytes, characters, tokens, cached tokens, and billed tokens.

## Completion Checklist

- [ ] Persistent context layers identified
- [ ] Global entries classified by scope
- [ ] Project destination created before removal
- [ ] Backup, hashes, and restore instructions recorded
- [ ] Project context verified in the intended directory
- [ ] Neutral or unrelated context verified clean
- [ ] Migrated markers removed from global memory
- [ ] Final check performed in a fresh session
- [ ] Report avoids private contents and unsupported cost claims
- [ ] Interactive tools remain available unless explicitly restricted
