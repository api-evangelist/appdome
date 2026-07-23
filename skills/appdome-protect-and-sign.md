---
name: Protect, sign, and download a mobile app
description: Use the Appdome Build2Secure API to upload a mobile app, fuse it with a Fusion Set, add context, sign it, and download the protected binary.
api: https://fusion.appdome.com/api/v1
operations:
  - POST /upload
  - GET /upload/{app_id}/status
  - POST /tasks/build
  - POST /tasks/context
  - POST /tasks/sign
  - GET /tasks/{task_id}/status
  - GET /tasks/{task_id}/output/buildapp
---

# Protect, sign, and download a mobile app

Automates Appdome's no-code build → context → sign → download pipeline.

## Auth
Send `Authorization: Bearer <Build2Secure API token>` on every request. Get the
token from the Appdome console under User Menu → Account and API. Supply
`fusion_set_id` (the security configuration) and, if operating in a team, `team_id`.

## Steps
1. **Upload** the app/SDK with `POST /upload` (or get a presigned URL via
   `GET /upload-link`, `PUT` the file, then `POST /upload-using-link`). Note the
   returned `application_id`.
2. **Wait for upload** with `GET /upload/{app_id}/status` until status is
   `completed` (states: queued, progress, completed, error). On `error`, read the
   `error` field and stop.
3. **Build/fuse** with `POST /tasks/build` using `application_id` + `fusion_set_id`.
   Capture the returned `task_id` (the "Appdome Build ID") — it becomes
   `parent_task_id` for later steps.
4. **Add context** (optional) with `POST /tasks/context` referencing
   `parent_task_id` for icon overlay, private URL, or app customization.
5. **Sign** with `POST /tasks/sign` (or `POST /tasks/privatesign` /
   `POST /tasks/autodev` for private / Auto-DEV signing).
6. **Poll** `GET /tasks/{task_id}/status` until the task completes.
7. **Download** the protected binary with `GET /tasks/{task_id}/output/buildapp`.

## Rules
- The pipeline is asynchronous — never assume completion; always poll status.
- There is no idempotency-key; a build is keyed by `application_id` + `fusion_set_id`.
- Errors surface as an HTTP status plus a JSON `error` field.
