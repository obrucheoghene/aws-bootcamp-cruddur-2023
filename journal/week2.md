# Week 2 — Distributed Tracing

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