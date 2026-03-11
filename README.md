# Librarium – Cloud-Native, Event-Driven Bookstore Platform

**Librarium** is a modern, production-grade reference implementation of a cloud-native bookstore built with a microservices architecture. It demonstrates event-driven communication, container orchestration, observability, secure authentication, and real payment integration — all while maintaining fast startup times and low memory footprint thanks to Quarkus.

The system supports two user roles:
- **Customers** register, browse the catalog, manage shopping carts, place orders, make payments, and leave reviews.
- **Managers** maintain the book catalog, stock levels, and create additional manager accounts.

## Key Features & Architecture Highlights

- **Event-driven design** using Apache Kafka for loose coupling and eventual consistency
  - Order placement → stock reservation → payment confirmation → notification
- **Reactive & non-blocking I/O** across services (Quarkus + SmallRye Reactive Messaging)
- **Polyglot persistence** — MongoDB (catalog & reviews) + PostgreSQL (orders, carts, identity)
- **Horizontal scalability** — each service runs with ≥3 replicas on Kubernetes
- **Distributed tracing** with Zipkin
- **OIDC authentication & authorization** via Keycloak
- **Real payment simulation** with Stripe Checkout
- **Email notifications** on successful purchases (implementation detail: e.g. via external SMTP or AWS SES)
- **Seed data generation** — 1,000 realistic books populated at startup using Datafaker

## Technology Stack

| Layer              | Technology                          | Purpose                                      |
|--------------------|-------------------------------------|----------------------------------------------|
| Backend            | Java 25, Quarkus                    | Fast, lightweight, reactive microservices    |
| Frontend           | Angular 21                          | Modern single-page application               |
| Persistence        | PostgreSQL 17, MongoDB              | Relational + document storage                |
| Messaging          | Apache Kafka                        | Asynchronous, reliable event streaming       |
| Authentication     | Keycloak                            | OpenID Connect / OAuth 2.0                   |
| Payment            | Stripe                              | Card payments simulation                     |
| Observability      | Zipkin                              | Distributed tracing                          |
| Orchestration      | Kubernetes                          | Service discovery, load balancing, scaling   |
| Containerization   | Docker, Docker Compose              | Local development & testing                  |
| Deployment target  | AWS EC2 (Kubernetes cluster)        | Production-like cloud environment            |

## Microservices Overview

| Service              | Responsibility                                      | Database       | Key Integrations                     | Scaling |
|----------------------|-----------------------------------------------------|----------------|--------------------------------------|---------|
| **Catalog**          | Book CRUD, stock management, reviews                | MongoDB        | Kafka (events), Datafaker seeding    | ≥3 replicas |
| **Shopping Cart**    | Cart operations, pre-order preparation              | PostgreSQL     | Kafka (order events)                 | ≥3 replicas |
| **Order / Payment**  | Order creation, Stripe payment processing           | PostgreSQL     | Kafka, Stripe API                    | ≥3 replicas |
| **Notification**     | Sending purchase confirmation emails                | —              | Kafka consumer, SMTP/SES             | ≥3 replicas |
| **Identity (Keycloak)** | User registration, login, role management         | PostgreSQL     | Admin API, OIDC                      | —       |

**Note:** The current description lists four services; consider consolidating or renaming for clarity (e.g., merging order & payment logic if appropriate).

## Functional Requirements

- Self-registration for customers
- Manager role can create additional managers
- Full CRUD operations on books & stock (managers only)
- Atomic stock reservation on order placement (via events)
- Review submission restricted to previously purchased books
- Stripe-powered checkout flow
- Email receipt on successful purchase

## Non-Functional Requirements & Design Choices

- **High availability** — minimum 3 replicas per stateless service
- **Service discovery & load balancing** — native Kubernetes features
- **Observability** — distributed tracing with Zipkin
- **Deployment** — containerized services orchestrated on Kubernetes (local via kind/minikube or cloud via AWS EKS/EC2+k3s)
- **Developer experience** — Docker Compose for local multi-service development
