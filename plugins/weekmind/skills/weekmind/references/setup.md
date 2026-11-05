# Setup and troubleshooting

## Connect

The plugin registers the `weekmind` MCP server at `https://weekmind.app/mcp`. The first time a tool is used, Claude opens the browser; the user logs in to Weekmind and confirms. The agent then shows up under "Verbundene Agenten" in Weekmind and can be disconnected there.

Other Weekmind address (staging, local): set `WEEKMIND_URL` before starting Claude Code, e.g. `WEEKMIND_URL=http://localhost:3000` (no trailing `/mcp`).

## Letting the agent apply changes

By default every change an agent makes reaches the plan as a **proposal** the user accepts in the app's week view. To let this agent apply changes directly (needed for `block_action` and `decide_proposal`), the user ticks "Direkt übernehmen" for the agent under Einstellungen → Schnittstellen.

## Problems

- **Tools not listed / "needs authentication":** run `/mcp`, select `weekmind`, authenticate.
- **"Pro plan required":** agent access is part of Weekmind Pro.
- **Block action refused:** the agent is not allowed to apply changes (see above).
- **Plan did not change after `replan`:** status `proposed`; the user has to accept it in the app.
