# 🐳 Schemind — Docker Build & Push Guide
### For Staff / New Team Members

---

## 📌 Golden Rule — Always Work From The Right Directory

All Docker commands **must be run from the `Schemind/` project folder**.

```
d:\Project\Devops-MiniProject\Schemind\   ← ✅ Run ALL commands here
d:\Project\Devops-MiniProject\            ← ❌ WRONG — one level too high
d:\Project\Devops-MiniProject\Schemind\backend\  ← ❌ WRONG — too deep
```

**How to open the correct terminal in VS Code:**
- Open VS Code
- Press `Ctrl + ~` to open terminal
- Check your prompt shows the `Schemind` folder
- If not, type: `cd d:\Project\Devops-MiniProject\Schemind`

---

## 🗂️ Project Structure Reference

```
Schemind/
├── Dockerfile.frontend      ← Frontend Docker recipe (lives HERE in root)
├── nginx.conf               ← Web server config (auto-copied by Dockerfile)
├── backend/
│   └── Dockerfile           ← Backend Docker recipe (lives inside backend/)
├── k8s/
│   ├── frontend.yaml        ← Kubernetes frontend config
│   └── backend.yaml         ← Kubernetes backend config
└── src/                     ← React frontend source code
```

---

## ⚙️ Step-by-Step: Build & Push Commands

### STEP 1 — Open Terminal in Schemind folder

```powershell
cd d:\Project\Devops-MiniProject\Schemind
```

---

### STEP 2 — Build the Backend Docker Image

```powershell
docker build -t prajitpranavk/schemind-backend:latest -f backend/Dockerfile ./backend
```

**Breaking it down (for beginners):**
| Part | Meaning |
|------|---------|
| `docker build` | Command to create a Docker image |
| `-t prajitpranavk/schemind-backend:latest` | Name the image (username/imagename:version) |
| `-f backend/Dockerfile` | Use the Dockerfile inside the `backend/` folder |
| `./backend` | The source files to package (the backend folder) |

✅ **Success looks like:** `naming to docker.io/prajitpranavk/schemind-backend:latest done`

---

### STEP 3 — Build the Frontend Docker Image

```powershell
docker build -t prajitpranavk/schemind-frontend:latest -f Dockerfile.frontend .
```

**Breaking it down:**
| Part | Meaning |
|------|---------|
| `docker build` | Command to create a Docker image |
| `-t prajitpranavk/schemind-frontend:latest` | Name the image |
| `-f Dockerfile.frontend` | Use the `Dockerfile.frontend` file in current folder |
| `.` | Package everything in the current directory (frontend source) |

> ⏳ This takes ~2 minutes — it installs all packages and builds the React app inside Docker.

✅ **Success looks like:** `✓ built in 7.48s` + `naming to docker.io/prajitpranavk/schemind-frontend:latest done`

---

### STEP 4 — Login to Docker Hub

```powershell
docker login
```

> Enter your Docker Hub username: `prajitpranavk`
> Enter your Docker Hub password when asked.

---

### STEP 5 — Push Backend to Docker Hub

```powershell
docker push prajitpranavk/schemind-backend:latest
```

✅ **Success looks like:** `latest: digest: sha256:...`

---

### STEP 6 — Push Frontend to Docker Hub

```powershell
docker push prajitpranavk/schemind-frontend:latest
```

✅ **Success looks like:** `latest: digest: sha256:...`

---

### STEP 7 — Verify Images Are on Docker Hub

Open your browser and visit:
- 🔗 https://hub.docker.com/r/prajitpranavk/schemind-backend
- 🔗 https://hub.docker.com/r/prajitpranavk/schemind-frontend

---

## ❌ Common Mistakes & Fixes

| Error Message | Cause | Fix |
|--------------|-------|-----|
| `open Dockerfile.frontend: no such file or directory` | Wrong folder | `cd` into `Schemind/` first |
| `path "./backend" not found` | Running from wrong directory | Must run from `Schemind/` not its parent |
| `denied: requested access to the resource is denied` | Not logged in | Run `docker login` first |
| `ERRO: failed to build` | Code error in Dockerfile | Check the line shown in error output |

---

## 🚀 Quick Copy-Paste (All Steps Together)

```powershell
# Step 1: Go to project root
cd d:\Project\Devops-MiniProject\Schemind

# Step 2: Build both images
docker build -t prajitpranavk/schemind-backend:latest -f backend/Dockerfile ./backend
docker build -t prajitpranavk/schemind-frontend:latest -f Dockerfile.frontend .

# Step 3: Login and push
docker login
docker push prajitpranavk/schemind-backend:latest
docker push prajitpranavk/schemind-frontend:latest

# Step 4: Verify
docker images | findstr schemind
```

---

## 📊 Expected Output Summary

```
✅ Backend  → prajitpranavk/schemind-backend:latest  (~55 MB)
✅ Frontend → prajitpranavk/schemind-frontend:latest (~27 MB nginx image)
```

Both images are now available on Docker Hub and ready to be deployed on any server or Kubernetes cluster! 🎉
