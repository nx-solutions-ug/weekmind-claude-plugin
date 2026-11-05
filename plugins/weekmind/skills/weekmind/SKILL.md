---
name: weekmind
description: Plan and manage the user's week with Weekmind, an AI planner that schedules tasks and routines around fixed calendar appointments. Use when the user asks to plan or replan their week or day, add or change tasks and deadlines, set up routines or focus time, check what fits before a deadline, find calendar events, handle a Weekmind proposal, or start, extend, snooze, move or finish a planned block.
---

# Weekmind

Weekmind is a hosted planner. It puts the user's **tasks** (with duration, priority, deadline) and **routines** (lunch, sport, protected focus time) into their calendars as **blocks**, around **fixed calendar events** such as meetings and doctor appointments. Events are never moved; the planner always plans around them. All tools come from the `weekmind` MCP server of this plugin. Times are in the user's own time zone (`2026-11-06T14:00`; a bare date means end of that day).

If the tools are missing or asking to authenticate, tell the user to run `/mcp`, pick `weekmind` and log in. A Weekmind Pro plan is needed for agent access; see [references/setup.md](references/setup.md).

## Always read before you write

1. `get_schedule` for the days in question. It returns events, planned blocks (with reason codes), working hours, free minutes, and what the last run could not fit.
2. `list_tasks` / `list_habits` for ids and current values.
3. `get_settings` when the question is about working hours, daily load, buffers or block sizes.

Never guess ids or recall them from earlier turns when they could be stale; block ids in particular come from `get_schedule`.

## Core workflows

**Add a task.** `create_task` with title and a realistic duration. If the user gave no estimate, estimate one and say so in your answer. Add a deadline when there is one. Do not set block-size limits unless asked. Then `replan` and `get_schedule` to show where it landed.

**"Does X still fit?"** Use `simulate` (a what-if on the real planner, saves nothing) before changing anything. Report the outcome and what moves; only then offer to make the change for real.

**Change priorities, deadlines or durations.** `update_task`. To finish a task use `status: "done"`; to drop it use `archived`; to reopen use `open`. Prefer these over `delete_task`, which is permanent and only for an explicit "delete".

**Routines and focus time.** `create_habit` with weekdays and a time window; the planner places it once per weekday somewhere inside the window, flexibly around meetings. To pause, `update_habit` with `active: false` instead of deleting.

**Absences and one-offs.** A free afternoon or a doctor's appointment is a calendar event, not a working-hours change. Tell the user to add it to their calendar; Weekmind picks it up and plans around it. Only touch working hours (`update_settings`) for lasting changes.

**Find an event.** `list_sources` shows the connected calendars and accounts; `search_events` searches descriptions, locations and attendees; `get_event` gives the details.

**Replan.** `replan` runs the planner now. It reports what could not be fitted. Call `get_schedule` afterwards to see the new blocks, and explain misses plainly (deadline too tight, not enough free time) with options: extend a deadline, shorten a task, drop a lower-priority one.

## Proposals: the user stays in control

Unless the user allowed this agent to apply changes, the effect of your changes on the plan becomes a **proposal** the user accepts in the Weekmind app. In that case `replan` reports status `"proposed"`.

- Say so clearly: "I changed the task; the new plan is waiting in Weekmind for you to accept." Never claim the calendar was changed when the status is `proposed`.
- `get_proposal` shows what the proposal would change (before/after, reason codes).
- `decide_proposal` (accept or reject) works only for agents allowed to apply. Never accept or reject on your own initiative; do it only when the user clearly asked. Pass the `proposalId` you showed them, so a newer proposal is not decided by accident. If the result is `stale`, the calendar changed meanwhile and the planner ran again; tell the user.

## Acting on blocks

`block_action` works on a block id from `get_schedule` (and only for agents allowed to apply; otherwise it answers with an error, in which case tell the user to do it in the app):

| action       | effect                                                                           |
| ------------ | -------------------------------------------------------------------------------- |
| `start`      | A future block moves to now; a running one is pinned                             |
| `complete`   | A running block ends now; `taskDone` also finishes the task                      |
| `snooze`     | The block goes; the task gets no time for `minutes` (default 60)                 |
| `extend`     | `minutes` more (default 15, max 240); the task's estimate grows too              |
| `skip`       | Routines only: no block on that day                                              |
| `move`       | New `start`; the block keeps its length and becomes fixed there                  |
| `unschedule` | Tasks only: all unstarted blocks go; the task waits under "Nicht eingeplant"     |
| `missed`     | A finished block that was never started didn't happen; the task is planned again |

Each action replans the rest right away. `snooze`, `skip`, `missed` and `unschedule` remove the block, so repeating one answers "not found".

## Style

- Lead with the result ("Report is planned Thursday 10:00-11:30, before your deadline"), then details. Use the user's language (the app supports German and English).
- Show times as the user would say them, with weekday and date for anything beyond today.
- Be honest about what did and did not happen: planned, proposed, or not fitted.
- Settings such as working hours or daily load change every future plan. Confirm with the user before changing them unless they asked explicitly.
- Do not read out calendar event descriptions or attendee lists that the user did not ask about; they can be private.

More on every tool and the REST fallback: [references/tools.md](references/tools.md).
