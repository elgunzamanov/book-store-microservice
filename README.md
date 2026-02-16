# 📚 Book Store – Microservices Architecture

**Book Store** backend is built with a modern **Spring Boot microservices architecture**.
The system is designed with clear separation between business logic and infrastructure components,
secure authentication mechanisms, and scalable inter-service communication.

---

## 🏗 Architecture Overview

* **discovery-server** – Service Registry & Discovery

### 🔹 Infrastructure Microservices

* **gateway-service** – API Gateway (infrastructure role)

### 🔹 Business Microservices

* **auth-service** – Authentication & Authorization
* **user-service** – User management & business logic
* **book-service** – Book management & business logic

All services (`auth-service`, `user-service`, `book-service`, `gateway-service`) are registered
in the **Discovery Server** for dynamic service resolution.
Internal service-to-service communication is handled using **OpenFeign**.

---

## 🔐 Authentication & Security

* JWT-based authentication
* Access Token sent via **Authorization header**
* CSRF protection enabled (X-XSRF-TOKEN required when applicable)
* OTP verification stored in **Redis**
* Role-based access control
* Admin can enable/disable users
* Strong password validation enforced during registration

---

### 🔑 Sign In / Login

Users sign in with email and password.

Successful login returns a JWT access token via the Authorization header.

CSRF protection enabled where applicable (X-XSRF-TOKEN required).

Two-factor authentication (2FA) is triggered after initial credentials verification.

---

### 📧 Two-Factor Authentication (2FA)

During the two-factor authentication process, the OTP code is sent via email.

For local development:

* MailHog UI is available at: **[http://localhost:8025/](http://localhost:8025/)**
* The OTP code will be delivered to the email address provided during the sign-in process.

This setup is intended for development purposes only. In production, a real SMTP provider should be configured.

---

### 📝 Sign Up / Registration

Users can register themselves via the sign-up endpoint.

No manual “create user” endpoint exists — all accounts must be self-registered.

Strong password validation is enforced during registration.

After registration, users can log in and access USER-level features.

---

### 🔑 Default Accounts

#### 👑 Admin

* **Email:** `admin@example.com`
* **Password:** `Baku@2020`

#### 📦 Publishers

* **Email:** `publisher1@example.com`

* **Password:** `Baku@2020`

* **Email:** `publisher2@example.com`

* **Password:** `Baku@2020`

#### 👤 User

* **Email:** `user@example.com`
* **Password:** `Baku@2020`

Publisher initially registers as a normal user. Admin upgrades the role from `USER` → `PUBLISHER` via Admin dashboard.

---

## 👥 Roles & Permissions

### 🟢 USER (Default Role)

* Register account
* Sign in
* Browse books
* Search books
* View book details
* Pagination support

> ❌ Users cannot create books
> ❌ No manual "create user" endpoint (self-registration only)

### 🟣 PUBLISHER

* All USER permissions
* Create new books
* Update own books
* Manage published content

### 🔴 ADMIN

* All USER permissions
* Enable / Disable users
* Upgrade USER → PUBLISHER and downgrade PUBLISHER → USER
* Manage all books (override permissions)
* View system-level operations

---

## 📦 API Documentation

Swagger documentation available for each business service:

* **user-service** → `http://localhost:8080/swagger-ui.html`
* **book-service** → `http://localhost:8090/swagger-ui.html`

---

## 🔄 Internal Communication

* **OpenFeign** used for inter-service communication
* Services resolved via Discovery Server
* Gateway handles external traffic routing

---

## 🌍 Frontend API Rewrites (Next.js)

Purpose is to hide backend endpoints in browser.

```js
`async rewrites() {
	...
}`
```

---

## 📑 Search & Pagination Behavior

After performing a search with pagination:

To return to the full list:

* Clear the search bar completely
* Leave it empty
* Press Enter

This resets the filter and loads the full dataset.

---

## 🧪 Testing

Unit tests have been implemented to ensure business logic correctness and reliability.

* Service layer logic is covered with unit tests
* Security-related components are validated
* Core business flows are tested independently of infrastructure

This ensures:

* Stable authentication flows
* Correct role-based authorization behavior
* Reliable business logic execution
* Safer refactoring and future feature additions

The project follows a clean separation of concerns, making the codebase testable and maintainable.

---

## 🔄 Entity Mapping

**MapStruct** is used for:

* Entity → DTO conversion
* DTO → Entity conversion

This ensures:

* Clean separation between layers
* High performance mapping
* Maintainable architecture

---

## 🔢 OTP & Redis

* OTP codes are stored in **Redis**
* Used during authentication flows
* Improves performance and temporary data management

---

## 🌱 Seed Data (Initial Data)

The system is preloaded with initial data for development and testing purposes.

### 👤 Authors

* 6 default authors are automatically created during application startup

### 📚 Books

* 10 default books are inserted into the database
* Books are associated with the predefined authors
* This allows immediate testing of search, pagination, and role-based access features

---

## 🐳 Docker Deployment

Navigate to:

```
book-store/docker-compose/default
```

Run the command in Docker terminal:

```bash
docker compose up -d
```

This starts:

* Discovery Server
* Auth Service
* User Service
* Book Service
* Required dependencies (DB, Redis, etc.)
* Gateway Service

---

## 🛠 Tech Stack

* Java 21
* Spring Boot
* Spring Security
* JWT
* OpenFeign
* Eureka Discovery Server
* Redis
* MapStruct
* Docker & Docker Compose
* Swagger / OpenAPI
* MySQL
* Liquibase Database Migration Tool
* MailHog

---

## 🚀 Key Architectural Decisions

* Infrastructure and business logic strictly separated
* Service discovery instead of hard-coded URLs
* Secure token-based authentication
* Role-driven authorization
* Clean DTO architecture
* Redis for temporary authentication data
* Gateway as a single external entry point

---

## 📌 Notes

* Users self-register (no manual create user endpoint)
* Strong password validation enforced
* Admin has full control over user lifecycle
* Microservices communicate internally via OpenFeign
* Swagger fully documents business services
* Production-oriented structure with containerized deployment