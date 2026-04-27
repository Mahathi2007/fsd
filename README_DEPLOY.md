# VEMU LMS — Render Deployment Guide

## Login Credentials (after seeding)
| Role      | Username       | Password     |
|-----------|---------------|--------------|
| Admin     | mahathitarugu  | 1234         |
| Admin     | admin          | admin123     |
| Librarian | LIB-001        | password123  |
| Faculty   | FAC-001        | password123  |
| Student   | STU-001        | password123  |

---

## Step 1 — Backend Environment Variables on Render
Go to: Backend Service → Environment → Add these variables:

| Key         | Value                                                                 |
|-------------|-----------------------------------------------------------------------|
| MONGO_URI   | mongodb+srv://USERNAME:PASSWORD@cluster.mongodb.net/vemu-library?retryWrites=true&w=majority |
| JWT_SECRET  | vemu_lms_mahathi_secret_2024_xYzAbc99                                |
| NODE_ENV    | production                                                            |
| PORT        | 5000                                                                  |

## Step 2 — Frontend Environment Variables on Render
Go to: Frontend Service → Environment → Add:

| Key           | Value                                          |
|---------------|------------------------------------------------|
| VITE_API_URL  | https://YOUR-BACKEND-SERVICE.onrender.com/api  |

Then click **Manual Deploy → Deploy latest commit** (Vite bakes env vars at build time).

## Step 3 — Seed the Database
After backend is running, open Render Shell for the backend and run:
```
node seed.js
```
This creates all demo users including mahathitarugu/1234.

## Step 4 — Test
Visit: https://fsd-frontend-eicv.onrender.com
Login with: mahathitarugu / 1234 / Admin
