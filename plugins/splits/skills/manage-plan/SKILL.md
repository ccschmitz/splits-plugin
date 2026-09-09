---
name: manage-plan
description: Save or adjust a Splits running plan, move or edit workouts, add or remove races, and restore plan versions. Use when the athlete requests changes to their Splits plan.
---

# Manage the plan

Call `get_plan` before changing a saved plan. Read its Source, Structure, races, dates, workout IDs, and completion marks. Use the live tool schemas as the wire contract. The current replacement proof is `replaces` with the Structure value; older `replacesRevision` and `update_days` instructions are obsolete.

The athlete's explicit request to make a specific change authorizes that change. Do not add a second approval gate to a fully specified request. For a change you propose while reviewing training, explain the affected dates and before/after sessions and obtain acceptance before saving. Resolve missing choices that materially affect the result.

## Choose the smallest suitable write

- Use `edit_plan` for changes within the existing span on either engine or externally authored plans. It accepts ordered structured operations, validates the batch, and saves nothing if an operation fails.
- Use `add_race` or `remove_race` for races on an engine plan. Call with `preview: true` first and inspect the diff. Adding or removing a race can rebuild weeks and replace hand edits inside the affected span. Explain material effects beyond the athlete's request before committing them. A-races own builds and tapers; B-races are tune-ups. A second A-race belongs on the same timeline. Removal addresses the exact race date; the only A-race cannot be removed.
- Use `push_plan` to create or intentionally replace a complete externally authored plan, including changing its races. Read [complete-plan.md](references/complete-plan.md) before using it.
- Use `list_revisions` to find a requested historical version, then `revert_plan` with its `toRevision`. For "undo the last change," `revert_plan` without an argument skips revisions that only changed completion marks. Synced completions reapply; reverting training does not erase logged runs. A different plan identity must be restored through the Splits app.

## Preserve sessions and completion

For a double, use `update_workout` or `remove_workout` with the `workoutId` returned by `get_plan`. `set_day` replaces every session on that date; use it only when that is intended. Never put an `id` inside a supplied workout object: Splits creates new IDs. Clear a date with `remove_day`, not an empty `set_day`.

`move_day` moves all sessions and their completion marks. If its destination is occupied, it swaps the dates. Check both dates before using it; if the athlete requested a one-way move rather than a swap, resolve that distinction. `set_day` retains the date's completion status, and `update_workout` retains the session's status. Do not imply that editing a done workout makes it uncompleted.

Build workouts using Splits' existing tree of steps and nested repeats, with distance in meters, time in seconds, and pace in seconds per kilometer. Retain warmups, cooldowns, and recoveries that the athlete did not ask to remove. A recovery inside a repeat runs on the last repetition too; account for that when translating a workout. Tool descriptions supply the schema and supported targets.

## Verify the outcome

Relay the server-computed diff and advisory warnings, then read `get_plan` to verify the requested result. Warnings can accompany a successful save; do not report them as a rejected write. For a validation failure, fix the indicated field without broadening the change. For a stale Structure conflict, reread and reconcile rather than blindly replacing the token. After an uncertain write response, inspect current state before retrying, especially for additions and moves.
