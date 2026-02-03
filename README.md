Hands-On L3 – Docker Multi-Container Application

This assignment demonstrates how to install Docker, run PostgreSQL, and deploy a multi-container web application using Flask, Redis, and Docker Compose.

What I Did
•	Installed and verified Docker Desktop
•	Ran PostgreSQL in a Docker container
•	Created a Flask web application
•	Used Redis to store hit counts
•	Built and ran multiple containers using Docker Compose
•	Tested the app in the browser
•	Pushed all files to GitHub
•	Logged errors in the GitHub Issues tab

Execution Steps
1. Check Docker Installation
docker -v
2. Pull PostgreSQL Image
docker pull postgres
3. Run PostgreSQL Container
docker run -d -p 5432:5432 --name postgres1 -e POSTGRES_PASSWORD=pass12345 postgres
4. Open PostgreSQL Container
docker exec -it postgres1 bash
5. Connect to PostgreSQL
psql -d postgres -U postgres
6. Build and Run the Application
docker compose up
7. Open in Browser
http://localhost:8000

Refreshing the page increases the hit count.

Code Files:
requirements.txt
flask
redis

app.py
import time
import redis
from flask import Flask
app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)
def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)
@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)

Dockerfile
FROM python:3.7-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run"]

compose.yaml
version: "3.9"
services:
  web:
    build: .
    ports:
      - "8000:5000"
    depends_on:
      - redis

  redis:
    image: "redis:alpine"

Screenshots:
Docker Desktop app showing the containers:
 <img width="975" height="609" alt="image" src="https://github.com/user-attachments/assets/9f198ea9-0d76-44bc-bf0e-522c01cfe1c6" />

Output obtained after the handson:
<img width="975" height="609" alt="image" src="https://github.com/user-attachments/assets/5b805619-b7b6-4dd7-90b8-4810f98b9628" />
