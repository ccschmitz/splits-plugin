# Splits plugin

Bring your [Splits](https://splits.coach) running plan into Codex: review upcoming training, adjust workouts and races, and record completed runs.

This repository contains the plugin package and its marketplace. Splits hosts the application and authenticated MCP server at `https://splits.coach/api/mcp`. Each runner connects their own Splits account; no credentials or personal training data are included here.

## Install

Add the marketplace:

```sh
codex plugin marketplace add ccschmitz/splits-plugin
```

Open the Plugins directory in Codex, select the **Splits Plugins** marketplace, and install **Splits**. Complete the Splits OAuth connection when prompted, then start a new task.

Try:

- “Review my Splits training and the week ahead.”
- “Move my long run to Sunday in Splits.”
- “Log a completed run in Splits.”

For local development, add this checkout instead:

```sh
codex plugin marketplace add /absolute/path/to/splits-plugin
```

Use one marketplace source at a time for development and release testing. An existing personal installation can continue to use its original marketplace.

## Included in v0.1.0

| Skill | Purpose |
| --- | --- |
| `review-training` | Review the saved plan, upcoming races, and completion marks. |
| `manage-plan` | Edit sessions, manage races, save full plans, and restore revisions. |
| `log-runs` | Record described runs or import supported vendor JSON exports. |

The connected server exposes `get_plan`, `edit_plan`, `push_plan`, `add_race`, `remove_race`, `list_revisions`, `revert_plan`, and `import_activities`. Live tool schemas are authoritative.

Plan edits and activity imports write to the connected runner's account. Specific runner requests authorize the requested actions; proposed coaching changes require acceptance. Full replacement has additional safeguards described by the tools.

**Not yet included:** automatic retrieval of recent runs from COROS or other connected services. The sync workflow is planned separately. The plugin does not currently send structured workouts to watches.

## Repository layout

```text
.agents/plugins/marketplace.json   Marketplace definition
plugins/splits/
  .codex-plugin/plugin.json        Package identity and listing
  .mcp.json                       Public Splits MCP endpoint
  assets/                         Existing Splits branding
  skills/                         Runner workflows
```

Edit the files under `plugins/splits`, not an installed Codex cache. Backend changes belong in the Splits application repository.

## Validate and release

With the Codex plugin-creator and skill-creator skills installed, run their validators (Python with PyYAML is required):

```sh
python3 "$HOME/.codex/skills/.system/plugin-creator/scripts/validate_plugin.py" plugins/splits
python3 "$HOME/.codex/skills/.system/skill-creator/scripts/quick_validate.py" plugins/splits/skills/review-training
python3 "$HOME/.codex/skills/.system/skill-creator/scripts/quick_validate.py" plugins/splits/skills/manage-plan
python3 "$HOME/.codex/skills/.system/skill-creator/scripts/quick_validate.py" plugins/splits/skills/log-runs
```

Validate the package and exercise the installed workflows with a test account before releasing changes. Keep normal read-only checks separate from live mutations. Record actual outcomes, including authentication, edit/undo behavior, and imports where changed.

For a release, update the package's semantic version, commit the tested files, and create a matching Git tag. Refresh a Git-backed marketplace with:

```sh
codex plugin marketplace upgrade splits-plugins
```

Then update or reinstall Splits in Codex and start a new task. During local iteration, follow the plugin-creator skill's cachebuster/reinstall procedure rather than editing cached files.

Publishing this Git repository makes the package available through its marketplace. It does not publish a reviewed listing in the universal ChatGPT/Codex Plugins Directory. That requires a separate [plugin submission](https://developers.openai.com/plugins/deploy/submission).

## Feedback

Report plugin bugs in [GitHub issues](https://github.com/ccschmitz/splits-plugin/issues). Include the requested workflow, expected behavior, and a redacted error. Do not include authentication codes, tokens, signed download URLs, or private activity data.
