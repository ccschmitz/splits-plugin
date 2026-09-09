# Saving a complete plan

Use `push_plan` only for a complete timeline the athlete intends to save. For a new training plan, establish race dates/distances/roles, relevant goals, recent training baseline, available running days, and schedule constraints. Ask only for missing information that changes the plan. If only a week's workouts are supplied and no plan exists, draft the week but do not present it as a complete race plan.

Send all races using `races[]`, with an explicit A/B role on each race, and every training day through the final race. Rest days are omitted; doubles share one date with multiple workouts. Include a race-category workout on each race date. The current tool supports 5k, 10k, half, marathon, and road 50k, up to eight races, 200 training days, and four workouts per day. Do not portray 50k as a trail-ultra generator. Consult the live schema for changes to these limits.

For an existing plan, pass `replaces` equal to the current `Structure:` value from `get_plan`. A push replaces the complete plan; a one-week push would discard other training. Preserve all existing races and untouched training in an intentional full replacement.

Replacing a previously pushed plan retains its identity and revision history. Pushing over an engine-generated plan creates a new identity with history starting at revision 1. The engine plan remains available in the app, but `revert_plan` cannot cross to it. Prefer structured `edit_plan` operations when the request is a local change; explain the identity/history consequence before an intentional full replacement if it was not already understood.

The server validates workouts and reports advisory load/taper warnings; it does not regenerate an externally authored plan. Report its saved link, actual diff, and relevant warnings. Do not claim to have used the deterministic Splits generator: no `create_plan` MCP tool is currently exposed.
