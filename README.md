# HelpDesk Ticket System

A full-stack support ticket management application built as a portfolio project. It combines a layered Spring Boot REST API with a responsive Vue dashboard for managing a ticket from creation through resolution.

## Features

- Create, view, update, and delete support tickets
- Filter tickets by status and priority, independently or together
- Track open, in-progress, and resolved work from a dashboard
- Validate requests in the browser and API
- Return consistent JSON errors for invalid input and missing tickets
- Preserve created and last-updated timestamps
- Restrict API cross-origin access to the configured frontend origin
- Persist local development data in an H2 file database
- Run service unit tests and full API integration tests against isolated in-memory H2
- Responsive interface with loading, empty, error, and confirmation states

## Tech stack

| Area | Technology |
| --- | --- |
| Backend | Java 21, Spring Boot, Spring MVC |
| Data | Spring Data JPA, Hibernate, H2 |
| Validation | Jakarta Bean Validation |
| Testing | JUnit, Mockito, MockMvc, AssertJ |
| Frontend | Vue 3, Vite, JavaScript, CSS |
| Tooling | Maven Wrapper, npm, Git |

## Project structure

```text
helpdesk-ticket-system/
├── backend/    # Spring Boot REST API and tests
└── frontend/   # Vue dashboard and API client
```

The backend separates HTTP handling (`TicketController`), application logic (`TicketService`), persistence (`TicketRepository`), API models, and global error handling. Entities are not exposed directly by the API.

## Prerequisites

- Java 21
- Node.js 20 or newer and npm

Maven does not need to be installed globally because the project includes the Maven Wrapper.

## Run locally

Open two terminals from the repository root.

### 1. Start the backend

Windows PowerShell:

```powershell
cd backend
.\mvnw.cmd spring-boot:run
```

macOS/Linux:

```bash
cd backend
./mvnw spring-boot:run
```

The API runs at `http://localhost:8080`. Local data is stored under `backend/data/` and is ignored by Git. The H2 console is available at `http://localhost:8080/h2-console` using JDBC URL `jdbc:h2:file:./data/helpdeskdb`, user `sa`, and an empty password.

### 2. Start the frontend

```bash
cd frontend
npm install
npm run dev
```

Open `http://localhost:5173`. During development, Vite proxies `/api` requests to the backend.

## Configuration

| Variable | Default | Purpose |
| --- | --- | --- |
| `CORS_ALLOWED_ORIGIN` | `http://localhost:5173` | Frontend origin accepted by the API |
| `VITE_API_BASE_URL` | `/api` | API base URL used by the Vue app |

For a frontend hosted separately, copy `frontend/.env.example` to `frontend/.env.local` and update the URL. Set `CORS_ALLOWED_ORIGIN` to the frontend's exact origin when starting the backend.

## API endpoints

Base URL: `http://localhost:8080/api/tickets`

| Method | Endpoint | Description |
| --- | --- | --- |
| `GET` | `/api/tickets` | List all tickets, newest first |
| `GET` | `/api/tickets?status=OPEN` | Filter by status |
| `GET` | `/api/tickets?priority=HIGH` | Filter by priority |
| `GET` | `/api/tickets?status=OPEN&priority=HIGH` | Combine filters |
| `GET` | `/api/tickets/{id}` | Get one ticket |
| `POST` | `/api/tickets` | Create a ticket |
| `PUT` | `/api/tickets/{id}` | Update a ticket |
| `DELETE` | `/api/tickets/{id}` | Delete a ticket |

Statuses: `OPEN`, `IN_PROGRESS`, `RESOLVED`

Priorities: `LOW`, `MEDIUM`, `HIGH`

Example request:

```json
{
  "title": "Cannot connect to VPN",
  "description": "The VPN client times out after authentication.",
  "status": "OPEN",
  "priority": "HIGH"
}
```

`status` may be omitted when creating a ticket and defaults to `OPEN`. Title and description cannot be blank; title is limited to 120 characters and description to 2,000 characters.

## Tests and production builds

Backend tests use a separate in-memory database:

```powershell
cd backend
.\mvnw.cmd test
```

Frontend production build:

```bash
cd frontend
npm install
npm run build
```

## Possible next steps

- Authentication and role-based access for agents and requesters
- Ticket assignment, categories, comments, and attachments
- Pagination and free-text search
- PostgreSQL and containerized deployment
