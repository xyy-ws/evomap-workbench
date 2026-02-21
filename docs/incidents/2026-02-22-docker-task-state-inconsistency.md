# EvoMap Incident - Docker Task State Inconsistency (2026-02-22)

## Task
- task_id: `cmlvt4vd30pjkpn3mg9ka0hyq`
- node_id: `node_2928238f2cbeef3d`
- known submission_id: `cmlvtj9rq1ke5pn3j9bhcy7vm`
- asset_id: `sha256:cfe318042b24401dea22dda58358a363c74a6960e0f7b7a5dc4723d45113439f`

## Reproduction / Evidence

### 1) Claim still fails
Request:
`POST /task/claim`
```json
{"task_id":"cmlvt4vd30pjkpn3mg9ka0hyq","node_id":"node_2928238f2cbeef3d"}
```
Response:
```json
{"error":"task_full"}
```

### 2) Complete returns submitted
Request:
`POST /task/complete`
```json
{"task_id":"cmlvt4vd30pjkpn3mg9ka0hyq","asset_id":"sha256:cfe318042b24401dea22dda58358a363c74a6960e0f7b7a5dc4723d45113439f","node_id":"node_2928238f2cbeef3d"}
```
Response:
```json
{"task_id":"cmlvt4vd30pjkpn3mg9ka0hyq","submission_id":"cmlvtj9rq1ke5pn3j9bhcy7vm","status":"submitted","asset_id":"sha256:cfe318042b24401dea22dda58358a363c74a6960e0f7b7a5dc4723d45113439f","node_id":"node_2928238f2cbeef3d"}
```

### 3) Task status endpoint still shows open
Request:
`GET /task/swarm/cmlvt4vd30pjkpn3mg9ka0hyq`

Observed fields:
- `parent_status: open`
- `claimed_by: null`
- `result_asset_id: null`

## Impact
- Agent cannot successfully claim task (`task_full`)
- Submission exists but task remains open and unbound
- Causes repeated retries and ambiguous completion state

## Requested Platform Action
- Reconcile task `cmlvt4vd30pjkpn3mg9ka0hyq` with submission `cmlvtj9rq1ke5pn3j9bhcy7vm`
- Backfill `claimed_by` and `result_asset_id` if submission is valid
- Ensure claim/complete/status endpoints return consistent task state
