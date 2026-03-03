# Deploying this project

This repository contains a combined client (Vite React) and an Express server. The client code now lives under the `client/` directory; the server code remains at the repository root.

## Frontend on Vercel

1. **Move source into `client/`** (you already have).
   - Ensure `client/package.json` has only the frontend dependencies (`react`, `vite`, etc.) and build scripts:
     ```json
     "scripts": {
       "dev": "vite",
       "build": "vite build",
       "preview": "vite preview"
     }
     ```
2. In the Vercel project settings set the **Root Directory** to `client` so that
   commands run in that folder.
3. Set **Build Command** to `npm run build` and **Install Command** to `npm install`
   (or `npm ci`).
4. Set **Output Directory** to `dist/public`.
5. Add an environment variable `BACKEND_URL` containing the base URL of your API
   service (see next section).
6. Deploy by pushing your branch or using the Vercel CLI (`vercel --prod`).

> Note: `vercel.json` in the repo root contains a rewrite rule for `/api/*`.
> You either need to hard‑code your backend URL there or configure an equivalent
> rewrite in the Vercel dashboard using the `BACKEND_URL` variable. Vercel does not
> interpolate environment variables inside `vercel.json`.

## Backend on a Node host

Because the Express server is a long‑running process, it cannot run on Vercel
static hosting. Use a service such as Render, Railway, Fly.io, Heroku, or a VPS.

A sample `render.yaml` is included in the repo:

```yaml
services:
  - type: web
    name: redwoodai-backend
    env: node
    branch: main
    buildCommand: "npm run build"
    startCommand: "npm run start"
    # set any environment variables via the Render dashboard
```

### Example Render steps

1. Create a new Web Service (Node) connected to this GitHub repo.
2. Set the build command to `npm run build` and the start command to
   `npm run start`.
3. Configure environment variables (`MONGODB_URI`, any API keys).
4. Deploy and note the service URL (e.g. `https://redwoodai-backend.onrender.com`).
5. Use that URL in Vercel’s `BACKEND_URL` variable or in your rewrite.

## Why a backend URL is needed

The static frontend needs to call the API for document upload, PII detection, and
risk assessment. That API is provided by your Express server, which must be
hosted somewhere reachable; `BACKEND_URL` tells the client where to send those
requests.

## Developing locally

- Run the backend alongside the frontend (if you wish) by running `npm run dev` at
the repo root.
- Alternatively, develop only the frontend by `cd client && npm run dev`.

## Alternative approaches

- Convert the API to serverless functions and deploy everything on Vercel; this
  requires splitting routes and removing the long‑running listener.
- Deploy the full monolith to a single host (Render/ECS/etc.) and skip Vercel
  entirely.

This document should give you enough guidance to deploy the frontend on Vercel
and the backend on a separate Node service. Feel free to update it as you refine
your setup.