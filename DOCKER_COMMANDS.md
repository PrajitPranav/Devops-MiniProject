# Schemind — Docker Build & Push Commands
# ──────────────────────────────────────────
# ALL commands must be run from the PROJECT ROOT:
#   cd d:\Project\Devops-MiniProject\Schemind
#
# ──────────────────────────────────────────────────────────────────────────────
# BUILD IMAGES
# ──────────────────────────────────────────────────────────────────────────────

# Build Backend image
# Context: ./backend  (the backend folder is the build context)
# Dockerfile: backend/Dockerfile
docker build -t prajitpranavk/schemind-backend:latest -f backend/Dockerfile ./backend

# Build Frontend image (local dev, no API URL baked in)
docker build -t prajitpranavk/schemind-frontend:latest -f Dockerfile.frontend .

# Build Frontend image (production — bakes in the K8s backend URL)
# Replace <NODE_IP> with your actual Kubernetes node IP
docker build --build-arg VITE_API_URL=http://<NODE_IP>:30081 -t prajitpranavk/schemind-frontend:latest -f Dockerfile.frontend .

# ──────────────────────────────────────────────────────────────────────────────
# PUSH IMAGES
# ──────────────────────────────────────────────────────────────────────────────

docker login
docker push prajitpranavk/schemind-backend:latest
docker push prajitpranavk/schemind-frontend:latest

# ──────────────────────────────────────────────────────────────────────────────
# KUBERNETES DEPLOY
# ──────────────────────────────────────────────────────────────────────────────

# Step 1 — Create namespace (run once)
kubectl apply -f k8s/namespace.yaml

# Step 2 — Create secrets (run once, replace values)
kubectl create secret generic schemind-secrets \
  --namespace=schemind \
  --from-literal=MONGO_URI="mongodb+srv://user:pass@cluster/schemind" \
  --from-literal=JWT_SECRET="your_secret_key"

# Step 3 — Deploy
kubectl apply -f k8s/backend.yaml
kubectl apply -f k8s/frontend.yaml

# Step 4 — Check pods
kubectl get pods -n schemind

# Step 5 — Get Node IP
kubectl get nodes -o wide

# Access:
#   Frontend → http://<NODE_IP>:30080
#   Backend  → http://<NODE_IP>:30081
#   Health   → http://<NODE_IP>:30081/api/health
