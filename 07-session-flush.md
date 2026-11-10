# SESSION FLUSH — write memory before the context dies (template)

> Addendum to the agents protocol (added November 2026, after an external audit of our rules).
> Closes the gap with commercial auto-extraction memory products: they harvest facts from every
> dialogue automatically; in a file protocol the same guarantee is a writing discipline.

## The rule

1. **Flush in the SAME run.** If a task produced a new fact, event or lesson — append it to your
   personal memory file *before finishing the answer*. Context dies when the session ends:
   what is not written now is lost forever. Do not wait for a "big enough" task.
2. **Anti-duplicate.** Before writing, re-read the last 5 lines of your own memory file.
   If today's line already exists — extend it, do not start a new one.
3. **Routine runs stay out.** A scheduled run that produced nothing new writes nothing
   (collapse such runs into one weekly summary line — see the diary template).

## Why it matters (field note)

The old wording — "write down after large tasks" — left the decision to the agent, and agents
are optimistic about the size of their tasks. Small facts leaked first: a renamed file, a
changed port, a lesson from a failed run. The fix cost one sentence of protocol, zero code.

## Hook for scheduler-driven agents

Agents that wake on timers (cron/alarms) do not know "what day it is" — and do not need to:
the schedule lives in the planner, not in the agent. The agent's contract is simple:
wake → read CORE + own memory → do the task → flush → sleep.
