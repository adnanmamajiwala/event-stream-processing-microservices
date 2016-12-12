# Event Stream Processing Microservice Example

[![Build Status](https://travis-ci.org/kbastani/event-stream-processing-microservices.svg?branch=master)](https://travis-ci.org/kbastani/event-stream-processing-microservices)

In an event-driven microservices architecture, the concept of a domain event is central to the behavior of each service. Popular practices such as _CQRS_ (Command Query Responsibility Segregation) in combination with _Event Sourcing_ are becoming more common in applications as microservice architectures continue to rise in popularity.

This reference architecture and sample project demonstrates an end-to-end example of building event-driven microservices that use Spring Boot and Spring Cloud. The project aims to show what an ideal development process might look like for building microservices that handle both HTTP and AMQP protocols for exchanging messages.

Demonstrated concepts:

- Event Sourcing
- Event Stream Processing
- Remediating Partial Failures
- Compensating Transactions
- Eventual Consistency
- Hypermedia Event Logs
- Serverless Functions

Spring projects used:

- [Spring Boot](http://projects.spring.io/spring-boot/)
- [Spring Cloud Netflix](https://cloud.spring.io/spring-cloud-netflix/)
- [Spring Cloud Stream](https://cloud.spring.io/spring-cloud-stream/)
- [Spring Cloud Data Flow](https://cloud.spring.io/spring-cloud-dataflow/)
- [Spring Statemachine](https://projects.spring.io/spring-statemachine/)

## Architecture

Each microservice in this reference architecture breaks down into three different independently deployable components.

![Account microservice](http://i.imgur.com/kjT4shO.png)

The diagram above details the system architecture of the bounded context for _Accounts_, which includes deployable units for each [Backing Service](https://12factor.net/backing-services), [Microservice](https://en.wikipedia.org/wiki/Microservices), and [AWS Lambda Function](https://en.wikipedia.org/wiki/AWS_Lambda).

## Usage

The repository contains two parent projects. Each microservice team is responsible for two separate component modules. The first component is the HTTP-driven web service for managing domain entities through a hypermedia-based REST API. The second component is an AMQP-driven event processor that drives the state of domain aggregates.

### Deployment Model

Each microservice team will be managing two separate deployment artifacts, one for HTTP-based interactions and one for AMQP-based interactions.

The components break down into two separate concerns: Web and Worker roles.

#### Web role

The web role contains a REST API with transactions that requires strong consistency. Any workflow that can be transacted within the boundaries of the single microservice can be done within the scope of a single HTTP request/response.

#### Worker role

The worker role contains an AMQP-based message processor that listens for events generated by the web role and from other microservices. The worker role will be responsible for managing workflows that are eventually consistent. Any transaction the requires complex durable communication between separate microservices will be handled in the worker role.

### Packaging

The deployment packaging for each microservice can vary depending on the desired size of the infrastructure footprint. You can either run both workloads as independently scalable components, or alternatively, you can package the components together into a single container.

#### Single Container Deployment

The first style of packaging is to combine together the HTTP (web role) and AMQP (worker role) components into a single container. The advantages with this approach have to do with minimizing the infrastructure footprint for operating the microservice.

Benefits:

* Run workloads side-by-side in a single container
* One microservice deployment per container
* Reduces infrastructure footprint

Trade-offs:

* Scales web and worker instances together
* Tight coupling of components prevents independent release cycles

#### Multi Container Deployment

The second style of deployment is to independently package the web application separate from the worker application. This approach provides multiple advantages at the cost of a larger and more complex infrastructure footprint.

Benefits:

* Allows separate release cycles for web and worker roles
* Independently scale web and worker application instances
* Changes to one component does not require deployment of other
* Reduces overall memory consumption

Trade-offs:

* Requires more application instances
* Higher operational complexity
* Requires two independent CI/CD pipelines


#### Serverless Functions

Each bounded context in this reference architecture contains a set of _action-mapped functions_ that can be deployed as Serverless functions. To learn more about how this works in practice, please take a look at the documentation for the [account-worker](https://github.com/kbastani/event-stream-processing-microservices/tree/master/account-parent/account-worker) application.

# License

This project is licensed under Apache License 2.0.
