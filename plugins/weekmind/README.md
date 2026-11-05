# Weekmind plugin for Claude

Plan your week with [Weekmind](https://weekmind.app) from Claude Code. The plugin contains no server code. It adds

- an HTTP connection to the hosted Weekmind MCP server (`https://weekmind.app/mcp`, OAuth login in the browser), and
- the `weekmind` skill, which teaches Claude how to read the schedule, add and change tasks and routines, check what-ifs, handle proposals and act on planned blocks.

## Install

```sh
/plugin marketplace add nx-solutions-ug/weekmind-claude-plugin
/plugin install weekmind@weekmind
```

Then run `/mcp`, choose `weekmind` and log in. Agent access needs a Weekmind Pro plan.

To use another Weekmind address (staging, local) set `WEEKMIND_URL`, e.g. `WEEKMIND_URL=http://localhost:3000`, before starting Claude Code.

Every change Claude makes reaches your plan as a proposal you accept in Weekmind, unless you allow the agent to apply changes under Einstellungen → Schnittstellen.

Privacy: <https://weekmind.app/en/privacy>
