# 📊 Devactics — Developer Analytics

[![Next.js](https://img.shields.io/badge/Next.js-14.2-black?style=flat&logo=next.js)](https://nextjs.org/)
[![React](https://img.shields.io/badge/React-18.3-blue?style=flat&logo=react)](https://react.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.7-blue?style=flat&logo=typescript)](https://www.typescriptlang.org/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-3.4-38B2AC?style=flat&logo=tailwind-css)](https://tailwindcss.com/)
[![Supabase](https://img.shields.io/badge/Supabase-Auth%20%26%20Postgres-3ECF8E?style=flat&logo=supabase)](https://supabase.com/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

> **Lightweight, privacy-conscious website traffic analytics with approximate IP geolocation, telemetry ping tracking, and Supabase storage.**

---

## 🌟 Overview

**Devactics** is an open-source, developer-friendly analytics platform tailored for personal projects, developer tools, and technical documentation. Unlike heavy enterprise tracking suites, Devactics prioritizes speed, developer privacy, and minimal client overhead:

- 🪶 **Ultra-lightweight Tracking Script (`tracker.js`)**: Under 2KB with zero external dependencies.
- 🌍 **Approximate IP Geolocation**: Resolves visitor country and region without storing invasive personal identifiable information (PII).
- 📱 **User-Agent Parsing**: Breaks down visitors by Operating System, Browser, and Device category.
- ⚡ **Supabase Backend**: Uses PostgreSQL with Row-Level Security (RLS) and Supabase Auth.
- 📈 **Interactive Dashboards**: Clean visual breakdowns of pageviews, referrers, unique visitors, and devices powered by Recharts and Lucide icons.

---

## 🏗️ Architecture

```
┌─────────────────────────────────┐
│     Client Website / Blog       │
│  <script src="/tracker.js">     │
└────────────────┬────────────────┘
                 │ POST /api/collect (ping)
                 ▼
┌─────────────────────────────────┐
│       Next.js 14 App Router     │
│  - IP Approximate Geolocation   │
│  - User-Agent Detection (UA)    │
└────────────────┬────────────────┘
                 │ SQL Insert
                 ▼
┌─────────────────────────────────┐
│        Supabase Postgres        │
│  - websites, events, sessions   │
└─────────────────────────────────┘
                 ▲
                 │ Query Analytics
┌────────────────┴────────────────┐
│      Devactics Dashboard        │
│  - Overview, Charts, Geolocation│
└─────────────────────────────────┘
```

---

## 🚀 Quick Start

### 1. Prerequisites
- **Node.js** >= 18.x
- **npm** or **pnpm**
- A **Supabase** project (free tier works great)

### 2. Clone & Install
```bash
git clone https://github.com/akshadit/analytics.git
cd analytics
npm install
```

### 3. Environment Variables
Create a `.env.local` file in the root directory:
```env
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-supabase-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-supabase-service-role-key
```

### 4. Supabase Database Schema
Execute the following table definitions in your Supabase SQL editor:
```sql
create table if not exists websites (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references auth.users(id) on delete cascade,
  name text not null,
  domain text not null,
  created_at timestamp with time zone default timezone('utc'::text, now()) not null
);

create table if not exists analytics_events (
  id uuid primary key default gen_random_uuid(),
  website_id uuid references websites(id) on delete cascade,
  pathname text not null,
  referrer text,
  browser text,
  os text,
  device text,
  country text,
  city text,
  created_at timestamp with time zone default timezone('utc'::text, now()) not null
);
```

### 5. Run Locally
```bash
npm run dev
```
Open [http://localhost:3000](http://localhost:3000) to view the registration and dashboard portal.

---

## 🔌 Embedding the Tracker

Add this single line of code inside the `<head>` or `<body>` tag of any website you wish to monitor:

```html
<script 
  defer 
  src="https://your-devactics-instance.com/tracker.js" 
  data-website-id="YOUR_WEBSITE_UUID">
</script>
```

The script automatically intercepts page loads and sends non-blocking beacon pings to `/api/collect`.

---

## 📡 API Endpoints

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `POST` | `/api/collect` | Ingestion endpoint receiving client tracking beacons |
| `GET` | `/api/analytics` | Aggregates views, referrers, and geo data for dashboard charts |
| `GET` / `POST` | `/api/websites` | List or register websites under the authenticated user |
| `POST` | `/api/auth/*` | Supabase auth management (login, signup, signout, me) |

---

## 🛠️ Tech Stack

- **Framework**: Next.js 14 (App Router)
- **Styling**: Tailwind CSS, PostCSS
- **Database & Auth**: Supabase (@supabase/ssr, @supabase/supabase-js)
- **Charts**: Recharts
- **Icons**: Lucide React
- **Telemetry Parsing**: `ua-parser-js`

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).