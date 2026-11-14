# Agent Context Footprint

> Keep AI-agent context relevant by separating global memory, project facts, session history, and reusable skills.

## TL;DR

A practical skill for identifying persistent AI-agent context, separating global memory from project-specific facts, and verifying clean project boundaries without disabling useful tools.

In one real-world Hermes cleanup, this method reduced the global memory and user-profile payload by **64.5%** and the measured neutral system prompt by **7.6%**. The result is less unrelated context repeatedly sent to the model and a larger share of the context budget available for the current task. This is an observed example, not a universal guarantee: actual token and billing savings depend on the starting configuration, tokenizer, provider-side prefix caching, and which prompt layers can be changed.

Long-lived agents may send more than the visible conversation to an LLM: global memory, user preferences, project instructions, skill descriptions, tool schemas, and other persistent context. Over time, facts from unrelated projects can begin appearing in fresh sessions.

This skill gives an agent a safe procedure to:

- identify persistent context;
- separate global preferences from project-specific facts;
- move information to the narrowest useful scope;
- verify project isolation with a neutral comparison;
- preserve backups, rollback, and useful interactive tools;
- avoid publishing private memory or local paths.

It is a procedure, not a background service. It does not automatically intercept or rewrite LLM requests. Its purpose is to help agents and users organize context correctly using the facilities their agent framework already provides.

## Install

After the repository is published, install `SKILL.md` using the skill-installation method supported by your agent, or copy it into the agent's local skills directory.

For Hermes, consult the current Hermes documentation for the supported `hermes skills install` syntax.

## Hermes quick check

Hermes can report the main prompt categories:

```bash
hermes prompt-size
```

Compare a neutral working directory with a project directory. The project context should appear only where it belongs.

## Privacy

Public reports should contain totals and category names only. Do not publish:

- raw memory or user profiles;
- session transcripts;
- credentials or tokens;
- local usernames and absolute paths;
- private project or customer names.

## Repository contents

- `SKILL.md` — the complete agent procedure
- `README.md` — overview and privacy notes
- `LICENSE` — MIT license

## License

MIT
