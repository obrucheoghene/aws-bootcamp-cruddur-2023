# Week 2 — Distributed Tracing

## Required Homework

### 1. Instrument our backend flask application to use Open Telemetry (OTEL) with Honeycomb.io as the provider

- I grabed my API keys from honey comb and set it in my environment variable and gitpod as well.
```
export HONEYCOMB_API_KEY=""
gp env HONEYCOMB_API_KEY=""
```
- I added the `OTEL_SERVICE_NAME` to `backend-flask` environment of my `docker-compose.yml` to make the service name specific to a service which will make tracing more easier.



### 2. Run queries to explore traces within Honeycomb.io


### 3. Instrument AWS X-Ray into backend flask application


### 4. Configure and provision X-Ray daemon within docker-compose and send data back to X-Ray API


### 5. Observe X-Ray traces within the AWS Console

### 6. Integrate Rollbar for Error Logging


### 7. Trigger an error an observe an error with Rollbar


### 8. Install WatchTower and write a custom logger to send application log data to CloudWatch Log group


In setting up Honeycomp for project
It is best practice to set up the OTEL service name in the dockercompose file. this is to ensure that every service have a unique name assigned to them.

You do distribute tracing for the backend.
You could also do it for the frontend but it is not a clean use case.

OTEL > OpentTelementry

CNFC - Cloud Native Foundation Community

A little white lie about container is that they say one to one, 
what you use for development you can use for production
but in practice, don't do that, you have to optimize you production image so have a different dockerfile for production is key.

In development you may go for ubuntu, you need ssh and vim installed 
but for production you basically use Alpine linus a very slim build of linus and you don't need ssh and vim installed.

XRAY

Update gitpod to start frontend-reactjs
Add AWS-xray-sdk to backend requirment
- Import aws-xray-sdk
- Create sampling

AWS X-Ray makes it easy for developers to analyze the behavior of their distributed applications by providing request tracing, exception collection, and profiling capabilities.

- Create sampling rules
aws xray create-sampling-rule --cli-input-json file://aws/json/xray.json

-- set up deamon - add to docker composer

- We need to add these two env vars to our backend-flask in our docker-compose.yml file