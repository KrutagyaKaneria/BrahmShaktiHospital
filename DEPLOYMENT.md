# Deployment Guide (Brahm Shakti Hospital)

This project is structured to deploy as one Node web service:
- Express API runs from `server/src/server.js`
- React app is built from `client/` and served by Express from `client/dist`

## Option A (Recommended): Deploy on Render in 10-15 minutes

### 1) Push code to GitHub

If not pushed yet:

```bash
git init
git add .
git commit -m "Prepare full-stack deployment"
git branch -M main
git remote add origin <your-github-repo-url>
git push -u origin main
```

### 2) Create MongoDB Atlas database

1. Create a free cluster on MongoDB Atlas.
2. Create database user and password.
3. Add your IP access rule (for setup, allow `0.0.0.0/0`; tighten later).
4. Copy connection string as `MONGODB_URI`.

Example format:

```text
mongodb+srv://<user>:<password>@<cluster>.mongodb.net/brahm_shakti_hospital?retryWrites=true&w=majority
```

### 3) Deploy with Render Blueprint

1. Go to Render -> New -> Blueprint.
2. Connect your GitHub repo.
3. Render auto-detects `render.yaml`.
4. Set secret env values when prompted:
   - `MONGODB_URI`: from Atlas
   - `JWT_SECRET`: a strong random string
5. Click Deploy.

Render service details from this repo:
- Build command: `npm run build`
- Start command: `npm start`
- Health check: `/api/health`

### 4) Verify after deploy

Open:
- `https://<your-render-url>/api/health` should return healthy JSON
- `https://<your-render-url>/` should open the website

### 5) Share with your friend

Send only the Render URL:

```text
https://<your-render-url>/
```

---

## Option B: Deploy frontend and backend separately

Use this only if you specifically want separate hosting.

- Backend: Render/Railway
- Frontend: Vercel/Netlify

If you do this, set frontend env:

```text
VITE_API_BASE_URL=https://<your-backend-domain>/api
```

---

## Required Environment Variables (Production)

Backend (`server/.env` equivalent):

```text
NODE_ENV=production
PORT=10000
MONGODB_URI=<atlas-connection-string>
JWT_SECRET=<strong-random-secret>
CLIENT_URL=<optional-frontend-url>
```

Notes:
- `PORT` is provided by most hosts automatically; keep default host value if required.
- If `MONGODB_URI` is missing/unreachable, app falls back to in-memory mode (demo only).

---

## Common Troubleshooting

1. Blank page after deploy:
- Check build logs for `npm run build` errors.
- Ensure `client/dist` was generated.

2. API not working:
- Test `GET /api/health`.
- Verify `MONGODB_URI` is valid.

3. Login/token issues:
- Ensure `JWT_SECRET` is set and unchanged between deploys.

4. CORS errors (separate frontend/backend setup):
- Ensure frontend uses correct backend URL in `VITE_API_BASE_URL`.

---

## One-time Security Hardening (after first successful deploy)

1. Rotate `JWT_SECRET` to a long random secret.
2. Tighten MongoDB Atlas network access.
3. Create separate prod database user with least privilege.
