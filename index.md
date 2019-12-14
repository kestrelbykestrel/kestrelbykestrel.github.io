# 14 Dec 2019

Thinking today about how to go about rebuilding the state of a system that not only rebuilds state, but also executes side effects.

One option is to have a strict rebuild phase that rebuilds state exactly; then have a 'run' phase that resumes processing new events and actually runs side effects.

Another strategy is to reify the side effects as events that are sent to another topic; and another process can consumer these 'action' events, and dedup them, within a defined grace period (e.g. 1 day or 1 hour).

Initially, the first stategy (call it Stategy A), seemed conceptually cleaner. It seems nice to be able to have a phase that rebuilds state exactly. This allows swapping out state-loading implementations - initially, you might rebuild upon every deploy, but when that gets too slow, you can switch to loading from disk.

But - if we need a deduplication strategy anyway, then Strategy B begins to looks more enticing. We don't have exactly-swappable state-loading implementations - but Strategy B tolerate duplicates by definition, so we might adopt a policy like "start processing events with timestamps within the past hour". (This assumes that our dedup grace period is 1h+.)
