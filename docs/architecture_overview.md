# Architecture overview

High-level public architecture:

Telegram chat -> update handler -> routing -> suppress -> context builder -> LLM call -> response policy -> Telegram side effects

Core design areas:

- routing: decide whether the bot should react to a message at all
- suppress: prevent noisy or low-value answers
- context: separate the current target message from recent background context
- memory: keep continuity without leaking irrelevant state
- reactions: use lightweight reactions instead of text spam
- image/vision: treat image handling as a side-effect boundary
- service layer: run as a managed service with backups and checks
- regression discipline: test behavior, not only syntax

This file intentionally omits implementation details, production prompts, and configuration values.
