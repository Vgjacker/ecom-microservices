# 🛒 Ecom Microservices

A backend e-commerce system built using **Java, Spring Boot, and Spring Data JPA**, decomposed into independent microservices — each owning its own PostgreSQL database and exposing a REST API for a specific business capability (users, products, cart & orders).

Built while following the [EmbarkX Spring Boot Microservices eCommerce course](https://youtu.be/i_9z3tNrphE), then extended and restructured independently.

---

## 🏗️ Architecture

```
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
│  User Service    │      │ Product Service  │      │  Order Service   │
│  (port 8082)     │      │  (port 8081)     │      │  (port 8083)     │
│  /api/users       │      │  /api/products    │      │  /api/orders      │
│                   │      │                   │      │  /api/cart        │
└────────┬─────────┘      └────────┬─────────┘      └────────┬─────────┘
         │                          │                          │
         ▼                          ▼                          ▼
   ┌──────────┐               ┌──────────┐               ┌──────────┐
   │ userdb    │               │ product   │               │ order     │
   │ (Postgres)│               │ (Postgres)│               │ (Postgres)│
   └──────────┘               └──────────┘               └──────────┘
```

Each service follows the **database-per-service** pattern — no shared tables, no direct DB access across service boundaries. Services communicate via REST and identify the calling user through an `X-User-ID` header (a lightweight stand-in for full gateway-based auth).

---

## ⚙️ Tech Stack

| Layer | Technology |
|---|---|
| Language | Java 21 |
| Framework | Spring Boot 4.1 |
| Data Access | Spring Data JPA / Hibernate |
| Database | PostgreSQL 14 |
| Boilerplate | Lombok |
| Containerization | Docker & Docker Compose |
| Build Tool | Maven |
| DB Admin | pgAdmin 4 |

---

## 📦 Services

### User Service — `:8082`
Manages customer accounts and shipping addresses.

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/users` | List all users |
| GET | `/api/users/{id}` | Get a user by ID |
| POST | `/api/users` | Create a new user |
| PUT | `/api/users` / `/api/users/{id}` | Update a user |

### Product Service — `:8081`
Manages the product catalog and inventory.

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/products` | List all products |
| GET | `/api/products/search?keyword=` | Search products by keyword |
| POST | `/api/products` | Create a new product |
| PUT | `/api/products/{id}` | Update a product |
| DELETE | `/api/products/{id}` | Delete a product |

### Order Service — `:8083`
Manages shopping carts and order placement, with stock validation on checkout.

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/cart` | View current cart (via `X-User-ID` header) |
| POST | `/api/cart` | Add an item to the cart |
| DELETE | `/api/cart/items/{productId}` | Remove an item from the cart |
| POST | `/api/orders` | Convert cart into an order |

---

## 🚀 Getting Started

### Prerequisites
- Java 21
- Maven
- Docker & Docker Compose

### 1. Start PostgreSQL + pgAdmin
```bash
docker compose up -d
```
This spins up Postgres on `localhost:5432` (credentials: `embarkx` / `embarkx`) and pgAdmin on `localhost:5050`.

### 2. Run each microservice
From each service's root directory (`user/user`, `product/product`, `order/order`):
```bash
./mvnw spring-boot:run
```

### 3. Try it out
```bash
# Create a user
curl -X POST localhost:8082/api/users -H "Content-Type: application/json" -d '{...}'

# Add a product
curl -X POST localhost:8081/api/products -H "Content-Type: application/json" -d '{...}'

# Add to cart and place an order
curl -X POST localhost:8083/api/cart -H "X-User-ID: 1" -H "Content-Type: application/json" -d '{...}'
curl -X POST localhost:8083/api/orders -H "X-User-ID: 1"
```

---

## 📁 Project Structure

```
ecom-microservices/
├── user/          # User Service — accounts & addresses
├── product/        # Product Service — catalog, inventory, search
├── order/           # Order Service — cart & order management
└── docker-compose.yml
```

---

## 🗺️ Roadmap

- [ ] API Gateway (Spring Cloud Gateway) as a single entry point
- [ ] Service discovery (Eureka)
- [ ] JWT-based authentication in place of `X-User-ID`
- [ ] Async order notifications (Kafka)
- [ ] Centralized config server
- [ ] Unit & integration test coverage

---

## 📄 License

This project is for educational and portfolio purposes.
