# 🚀 FastAPI + Docker + CI/CD Demo
 
A hands-on project to understand **Docker** and **CI/CD pipelines** using FastAPI and GitHub Actions.
 
---
 
## 🎯 What This Project Does
 
This repo demonstrates:
- A real **FastAPI app** with working endpoints
- **Docker** — packages the app so it runs identically everywhere
- **GitHub Actions CI/CD** — automatically tests and builds Docker on every push
 
---
 
## 📁 Project Structure
 
```
fastapi-docker-demo/
├── .github/
│   └── workflows/
│       └── ci.yml          ← GitHub Actions pipeline
├── main.py                 ← FastAPI app
├── test_main.py            ← pytest tests
├── Dockerfile              ← Docker instructions
└── requirements.txt        ← dependencies
```
 
---
 
## 🧠 Key Concepts Learned
 
### What is CI/CD?
```
You push code to GitHub
        ↓
GitHub Actions runs automatically
        ↓
❌ Tests fail → stops, tells you the bug → app stays safe
✅ Tests pass → builds Docker image → ready to deploy
```
 
### What is Docker?
```
WITHOUT DOCKER
Your Windows PC  →  Cloud Server (Linux) = ❌ breaks
Different Python versions, different libraries
 
WITH DOCKER
Pack everything into one box 📦
Your code + Python 3.11 + all libraries
Same box runs on ANY machine ✅
```
 
### How They Work Together
```
You write code
      ↓
git push  ← only thing YOU do
      ↓
CI/CD automatically:
  ✅ Runs tests
  ✅ Builds Docker image
  ✅ Deploys to cloud (when configured)
      ↓
🌍 Live API — zero manual work
```
 
---
 
## 🔗 API Endpoints
 
| Endpoint | What it does | Example |
|----------|-------------|---------|
| `GET /` | Returns hello message | `{"message": "Hello from FastAPI!"}` |
| `GET /predict/{number}` | Checks if number is even or odd | `/predict/4` → `{"number": 4, "result": "even"}` |
 
---
 
## ⚙️ Files Explained
 
### `main.py` — The FastAPI App
```python
from fastapi import FastAPI
 
app = FastAPI()
 
@app.get("/")
def home():
    return {"message": "Hello from FastAPI!"}
 
@app.get("/predict/{number}")
def predict(number: int):
    if number % 2 == 0:
        result = "even"
    else:
        result = "odd"
    return {"number": number, "result": result}
```
 
### `test_main.py` — Automated Tests
```python
from fastapi.testclient import TestClient
from main import app
 
client = TestClient(app)
 
def test_home():
    response = client.get("/")
    assert response.status_code == 200
    assert response.json() == {"message": "Hello from FastAPI!"}
 
def test_predict_even():
    response = client.get("/predict/4")
    assert response.json()["result"] == "even"
 
def test_predict_odd():
    response = client.get("/predict/3")
    assert response.json()["result"] == "odd"
```
 
### `Dockerfile` — Docker Instructions
```dockerfile
FROM python:3.11        # use Python 3.11 as base
WORKDIR /app            # set working folder inside container
COPY requirements.txt . # copy requirements first
RUN pip install -r requirements.txt  # install dependencies
COPY . .                # copy all your code
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]  # start app
```
 
Each line explained:
- `FROM` → which base environment to use (like choosing an OS)
- `WORKDIR` → which folder to work in inside the container
- `COPY` → copy files from your PC into the container
- `RUN` → run a command while building the container
- `CMD` → command to start your app when container runs
 
### `ci.yml` — GitHub Actions Pipeline
```yaml
name: CI Pipeline - FastAPI Docker
 
on:
  push:
    branches: [main]     # runs on every push to main
 
jobs:
  test-and-build:
    runs-on: ubuntu-latest   # GitHub's server (has Docker!)
 
    steps:
      - name: Checkout code
        uses: actions/checkout@v3      # get your code
 
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: "3.11"       # install Python
 
      - name: Install dependencies
        run: pip install -r requirements.txt  # install libraries
 
      - name: Run tests
        run: pytest                    # run all tests
 
      - name: Build Docker image
        run: docker build -t fastapi-docker-demo .  # build Docker box
```
 
---
 
## 💻 All Commands Reference
 
### Git Commands (Push your code)
```bash
git add .                            # stage all files
git commit -m "your message"        # save changes
git push                             # push to GitHub → triggers CI/CD
```
 
### Run FastAPI Locally (without Docker)
```bash
pip install -r requirements.txt     # install dependencies
uvicorn main:app --reload           # start the app
# Open → http://localhost:8000
```
 
### Run Tests Locally
```bash
pytest                               # run all tests
pytest -v                            # run with details
```
 
### Docker Commands (when Docker is installed)
```bash
docker build -t fastapi-docker-demo .   # build the Docker image
docker run -p 8000:8000 fastapi-docker-demo  # run the container
# Open → http://localhost:8000
docker ps                               # see running containers
docker stop <container_id>             # stop a container
```
 
---
 
## ✅ How to Test the CI/CD Pipeline
 
### Break it intentionally — see red ❌
```python
# In test_main.py — change to wrong value
def test_home():
    assert response.json() == {"message": "Wrong message!"}  # wrong!
```
```bash
git add . && git commit -m "break test" && git push
# Go to Actions tab → see ❌ red failure
```
 
### Fix it — see green ✅
```python
# Restore correct value
def test_home():
    assert response.json() == {"message": "Hello from FastAPI!"}  # correct!
```
```bash
git add . && git commit -m "fix test" && git push
# Go to Actions tab → see ✅ green success
```
 
---
 
## 🔄 What Happens on Every Push
 
```
git push
   ↓
GitHub Actions starts automatically
   ↓
✅ Checkout code
✅ Setup Python 3.11
✅ Install dependencies (fastapi, uvicorn, httpx, pytest)
✅ Run pytest (3 tests)
   ↓
❌ Any test fails → pipeline stops → Docker never builds → app stays safe
✅ All tests pass → Docker image builds → ready for deployment
```
 
---
 
## 🧱 What's Next (To Complete the Full CI/CD Pipeline)
 
| Step | What to learn | Tool |
|------|--------------|------|
| ✅ Done | CI + Docker build | GitHub Actions + Docker |
| 🔜 Next | Auto deploy to cloud | Render / Railway |
| 🔜 Later | API testing in CI | pytest + httpx |
| 🔜 Later | Secrets management | GitHub Secrets |
 
---
 
## 🛠️ Tech Used
 
- **Python** — programming language
- **FastAPI** — modern Python web framework for building APIs
- **pytest** — automated testing
- **Docker** — containerization (run app same everywhere)
- **GitHub Actions** — CI/CD automation
 
---
 
## 💡 Real World Value
 
| Without CI/CD + Docker | With CI/CD + Docker |
|-----------------------|---------------------|
| Manual testing every time | Automatic on every push |
| "Works on my machine" problem | Runs same everywhere |
| Bugs reach production | Bugs caught before deploy |
| Manual deployment | Auto deploy on green tests |
| Hours of setup for new team member | 5 minutes with Docker |
