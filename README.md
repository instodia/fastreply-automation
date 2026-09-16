<div align="center">

# ⚡ FastReply

**Automated Instagram comment-to-DM platform built on official Meta Graph APIs.**

[![License: MIT](https://img.shields.io/badge/License-MIT-black.svg)](LICENSE)
[![Built with Next.js](https://img.shields.io/badge/Next.js-16-black.svg)](https://nextjs.org)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-blue.svg)](https://postgresql.org)
[![Redis & BullMQ](https://img.shields.io/badge/BullMQ-Queue-red.svg)](https://bullmq.io)

</div>

Someone comments `LINK` on your reel or post, and **FastReply** queues an automated DM with your resource link. FastReply watches Instagram post comments in real-time via official webhooks and dispatches private replies compliant with Meta policies.

---

## 🚀 Features

- **Keyword to DM**: Trigger private replies on single or multiple keyword matches (whole-word or partial).
- **Public Comment Replies**: Automatically post a public comment reply along with the private DM.
- **Story Reply & Inbound DM Triggers**: Trigger campaigns when someone DMs a keyword or replies to your Story.
- **Tracked Links & Click Analytics**: Measure link clicks, conversion rates, and campaign performance.
- **Follow Gate**: Option to require users to follow your account before delivering the link.
- **Personalization**: Personalize messages with `{username}` dynamically.
- **Rate-Limiting & Queue Safety**: BullMQ message queue prevents hitting Meta API rate caps.
- **Multi-Account Workspaces**: Connect multiple professional Instagram accounts under one workspace.
- **Live Inbox**: Read and reply to Instagram conversations directly from the dashboard.

---

## 🏗️ System Architecture

FastReply operates with **two runtime processes** and **two datastores**:

```
Instagram User Comments
         │
         ▼
[Meta Webhook] ───► [FastReply Web App] (Next.js 16)
                           │
                     Enqueues Job
                           ▼
                 [BullMQ Queue on Redis]
                           │
                     Processes Job
                           ▼
               [Background Queue Worker] ───► [Meta Graph API] ───► Instagram User DM
```

1. **Web App (`app/`)**: Next.js 16 App Router + React 19 + Tailwind CSS (UI, Auth, Webhooks).
2. **Background Worker (`worker/dm-worker.ts`)**: Always-on Node.js queue worker (BullMQ + Meta Graph API).
3. **PostgreSQL**: Stores accounts, campaigns, logs, and sessions.
4. **Redis**: Manages queues and rate limits.

---

## 📦 Getting Started Locally

```bash
# 1. Install dependencies
npm install

# 2. Configure environment variables
cp .env.example .env

# 3. Start PostgreSQL and Redis (via Docker)
docker-compose up -d

# 4. Generate database schema & apply migrations
npm run db:generate
npm run db:migrate

# 5. Start Web Application (Terminal 1)
npm run dev

# 6. Start Background Queue Worker (Terminal 2)
npm run worker
```

---

## ☁️ Deployment (Render)

FastReply includes a pre-configured [`render.yaml`](render.yaml) blueprint for one-click deployment on Render:

1. **PostgreSQL Database** (`fastreply-db`)
2. **Redis Instance** (`fastreply-redis`)
3. **Web Service** (`fastreply-web`): `npm install && npm run vercel-build` -> `npm start`
4. **Background Worker** (`fastreply-worker`): `npm install && npm run db:generate` -> `npm run worker`

See [`docs/setup.md`](docs/setup.md) and [`AGENTS.md`](AGENTS.md) for full deployment details.

---

## 📜 License

MIT License. See [LICENSE](LICENSE).

