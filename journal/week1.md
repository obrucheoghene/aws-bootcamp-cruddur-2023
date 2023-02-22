# Week 1 — App Containerization

## Required Homework

### 1. Containerize Application (Dockerfiles, Docker Compose)
Here are the steps I took to containerize the Cruddur application.
I started our by containerizing each of the application

### Steps I followed to containerize Backend-flask
a. I created a `Dockerfile` in `backend-flask` directory

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
b. I ran the command below to build backend-flask docker image 
 ```sh
 docker build -t  backend-flask ./backend-flask
 ```
 Here is the result showing that a `backend-flask` image has been created
 ![Backend-Flask Docker Image Build](./assets/backend-img-build.png)
 
c. After which I ran the container with the command below

```sh
docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask
```
This command started the container and set environment variable for `FRONTEND_URL` and `BACKEND_URL` to `*`


### 2. Document the Notification Endpoint for the OpenAI Document 
### 3. Write a Flask Backend Endpoint for Notifications

### 4. 	Write a React Page for Notifications

### 5. 	Run DynamoDB Local Container and ensure it works

### 6. Run Postgres Container and ensure it works
