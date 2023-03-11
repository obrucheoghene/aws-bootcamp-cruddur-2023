# Week 2 — Distributed Tracing

## Required Homework

### 1. Instrument our backend flask application to use Open Telemetry (OTEL) with Honeycomb.io as the provider

- I grabed my API keys from honey comb and set it in my environment variable and gitpod as well.
```sh
export HONEYCOMB_API_KEY=""
gp env HONEYCOMB_API_KEY=""
```
- I added the `OTEL_SERVICE_NAME` to `backend-flask` environment of my `docker-compose.yml` to make the service name specific to a service which will make tracing more easier.

- I added `OTEL_EXPORTER_OTLP_ENDPOINT` and `OTEL_EXPORTER_OTLP_HEADERS` to my `backend-flask` environment of my `docker-compose.yml` to configure Opentelementry to send data to honeycomb.

**OpenTelemetry also know as OTEL is an open source observibility framework and standard for instrumenting, generating, collectin and exporting telemetry data such as traces, metrics and logs.**

- Added `Opentelemetry` dependencies to `requirement.txt`
```
opentelemetry-api 
opentelemetry-sdk 
opentelemetry-exporter-otlp-proto-http 
opentelemetry-instrumentation-flask 
opentelemetry-instrumentation-requests
```
- Installed the dependencies
```sh
cd backend-flask/
pip install -r requirement.txt
```

- I imported Opentelemtry in my `backend-flask` `app.py`
```py
from opentelemetry import trace
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
```

- I initialized tracing and an exporter that can send data to Honeycomb
```py
provider = TracerProvider()
processor = BatchSpanProcessor(OTLPSpanExporter())
provider.add_span_processor(processor)
trace.set_tracer_provider(provider)
tracer = trace.get_tracer(__name__)
```

- Below this line of code `app = Flask(__name__)` I add the code below to initialize automatic instrumentation with Flask

```py
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()
```


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