---
name: Run a Snappr editing job
description: Submit images to a Snappr editing preset and retrieve the edited results.
api: openapi/snappr-openapi.yml
operations: [listPresets, createEditingJob, getEditingJob, listEditingJobImages]
---

# Run a Snappr editing job

The Editing Jobs API is in beta — ask your account manager to enable it.

## Auth & environment
- `Authorization: Bearer <api_key>`; base URL `https://api.snappr.com`.

## Steps
1. **Choose a preset** — call `listPresets` and take a preset `uid` (`preset_id`).
2. **Create the job** — call `createEditingJob` with `type`, `preset_id`, and
   either an `images[]` array (each item needs `url_source`) or `images: null`
   to have Snappr collect source files from a third-party uploader (supply
   `uploader_email`).
3. **Poll status** — call `getEditingJob` with the returned `uid` until `status`
   is `completed` (progresses through `creating`/`pending_upload`/`paid`).
4. **Fetch results** — call `listEditingJobImages` with the `editing_job_uid`;
   each result exposes `source` and `final` image objects with `url_original`.

## Rules
- A 402 `InsufficientCredits` blocks job creation — top up credits in the GUI.
- `final` URLs are signed and expire; download promptly.
- No idempotency key — check `listEditingJobs` before recreating a job.
