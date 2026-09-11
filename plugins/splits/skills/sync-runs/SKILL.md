---
name: sync-runs
description: Sync recent completed runs from a supported connected activity provider into Splits. Use when the athlete asks to sync or pull runs from a connected account; do not use for a described run or a supplied JSON export.
---

# Sync connected runs

Use the connected provider and Splits tools for an explicit on-demand sync. The request authorizes saving completed activities and ordinary plan matching only. It does not authorize changing the training prescription, switching activity sources, or reading unrelated health, sleep, recovery, or wellness data.

Resolve the athlete's local date and timezone. Unless they gave a range, use today plus the preceding six local calendar dates and state the exact range in the result.

COROS is the only supported connected-provider adapter in v1. If the athlete names another provider, say connected sync is not supported for it yet and stop; do not improvise an adapter, relabel its data as manual, or switch to another provider. If no provider is named, use COROS only when its connector is available; otherwise ask the athlete to connect it.

1. Resolve the provider, exact date range, and required connectors.
2. Call Splits `get_sync_context` for that provider and range. If a connector needs authentication, ask the athlete to connect it. A `providerRole` of `supplemental` means the provider can fill gaps by default. When `approvalRequired` is true, keep the returned `secondarySourceApproval` object for this request; it is not approval by itself. No Account setting or source change is needed. If an older server reports a source conflict or does not expose the approval contract for a supplemental source, stop and explain that the server needs the import-approval update; do not switch sources or bypass the conflict.
3. Use the provider adapter below to discover stable activity ids. If discovery reaches a limit, split the date window and deduplicate ids; if one day is still capped without pagination, report that discovery is incomplete.
4. Compare discovered ids with `get_sync_context`. If every discovered run is already complete and the athlete has not requested an explicit refresh, report that without requesting approval or fetching FIT files. Otherwise, when approval is required, explain the concrete import: name the provider, exact dates and discovered run count, name the primary that will stay unchanged, and state that equivalent primary-source runs will be skipped while distinct runs can be added. Ask for approval before fetching short-lived FIT URLs or calling `import_activities`. For example: “Strava is your primary source. Import the COROS run from September 9 to fill a gap, skipping it if Strava already has the same run?” Wait for the athlete's answer. A generic request to sync is not this additional approval; reuse explicit approval already given for this same scope rather than asking again. A decline means nothing is imported. After approval, pass the exact `secondarySourceApproval` object on each batch and retry of this requested import only. If the primary, provider, or dates change, get fresh context and approval. Fetch authoritative source data only for new activities or records whose summary/detail is incomplete; an explicit refresh may revisit an existing id. Import in the adapter's bounded format without reconstructing metrics from display prose.
5. Retry an expired or quota-failed source reference once for the same id. Keep earlier successful batches and stop repeated failures.
6. Call `get_sync_context` again for the same provider and range. Report imported, updated, already present, duplicate-skipped, matched, unmatched, and failed results from the Splits receipt. When this provider is supplemental, state that the named primary source stayed unchanged. Distinguish a saved activity from a matched workout and name any truncated discovery or remaining failure.

## Provider adapters

### COROS

- Discover runs with `querySportRecords` using numeric sport codes 100, 101, 102, and 103 only. Preserve every `labelId` as a string.
- For each needed run, call `queryActivityFitFileDownloadUrls` with its `labelId` and `sportType`.
- Pass the returned URL immediately to Splits `import_activities` as `source: "coros"` and `files: [{ activityId, url, name? }]`. Include `secondarySourceApproval` for an approved secondary import. Batch at most 10 FIT references per call.
- Never reconstruct distance, duration, heart rate, or laps from COROS prose. Request a fresh FIT URL for the one allowed retry.

Do not use the binary FIT-resource tool when this client cannot receive it; the URL-reference path lets Splits perform bounded validation and deterministic decoding. Do not expose FIT URLs in the response.
