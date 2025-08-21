🚀 CI/CD Pipeline with GitHub Actions & Docker










This project demonstrates a CI/CD pipeline that builds a Docker image of a Python Flask app, runs tests, pushes the image to Docker Hub, and deploys it locally using Docker.

📌 Objective

Automate the process of building, testing, and deploying a Flask app using GitHub Actions and Docker.

Ensure every code change is automatically tested and deployed with minimal effort.

🛠️ Tools & Technologies

Python (Flask) – Web framework for the sample app

Docker – Containerization

Docker Hub – Remote container registry

GitHub Actions – CI/CD automation

Local VM (Ubuntu/Windows/Linux) – Deployment environment

📂 Project Structure
├── .github/workflows/ci.yml         # GitHub Actions pipeline
├── Dockerfile                       # Docker build instructions
├── docker-compose.yml               # Compose file for local deployment
├── app.py                           # Flask application
├── tests/test_app.py                # Unit tests
├── requirements.txt                 # Python dependencies
├── screenshots/                     # Proof of steps
│   ├── workflows-action.png
│   ├── docker-hub-repo.png
│   ├── my-flask-image.png
│   ├── running-container.png
│   └── deploy-local.png

⚙️ Setup & Run Locally

Clone the repo

git clone https://github.com/your-username/my-flask-cicd.git
cd my-flask-cicd


Build the Docker image

docker build -t my-flask-app .


Run the container

docker run -d -p 8000:8000 my-flask-app


Access the app
👉 Open http://localhost:8000 in your browser.

🐳 Docker Hub Image

The image is automatically built & pushed to Docker Hub via GitHub Actions.

🔹 Pull image from Docker Hub:
docker pull your-dockerhub-username/my-flask-cicd:latest

🔹 Run container from pulled image:
docker run -d -p 8000:8000 your-dockerhub-username/my-flask-cicd:latest


👉 Open http://localhost:8000
 to access the Flask app.

🔄 CI/CD Workflow with GitHub Actions

The workflow is defined in .github/workflows/ci.yml:

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
          cache-from: type=gha
          cache-to: type=gha,mode=max

✅ Deliverables

GitHub Repository – Contains workflows, Dockerfile, app, and tests

Docker Hub Image – Uploaded automatically via GitHub Actions

Screenshots of Results

1. CI/CD Workflow Run ✅

2. Docker Hub Repo ✅

3. Built Docker Image ✅

4. Running Container ✅

5. Local Deployment ✅

📜 Conclusion

This project shows how to set up a full CI/CD pipeline with GitHub Actions, Docker, and Docker Hub. It automates the entire process:
build → test → push → deploy for a Python Flask app.
