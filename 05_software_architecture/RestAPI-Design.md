REST API Design

Source: https://restfulapi.net/

> [!NOTE]  
> REST is the acronym for **RE**presentational **S**tate **T**ransfer.

A REST API is an architectural style for distributed hypermedia systems.
It is not a protocol or standard.
In order to call a service interface RESTful, it has to statisfy principles and constraints.

Principles of REST

> [!NOTE]  
> Aim of Principles  
> Promote simplicity, scalability and statelessness.

> [!NOTE]  
> 🍩 mnemonic bridge 🍩
> Uwes Clown Serviert Cremige Lasagne Clever.

## Uniform interface

> [!NOTE]  
> Goal of **Uniform interface**  
> Simplify system architecture and improve the visibility of interactions.

We can archieve this goal by applying the **principle of generality**.

> [!NOTE]  
> **Principle of generality**  
> In order to build a general system it has to be **free of unnatural limitations** and restrictions.

REST defines a consistent and uniform interface for interactions between client and server.
For example HTTP-based REST APIs make use of the standard HTTP methods (GET, POST, PUT, DELETE, ...) and
the URIs (Uniform Resource Identifiers) in order to identify resources.

## Client-server

> [!NOTE]  
> Goal of **Client-Server**  
> Introduce independency between client and server and improve scalability by simplifying the server components.

In our use case the REST API can be easily reused by different clients and the clients do not have to take care about the handling of the data.

> [!IMPORTANT]  
> It is important that the contract between client and server does not break.

## Stateless

The server does not store any information about the request, instead each request must contain all information for the server to understand and complete the request.

For this reason the client application must entirely keep the session state.

## Cacheable

If a resource is requested again and again, the request can be handled by a cache which lays before the server and reduces the servers load and latency.

## Layered system

A client cannot tell whether it is connected directly to the end server, or to an intermediary (like a proxy, load balancer, or cache). The system is organized into layers: each layer has a limited scope and responsibilities. Layers cannot "see through" each other — each one only interacts with the adjacent one.

Imagine this flow in a RESTful API call:

Web Client → Load Balancer → API Gateway → Authentication Server → Application Server → Database

Each one is a separate layer:

The client sends a request.

The load balancer routes it to a healthy server.

The API gateway might apply authentication checks.

The application server processes the business logic.

The database stores and retrieves data.

The client only sees the API endpoint — it doesn't know about the load balancer, the gateway, or even the specific server it hits.

✅ This separation makes the system scalable, flexible, and resilient.

## Code on demand

REST allows client functionality to be extended by downloading or executing scripts from the server.
