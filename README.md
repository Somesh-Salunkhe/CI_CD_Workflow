# CI/CD Workflow for Dockerized Flask App

![Python](https://img.shields.io/badge/Python-3.9%2B-blue?logo=python)
![Flask](https://img.shields.io/badge/Flask-Web%20Framework-black?logo=flask)
![Docker](https://img.shields.io/badge/Docker-Containerized-blue?logo=docker)
![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-CI%2FCD-2088FF?logo=githubactions)

A minimal end-to-end **CI/CD pipeline** demo using **GitHub Actions**, **Flask**, **pytest**, and **Docker**. On every push or pull request to `main`, the pipeline automatically runs tests and — if they pass — builds and publishes a Docker image to DockerHub.

---

## Project Structure

```
CI_CD_Workflow/
├── .github/
│   └── workflows/
│       └── cicd.yaml        # GitHub Actions pipeline definition
├── app.py                   # Flask application
├── test_app.py              # pytest unit tests
├── Dockerfile               # Docker image build instructions
├── requirements.txt         # Python dependencies
└── .gitignore
```

---

## Application

`app.py` is a minimal Flask web server with a single route:

```python
@app.route("/")
def home():
    return "Hello World"
```

The app listens on `0.0.0.0:5000`, making it accessible inside Docker containers.

---

## CI/CD Pipeline

The pipeline is defined in [`.github/workflows/cicd.yaml`](.github/workflows/cicd.yaml) and is triggered on **push** or **pull request** to the `main` branch. It consists of two sequential jobs:

### Job 1 — `build-and-test`

Runs on `ubuntu-latest` and ensures the application is functional before any image is built.

| Step | Action |
|------|--------|
| Checkout code | `actions/checkout@v5` |
| Set up Python 3.12 | `actions/setup-python@v6` |
| Install dependencies | `pip install -r requirements.txt` |
| Run tests | `pytest` |

### Job 2 — `build-and-publish`

Only runs if `build-and-test` passes (`needs: build-and-test`). Builds the Docker image and pushes it to DockerHub.

| Step | Action |
|------|--------|
| Checkout code | `actions/checkout@v5` |
| Set up Docker Buildx | `docker/setup-buildx-action@v3` |
| Login to DockerHub | `docker/login-action@v3` |
| Build & push image | `docker/build-push-action@v6` |
| Print image digest | `echo` digest output |

---

## Docker

The `Dockerfile` uses the official `python:3.9-slim` base image:

```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . /app
RUN pip install -r requirements.txt
EXPOSE 5000
CMD ["python", "app.py"]
```

### Running Locally with Docker

```bash
# Build the image
docker build -t flask-app .

# Run the container
docker run -p 5000:5000 flask-app
```

Visit `http://localhost:5000` — you should see `Hello World`.

---

## Getting Started (Without Docker)

### Prerequisites

- Python 3.9+
- pip

### Installation

```bash
# Clone the repository
git clone https://github.com/Somesh-Salunkhe/CI_CD_Workflow.git
cd CI_CD_Workflow

# Install dependencies
pip install -r requirements.txt
```

### Run the App

```bash
python app.py
```

### Run Tests

```bash
pytest
```

---

## Secrets Configuration

To enable the `build-and-publish` job, add the following secrets to your GitHub repository under **Settings → Secrets and variables → Actions**:

| Secret | Description |
|--------|-------------|
| `DOCKER_USERNAME` | Your DockerHub username |
| `DOCKER_PASSWORD` | Your DockerHub password or access token |

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| Flask | Lightweight Python web framework |
| pytest | Unit testing framework |
| Docker | Containerization and image packaging |
| GitHub Actions | Automated CI/CD pipeline |
| DockerHub | Container image registry |

---

## License

This project is open-source and available under the [MIT License](LICENSE).
