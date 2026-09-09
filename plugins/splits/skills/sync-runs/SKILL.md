---
name: sync-runs
description: Sync recent completed runs from a connected activity provider into Splits. Use when the athlete asks to sync or pull runs from COROS; do not use for a described run or a supplied JSON export.
---

# Sync connected runs

Use the connected COROS and Splits tools for an explicit on-demand sync. The request authorizes saving completed activities and ordinary plan matching only. It does not authorize changing the training prescription, switching activity sources, or reading unrelated health, sleep, recovery, or wellness data.

Resolve the athlete's local date and timezone. Unless they gave a range, use today plus the preceding six local calendar dates and state the exact range in the result.

1. Call Splits `get_sync_context` for `provider: "coros"` and the exact range. If either connector needs authentication, ask the athlete to connect it. If Splits reports a source conflict, stop: do not switch providers or relabel COROS data as manual.
2. Call COROS `querySportRecords` for numeric sport codes 100, 101, 102, and 103 only. Preserve every `labelId` as a string. If the result reaches its limit, split the date window and deduplicate ids; if one day is still capped with no pagination, report that discovery is incomplete.
3. Compare discovered ids with `get_sync_context`. Fetch FIT only for new runs or records whose summary/detail is incomplete. An explicit refresh may revisit an existing id.
4. For each needed run, call COROS `queryActivityFitFileDownloadUrls` with its `labelId` and `sportType`. Pass the returned URL immediately to Splits `import_activities` as `source: "coros"` and `files: [{ activityId, url, name? }]`. Never reconstruct distance, duration, heart rate, or laps from COROS prose. Batch at most 10 FIT references per import call.
5. Retry an expired or quota-failed URL once by requesting a fresh URL for the same id. Keep earlier successful batches and stop repeated failures.
6. Call `get_sync_context` again for the same range. Report imported, updated, already present, matched, unmatched, and failed results from the Splits receipt. Distinguish a saved activity from a matched workout and name any truncated discovery or remaining failure.

Do not use the binary FIT-resource tool when this client cannot receive it; the URL-reference path lets Splits perform bounded validation and deterministic decoding. Do not expose FIT URLs in the response.
