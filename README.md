# Pharmaceutical Savings Data Platform API

A production-ready FastAPI backend for drug affordability and copay program access, live at **https://api.inforx.health/**.

This project demonstrates secure API design, API key lifecycle management, token-bucket rate limiting (Redis-backed), structured logging, metrics collection, and clean environment-based configuration. It is designed to showcase production-grade backend engineering practices.

---

## 🚀 Features

### 🔐 Authentication & Authorization
- API Key authentication via `x-api-key`
- Separate Admin API key (`x-admin-key`)
- Secure key lifecycle management:
  - Create
  - Revoke
  - Activate / Deactivate
  - Rotate keys
- Key masking in listings

### ⚡ Rate Limiting
- Token Bucket algorithm
- Redis-backed limiter for production
- In-memory fallback for development
- Per-key configurable rate limits
- Configurable rate period

### 🗄 Database Support
- PostgreSQL (production)
- SQLite (development fallback)
- Automatic schema initialization
- Safe migrations for additional columns

### 📊 Observability
- Structured request logging
- Unique `request_id` per request
- Health checks (`/healthz`)
- Readiness checks (`/readyz`)
- Metrics endpoint (`/metrics`)

### 🧱 API Design
- Consistent response envelope pattern
- Typed request models (Pydantic)
- Structured error handling
- No internal exception leakage in production

---

## 🏗 Architecture Overview

Client  
↓  
FastAPI App  
├── Middleware (Request ID + Logging)  
├── Rate Limiter (Redis / In-Memory)  
├── API Key Validation  
├── Business Logic  
├── Database Layer  
└── Response Envelope  

### Key Design Principles

- Separation of concerns (auth, db, rate limiting, metrics isolated)
- Environment-based configuration
- Defensive error handling
- Production-safe defaults
- Pluggable infrastructure components
- Minimal external dependencies

---

## 📦 Project Structure

    .
    ├── main.py              # FastAPI app and endpoints
    ├── auth_db.py           # API key management (SQLite/Postgres)
    ├── db.py                # Coupons database access layer
    ├── config.py            # Environment-based settings
    ├── rate_limiter.py      # Token bucket implementations
    ├── metrics.py           # In-memory metrics counters
    ├── responses.py         # Consistent response envelope
    └── *.db                 # Local SQLite databases (dev)

------------------------------------------------------------------------

## 🛠 Installation

### 1️⃣ Clone the repository

``` bash
git clone https://github.com/YOUR_USERNAME/copay-programs-api.git
cd copay-programs-api
```

### 2️⃣ Create virtual environment

``` bash
python -m venv venv
source venv/bin/activate  # macOS/Linux
venv\Scripts\activate     # Windows
```

### 3️⃣ Install dependencies

``` bash
pip install -r requirements.txt
```

------------------------------------------------------------------------

## ⚙️ Configuration

Configuration is environment-based.

  Variable                Description
  ----------------------- -------------------------------------------------------
  ENV                     `dev` or `prod`
  ADMIN_API_KEY           Required for admin endpoints
  API_KEYS_DATABASE_URL   Postgres URL (if set, Postgres is used)
  API_KEYS_DB_PATH        SQLite path (dev default)
  COUPONS_DB_PATH         Coupons database path
  REDIS_URL               Redis connection string (enables Redis rate limiting)
  SEED_DEV_KEY            Seed dev API key automatically

### Example `.env`

``` env
ENV=dev
ADMIN_API_KEY=supersecretadminkey
REDIS_URL=redis://localhost:6379
```

------------------------------------------------------------------------

## ▶️ Running the API

``` bash
uvicorn main:app --reload
```

Docs available at:

https://api.inforx.health/docs

------------------------------------------------------------------------

## 🔐 Authentication

All non-public endpoints require:

x-api-key: YOUR_API_KEY

Admin endpoints additionally require:

x-admin-key: YOUR_ADMIN_API_KEY

------------------------------------------------------------------------

## 📡 Endpoints

### Public

-   GET /healthz
-   GET /readyz
-   GET /metrics

### Authenticated

-   POST /v1/drug-affordability
-   GET /coupons

### Admin

-   GET /admin/keys
-   POST /admin/keys
-   POST /admin/keys/{api_key}/revoke
-   POST /admin/keys/{api_key}/activate
-   POST /admin/keys/{api_key}/rotate

------------------------------------------------------------------------

## 📊 Metrics

Example response from /metrics:

``` json
{
  "requests_total": 120,
  "errors_total": 3,
  "rate_limited_total": 5,
  "auth_failed_total": 2
}
```

------------------------------------------------------------------------

## 🧪 Rate Limiting Strategy

Token bucket algorithm:

-   Refilled per RATE_PERIOD
-   Enforced per API key
-   Atomic Lua script when using Redis
-   Graceful fallback to in-memory implementation

------------------------------------------------------------------------

## 🧠 Engineering Highlights

This project demonstrates:

-   Production-ready API design
-   Secure API key management
-   Rate limiting at infrastructure level
-   Clean separation of middleware and business logic
-   Environment-aware behavior
-   Structured logging and observability
-   Controlled error exposure
-   Database abstraction across environments

------------------------------------------------------------------------

## 🚀 Production Considerations

To deploy in production:

-   Use Postgres (DATABASE_URL)
-   Use Redis for rate limiting
-   Set ENV=prod
-   Configure a strong ADMIN_API_KEY
-   Run behind a reverse proxy (e.g., Nginx)
-   Use HTTPS

------------------------------------------------------------------------

## 📌 Future Improvements

-   OpenTelemetry integration
-   Prometheus metrics exporter
-   JWT alternative auth mechanism
-   Role-based access control
-   CI/CD pipeline
-   Docker support
-   Unit & integration tests

------------------------------------------------------------------------

## 📄 License

Proprietary

