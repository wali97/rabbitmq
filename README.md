# Overview

RabbitMQ is an open source message broker that implements the Advanced Message
Queuing Protocol (AMQP) to serve a variety of messaging applications.


## Design
rabbitmq-k8s-app-architecture.png![image](https://github.com/wali97/rabbitmq/assets/76784894/d1f996d6-05ea-429d-816d-65dd79c8376e)

RabbitMQ cluster is deployed within a Kubernetes `StatefulSet`. The
configuration is attached with a `ConfigMap` (which contents is copied to
writable location at `/etc/rabbitmq` by an init container). An Erlang cookie
required by the application is generated dynamically and passed to the
deployment with a Secret object.

The deployment creates two services:

-   A client-facing service, to be used for client connections to the RabbitMQ
    cluster with port forwarding or using a LoadBalancer
-   Service discovery - a headless service for connections between the RabbitMQ
    nodes.

The RabbitMQ K8s application has the following ports configured:

-   ports `5671` and `5672` are enabled for communication from AMQP clients
-   port `15692` is enabled to Prometheus metrics for `/metrics` endpoint
-   port `15672` is enabled for RabbitMQ administration over HTTP API
-   port `25672` is enabled as a distribution port for communication with CLI and for clustering purpose
    tools

This deployment applies an High Availability (HA) policy, which configures
mirroring for all RabbitMQ nodes in the cluster, and automatically synchronizes
with new mirrors that join the cluster. It is enabled as part of the
installation, on each node's `postStart` event.



