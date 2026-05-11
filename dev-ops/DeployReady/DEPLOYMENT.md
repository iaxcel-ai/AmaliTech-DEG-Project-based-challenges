# Deployment Documentation

## Cloud Provider

**Provider:** Render (render.com)
**Service type:** Web Service (Docker runtime)
**URL:** https://amalitech-deg-project-based-challenges-gycg.onrender.com

Render was chosen because it provides managed container hosting with a free tier, built-in HTTPS, and deploy hook support — allowing the GitHub Actions pipeline to trigger redeploys without managing a VM directly.

---

## How the Service Was Set Up

1. Created a Render account at render.com and connected it to the GitHub repository (`iaxcel-ai/AmaliTech-DEG-Project-based-challenges`).
2. Created a new **Web Service** with the following settings:
   - **Runtime:** Docker
   - **Root Directory:** `dev-ops/DeployReady`
   - **Dockerfile Path:** `./Dockerfile`
   - **Instance type:** Free
   - **Environment variable:** `PORT=3000`
3. Render builds the Docker image from the `Dockerfile` on every deploy and runs the container automatically.

---

## How Docker and the Image Are Managed

Render handles Docker installation and image management internally. On each deploy:

1. Render clones the repository at the target commit.
2. It builds the image using the `Dockerfile` in `dev-ops/DeployReady/`.
3. It runs the container with `PORT=3000` injected as an environment variable.

The GitHub Actions pipeline also independently builds and pushes the image to GitHub Container Registry (GHCR) for traceability, tagged with the Git commit SHA:

```
ghcr.io/iaxcel-ai/kora-analytics-api:<commit-sha>
ghcr.io/iaxcel-ai/kora-analytics-api:latest
```

The pipeline then triggers a Render redeploy via a deploy hook URL stored as the `RENDER_DEPLOY_HOOK` GitHub repository secret.

---

## Checking If the Container Is Running

Visit the Render dashboard → select the **AmaliTech-DEG-Project-based-challenges** service → check the status badge (should show **Live**).

To verify via HTTP:

```bash
curl https://amalitech-deg-project-based-challenges-gycg.onrender.com/health
# Expected: {"status":"ok"}
```

---

## Viewing Application Logs

In the Render dashboard:

1. Select the service.
2. Click the **Logs** tab in the left sidebar.
3. Live logs stream in real time, including startup messages and request logs.

Alternatively, use the Render CLI:

```bash
render logs --service-id srv-d80sb39o3t8c73e2k3bg --tail
```

---

## Pipeline Overview

Every push to `main` triggers the following sequence in GitHub Actions:

| Step | What it does |
|------|-------------|
| **Test** | Runs `npm test` (Jest). Pipeline stops if any test fails. |
| **Build & Push** | Builds the Docker image and pushes it to GHCR tagged with the commit SHA. |
| **Deploy** | Calls the Render deploy hook to trigger a redeploy on Render. |

Secrets are stored as GitHub repository secrets and never committed to the repository.
