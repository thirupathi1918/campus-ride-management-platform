# Velocis Transit Network

### Intelligent Campus Mobility & Smart Fleet Coordination Platform
#### IIT Roorkee — Real-Time Campus E-Rickshaw Ride Management System

---

## Development Team

| Name | Enrollment No |
|---|---|
| Badavath Thirupathi | 23116022 |
| Methari Sudhishna | 23116062 |
| Majji Homesh Babu | 23116055 |
| Dhanavath Mahesh | 23321011 |

---

## Project Resources

| Resource | Link |
|---|---|
| 📹 Video Demo & 📄 Design Document | [Google Drive Folder](https://drive.google.com/drive/folders/1QUMi2s50lGN0vCdV4xi2n1Ps74m54lRr?usp=sharing) |

> The above Drive folder contains the full video demonstration of the platform and the design document for this project.

---

## Overview

Velocis Transit Network is a full-stack, event-driven campus ride-hailing platform engineered specifically for the IIT Roorkee e-rickshaw ecosystem. The system replaces fragmented, manual coordination workflows with a centralized, real-time web application that connects passengers and operators through a live STOMP WebSocket message bus.

Passengers submit ride requests with pickup and destination coordinates drawn from seeded campus locations. Drivers toggle their fleet availability state, receive incoming ride notifications instantly, and accept requests through a database-locked atomic transaction that prevents double-assignment. Both roles receive continuous, low-latency state updates as rides move through their full lifecycle — from request to completion.

The platform additionally features a fully overhauled presentation layer with premium UI customizations, a live Leaflet map integration powered by the browser Geolocation API, a driver analytics dashboard built with Recharts, and a dedicated on-screen SOS emergency hub for active operators.

---

## Technology Stack

### Backend Engine
- **Runtime:** Java 21
- **Framework:** Spring Boot 3
- **Security:** Spring Security with stateless JWT authentication
- **Persistence:** Spring Data JPA with Hibernate ORM
- **Database:** PostgreSQL
- **Real-Time Layer:** Asynchronous STOMP Protocol over WebSockets

### Frontend Terminal
- **Framework:** React 18 with TypeScript
- **Bundler:** Vite
- **Map Integration:** Leaflet API with OpenStreetMap tile layer
- **Charts:** Recharts Engine
- **Icons:** Lucide React

---

## Unique Platform Customizations

The Velocis presentation layer and user terminal workflows have been completely rebuilt from the ground up. The following premium UX enhancements differentiate this platform from standard implementations:

### 1. Flipped Input Orientation Viewport
The traditional form-above-branding layout has been inverted. Input fields, state-mutation controls, and action buttons are anchored to the left column, while interactive branding frames and visual identity blocks are positioned to the right. This layout mirrors natural left-to-right visual reading flow and creates a cleaner, more intentional interface hierarchy.

### 2. Moving Cosmic Starfield Backdrop Canvas
The application background runs a continuously animated cosmic canvas using hardware-accelerated CSS keyframe animations. The `space-drift` animation sequence drives an infinite-loop flowing gradient matrix layered with small floating e-rickshaw icon paths that drift across the viewport. The effect is implemented entirely in `frontend/src/styles.css` without any JavaScript animation libraries or canvas rendering overhead, ensuring consistent performance across all modern browsers.

### 3. Integrated Session Disconnect Action
The standard sign-out button has been removed as a standalone element. Instead, the logout action is merged directly inside the active user profile card as a unified `Disconnect Session` configuration block. This consolidates identity controls into a single coherent panel and removes visual noise from the primary navigation surface.

### 4. Active Operator SOS Emergency Hub
A high-visibility emergency safety widget is permanently anchored at the base of the driver's sidebar terminal. The SOS hub is styled with a crimson accent to draw immediate attention and provides two click-to-call emergency shortcuts accessible at any point during an active session:
- **112** — National Emergency Services
- **102** — Campus Medical Dispatch

This feature addresses a real safety gap in campus transit operations by giving drivers instant access to emergency contacts without navigating away from their active ride screen.

---

## Features

### Core Functionality
- Passenger and driver account registration and login
- Stateless JWT authentication with role-based backend access control
- Passenger profile viewing and update
- Driver vehicle registration, license details, and verification data
- Driver fleet state management: `ONLINE`, `OFFLINE`, `BUSY`
- Available driver listing visible to passengers in real time
- Ride request creation with named pickup and destination selections
- Driver incoming request queue with accept and reject controls
- Database row-level locking on ride acceptance to prevent concurrent double-assignment
- Full ride lifecycle state machine: `REQUESTED` → `ACCEPTED` → `IN_PROGRESS` → `COMPLETED` / `CANCELLED`
- Real-time broadcast of driver availability, new ride requests, ride status transitions, and driver assignment events
- Driver analytics dashboard with completed rides, active ride status, ride history, rating summary, and a Recharts chart
- Post-trip passenger rating with a 1–5 score and optional written review
- Running average driver rating calculated and stored after every new submission

### Map & Location Features
- Interactive Leaflet map with OpenStreetMap tile rendering
- Campus pickup and destination coordinates seeded into the `campus_locations` PostgreSQL table
- Passenger map displaying pickup marker, destination marker, and assigned driver marker
- Driver map showing pickup and destination for active and incoming rides
- Live driver GPS tracking using the browser HTML5 Geolocation API
- Continuous driver coordinate heartbeats streamed to the passenger's Leaflet map canvas via WebSocket events

---

## Project Structure

```
RideManagementPlatform/
├── backend/                        # Spring Boot API & STOMP Message Broker
│   ├── src/
│   └── mvnw.cmd
├── frontend/                       # React Vite Client Terminal
│   ├── src/
│   │   ├── App.tsx                 # Refactored Main Shell Code
│   │   └── styles.css              # Cosmic Animation Layout Styling
│   └── package.json
└── README.md
```

---

## Prerequisites

Ensure the following are installed on every team member's local machine before proceeding:

| Dependency | Purpose | Required |
|---|---|---|
| Java 21 JDK | Compiles and runs the Spring Boot backend | Yes |
| Node.js and npm | Installs and runs the React Vite frontend | Yes |
| PostgreSQL | Local relational database server | Yes (local setup) |
| IntelliJ IDEA | Backend IDE with Spring Boot run support | Recommended |
| pgAdmin | PostgreSQL GUI for viewing and editing tables | Recommended |

> The Maven wrapper `mvnw` / `mvnw.cmd` is bundled inside the `backend/` directory. IntelliJ is not required to run the backend — the wrapper handles compilation and startup independently.

---

## Database Setup

### Local PostgreSQL Configuration

PostgreSQL must be running locally on port `5432` before starting the backend.

The backend is pre-configured to connect to:

```
Database : ride_management_db
Username : ride_user
Password : ride_password
```

Open a `psql` terminal or pgAdmin Query Tool and run the following SQL block to create the required role and database:

```sql
CREATE ROLE ride_user WITH LOGIN PASSWORD 'ride_password';
CREATE DATABASE ride_management_db OWNER ride_user;
GRANT ALL PRIVILEGES ON DATABASE ride_management_db TO ride_user;
```

Spring Boot will automatically create and update all required tables on first startup using:

```properties
spring.jpa.hibernate.ddl-auto=update
```

No manual table creation or migration scripts are needed.

**Starting PostgreSQL on macOS with Homebrew:**

```bash
brew services start postgresql@18
```

---

## Shared Cloud Database Setup (Team Collaboration)

Use this configuration when all teammates need to read and write to the same live dataset — shared users, drivers, rides, ratings, and campus locations.

Each teammate still runs the backend and frontend locally. Every local backend instance points to the same hosted PostgreSQL database:

```
Teammate A backend  →  Shared Hosted PostgreSQL (e.g. Render)
Teammate B backend  →  Shared Hosted PostgreSQL (e.g. Render)
Teammate C backend  →  Shared Hosted PostgreSQL (e.g. Render)
```

> **Important:** Never commit database credentials or hosted URLs to GitHub. Share them privately with teammates through a secure channel.

### Step 1 — Create the local environment file

```bash
cd backend
cp .env.example .env
```

### Step 2 — Edit `backend/.env` with the shared credentials

```properties
DATABASE_URL=jdbc:postgresql://<external-render-host>:5432/ride_management_db?sslmode=require
DATABASE_USERNAME=ride_user
DATABASE_PASSWORD=<shared-database-password>
JWT_SECRET=<shared-jwt-secret>
```

> For Render PostgreSQL instances, always use the **external connection hostname** for local development laptops, not the internal hostname. The internal hostname is only reachable from within Render's network.

### Step 3 — Start the backend normally

The backend will detect the `.env` file automatically and override local defaults with the shared credentials. If the shared database is empty, Spring Boot creates all tables on first startup.

`backend/.env` is listed in `.gitignore` and will never be committed to the repository.

---

## Quick Start

Follow these steps in order to bring up the full application stack locally.

### Step 1 — Start PostgreSQL

Ensure your local PostgreSQL server is running on port `5432`.

### Step 2 — Create the database and user

```sql
CREATE ROLE ride_user WITH LOGIN PASSWORD 'ride_password';
CREATE DATABASE ride_management_db OWNER ride_user;
GRANT ALL PRIVILEGES ON DATABASE ride_management_db TO ride_user;
```

---

### Phase 1 — Backend Initialization

Navigate into the backend directory and start the Spring Boot server using the bundled Maven wrapper.

**macOS / Linux:**

```bash
cd backend
./mvnw spring-boot:run
```

**Windows:**

```bat
cd backend
mvnw.cmd spring-boot:run
```

The backend API server and STOMP WebSocket broker will start at:

```
http://localhost:8099
```

Spring Boot will automatically create all database tables on first run.

---

### Phase 2 — Frontend Initialization

Open a second terminal window and navigate into the frontend directory.

```bash
cd frontend
npm install
npm run dev
```

The React Vite client terminal will start at:

```
http://localhost:5173
```

During local development, Vite proxies all `/api` and `/ws` traffic from port `5173` directly to the Spring Boot backend on port `8099`. No CORS configuration changes are needed.

---

## pgAdmin Setup (Optional)

pgAdmin is the recommended tool for inspecting database tables and manually editing campus map coordinates.

**Download pgAdmin:**

```
https://www.pgadmin.org/download/
```

**Register the local PostgreSQL server:**

```
Right-click Servers → Register → Server
```

General tab:

```
Name: Velocis Local DB
```

Connection tab:

```
Host name / address : localhost
Port                : 5432
Maintenance database: ride_management_db
Username            : ride_user
Password            : ride_password
```

**Open the Query Tool:**

```
Servers → Velocis Local DB → Databases → ride_management_db → Tools → Query Tool
```

**Insert a new campus location:**

```sql
INSERT INTO campus_locations (name, category, latitude, longitude)
VALUES ('Department of Computer Science', 'Academic', 29.864200, 77.896800);
```

**Update an existing campus location coordinate:**

```sql
UPDATE campus_locations
SET latitude = 29.864123,
    longitude = 77.897456
WHERE name = 'Central Library';
```

**View all seeded campus locations:**

```sql
SELECT * FROM campus_locations ORDER BY name;
```

---

## REST API Reference

All synchronous data mutations and account operations route through the HTTP REST controllers.

```
POST   /api/auth/register/passenger       Register a new passenger account
POST   /api/auth/register/driver          Register a new driver account with vehicle details
POST   /api/auth/login                    Authenticate and receive a signed JWT

GET    /api/users/me                      Retrieve the authenticated user's profile
PUT    /api/users/me                      Update profile fields

POST   /api/drivers/availability/online   Set driver status to ONLINE
POST   /api/drivers/availability/offline  Set driver status to OFFLINE
GET    /api/drivers/available             Retrieve all currently ONLINE drivers
GET    /api/drivers/requests              Retrieve all pending ride requests for a driver
GET    /api/drivers/dashboard             Retrieve driver analytics and ride history
POST   /api/drivers/location             Push a new GPS coordinate from the driver's device

GET    /api/locations                     Retrieve all seeded campus pickup and destination points

POST   /api/rides                         Submit a new ride request
GET    /api/rides/my                      Retrieve all rides for the authenticated user
GET    /api/rides/{rideId}                Retrieve full details for a specific ride
POST   /api/rides/{rideId}/accept         Atomically lock and accept a ride (prevents double-assignment)
POST   /api/rides/{rideId}/reject         Reject an incoming ride request
POST   /api/rides/{rideId}/start          Transition ride state to IN_PROGRESS
POST   /api/rides/{rideId}/complete       Mark ride as COMPLETED and log completion timestamp
POST   /api/rides/{rideId}/cancel         Cancel a ride request

POST   /api/ratings                       Submit a star rating and optional review for a completed ride
```

---

## WebSocket STOMP Topic Reference

The frontend establishes a persistent STOMP connection to:

```
ws://localhost:8099/ws
```

The following topics are used for all real-time event streaming:

| Topic | Type | Description |
|---|---|---|
| `/topic/drivers/availability` | Broadcast | Pushed to all connected clients whenever any driver changes their ONLINE / OFFLINE / BUSY state |
| `/topic/drivers/location` | Broadcast | Carries the latest GPS coordinate payload from any driver whose device is actively transmitting |
| `/topic/rides/requests` | Queue Broadcast | Delivered to all ONLINE drivers the moment a new passenger ride request is submitted |
| `/topic/rides/{rideId}` | Unicast | Streams ride lifecycle state transitions (ACCEPTED, IN_PROGRESS, COMPLETED, CANCELLED) directly to the requesting passenger's terminal |
| `/topic/rides/{rideId}/driver-location` | Live Stream | Carries continuous HTML5 Geolocation API heartbeat coordinates from the assigned driver's device, plotted in real time on the passenger's Leaflet map canvas |
| `/topic/users/{userId}/notifications` | Direct Target | Delivers transactional alert messages and assignment confirmations to a specific user's interface |
| `/topic/drivers/{driverId}/dashboard` | Direct Target | Pushes live dashboard metric updates to the driver's analytics panel |

---

## Build and Test

**Backend unit and integration tests:**

```bash
cd backend
./mvnw test
```

**Frontend production build:**

```bash
cd frontend
npm run build
```

---

## Notes

- Deployment to a cloud host is intentionally out of scope and was not part of the project challenge requirements.
- Drivers must grant browser location permission after going ONLINE for live GPS heartbeats to begin transmitting to the WebSocket broker.
- The concurrent ride acceptance lock is implemented using a Spring `@Transactional` block with a database row-level lock. If two drivers attempt to accept the same ride simultaneously, the second request receives an HTTP `409 Conflict` response and the ride remains assigned only to the first.
- The `backend/.env` file is gitignored. Never commit real database credentials, JWT secrets, or hosted connection strings to the repository.
- Additional features such as route polyline overlays, advance scheduling, payment gateway integration, and demand forecasting analytics can be integrated in future iterations without restructuring the core event-driven architecture.