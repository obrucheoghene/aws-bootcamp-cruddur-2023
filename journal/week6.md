# Week 6 — Deploying Containers


## Required Homework

### Deploy an ECS Cluster using ECS Service Connect

I ran the command below on the aws cli to create ECS cluster name cruddur
```sh
aws ecs create-cluster \
--cluster-name cruddur \
--service-connect-defaults namespace=cruddur
```
![Cruddur ECS Cluster](./assets/create-ecs-cluster.png)

![Cluster](./assets/ecs-cruddur-cluster.png)


### Deploy serverless containers using Fargate for the Backend and Frontend Application

Logined in to ECR on aws cli using
```sh
aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin "$AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com"
```
I created an ECR (Elastic Container Registery) Reposittory
- backend-flask
```sh
aws ecr create-repository \
  --repository-name backend-flask \
  --image-tag-mutability MUTABLE
```
- Frontend-react-js
```sh
aws ecr create-repository \
  --repository-name frontend-react-js \
  --image-tag-mutability MUTABLE
```
- cruddur-python
```sh
aws ecr create-repository \
  --repository-name cruddur-python \
  --image-tag-mutability MUTABLE
```




I created an ECR (Elastic Container Registery) Reposittory
- backend-flask
- Frontend-react-js
- cruddur-python

### Route traffic to the frontend and backend on different subdomains using Application Load Balancer
### Securing our flask container



### Creating several bash utility scripts to easily work with serverless containers.

#### RDS connection Test
Because PosgreSQL (PSQL) command will not be installed in the container, I created a python script to test RDS Posgres connection.
```
#!/usr/bin/env python3

import psycopg
import os
import sys

connection_url = os.getenv("POSTGRESQL_PROD_CONNECTION_URL")

conn = None
try:
  print('attempting connection')
  conn = psycopg.connect(connection_url)
  print("Connection successful!")
except psycopg.Error as e:
  print("Unable to connect to the database:", e)
finally:
  conn.close()
 ```



