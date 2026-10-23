# Week 7 Task API

SENG3080, Backend Lab - Week 7. Express API with Sequelize (SQLite), express-validator, Helmet, Passport and Redis caching.

## Installation

```bash
npm install
cp .env.example .env
```

Change the value of `SESSION_SECRET` in .env.

## Starting Redis

```bash
docker run --name week7-redis -p 6379:6379 redis:7-alpine
```

Must be Redis 6+ version. The server must be started before Redis is running.

## Running the server

```bash
npm run dev
```

The API is available from http://localhost:3000.

## Test record

Test | Result Expected | Status | Response excerpt | Result |
|---|---|---|---|---|
| GET /api/tasks/health | 200 JSON status response; request logger output | 200 | `{"status":"ok","service":"task-api"}`, console shows `GET /api/tasks/health` | Pass |
| POST invalid task `{"title":"x","priority":99}` | 400 with validation errors | 400 | `{"errors":[{"msg":"title must be 3-120 characters","path":"title"},{"msg":"Invalid value","path":"priority"}]}` | Pass |
| POST valid task `{"title":"Finish Week 7 lab","priority":1}` | 201 and a task with id | 201 | `{"completed":false,"id":1,"title":"Finish Week 7 lab","priority":1,...}` | Pass |
| GET /api/tasks/999 | 404 Task not found | 404 | `{"error":"Task not found"}` | Pass |
Security header check | The security header at least one of Helmet is present | 200 | `X-Content-Type-Options: nosniff` | Pass
| GET /api/auth/me before login | 401 Sign in required | 401 | `{"error":"Sign in required"}` | Pass |
| Register, login, then GET /me | 201, 200, then 200 with current user | 201, 200, 200 | `{"id":1,"email":"student@example.com"}` | Pass |
The task should correctly route to both GET /api/tasks calls, first to the database and then to the cache.The task should correctly route to both GET /api/tasks calls, first to the database and then to the cache.
Create task then GET /api/tasks: Passed when invalidated, returns "data": { "source":"database" }, "new task": { "version":200, "content": "new task data" }

## Security verification

Get the state of the health checker for a task via a helmet header on `GET /api/tasks/health`:

```
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Strict-Transport-Security: max-age=31536000; includeSubDomains
```

`npm audit`:

```
uuid  <11.1.1
Severity: moderate
fix available via `npm audit fix --force`
Will install sequelize@3.30.0, which is a breaking change
2 moderate severity vulnerabilities
```

The upgrading of Sequelize is broken and npm audit fix --force would downgrade Sequelize from v6 to v3, so the upgrade was not applied.
