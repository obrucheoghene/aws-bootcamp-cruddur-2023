# Week 6 — Deploying Containers


## Required Homework

### Deploy an ECS Cluster using ECS Service Connect
### Deploy serverless containers using Fargate for the Backend and Frontend Application
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


Create Cluster

![Cluster](./assets/create-ecs-cluster.png)
![Cluster](./assets/ecs-cruddur-cluster.png)
