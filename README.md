# VEMU Library Management System (MERN)

```
library-management-mern/
├── backend/     ← Node + Express + MongoDB API   (port 5000)
├── frontend/    ← React + Vite SPA               (port 5173)
└── README.md    ← you are here
```

---

## What was fixed in this version

| Issue | Fix |
|---|---|
| MongoDB never connected from MongoDB Compass / locally — `.env` used `mongodb://localhost:27017/...`. On Node 18+, `localhost` resolves to IPv6 `::1`, but MongoDB only listens on IPv4 by default → `ECONNREFUSED`. | `backend/.env` (and the seed-script fallback) now use `mongodb://127.0.0.1:27017/vemu-library`. |
| Server started **before** the DB was ready, so the first requests after boot failed silently. | `backend/server.js` now `await`s `connectDB()` before `app.listen()`. |
| Connection failures only showed a one-line message. | `backend/config/db.js` now prints the URI it tried, the real reason and a step-by-step checklist. |
| Dashboards looked clumsy / low-contrast — they're designed for a dark backdrop, but the default theme was `light`, leaving white text on white cards. | Default theme is now `dark`, and the `:root` CSS variables fall back to dark values, so dashboards render with proper contrast on first load. |
| Project layout was messy: legacy static HTML/CSS/images at the repo root, a `Library_Management/` static-site folder, an extra `convert.cjs`, etc. | Cleaned up — only `backend/`, `frontend/`, this `README.md` and a `.gitignore` remain. |

---

## Prerequisites

1. **Node.js 18 or newer** — <https://nodejs.org/>
2. **MongoDB Community Server** running locally on the default port `27017`.
   * Quick check: open **MongoDB Compass** and connect to `mongodb://127.0.0.1:27017`. If the connection succeeds, the database is up.
   * Windows — make sure the **MongoDB** service is started (`services.msc` → MongoDB → Start).
   * macOS (Homebrew) — `brew services start mongodb-community`.
   * Linux — `sudo systemctl start mongod`.

---

## How to run in the VS Code terminal

Open the project folder (`library-management-mern/`) in VS Code and open **two integrated terminals**.

### Terminal 1 — Backend API

```bash
cd backend
npm install
npm run seed          # one-time: creates default admin / librarian / faculty / student
node seedBooks.js     # one-time: seeds 10 sample books
npm run dev           # starts the API on http://localhost:5000 (uses nodemon)
```

You should see:

```
MongoDB Connected: 127.0.0.1/vemu-library
Server running on port 5000
```

### Terminal 2 — React frontend

```bash
cd frontend
npm install
npm run dev           # starts Vite, typically on http://localhost:5173
```

Open the URL Vite prints in your browser.

---

## Default test accounts (created by `npm run seed`)

| Role      | User ID   | Password      |
|-----------|-----------|---------------|
| Admin     | `admin`   | `admin123`    |
| Librarian | `LIB-001` | `password123` |
| Faculty   | `FAC-001` | `password123` |
| Student   | `STU-001` | `password123` |

On the login screen, pick the matching role tab, then enter the User ID and password.

---

## Configuration

* **Backend** — edit `backend/.env`:
  ```env
  PORT=5000
  MONGO_URI=mongodb://127.0.0.1:27017/vemu-library
  JWT_SECRET=change_me_to_a_long_random_string
  NODE_ENV=development
  ```

* **Frontend** — by default it calls `http://localhost:5000/api`. To override (different host/port), create `frontend/.env`:
  ```env
  VITE_API_URL=http://localhost:5000/api
  ```
  Then restart `npm run dev`.

---

## Verifying with MongoDB Compass

1. Open Compass, connect to `mongodb://127.0.0.1:27017`.
2. Open the **`vemu-library`** database — after the seed scripts run you'll see the collections:
   * `users` — admin / librarian / faculty / students
   * `books` — sample catalogue
   * `transactions` — issued / returned books
   * `activitylogs` — audit trail
   * `recommendations` — faculty book recommendations

---

## Common errors & quick fixes

| Symptom | Fix |
|---|---|
| `MongoDB Connection Failed ... ECONNREFUSED ::1:27017` | MongoDB isn't running. Start the service (`services.msc` on Windows, `brew services start mongodb-community` on Mac, `sudo systemctl start mongod` on Linux). |
| `EADDRINUSE: address already in use :::5000` | Change `PORT` in `backend/.env`, then update `VITE_API_URL` in `frontend/.env` to match. |
| `EADDRINUSE` on 5173 | Vite will auto-pick the next free port — use whatever URL it prints. |
| CORS error in the browser console | Make sure the backend is running on the URL the frontend is calling. |
| `401 Unauthorized` after login | The JWT may have expired — log out and log in again. |
| Login succeeds but pages are blank | Make sure both the backend and the React dev server are running, and your browser has localStorage enabled. |
