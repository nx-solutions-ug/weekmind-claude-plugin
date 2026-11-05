# Weekmind tools

Read tools (safe, no side effects): `get_schedule`, `list_tasks`, `list_habits`, `get_settings`, `get_proposal`, `list_sources`, `search_events`, `get_event`, `simulate`.

Write tools: `create_task`, `update_task`, `delete_task`, `create_habit`, `update_habit`, `delete_habit`, `update_settings`, `replan`. For agents not allowed to apply changes, their effect on the plan becomes a proposal.

Plan actions (only for agents allowed to apply): `block_action`, `decide_proposal`.

| Tool              | Use it to                                                                                                                     |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `get_schedule`    | Read days: events (fixed), planned blocks with reason codes, working hours, free minutes, what the last run could not fit     |
| `list_tasks`      | Tasks with ids, duration, priority, deadline, source, `autoSchedule`, and how much is planned in the next two weeks           |
| `create_task`     | Add a task for the planner to schedule                                                                                        |
| `update_task`     | Change priority, duration, deadline (null removes), earliest start, block sizes, notes, `autoSchedule`, `hoursType`, `status` |
| `delete_task`     | Permanent delete; prefer `status` done/archived                                                                               |
| `list_habits`     | Routines (recurring habits, protected focus time) with weekdays and windows                                                   |
| `create_habit`    | Place a routine once per given weekday inside a window                                                                        |
| `update_habit`    | Change a routine; `active: false` pauses it                                                                                   |
| `delete_habit`    | Delete a routine                                                                                                              |
| `get_settings`    | Working hours, personal time, max daily load, buffer around meetings, focus time, one-piece planning, block-length cap        |
| `update_settings` | Change those; not for one-off absences                                                                                        |
| `replan`          | Run the planner now; returns what could not be fitted                                                                         |
| `simulate`        | What-if on the real planner, saves nothing                                                                                    |
| `get_proposal`    | The open proposal and its block changes                                                                                       |
| `decide_proposal` | Accept or reject the open proposal                                                                                            |
| `block_action`    | start, complete, snooze, extend, skip, move, unschedule, missed on a planned block                                            |
| `list_sources`    | Connected calendars and accounts                                                                                              |
| `search_events`   | Search events across all connected calendars                                                                                  |
| `get_event`       | Details of one event                                                                                                          |

Tasks imported from Jira, GitHub, Todoist or Google Tasks wait under "Nicht eingeplant" until the user picks them (`autoSchedule`). Reading Jira and GitHub items themselves is not available over MCP; that stays in the Weekmind app.

Task and routine `hoursType` is `work` or `private`: work blocks go to the work calendar, private ones to the private calendar, and private tasks use the user's personal time.

## REST fallback

For scripts and agents without OAuth, a personal token (Weekmind → Einstellungen → Schnittstellen) works as `Authorization: Bearer pln_…` against `<base>/api/v1`. The OpenAPI description is at `<base>/api/v1/openapi.json`.
