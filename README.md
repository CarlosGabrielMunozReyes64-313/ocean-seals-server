# Welcome to Colyseus!

This project was created with [⚔️ `create-colyseus-app`](https://github.com/colyseus/create-colyseus-app/).

[Documentation](https://docs.colyseus.io/)

## :crossed_swords: Usage

```
npm start
```

Then open http://localhost:2567 for the playground, or /monitor for the monitor.

## Structure

- `src/index.ts`: entry point — leave it alone if you plan to deploy to Colyseus Cloud
- `src/app.config.ts`: server configuration — rooms, HTTP routes, express middleware
- `src/rooms/MyRoom.ts`: your room handler
- `src/rooms/schema/MyRoomState.ts`: the state synchronized to every client in the room
- `test/MyRoom.test.ts`: boots the real server and connects a real client
- `loadtest/example.ts`: scriptable client for `npm run loadtest`
- `ecosystem.config.cjs`: pm2 configuration, used when deploying to Colyseus Cloud

## Scripts

- `npm start`: run the server in watch mode (`tsx watch src/index.ts`)
- `npm test`: run the mocha test suite
- `npm run build`: compile to `build/`
- `npm run loadtest`: connect N simulated clients with [`@colyseus/loadtest`](https://github.com/colyseus/colyseus-loadtest/)

## What's included

### Reconnection

`MyRoom.onDrop()` holds a dropped client's seat for 30 seconds via
`allowReconnection()`. The SDK retries automatically with exponential backoff;
`onReconnect()` fires if it gets back in time, `onLeave()` if it does not.

- https://docs.colyseus.io/room/reconnection

### Authentication

`@colyseus/auth` mounts its routes under `auth.prefix`, giving you anonymous
sign-in and email/password registration without writing endpoints. The room's
static `onAuth` verifies the issued JWT automatically.

`src/config/auth.ts` holds the storage callbacks. It ships with an **in-memory
array** so the flow works on the first run — replace `onFindUserByEmail`,
`onRegisterWithEmailAndPassword` and `onRegisterAnonymously` with real queries
before you deploy anything.

`JWT_SECRET` and `SESSION_SECRET` were generated into `.env.development` and
`.env.production`. Rotating `JWT_SECRET` logs everyone out; keep both out of
version control.

- https://docs.colyseus.io/auth/module

### Database

`@colyseus/database` gives you one typed connection behind `db.auth`, `db.saves`,
`db.leaderboards`, `db.configs` and friends, and `DatabaseDriver` reuses it for
matchmaking so there is no separate Redis to run.

The dialect is inferred from `DATABASE_URL`. `.env.development` points at a local
SQLite file (`colyseus.db`, already gitignored); set `.env.production` to a
`postgres://…` URL and `npm install postgres` before deploying — an empty value
silently falls back to SQLite, which is not what you want on a server that
scales past one process.

Migrations run at boot in `"auto"` mode: missing tables and columns are created.
Switch to `{ files: "./drizzle" }` once the schema matters.

Import `db` from `src/app.config.ts` inside your rooms:

```ts
import { db } from "../app.config.js";
```

- https://docs.colyseus.io/database
# ocean-seals-server
