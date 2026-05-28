# Runtime layers

Public high-level breakdown of the Johnny bot runtime.

## routing
Decides whether the bot should process the message at all.

Signals:
- direct reply to the bot
- explicit address or mention
- message type
- whether the message is a new task or background context

## suppress
Prevents the bot from being too active in a group chat.

Typical suppress cases:
- low-value acknowledgement
- naked trigger without useful task
- excessive bot share in recent chat history
- reply-chain loops

## context
Separates the current target message from recent background context.

Main rule:
- TARGET_MESSAGE is the task
- RECENT_CONTEXT is only supporting context

## reaction
Uses lightweight reactions instead of full text answers for low-value replies.

## side-effect
Any public action must be treated as a production boundary:
- Telegram message send
- file send
- reaction
- image generation
- DB write

## service
The bot is treated as a service, not as a console script:
- managed process
- restart discipline
- logs
- backups
- regression checks
