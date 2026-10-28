<div align="center">

# Simple API Mock Server

[![Python Version](https://img.shields.io/badge/python-3.10%2B-blue.svg)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.109.0-009688.svg?logo=fastapi)](https://fastapi.tiangolo.com/)
[![CI Status](https://github.com/daily-project-factory/simple-api-mock-server/actions/workflows/ci.yml/badge.svg)](https://github.com/daily-project-factory/simple-api-mock-server/actions)
[![Code Style: Black](https://img.shields.io/badge/code%25style-black-000000.svg)](https://github.com/psf/black)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A high-performance, developer-first web application and utility for defining, managing, and serving custom mock HTTP endpoints with real-time analytics, latency simulation, and OpenAPI 3.0 synchronization.

</div>

---

## 📌 Problem Statement & Target Audience

### The Problem
Frontend development, mobile apps, and automated integration testing are frequently blocked by unavailable, incomplete, or unstable backend APIs. Engineering teams are routinely forced to waste cycles writing temporary hardcoded mock files, setting up fragile local node scripts, or waiting on database migrations just to test simple UI states and error workflows.

### Target Audience
* **Frontend & Mobile Engineers** requiring deterministic, instantaneous stubs for complex JSON responses and error states.
* **QA & Automation Engineers** needing controllable failure injection and artificial network latency to test client resilience.
* **Full-Stack Developers** who want a lightweight, self-hosted mock registry synchronized directly with existing OpenAPI specifications.

---

## 🚀 Key Features

* **Interactive Route Management:** Define and manage REST routes, HTTP methods (`GET`, `POST`, `PUT`, `DELETE`, `PATCH`), custom response headers, precise HTTP status codes, and mock JSON response payloads.
* **Network Condition Simulation:** Test client application robustness with configurable artificial latency delays (ms) and probabilistic failure injection rates (e.g., simulating random 502/503 errors).
* **Real-Time Request Logger:** Inspect live client traffic history capturing incoming headers, query parameters, raw request payloads, route matching statuses, and response times.
* **OpenAPI 3.0 Interoperability:** Import and export OpenAPI 3.0 specifications instantly to auto-generate mock routes and maintain synchronization with team API contracts.

---

## 🏗️ Architecture & System Design

The Simple API Mock Server is built with a decoupled architecture, separating control-plane management from the high-throughput dynamic execution engine.

```
                  +-----------------------------------+
                  |         Client / Frontend         |
                  +-----------------------------------+
                     |                              |
            (Management UI)                 (Dynamic Requests)
                     |                              |
                     v                              v
        +-------------------------+    +-------------------------+
        |   Management API        |    |   Catch-All Router      |
        |   (/api/v1/*)           |    |   (/mock/*)             |
        +-------------------------+    +-------------------------+
                     \                              /
                      \                            /
                       v                          v
                  +-----------------------------------+
                  |           Core Engine             |
                  |  (Matching, Latency, Injection)   |
                  +-----------------------------------+
                                    |
                                    v
                  +-----------------------------------+
                  |         SQLite / Storage          |
                  +-----------------------------------+
```

### Core Architecture Modules
* `src/models.py`: Pydantic schemas validating mock route definitions, responses, query filters, and persistent request logs.
* `src/database.py`: SQLite database bootstrap, connection pooling, table initialization, and session management.
* `src/core.py`: The heart of the server—handling high-speed route matching, artificial latency simulation, failure injection algorithms, and OpenAPI 3.0 parsing.
* `src/api.py`: FastAPI application router implementing the CRUD Management API (`/api/v1/*`) and the dynamic Catch-All Router (`/mock/*`).
* `src/main.py`: Uvicorn server initialization, middleware binding, and application bootstrap.

---

## 💻 Technology Stack

* **Core Framework:** [FastAPI](https://fastapi.tiangolo.com/) (Async web framework)
* **Server Runtime:** [Uvicorn](https://www.uvicorn.org/) (Lightning-fast ASGI server)
* **Data Validation & Settings:** [Pydantic v2](https://docs.pydantic.dev/)
* **Database & ORM:** SQLite with raw SQL / Lightweight Session wrappers
* **Testing Suite:** [pytest](https://docs.pytest.org/) & `pytest-asyncio`
* **Interface & Tooling:** React, TypeScript, TailwindCSS (Frontend Dashboard)

---

## ⚙️ Installation & Virtual Environment Setup

Ensure you have **Python 3.10+** installed on your system.

1. **Clone the repository:**
   ```bash
   git clone https://github.com/daily-project-factory/simple-api-mock-server.git
   cd simple-api-mock-server
   ```

2. **Create and activate a virtual environment:**
   ```bash
   python -m venv venv
   
   # On macOS / Linux:
   source venv/bin/activate
   
   # On Windows:
   venv\Scripts\activate
   ```

3. **Install dependencies:**
   ```bash
   pip install --upgrade pip
   pip install -r requirements.txt
   ```

---

## 🔑 Environment Variables Configuration

Copy the example configuration file to create your local environment file:

```bash
cp .env.example .env
```

### Environment Parameters (`.env`)
| Variable | Description | Default |
| :--- | :--- | :--- |
| `HOST` | Bind socket address for the Uvicorn server | `0.0.0.0` |
| `PORT` | Port number to expose the API server | `8000` |
| `DATABASE_URL` | SQLite file connection URI | `sqlite:///./mock_server.db` |
| `LOG_LEVEL` | Application logging verbosity (`DEBUG`, `INFO`, `WARNING`) | `INFO` |

---

## 🕹️ Usage: CLI Commands & API Endpoints

### Starting the Server
Run the application using the entrypoint script or directly via Uvicorn:

```bash
python src/main.py
```
Or via uvicorn CLI:
```bash
uvicorn src.api:app --host 0.0.0.0 --port 8000 --reload
```
Once running, open your browser to `http://localhost:8000/docs` to view the interactive Swagger UI.

---

### API Endpoints Reference

#### Management API (`/api/v1`)
* `GET /api/v1/routes` - Retrieve all configured mock routes.
* `POST /api/v1/routes` - Create a new mock endpoint rule.
* `DELETE /api/v1/routes/{route_id}` - Delete an existing mock route.
* `POST /api/v1/openapi/import` - Import an OpenAPI 3.0 JSON/YAML specification to generate routes.
* `GET /api/v1/logs` - Retrieve historical logs of incoming mock requests.

#### Dynamic Mock Execution Engine (`/mock`)
* Any HTTP method sent to `/mock/{path:path}` is intercepted by the dynamic router, evaluated against configured path patterns, subjected to artificial latency and failure injections, and resolved to the defined mock response.

---

## 🧪 Automated Testing Instructions

The test suite is built using `pytest`. It covers core route matching algorithms, latency handlers, database transactions, and FastAPI endpoints.

Run the full test suite with:
```bash
pytest -v
```

Run specific test modules:
```bash
pytest tests/test_core.py -v
pytest tests/test_api.py -v
```

---

## 🗂️ Project Structure Tree

```text
simple-api-mock-server/
├── .env.example
├── .gitignore
├── README.md
├── requirements.txt
├── src/
│   ├── __init__.py
│   ├── api.py
│   ├── core.py
│   ├── database.py
│   ├── main.py
│   └── models.py
└── tests/
    ├── __init__.py
    ├── test_api.py
    └── test_core.py
```

---

## 🗺️ Roadmap & Future Enhancements

- [ ] **GraphQL Mock Support:** Add schema stitching and query-matching resolvers for GraphQL endpoints.
- [ ] **Dynamic Response Templating:** Integrate Jinja2 or Handlebars inside mock response bodies for dynamic payload generation (e.g., random UUIDs, dates, and echoed parameters).
- [ ] **Webhook Triggering:** Configure mock endpoints to fire asynchronous outbound webhooks upon receiving matched requests.
- [ ] **Team Workspaces:** Introduce user authentication and isolated multi-tenant workspaces for collaborative API staging.

---

## 📄 License

Distributed under the **MIT License**. See `LICENSE` for more information.