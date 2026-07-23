---
name: Generate and verify a Certified Secure certificate
description: Download Appdome's Certified Secure certificate for a protected build and validate app integrity via the Build2Secure API.
api: https://fusion.appdome.com/api/v1
operations:
  - GET /tasks/{task_id}/certificate
  - GET /tasks/{task_id}/certificate-json
  - POST /verify-certificate
  - POST /validation-upload
  - GET /validation/{appid}/status
---

# Generate and verify a Certified Secure certificate

Produces and validates Appdome's Certified Secure proof for a protected app/SDK.

## Auth
Send `Authorization: Bearer <Build2Secure API token>` on every request.

## Steps
1. After a successful sign task, take its `task_id`.
2. **Download the certificate** with `GET /tasks/{task_id}/certificate` (PDF) or
   `GET /tasks/{task_id}/certificate-json` (JSON).
3. **Verify** the certificate with `POST /verify-certificate`, passing the `task_id`,
   to confirm it is authentic for the secured app/SDK.
4. **Validate app integrity** (optional): upload the app with
   `POST /validation-upload`, then poll `GET /validation/{appid}/status` until the
   validation completes.

## Rules
- Certificates are tied to a specific `task_id` (build) — always reference the exact
  build you protected.
- Validation is asynchronous; poll status rather than assuming completion.
- Errors surface as an HTTP status plus a JSON `error` field.
