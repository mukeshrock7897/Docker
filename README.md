
---

# 🐳 Docker

Minimal, practical notes to learn and use Docker fast. Each section is tiny and action-oriented.

---

## 📚 Contents

0. [Core Components of Docker](#-0-core-components-of-docker)
1. [Introduction to Docker](#-1-introduction-to-docker)
2. [Docker Installation & Setup](#-2-docker-installation--setup)
3. [Working with Images & Containers](#-3-working-with-docker-images--containers)
4. [Dockerfile & Image Building](#-4-dockerfile--image-building)
5. [Volumes & Bind Mounts](#-5-docker-volumes--bind-mounts)
6. [Networking](#-6-docker-networking)
7. [Docker Compose](#-7-docker-compose)
8. [Image Management](#-8-docker-image-management)
9. [Security Best Practices](#-9-security-best-practices)
10. [Advanced Topics](#-10-advanced-topics)
11. [Docker with Kubernetes (K8s)](#-11-docker-with-kubernetes-k8s)
12. [CI/CD & Docker](#-12-cicd--docker)
13. [Private Registries](#-13-private-registries)
14. [Testing & Debugging](#-14-testing--debugging)
15. [Monitoring & Logging](#-15-monitoring--logging)

---

## ✅ Quickstart

```bash
docker --version
docker run --rm hello-world
```

---

## ⚙️ 0. Core Components of Docker

* **Docker Engine** (daemon + CLI), **Images**, **Containers**
* **Dockerfile**, **Registry** (Hub/ECR/GHCR), **Volumes**, **Networks**

---

## 🔰 1. Introduction to Docker

* Package app + deps into **portable containers**
* **VM vs Docker**: seconds vs minutes; MBs vs GBs
* **Image** = blueprint, **Container** = running instance

```bash
docker pull python:3.10 && docker run -it --rm python:3.10
```

---

## 📦 2. Docker Installation & Setup

* Desktop (macOS/Win) or CLI (Linux)
* Verify & login

```bash
docker info
docker run --rm hello-world
docker login
```

---

## 🏗️ 3. Working with Docker Images & Containers

```bash
docker pull nginx
docker run -d --name web -p 8080:80 nginx
docker logs -f web
docker exec -it web sh
docker stop web && docker rm web
```

---

## 🛠️ 4. Dockerfile & Image Building

```dockerfile
FROM python:3.10-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python","app.py"]
```

```bash
docker build -t myapp .
```

* Use **slim/alpine**, cache-friendly order, `.dockerignore`, non-root user

---

## 📂 5. Docker Volumes & Bind Mounts

```bash
docker volume create mydata
docker run -d -v mydata:/var/lib/postgresql/data postgres
docker run -d -v "$(pwd)"/site:/usr/share/nginx/html:ro nginx
```

* **Named volume** for prod; **bind mount** for dev

---

## 🌐 6. Docker Networking

```bash
docker network create appnet
docker run -d --name web --network appnet nginx
docker run --rm -it --network appnet busybox ping -c1 web
docker run -d -p 8080:80 nginx   # host:8080 -> container:80
```

* Prefer **user-defined bridge** (DNS by name)

---

## 🧩 7. Docker Compose

`compose.yaml`

```yaml
services:
  web:
    image: nginx
    ports: ["8080:80"]
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: pass
    volumes:
      - pgdata:/var/lib/postgresql/data
volumes: { pgdata: {} }
```

```bash
docker compose up -d
docker compose logs -f web
docker compose down
```

---

## 🔄 8. Docker Image Management

```bash
docker tag myapp:latest user/myapp:v1
docker push user/myapp:v1
docker save -o myapp.tar user/myapp:v1
docker load -i myapp.tar
docker system prune -f            # cleanup (keeps volumes)
```

---

## 🔒 9. Security Best Practices

* Run **non-root**, drop caps, read-only FS, limit CPU/RAM
* Scan images; sign/verify; keep images small & pinned

```bash
docker run --user 1000:1000 --cap-drop ALL --read-only --tmpfs /tmp myapp
docker scan myapp
```

---

## 🚀 10. Advanced Topics

* **Contexts** (remote): `docker context create/use`
* **BuildKit**: `DOCKER_BUILDKIT=1 docker build ...`
* **Healthcheck** in Dockerfile; **--init**; multi-arch with **buildx**

---

## ☸️ 11. Docker with Kubernetes (K8s)

* Pod = smallest unit (1+ containers, shared IP/volumes)
* Build → Push → **Reference image** in Deployment
* Use **Service/Ingress**, **ConfigMap/Secret**, **PVC**, **HPA**

```bash
kubectl apply -f k8s/
kubectl get pods,svc,deploy -n app
```

---

## 🔁 12. CI/CD & Docker

* **Stages**: checkout → build → test → scan → push → (deploy)
* GitHub Actions minimal:

```yaml
- run: echo "${{ secrets.TOKEN }}" | docker login -u user --password-stdin
- run: DOCKER_BUILDKIT=1 docker build -t user/app:${{ github.sha }} .
- run: docker push user/app:${{ github.sha }}
- run: docker run --rm user/app:${{ github.sha }} pytest -q
```

---

## 🗃️ 13. Private Registries

* **Docker Hub** vs self-host (registry/Harbor)
* Login, RBAC, retention, scanning

```bash
docker login my-registry.example.com
docker tag myapp my-registry.example.com/team/myapp:v1
docker push my-registry.example.com/team/myapp:v1
```

---

## 🧪 14. Testing & Debugging

```bash
docker logs -f <name>
docker exec -it <name> sh
docker run --rm -it --entrypoint sh myimg
docker inspect <name> ; docker diff <name> ; docker top <name>
```

---

## 📈 15. Monitoring & Logging

* Quick: `docker stats`, `docker events`, `docker logs -f`
* Metrics: **cAdvisor → Prometheus → Grafana**
* Logs: **Fluent Bit/Filebeat → Elasticsearch → Kibana**

```bash
docker run -d --name=cadvisor -p 8080:8080 gcr.io/cadvisor/cadvisor
```

---

## 🧭 Conventions

* Prefer `docker compose` (V2), immutable tags (no `:latest` in prod)
* Keep images tiny, configs in env/ConfigMap/Secret, **volumes for data**

---

Happy shipping! 🐳
---
---