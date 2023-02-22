# Week 1 — App Containerization

## Required Homework

### 1. Containerize Application (Dockerfiles, Docker Compose)
Here are the steps I took to containerize the Cruddur application.

#### Backend Containization
- Created a `Dockerfile` in `backend-flask` directory

```dockerfile
FROM python:3.10-slim-buster

WORKDIR /backend-flask

COPY requirements.txt requirements.txt
RUN pip3 install -r requirements.txt

COPY . .

ENV FLASK_ENV=development

EXPOSE ${PORT}
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
```

### 2. Document the Notification Endpoint for the OpenAI Document

### 3. Write a Flask Backend Endpoint for Notifications

### 4. 	Write a React Page for Notifications

### 5. 	Run DynamoDB Local Container and ensure it works

### 6. Run Postgres Container and ensure it works
