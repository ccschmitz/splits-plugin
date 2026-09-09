---
name: review-training
description: Review a runner's saved Splits training, explain upcoming workouts, and discuss progress toward their races. Use for Splits coaching check-ins and weekly reviews.
---

# Review training

Use the connected Splits MCP tools. Call `get_plan` to ground the conversation in the athlete's current races, scheduled workouts, completion marks, weekly mileage, and warnings. If the connection needs authentication, direct the athlete to connect Splits in Codex. Never invent account data.

Orient the discussion around the next A-race and its build, taper, or recovery; B-races are tune-ups within a block. Several A-races can belong to one timeline. Resolve relative dates using the athlete's local date and timezone.

For a check-in, explain what is scheduled, what is marked done or skipped, and the most useful implication for the upcoming training. Ask about effort, fatigue, or schedule constraints when that missing context changes the advice. Match the athlete's preferred units and distinguish measured distance from estimates for time-based sessions.

Completion marks show whether training was recorded, not how well a run went. The current MCP tools do not expose a detailed activity-history, lap, sleep, recovery, or athlete-profile reader. Do not infer heart rate, pace, readiness, or injury status from completion marks. Use details the athlete supplies and state gaps when they matter.

If there is no saved plan, explain that and help the athlete choose between creating one in the Splits app or supplying a complete plan to save. Do not turn an empty account into an invented training history.

An assessment or recommendation alone does not authorize a plan edit. When the athlete wants a change, use the plugin's `manage-plan` workflow; save an authorized change and report the actual returned diff. A request to record a completed run belongs to `log-runs`.

Do not promise automatic workout delivery to a watch. The available tools save the Splits plan and import completed runs; they do not expose a structured-workout push to COROS or Garmin.
