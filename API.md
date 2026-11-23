# API

Every route the portal server answers. The page talks to the server only through these; build another backend
that answers them and the page works unchanged.

- **Auth.** Pairing (`POST /pair` with a one-time token) sets an HttpOnly cookie; the page sends it with every
  request (`credentials: 'include'`). Every write also carries the header `x-aide-approve: 1`. A 401 means "not paired"
  and the page shows its pairing screen.
- **Agents** are addressed by `host` (machine name) and `pane` (the agent's terminal pane id, e.g. `w1:p1`).
- **Build.** Answers carry `x-aide-build`; when it changes the page offers a reload.
- Query keys and example answers below come from a demo recording with invented data (366 requests);
  body keys come from the page's own calls. A route with neither was not exercised by the recording.

| Method | Path | Query keys (seen) | Body keys (page) |
|---|---|---|---|
| GET | `/` |  |  |
| GET | `/manifest.webmanifest` |  |  |
| GET | `/healthz` |  |  |
| POST | `/pair` |  |  |
| GET | `/api/pending` |  |  |
| POST | `/api/decide` |  | `host` `id` `decision` `message` |
| GET | `/api/agents` |  |  |
| GET | `/api/agents/read` | `host` `lines` `pane` |  |
| POST | `/api/agents/send` |  | `host` `pane` `text` `comment` `comment_host` |
| POST | `/api/agents/interrupt` |  |  |
| GET | `/api/agents/messages` | `host` `limit` `pane` `since` |  |
| GET | `/api/files/list` | `host` `pane` `path` |  |
| GET | `/api/files/raw` | `host` `pane` `path` |  |
| POST | `/api/jobs/claim` |  |  |
| POST | `/api/jobs/stop` |  | `host` `job` |
| POST | `/api/jobs/rerun` |  | `host` `project` |
| GET | `/api/machine/resources` | `host` |  |
| GET | `/api/machine/usage` | `host` |  |
| GET | `/api/machine/gpu` | `host` |  |
| GET | `/api/quota` |  |  |
| POST | `/api/quota/thresholds` |  | `thresholds` |
| POST | `/api/questions/approve` |  | `host` `id` |
| GET | `/api/canvas` | `host` `pane` |  |
| GET | `/api/canvas/raw` |  |  |
| GET | `/api/push/key` |  |  |
| POST | `/api/push/subscribe` |  |  |
| POST | `/api/push/test` |  |  |
| GET | `/sw.js` |  |  |
| GET | `/vendor/html2canvas.js` |  |  |
| GET | `/vendor/pdf.min.mjs` |  |  |
| GET | `/vendor/pdf.worker.min.mjs` |  |  |
| POST | `/api/notify` |  |  |
| POST | `/api/bugs` |  |  |
| POST | `/api/okr` |  | `host` `pane` `text` |
| POST | `/api/page-state` |  |  |
| GET | `/api/notices` |  |  |
| POST | `/api/share` |  |  |
| GET | `/api/shares` |  |  |
| GET | `/api/favicon` | `host` |  |
| GET | `/api/shares/body` |  |  |
| POST | `/api/uploads` |  |  |
| GET | `/api/uploads/raw` |  |  |
| POST | `/api/uploads/send` |  |  |
| POST | `/api/screen/keys` |  |  |
| POST | `/api/login` |  |  |
| POST | `/api/screen/text` |  |  |
| GET | `/api/config` |  |  |
| POST | `/api/workspace/move` |  |  |
| POST | `/api/backup/pair` |  |  |
| POST | `/api/peer/mint-pair` |  |  |
| GET | `/api/specs` |  |  |
| POST | `/api/specs/approve` |  | `host` `project` `id` `shown` |
| GET | `/api/harness/cards` |  |  |
| POST | `/api/harness/decide` |  | `id` `shown` `decision` |
| GET | `/api/jobs` |  |  |
| POST | `/api/jobs/comment` |  | `host` `job` `text` `version` `verdict` |
| GET | `/api/decisions` |  |  |
| GET | `/api/prds` |  |  |
| POST | `/api/prds/approve` |  | `host` `id` `shown` |
| GET, POST | `/api/queue` |  |  |
| POST | `/api/queue/{id}/{action}` |  |  |
| GET | `/api/alerts` |  |  |
| GET | `/api/asks` | `all` |  |
| POST | `/api/asks/act` |  | `host` `id` `act` `text` |

## Example answers

### `GET /api/pending`

`GET /api/pending`

```json
[
  {
    "age_secs": 1,
    "created": "2026-11-23T12:46:38.407Z",
    "cwd": "/home/demo/work/web-app",
    "cwd_name": "web-app",
    "gist": "Bash · npm test checkout",
    "hold_left_secs": 86399,
    "host": "studio-mac",
    "id": "57656a310afa0d5b",
    "input_fp": "4395966a5102d06b",
    "kind": "hook",
    "pane": "w1:p1",
    "permission_mode": "default",
    "preview": "command: npm test -- --watch=false checkout",
    "preview_truncated": false,
    "reason": null,
    "session_id": "d3e0a001-0000-4000-8000-000000000001",
    "title": "Run the checkout tests once",
    "tool": "Bash",
    "workspace": "w1"
  },
  {
    "age_secs": 1,
    "created": "2026-11-23T12:46:38.407Z",
    "cwd": "aide-clipper/web-app",
    "cwd_name": "web-app",
    "gist": "aide clipper · upload_file · https://shop-admin.example.com/",
    "hold_left_secs": 86399,
    "host": "studio-mac",
…
```

### `GET /api/agents`

`GET /api/agents`

```json
[
  {
    "agent": "claude",
    "cwd_name": "web-app",
    "host": "studio-mac",
    "model": null,
    "name": "web-app",
    "owner": null,
    "owner_via": null,
    "pane_id": "w1:p1",
    "status": "blocked",
    "title": "Fix the flaky checkout test"
  },
  {
    "agent": "claude",
    "cwd_name": "api",
    "host": "studio-mac",
    "model": null,
    "name": "api",
    "owner": null,
    "owner_via": null,
    "pane_id": "w2:p1",
    "status": "blocked",
    "title": "Add caching to the search endpoint"
  },
  {
    "agent": "claude",
    "cwd_name": "docs",
    "host": "studio-mac",
    "model": null,
…
```

### `GET /api/agents/read`

`GET /api/agents/read?host=gpu-box&pane=w1%3Ap1&lines=200`

```json
{
  "text": ""
}
```

### `GET /api/agents/messages`

`GET /api/agents/messages?host=studio-mac&pane=w1%3Ap1&limit=120`

```json
{
  "agent": "claude",
  "messages": [
    {
      "role": "user",
      "text": "The checkout test fails about one run in five on CI. Find out why and fix it.",
      "ts": "2026-11-20T08:02:10.000Z"
    },
    {
      "model": "claude-opus-5-5",
      "model_changed": true,
      "role": "assistant",
      "text": "I'll read the test and the code it covers first.",
      "ts": "2026-11-20T08:02:31.000Z"
    },
    {
      "is_error": false,
      "name": "Read",
      "result": "(the test file)",
      "result_truncated": false,
      "role": "tool",
      "summary": "/home/demo/work/web-app/src/checkout.test.ts",
      "ts": "2026-11-20T08:02:31.000Z"
    },
    {
      "model": "claude-opus-5-5",
      "role": "assistant",
      "text": "Found it. The test awaits the price update but not the tax call, so on a slow runner it reads the total before tax arrives.\n\n- `checkout.test.ts` line 48 checks `total` right after `setQuantity(2)`.\n- `updateTotals()` starts two requests and only the first is awaited.\n\nI changed the test to wait for both, and made `updateTotals()` return one promise for the two. Now I want to run the checkout tests to confirm.",
      "ts": "2026-11-20T08:03:40.000Z"
    }
…
```

### `GET /api/files/list`

`GET /api/files/list?host=studio-mac&pane=w1%3Ap1&path=`

```json
{
  "entries": [
    {
      "dir": true,
      "kind": "dir",
      "mtime": 1795437998,
      "name": "docs",
      "size": 0
    },
    {
      "dir": true,
      "kind": "dir",
      "mtime": 1795437998,
      "name": "src",
      "size": 0
    },
    {
      "dir": true,
      "kind": "dir",
      "mtime": 1795437998,
      "name": "wikinote",
      "size": 0
    },
    {
      "dir": false,
      "kind": "md",
      "mtime": 1795437998,
      "name": "README.md",
      "size": 62
    }
…
```

### `GET /api/machine/resources`

`GET /api/machine/resources?host=studio-mac`

```json
{
  "disk": null,
  "disks": [],
  "host": "studio-mac",
  "os": "none",
  "ts": "2026-11-23T12:46:40.927Z"
}
```

### `GET /api/machine/usage`

`GET /api/machine/usage?host=studio-mac`

```json
{
  "host": "studio-mac",
  "scanned": {
    "bytes": 2720,
    "files": 4,
    "partial": false
  },
  "ts": "2026-11-23T12:46:40.941585598+00:00",
  "windows": {
    "24h": {
      "hooks": [],
      "logs": [],
      "skills": []
    },
    "7d": {
      "hooks": [],
      "logs": [],
      "skills": []
    }
  }
}
```

### `GET /api/machine/gpu`

`GET /api/machine/gpu?host=gpu-box`

```json
{
  "available": true,
  "updated": 1795437897,
  "rule": "One job at a time on the GPU: book a slot before you run anything else.",
  "order": [
    "paper-proofs",
    "evals",
    "playground"
  ],
  "cluster": {
    "req": 2,
    "jobs": [
      [
        "40112",
        "gen-oss",
        "RUNNING"
      ]
    ]
  },
  "projects": {
    "paper-proofs": {
      "prefix": "pp-",
      "hq": "research",
      "base": 3,
      "waiting": 4,
      "placed": 1,
      "oldest_wait_h": 1.6,
      "done_7d": 37,
      "failed_7d": 1,
      "gpu_s_7d": 14820,
…
```

### `GET /api/quota`

`GET /api/quota`

```json
{
  "providers": [
    {
      "id": "claude",
      "name": "Claude",
      "plan": "Claude Max",
      "pools": [
        {
          "known": true,
          "label": "5h",
          "resets_at": null,
          "used": 34
        },
        {
          "known": true,
          "label": "7d",
          "resets_at": "2026-12-01T06:00:00Z",
          "used": 52
        }
      ]
    },
    {
      "id": "codex",
      "name": "Codex",
      "plan": "ChatGPT Plus",
      "pools": [
        {
          "known": true,
          "label": "5h",
          "resets_at": null,
…
```

### `GET /api/canvas`

`GET /api/canvas?host=studio-mac&pane=w1%3Ap1`

```json
{
  "agent": "web-app",
  "cwd": "/home/demo/work/web-app",
  "dir": "wikinote/canvas",
  "exists": false,
  "files": [],
  "git": false,
  "host": "studio-mac",
  "pane": "w1:p1"
}
```

### `GET /api/shares`

`GET /api/shares`

```json
{
  "capture": false,
  "host": "studio-mac",
  "shares": [
    {
      "id": "dem0share0002",
      "kind": "share",
      "preview": "Tailscale is a WireGuard-based app that makes secure, private networks easy for teams of any scale.",
      "status": "captured",
      "title": "tailscale/tailscale: The easiest, most secure way to use WireGuard and 2FA.",
      "ts": "2026-11-19T12:30:25.000Z",
      "url": "https://github.com/tailscale/tailscale"
    },
    {
      "id": "dem0share0001",
      "kind": "share",
      "preview": "A benchmark of 2,294 software engineering problems drawn from real GitHub issues and pull requests across 12 popular Python repositories.",
      "status": "captured",
      "title": "SWE-bench: Can Language Models Resolve Real-World GitHub Issues?",
      "ts": "2026-11-20T03:10:20.000Z",
      "url": "https://arxiv.org/abs/2310.06770"
    }
  ]
}
```

### `GET /api/config`

`GET /api/config`

```json
{
  "host": "studio-mac",
  "peers": [],
  "url": null
}
```

### `GET /api/jobs`

`GET /api/jobs`

```json
[
  {
    "agent": {
      "host": "studio-mac",
      "name": "web-app",
      "pane_id": "w1:p1"
    },
    "cwd": "/home/demo/work/web-app",
    "deadline": null,
    "ended": null,
    "host": "studio-mac",
    "id": "agent:w1:p1",
    "kind": "job",
    "last_line": "Fix the flaky checkout test",
    "next": null,
    "ok": null,
    "owner": "web-app",
    "owner_via": "",
    "project": null,
    "raw": "{\n  \"agent\": \"claude\",\n  \"cwd\": \"/home/demo/work/web-app\",\n  \"name\": \"web-app\",\n  \"pane_id\": \"w1:p1\",\n  \"status\": \"blocked\"\n}",
    "section": "running",
    "session_gone": false,
    "source": "agent",
    "started": null,
    "state": "在等你回答",
    "stop": null,
    "stopped_by_user": false,
    "title": "web-app",
    "unclaimed": false,
    "what": "Fix the flaky checkout test"
…
```

### `GET, POST /api/queue`

`GET /api/queue`

```json
[
  {
    "agent": {
      "cwd_name": "docs",
      "host": "studio-mac",
      "name": "docs",
      "pane_id": "w3:p1"
    },
    "attempts": 0,
    "claude_queued": false,
    "comment": null,
    "comment_host": null,
    "created": "2026-11-20T06:20:00Z",
    "device": null,
    "done_at": null,
    "from": "user",
    "host": "studio-mac",
    "id": "q101",
    "kind": "queue",
    "model": null,
    "note": null,
    "priority": 0,
    "rank": 1,
    "sent_at": null,
    "session_id": "d3e0a001-0000-4000-8000-000000000003",
    "state": "queued",
    "taken_at": null,
    "text": "When the guide is done, add a troubleshooting section for Windows.",
    "turn_ended_at": null
  },
…
```

### `GET /api/asks`

`GET /api/asks?all=1`

```json
[
  {
    "action": "Sign in to App Store Connect, open Business, and accept the updated agreement.",
    "door": "one-way",
    "host": "studio-mac",
    "hq": "web-app",
    "id": "ua5e1f0a2b",
    "link": "https://appstoreconnect.apple.com/",
    "pane": "w1:p1",
    "state": "open",
    "title": "Accept the App Store Connect agreement",
    "ts": "2026-11-20T09:30:00Z",
    "why": "TestFlight uploads are refused until the new agreement is accepted; the beta (O2) waits on it."
  }
]
```
