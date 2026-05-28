# Public regression checklist

This is a public, non-production checklist extracted from the project experience.

## Before closing a routing/suppress patch

- addressed question should be answered
- direct reply follow-up should not be dropped
- low-value reply should not trigger text spam
- naked trigger should not wake the bot unnecessarily
- recent context must not override target message
- cooldown must delay or suppress intentionally, not corrupt task ownership

## Before closing a side-effect patch

- no accidental Telegram send in dry-run
- no accidental file send
- no accidental image generation
- no marker/debug text in public history
- no DB/history leak
- fresh logs have no ERROR or Traceback

## Main lesson

py_compile proves syntax, not live behavior.

For group-chat LLM bots, behavior must be checked through scenario matrices.
