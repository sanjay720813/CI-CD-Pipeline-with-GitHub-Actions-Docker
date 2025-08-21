# Task: CI/CD Pipeline for Flask App with Docker & GitHub Actions

## 🎯 Objective
Build a simple **Flask application**, containerize it using **Docker**, and set up a **CI/CD pipeline** with **GitHub Actions** to automatically test, build, and push images to Docker Hub.

---

## 🛠 Tools & Technologies
- Python (Flask, Gunicorn, Pytest)
- Docker & Docker Compose
- GitHub Actions
- AWS EC2 (for optional deployment)

---

## 📦 Deliverables
- ✅ Running Flask app (with `/` and `/healthz` endpoints)  
- ✅ Dockerized application  
- ✅ Automated CI/CD workflow (test, build, push to Docker Hub)  
- ✅ Screenshots of build logs and container run  

---

## 📌 Steps to Reproduce

### 1️⃣ Create & Initialize Repository
```bash
git config --global init.defaultBranch main
mkdir my-flask-cicd && cd my-flask-cicd
git init
git remote add origin https://github.com/sanjay1207/my-flask-cicd.git

2️⃣ Application Code

app.py

from flask import Flask, jsonify

app = Flask(__name__)

@app.get("/")
def index():
    return jsonify(message="Hello from CI/CD!"), 200

@app.get("/healthz")
def healthz():
    return jsonify(status="ok"), 200

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)


requirements.txt

flask==3.0.3
gunicorn==21.2.0
pytest==8.2.0


tests/test_app.py

from app import app

def test_healthz():
    client = app.test_client()
    res = client.get("/healthz")
    assert res.status_code == 200
    assert res.get_json()["status"] == "ok"

3️⃣ Containerization

Dockerfile

FROM python:3.11-slim
ENV PYTHONDONTWRITEBYTECODE=1 PYTHONUNBUFFERED=1
WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .
EXPOSE 8000
CMD ["gunicorn", "-w", "2", "-b", "0.0.0.0:8000", "app:app"]


docker-compose.yml

version: "3.8"
services:
  web:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8000:8000"

4️⃣ Local Test
docker compose up --build -d
curl http://localhost:8000/healthz   # {"status":"ok"}
docker compose down

5️⃣ GitHub Actions CI/CD Workflow

.github/workflows/ci.yml

name: CI-CD

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

env:
  IMAGE_NAME: ${{ secrets.DOCKERHUB_USERNAME }}/my-flask-cicd

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt
      - name: Run tests
        run: pytest -q

  build-and-push:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Log in to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Set up Buildx
        uses: docker/setup-buildx-action@v3
      - name: Build and push
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: |
            ${{ env.IMAGE_NAME }}:latest
            ${{ env.IMAGE_NAME }}:${{ github.sha }}

6️⃣ Commit & Push
git add .
git commit -m "CI/CD: Flask app with Docker and GH Actions"
git branch -M main
git push -u origin main


GitHub Actions will:

✅ Run tests

✅ Build Docker image

✅ Push to Docker Hub

7️⃣ Run Image from Docker Hub
docker pull <your-username>/my-flask-cicd:latest
docker run -d -p 8000:8000 --name my-flask-app <your-username>/my-flask-cicd:latest
curl http://localhost:8000/   # {"message":"Hello from CI/CD!"}

📸 Proof of Work



✅ Outcome

By the end of this task, you will have:

A Flask app running in Docker

Automated CI/CD pipeline on GitHub Actions

Image deployed to Docker Hub

Proof screenshots of successful build & deployment
