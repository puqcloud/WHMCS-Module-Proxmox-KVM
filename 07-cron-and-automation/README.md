# Cron and Automation

How the module's cron-driven state machines work: the deploy pipeline that walks a new VM from creation to ready, the change package pipeline for upgrades and downgrades, the asynchronous terminate pipeline introduced in v3.2, and all other scheduled tasks (snapshot cleanup, backup schedule, stats collection) with their intervals and locking behaviour. Every long-running lifecycle operation runs through cron with live per-step output, so clients never wait on synchronous HTTP requests and admins see real-time progress in the logs.
