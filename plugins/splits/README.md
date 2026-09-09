# Splits for Codex

Connect your Splits account to bring your running plan into the conversation.

## Included

- **Review training:** discuss the saved plan, completion marks, upcoming races, and the week ahead.
- **Manage plan:** edit sessions, move workouts, manage races, save full plans, and undo changes.
- **Log runs:** record a described run or import Garmin, COROS, and Strava JSON exports.
- **Sync runs:** fetch recent COROS runs through the athlete's connected COROS MCP and save them to Splits with deterministic FIT decoding.

The plugin connects to `https://splits.coach/api/mcp` using the service's OAuth flow. No API keys, personal training data, or account credentials are bundled. It uses Splits' existing branding.

## Install and try it

Add the `ccschmitz/splits-plugin` marketplace in Codex, install **Splits**, and complete the Splits connection flow if prompted. Start a new task so Codex picks up the plugin's skills and tools. See the [repository installation guide](https://github.com/ccschmitz/splits-plugin#install) for commands.

Try: "Review my Splits training and the week ahead." Then try a specific schedule change or ask to log a completed run.

## Tools and boundaries

The server exposes `get_plan`, `get_sync_context`, `edit_plan`, `push_plan`, `add_race`, `remove_race`, `list_revisions`, `revert_plan`, and `import_activities`.

Plan edits are real account writes. Specific athlete instructions can be applied directly; recommendations need acceptance. Race changes support preview. Full replacement requires the saved plan's Structure fingerprint. Detailed activity-history/recovery reads, deterministic plan generation, and direct structured-workout delivery to a watch are not exposed by these tools.

## Local development

The maintained source is `plugins/splits` in the [splits-plugin repository](https://github.com/ccschmitz/splits-plugin). The repository's marketplace is `.agents/plugins/marketplace.json` (`splits-plugins`).

After edits, validate with the plugin-creator skill's `scripts/validate_plugin.py`. For a registered local plugin update, use that skill's `scripts/read_marketplace_name.py` with the actual marketplace path and `scripts/update_plugin_cachebuster.py`, then reinstall from the validated marketplace. Existing personal installations may still use `personal`; repository installations use `splits-plugins`. Start a new task after reinstalling.
