# Product Requirements Document (PRD)
## Real-Time Bus GPS Tracking App + AI Route Optimization Engine

---

| Field            | Details                                          |
|------------------|--------------------------------------------------|
| **Product Name** | BusTrack AI                                      |
| **Version**      | 1.0.0                                            |
| **Date**         | March 9, 2026                                    |
| **Status**       | Draft                                            |
| **Prepared By**  | Product & Engineering Team                       |
| **Stakeholders** | School Admins, Transport Managers, Parents, Students, Drivers |

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Problem Statement](#2-problem-statement)
3. [Goals & Objectives](#3-goals--objectives)
4. [Scope](#4-scope)
5. [Stakeholders & User Personas](#5-stakeholders--user-personas)
6. [Functional Requirements](#6-functional-requirements)
7. [Non-Functional Requirements](#7-non-functional-requirements)
8. [System Architecture](#8-system-architecture)
9. [Technology Stack](#9-technology-stack)
10. [AI/ML Engine Specification](#10-aiml-engine-specification)
11. [GPS & Telemetry Layer](#11-gps--telemetry-layer)
12. [API Contracts](#12-api-contracts)
13. [Data Models](#13-data-models)
14. [UI/UX Requirements](#14-uiux-requirements)
15. [Security Requirements](#15-security-requirements)
16. [Release Milestones](#16-release-milestones)
17. [Risks & Mitigations](#17-risks--mitigations)
18. [Appendix](#18-appendix)

---

## 1. Executive Summary

**BusTrack AI** is a full-stack, AI-powered school bus management platform that provides real-time GPS tracking, intelligent route optimization, and automated delay notifications. The system serves students, parents, school administrators, and bus drivers through a seamless mobile application backed by a high-availability Node.js infrastructure and a dedicated AI/ML route optimization engine.

The platform ingests live telemetry data from GPS hardware units installed on buses, processes it through a real-time backend pipeline, and delivers enriched insights — including ETA predictions, geofence alerts, and dynamically optimized routes — to all stakeholders via a Flutter-based mobile app.

The AI/ML engine solves the Capacitated Vehicle Routing Problem with Time Windows (CVRPTW) to minimize fleet travel time and fuel consumption, adapting routes daily based on live traffic patterns and student attendance data.

---

## 2. Problem Statement

School transportation remains one of the most operationally inefficient and anxiety-inducing aspects of modern education management:

- **Parents have zero visibility** into where their child's bus is, leading to unnecessary waiting, missed buses, and safety concerns.
- **Schools operate with static routes** that do not account for real-time traffic, resulting in avoidable delays and wasted fuel.
- **Drivers receive no intelligent assistance** for multi-stop routing, leading to inconsistent pickup times.
- **Administrators lack a unified operations view**, making fleet oversight reactive rather than proactive.
- **Delays and breakdowns go uncommunicated**, causing cascading disruptions across the school day.

There is no single, integrated product that solves tracking, optimization, and communication in one platform — this is the gap BusTrack AI fills.

---

## 3. Goals & Objectives

### Primary Goals
| # | Goal | Success Metric |
|---|------|----------------|
| G1 | Provide real-time bus tracking to parents and students | GPS position updated every 5 seconds on map |
| G2 | Deliver accurate ETA predictions per bus stop | ETA accuracy within ±2 minutes in 90% of trips |
| G3 | Reduce fleet fuel consumption via AI route optimization | 20%+ reduction in total daily route kilometers |
| G4 | Send timely push alerts for delays and critical events | Notification delivered within 10 seconds of trigger |
| G5 | Provide administrators a live fleet operations dashboard | 100% fleet visibility from single dashboard screen |

### Secondary Goals
- Reduce parent-to-school inquiry calls by 60%.
- Enable paperless digital boarding records.
- Support multi-school / multi-fleet deployment from a single platform instance.

---

## 4. Scope

### In Scope (v1.0)
- Flutter mobile app for Android & iOS (Parent, Student, Driver roles).
- Node.js backend: REST API, WebSocket server, and Message Queue pipeline.
- AI/ML route optimization engine (Python microservice).
- GPS telemetry ingestion and real-time position broadcast.
- Push notification system (FCM).
- Admin web dashboard (React.js).
- Geofencing for stop-proximity alerts.
- Role-based access control (Parent / Student / Driver / Admin).

### Out of Scope (v1.0 — Future Roadmap)
- Payment gateway for transport fee collection.
- In-app driver–parent chat.
- Wearable device integration.
- AI-powered CCTV inside buses.
- Public transit / non-school use cases.

---

## 5. Stakeholders & User Personas

### Persona 1 — Parent (Primary User)
> **Priya Sharma**, 38, mother of two school-going children.
- **Needs:** Know when the bus will arrive, get alerted if the bus is delayed, confirm her child boarded safely.
- **Frustration:** Currently calls the school 3–4 times a week to ask about bus location.
- **Device:** Android smartphone.

### Persona 2 — Student (Secondary User)
> **Arjun, 14**, high school student with an afternoon sports practice.
- **Needs:** See bus ETA to know how much time he has before it arrives.
- **Device:** iOS smartphone.

### Persona 3 — Bus Driver
> **Ramesh Kumar**, 45, drives a 42-seat school bus with 30 student stops daily.
- **Needs:** Turn-by-turn optimized route, student list per stop, real-time traffic warnings.
- **Device:** Android smartphone mounted on dashboard.

### Persona 4 — Transport Administrator
> **Mrs. Anitha**, 52, manages a fleet of 12 buses across 3 school branches.
- **Needs:** Live map of all buses, daily route reports, fuel usage analytics, ability to reassign routes.
- **Device:** Desktop browser + mobile for on-the-go alerts.

---

## 6. Functional Requirements

### 6.1 Authentication & Authorization

| ID | Requirement |
|----|-------------|
| FR-AUTH-01 | Users shall register and log in using email/password with JWT-based session management. |
| FR-AUTH-02 | OTP verification via SMS/email shall be required on first login. |
| FR-AUTH-03 | Role-based access: Parent, Student, Driver, Admin — each with isolated views and permissions. |
| FR-AUTH-04 | Session tokens shall expire after 24 hours; refresh tokens valid for 30 days. |
| FR-AUTH-05 | Admin accounts shall support 2FA via TOTP (e.g., Google Authenticator). |

---

### 6.2 GPS Tracking & Telemetry

| ID | Requirement |
|----|-------------|
| FR-GPS-01 | The GPS hardware unit on each bus shall broadcast telemetry (latitude, longitude, speed, heading, timestamp) every 5 seconds. |
| FR-GPS-02 | The driver app shall serve as a fallback telemetry source if the GPS hardware is offline. |
| FR-GPS-03 | The backend shall ingest GPS streams via MQTT over TLS. |
| FR-GPS-04 | Last known position shall be stored in Redis with a TTL of 60 seconds for live queries. |
| FR-GPS-05 | Full position history shall be persisted to a time-series database for replay and analytics. |
| FR-GPS-06 | The system shall detect GPS signal loss and alert the admin if a bus goes silent for more than 30 seconds. |
| FR-GPS-07 | Bus speed shall be monitored; alerts triggered if speed exceeds 60 km/h in a school zone. |

---

### 6.3 Real-Time Map (Mobile App — Parent/Student)

| ID | Requirement |
|----|-------------|
| FR-MAP-01 | The mobile map shall display the live position of the tracked bus with a moving animated marker. |
| FR-MAP-02 | The route path (planned vs. traveled) shall be rendered as overlay polylines. |
| FR-MAP-03 | All stops on the route shall be rendered as map markers with stop name and sequence. |
| FR-MAP-04 | The map shall auto-pan to keep the bus marker centered as it moves. |
| FR-MAP-05 | A collapsible bottom card shall show: Bus ID, Driver Name, ETA to next stop, current speed. |
| FR-MAP-06 | Parents shall only see the bus assigned to their child's route. |

---

### 6.4 ETA Prediction

| ID | Requirement |
|----|-------------|
| FR-ETA-01 | The system shall compute and serve ETA for each upcoming stop in real time. |
| FR-ETA-02 | ETA shall be updated every 15 seconds based on current GPS position and live traffic data. |
| FR-ETA-03 | ETA algorithm shall account for: distance remaining, current speed, historical traffic patterns, and scheduled stop dwell time. |
| FR-ETA-04 | ETA shall be displayed in human-readable format: "Arriving in 4 min" or "At Stop". |

---

### 6.5 Push Notifications & Alerts

| ID | Requirement |
|----|-------------|
| FR-NOTIF-01 | Push notifications shall be delivered via Firebase Cloud Messaging (FCM). |
| FR-NOTIF-02 | Notification triggers shall include: bus departing depot, bus approaching stop (500m geofence), delay >5 minutes, route deviation, bus breakdown flag, trip completed. |
| FR-NOTIF-03 | Parents shall receive a "Bus is 2 stops away" alert as a pre-arrival warning. |
| FR-NOTIF-04 | All notifications shall be logged and viewable in an in-app notification history. |
| FR-NOTIF-05 | Users shall be able to configure notification preferences (mute certain alert types). |

---

### 6.6 AI Route Optimization

| ID | Requirement |
|----|-------------|
| FR-ROUTE-01 | The AI engine shall generate optimized routes for the fleet every morning before the first trip begins. |
| FR-ROUTE-02 | Optimization shall accept: student pickup coordinates, school location, bus capacity, school start time, and live traffic data as inputs. |
| FR-ROUTE-03 | The optimizer shall solve the CVRPTW problem to minimize total travel time and distance. |
| FR-ROUTE-04 | Optimized routes shall be pushed to the driver app before trip start. |
| FR-ROUTE-05 | In the case of unexpected congestion, the engine shall re-optimize the affected route and push an updated route within 90 seconds. |
| FR-ROUTE-06 | The admin shall be able to manually lock or override any AI-generated route. |
| FR-ROUTE-07 | Route optimization results shall include: stop sequence, estimated leg durations, total distance, and CO₂ savings estimate. |

---

### 6.7 Driver App

| ID | Requirement |
|----|-------------|
| FR-DRV-01 | The driver app shall display the turn-by-turn optimized route with map navigation. |
| FR-DRV-02 | Each stop shall show: student names boarding/alighting, expected arrival time, and boarding confirmation toggle. |
| FR-DRV-03 | The driver shall mark trip start/end explicitly via the app. |
| FR-DRV-04 | Drivers shall be able to flag an emergency (breakdown, incident) with a single tap. |
| FR-DRV-05 | The app shall work in low-connectivity areas by queuing telemetry data locally and syncing when connection restores. |

---

### 6.8 Admin Dashboard

| ID | Requirement |
|----|-------------|
| FR-ADMIN-01 | The dashboard shall display a live map of all active buses with real-time position updates. |
| FR-ADMIN-02 | Admins shall be able to view the full route and student list for any bus. |
| FR-ADMIN-03 | Fleet-level analytics available: total km per bus, average delay per route, fuel efficiency score. |
| FR-ADMIN-04 | Admins shall be able to add/remove students, assign them to routes, and update pickup addresses. |
| FR-ADMIN-05 | Export capability for daily trip reports in PDF and CSV format. |
| FR-ADMIN-06 | Admin shall receive broadcast alert if any bus triggers an emergency flag. |

---

## 7. Non-Functional Requirements

### 7.1 Performance
| ID | Requirement |
|----|-------------|
| NFR-PERF-01 | GPS position updates shall be reflected on the client map within **< 1 second** of ingestion. |
| NFR-PERF-02 | REST API response time shall be **< 200ms** for 95th percentile under normal load. |
| NFR-PERF-03 | The backend shall support **1,000 concurrent WebSocket connections** without degradation. |
| NFR-PERF-04 | Route optimization for 30 stops shall complete within **< 10 seconds**. |

### 7.2 Availability & Reliability
| ID | Requirement |
|----|-------------|
| NFR-REL-01 | System uptime target: **99.9% SLA** (excluding scheduled maintenance). |
| NFR-REL-02 | GPS telemetry ingestion shall be fault-tolerant with message queue persistence (no data loss on server restart). |
| NFR-REL-03 | The system shall gracefully degrade — if the AI engine is unavailable, previously optimized routes shall be served from cache. |

### 7.3 Scalability
| ID | Requirement |
|----|-------------|
| NFR-SCALE-01 | Architecture shall horizontally scale to support **100+ buses** per deployment. |
| NFR-SCALE-02 | Multi-tenancy support shall allow multiple school organizations on a single platform instance. |

### 7.4 Security
| ID | Requirement |
|----|-------------|
| NFR-SEC-01 | All data in transit shall be encrypted with **TLS 1.3**. |
| NFR-SEC-02 | At-rest encryption for all PII (student names, addresses, parent contacts). |
| NFR-SEC-03 | API endpoints shall enforce rate limiting (100 req/min per user). |
| NFR-SEC-04 | JWT tokens shall be signed with RS256. |

### 7.5 Usability
| ID | Requirement |
|----|-------------|
| NFR-UX-01 | The app shall reach active map view within **3 taps** from launch. |
| NFR-UX-02 | The app shall be fully functional in **offline mode** for viewing last-known bus position and cached route info. |
| NFR-UX-03 | App UI shall support both **light and dark mode**. |
| NFR-UX-04 | Supported languages: **English, Hindi** (v1.0); extensible for more via i18n. |

---

## 8. System Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         BusTrack AI — System Architecture               │
└─────────────────────────────────────────────────────────────────────────┘

  ┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
  │  Flutter Mobile │       │  Flutter Driver │       │  React Admin    │
  │  App            │       │  App            │       │  Dashboard      │
  │  (Parent/Student│       │  (Bus Driver)   │       │  (Web)          │
  └────────┬────────┘       └────────┬────────┘       └────────┬────────┘
           │  REST / WebSocket        │  MQTT + REST            │  REST / WS
           │                         │                          │
           └─────────────────────────┼──────────────────────────┘
                                     │
                         ┌───────────▼────────────┐
                         │      API Gateway        │
                         │  (Rate Limit + Auth)    │
                         └───────────┬─────────────┘
                                     │
              ┌──────────────────────┼──────────────────────┐
              │                      │                      │
   ┌──────────▼──────────┐ ┌─────────▼─────────┐ ┌────────▼─────────┐
   │  REST API Server    │ │  WebSocket Server  │ │  MQTT Broker     │
   │  (Node.js/Express)  │ │  (Socket.IO)       │ │  (Mosquitto)     │
   │  - Auth             │ │  - Live GPS push   │ │  - GPS Telemetry │
   │  - Routes           │ │  - ETA broadcast   │ │    Ingestion     │
   │  - Students         │ │  - Notifications   │ └────────┬─────────┘
   │  - Admin ops        │ └────────────────────┘          │
   └──────────┬──────────┘          │                      │
              │                     │                      │
              └─────────────────────┼──────────────────────┘
                                    │
                        ┌───────────▼────────────┐
                        │    Message Queue        │
                        │    (Redis / BullMQ)     │
                        │  - GPS stream relay     │
                        │  - Notification jobs    │
                        │  - Route opt. trigger   │
                        └───────────┬─────────────┘
                                    │
              ┌─────────────────────┼─────────────────────┐
              │                     │                     │
   ┌──────────▼──────────┐ ┌────────▼────────┐ ┌─────────▼────────┐
   │   PostgreSQL +      │ │  Redis Cache    │ │  TimescaleDB     │
   │   PostGIS           │ │  (Live GPS,     │ │  (GPS History,   │
   │  (Core Data Store)  │ │   Sessions,     │ │   Trip Logs,     │
   │                     │ │   ETA cache)    │ │   Analytics)     │
   └─────────────────────┘ └─────────────────┘ └──────────────────┘
                                    │
                        ┌───────────▼────────────┐
                        │   AI/ML Engine          │
                        │   (Python / FastAPI)    │
                        │  - CVRPTW Solver        │
                        │    (Google OR-Tools)    │
                        │  - ETA Predictor        │
                        │    (XGBoost)            │
                        │  - Traffic Analyzer     │
                        └───────────┬─────────────┘
                                    │
                        ┌───────────▼────────────┐
                        │   GPS Telemetry Layer   │
                        │  - GPS Hardware Units   │
                        │    (on-bus devices)     │
                        │  - Telemetry Collector  │
                        │  - Leaflet.js Map API   │
                        │    (Geospatial engine)  │
                        └────────────────────────┘
```

### Architecture Layers

| Layer | Responsibility |
|-------|---------------|
| **Client Layer** | Flutter mobile apps (Parent/Student/Driver) + React Admin Web |
| **API Gateway** | Authentication enforcement, rate limiting, request routing |
| **Application Layer** | Node.js REST API + Socket.IO WebSocket server |
| **Messaging Layer** | Redis Pub/Sub + BullMQ for async job processing |
| **Data Layer** | PostgreSQL (relational), Redis (cache), TimescaleDB (time-series) |
| **AI Engine** | Python FastAPI microservice for route optimization and ETA prediction |
| **Telemetry Layer** | GPS hardware telemetry + Leaflet.js geospatial rendering |

---

## 9. Technology Stack

### 9.1 Mobile Application (Flutter / Dart)

| Component | Technology | Details |
|-----------|-----------|---------|
| **Framework** | Flutter 3.x (Dart) | Single codebase for Android & iOS |
| **State Management** | Riverpod 2.x | Reactive, scalable state management |
| **Maps & Geospatial** | `flutter_map` + Leaflet tiles | Interactive map with bus markers, route overlays |
| **Real-Time** | `socket_io_client` | WebSocket connection to backend for live updates |
| **REST Client** | `dio` | HTTP client with interceptors for auth tokens |
| **Push Notifications** | `firebase_messaging` | FCM push alerts |
| **Local Storage** | `hive` | Offline caching of route and bus data |
| **Navigation** | `go_router` | Declarative routing |
| **Secure Storage** | `flutter_secure_storage` | JWT token storage |
| **Background Location** | `geolocator` + `background_locator` | Driver app GPS broadcasting |
| **Build Targets** | Android 6.0+ / iOS 13.0+ | Wide device coverage |

---

### 9.2 Backend (Node.js)

| Component | Technology | Details |
|-----------|-----------|---------|
| **Runtime** | Node.js 20 LTS (TypeScript) | High-concurrency I/O for GPS streams |
| **REST Framework** | Express.js 5.x | RESTful API endpoints |
| **WebSocket** | Socket.IO 4.x | Real-time GPS broadcasting to app clients |
| **MQTT Broker** | Mosquitto + `mqtt.js` | GPS telemetry ingestion over MQTT/TLS |
| **Message Queue** | BullMQ + Redis | Async job processing (notifications, route triggers) |
| **Auth** | `jsonwebtoken` + `bcrypt` | JWT RS256 token issuance and validation |
| **ORM** | Prisma | Type-safe PostgreSQL queries |
| **Validation** | Zod | Runtime schema validation on all inputs |
| **Logging** | Winston + Morgan | Structured request and application logging |
| **Testing** | Jest + Supertest | Unit and integration testing |
| **Process Manager** | PM2 | Zero-downtime restarts, clustering |

---

### 9.3 AI/ML Engine (Python)

| Component | Technology | Details |
|-----------|-----------|---------|
| **API Framework** | FastAPI | High-performance REST exposure of ML services |
| **VRP Solver** | Google OR-Tools | CVRPTW route optimization |
| **ETA Predictor** | XGBoost | Regression model trained on trip history |
| **Traffic Data** | Leaflet + OpenStreetMap Routing | Road network and traffic-aware distances |
| **Matrix Computation** | NumPy / Pandas | Distance and time matrix generation |
| **Model Serving** | `joblib` | Serialized model persistence and loading |

---

### 9.4 GPS Telemetry Layer

| Component | Technology | Details |
|-----------|-----------|---------|
| **GPS Hardware** | On-bus embedded GPS units | MQTT telemetry over 4G LTE/GSM |
| **Telemetry Protocol** | MQTT over TLS | Lightweight pub/sub for IoT-grade reliability |
| **Geospatial Rendering** | Leaflet.js / `flutter_map` | Map rendering, polylines, stop markers, geofencing |
| **Geofence Engine** | PostGIS `ST_DWithin` | Server-side proximity detection for stop alerts |
| **Coordinate System** | WGS84 (EPSG:4326) | Standard GPS coordinate reference system |

---

### 9.5 Infrastructure

| Component | Technology |
|-----------|-----------|
| **Cloud Platform** | AWS (EC2, RDS, ElastiCache, SNS) |
| **Containerization** | Docker + Docker Compose |
| **Orchestration** | Kubernetes (EKS) |
| **CI/CD** | GitHub Actions |
| **Monitoring** | Grafana + Prometheus |
| **Log Management** | ELK Stack |
| **CDN** | AWS CloudFront |
| **Object Storage** | AWS S3 (exports, driver documents) |

---

## 10. AI/ML Engine Specification

### 10.1 Route Optimizer — CVRPTW Solver

**Problem Definition:**
Given a set of $n$ student pickup locations $\{s_1, s_2, ..., s_n\}$, a fleet of $k$ buses each with capacity $C$, a school depot location $D$, and a school start time $T_{school}$, find routes $R_1, R_2, ..., R_k$ such that:

$$\text{Minimize} \sum_{i=1}^{k} \sum_{j=0}^{|R_i|} d(R_i[j], R_i[j+1])$$

Subject to:
- Each student $s_i$ is visited exactly once.
- $|R_k| \leq C$ (bus capacity constraint).
- All routes terminate at depot $D$ before $T_{school}$.
- Time windows per stop are respected.

**Solver:** Google OR-Tools `pywrapcp` with `AUTOMATIC` first solution strategy and `GUIDED_LOCAL_SEARCH` metaheuristic.

**Traffic Matrix:** Distance/time matrix generated using OpenStreetMap road network with time-of-day traffic weights.

**Optimization Schedule:**
- **Pre-trip (06:00 daily):** Full fleet route optimization.
- **Dynamic re-route:** Triggered when a bus is delayed >5 minutes; re-optimizes remaining stops on that route.

---

### 10.2 ETA Predictor

**Model:** XGBoost Regressor

**Input Features:**

| Feature | Description |
|---------|-------------|
| `remaining_distance_km` | Distance from current position to target stop |
| `current_speed_kmh` | Live bus speed from GPS |
| `stops_remaining` | Number of stops before target |
| `hour_of_day` | Time-of-day feature (traffic proxy) |
| `day_of_week` | Weekday traffic pattern |
| `avg_dwell_time_sec` | Historical average stop dwell time |
| `weather_condition` | Clear / Rain / Fog encoding |

**Output:** Predicted arrival time in seconds from current timestamp.

**Training Data:** Historical trip logs from TimescaleDB — minimum 60-day trip history.

**Model Refresh:** Retrained weekly via scheduled BullMQ job.

---

## 11. GPS & Telemetry Layer

### 11.1 GPS Hardware Unit

Each bus operates with a dedicated GPS telemetry unit configured as follows:

| Parameter | Value |
|-----------|-------|
| **Transmission Protocol** | MQTT over TLS 1.3 |
| **Update Frequency** | Every 5 seconds (configurable: 3–10s) |
| **Connectivity** | 4G LTE with GSM fallback |
| **Payload Format** | JSON over MQTT topic `bus/{busId}/telemetry` |

**Telemetry Payload Schema:**
```json
{
  "busId": "BUS-042",
  "routeId": "ROUTE-07",
  "lat": 12.971598,
  "lng": 77.594563,
  "speed": 34.5,
  "heading": 245,
  "altitude": 920.0,
  "satellites": 9,
  "accuracy": 2.4,
  "timestamp": "2026-03-09T07:23:41.000Z",
  "ignition": true,
  "batteryVoltage": 12.6
}
```

### 11.2 Geospatial Engine (Leaflet.js + PostGIS)

The geospatial rendering layer handles all map-based operations:

| Feature | Implementation |
|---------|---------------|
| **Live bus marker** | Leaflet animated marker updated via WebSocket |
| **Route polyline** | GeoJSON LineString rendered as planned vs. traveled overlays |
| **Stop markers** | Leaflet circle markers with popup (stop name, ETA) |
| **Geofence trigger** | PostGIS `ST_DWithin(bus_position, stop_point, 500)` — evaluated on each GPS event |
| **Heatmap (admin)** | Leaflet.heat plugin for historical route density visualization |
| **Map tiles** | OpenStreetMap tile server (self-hosted for production) |

---

## 12. API Contracts

### 12.1 REST API — Key Endpoints

#### Authentication
```
POST   /api/v1/auth/register          Register new user
POST   /api/v1/auth/login             Login, returns JWT + refresh token
POST   /api/v1/auth/refresh           Refresh access token
POST   /api/v1/auth/logout            Invalidate session
POST   /api/v1/auth/verify-otp        OTP verification
```

#### Buses & Routes
```
GET    /api/v1/buses                  List all buses (Admin)
GET    /api/v1/buses/:busId           Get bus details
GET    /api/v1/buses/:busId/location  Get last known GPS position
GET    /api/v1/routes                 List all routes
GET    /api/v1/routes/:routeId        Get full route with stops
GET    /api/v1/routes/:routeId/eta    Get ETA for all stops on route
POST   /api/v1/routes/optimize        Trigger AI route optimization (Admin)
```

#### Students & Assignments
```
GET    /api/v1/students               List students (Admin)
POST   /api/v1/students               Add student
PUT    /api/v1/students/:id           Update student/pickup address
DELETE /api/v1/students/:id           Remove student
GET    /api/v1/students/:id/bus       Get assigned bus & route
```

#### Trips
```
GET    /api/v1/trips                  List trips (Admin, filterable by date/bus)
GET    /api/v1/trips/:tripId          Get trip detail + stop log
POST   /api/v1/trips/:tripId/start    Driver marks trip start
POST   /api/v1/trips/:tripId/end      Driver marks trip end
POST   /api/v1/trips/:tripId/stop/:stopId/board   Mark student boarding
```

#### Notifications
```
GET    /api/v1/notifications          Get notification history for user
PUT    /api/v1/notifications/prefs    Update notification preferences
```

---

### 12.2 WebSocket Events (Socket.IO)

#### Client → Server
| Event | Payload | Description |
|-------|---------|-------------|
| `subscribe:bus` | `{ busId: string }` | Subscribe to live updates for a bus |
| `unsubscribe:bus` | `{ busId: string }` | Unsubscribe from bus updates |
| `driver:telemetry` | `TelemetryPayload` | Driver app sends GPS position (fallback) |

#### Server → Client
| Event | Payload | Description |
|-------|---------|-------------|
| `bus:position` | `{ busId, lat, lng, speed, heading, timestamp }` | Live GPS position broadcast (every 5s) |
| `bus:eta` | `{ busId, stops: [{stopId, eta, minutesAway}] }` | Updated ETA for all stops |
| `alert:delay` | `{ busId, routeId, delayMinutes, message }` | Delay alert broadcast |
| `alert:geofence` | `{ busId, stopId, stopName, message }` | Bus near stop geofence trigger |
| `alert:emergency` | `{ busId, driverName, lat, lng, type }` | Emergency flag broadcast |
| `route:updated` | `{ routeId, newStopSequence }` | Updated optimized route push |

---

## 13. Data Models

### 13.1 Core Entities (PostgreSQL + PostGIS)

```sql
-- Users table
CREATE TABLE users (
    id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name          VARCHAR(100) NOT NULL,
    email         VARCHAR(150) UNIQUE NOT NULL,
    phone         VARCHAR(20),
    password_hash TEXT NOT NULL,
    role          ENUM('parent', 'student', 'driver', 'admin') NOT NULL,
    is_verified   BOOLEAN DEFAULT FALSE,
    created_at    TIMESTAMPTZ DEFAULT NOW()
);

-- Buses table
CREATE TABLE buses (
    id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    fleet_number  VARCHAR(20) UNIQUE NOT NULL,
    registration  VARCHAR(20) UNIQUE NOT NULL,
    capacity      INT NOT NULL,
    driver_id     UUID REFERENCES users(id),
    is_active     BOOLEAN DEFAULT TRUE,
    created_at    TIMESTAMPTZ DEFAULT NOW()
);

-- Routes table
CREATE TABLE routes (
    id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name          VARCHAR(100) NOT NULL,
    bus_id        UUID REFERENCES buses(id),
    school_id     UUID,
    is_optimized  BOOLEAN DEFAULT FALSE,
    optimized_at  TIMESTAMPTZ,
    total_distance_km   DECIMAL(8,2),
    estimated_duration_min INT,
    created_at    TIMESTAMPTZ DEFAULT NOW()
);

-- Stops table
CREATE TABLE stops (
    id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    route_id      UUID REFERENCES routes(id),
    name          VARCHAR(100) NOT NULL,
    sequence      INT NOT NULL,
    location      GEOGRAPHY(POINT, 4326) NOT NULL,
    scheduled_time TIME,
    radius_meters INT DEFAULT 100
);

-- Students table
CREATE TABLE students (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id         UUID REFERENCES users(id),
    parent_id       UUID REFERENCES users(id),
    grade           VARCHAR(10),
    stop_id         UUID REFERENCES stops(id),
    pickup_location GEOGRAPHY(POINT, 4326),
    created_at      TIMESTAMPTZ DEFAULT NOW()
);

-- Trips table
CREATE TABLE trips (
    id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    route_id      UUID REFERENCES routes(id),
    bus_id        UUID REFERENCES buses(id),
    driver_id     UUID REFERENCES users(id),
    start_time    TIMESTAMPTZ,
    end_time      TIMESTAMPTZ,
    status        ENUM('scheduled', 'in_progress', 'completed', 'cancelled'),
    total_km      DECIMAL(8,2),
    created_at    TIMESTAMPTZ DEFAULT NOW()
);
```

### 13.2 Time-Series (TimescaleDB)
```sql
-- GPS telemetry hypertable
CREATE TABLE gps_telemetry (
    time          TIMESTAMPTZ NOT NULL,
    bus_id        UUID NOT NULL,
    lat           DOUBLE PRECISION,
    lng           DOUBLE PRECISION,
    speed         REAL,
    heading       SMALLINT,
    accuracy      REAL,
    trip_id       UUID
);
SELECT create_hypertable('gps_telemetry', 'time');
CREATE INDEX ON gps_telemetry (bus_id, time DESC);
```

---

## 14. UI/UX Requirements

### 14.1 App Screens — Parent/Student

| Screen | Key Elements |
|--------|-------------|
| **Splash / Onboarding** | Animated logo, role selection (Parent / Student) |
| **Login** | Email/password, social sign-in, OTP flow |
| **Home / Map** | Full-screen map, live bus marker, route polyline, stop markers, bottom ETA card |
| **Bus Detail** | Assigned bus info, driver profile, stop list with ETAs, trip status timeline |
| **Notifications** | Chronological alert list with type icons and deep-link to map |
| **Settings** | Profile management, notification preferences, language switch |

### 14.2 App Screens — Driver

| Screen | Key Elements |
|--------|-------------|
| **Trip Dashboard** | Today's route summary, start trip CTA, student count |
| **Navigation** | Turn-by-turn map, next stop highlighted, student list per stop |
| **Stop Arrival** | Student boarding checklist, mark arrived/departed CTA |
| **Emergency** | Single-tap SOS button with incident type selector |

### 14.3 Design System

| Token | Value |
|-------|-------|
| **Primary Color** | `#1A73E8` (Google Blue) |
| **Secondary Color** | `#34A853` (Bus active green) |
| **Alert Color** | `#EA4335` (Critical red) |
| **Warning Color** | `#FBBC04` (Delay amber) |
| **Font** | Inter (primary), Roboto Mono (data values) |
| **Border Radius** | 12px (cards), 8px (buttons) |
| **Map Style** | Light (day) / Dark (night) — auto based on system |

---

## 15. Security Requirements

| Category | Requirement |
|----------|-------------|
| **Transport Security** | TLS 1.3 on all endpoints; HSTS headers enforced |
| **API Security** | JWT RS256 authentication; rate limiting per IP and per user |
| **Data Privacy** | Student PII encrypted at rest (AES-256); parents can only access their own child's data |
| **Input Validation** | All inputs validated server-side via Zod schemas; parameterized queries only (no raw SQL) |
| **GPS Data** | Location data access scoped strictly to assigned route — no cross-route visibility |
| **Admin Access** | TOTP-based 2FA mandatory for all admin accounts |
| **Audit Logging** | All admin actions logged to immutable audit trail |
| **Dependency Security** | `npm audit` and `safety` (Python) run in CI pipeline on every PR |

---

## 16. Release Milestones

### Sprint Plan (2-Week Sprints)

| Milestone | Sprint # | Deliverables |
|-----------|----------|-------------|
| **M1 — Foundation** | Sprint 1–2 | Project setup, auth system, DB schema, CI/CD pipeline |
| **M2 — GPS Core** | Sprint 3–4 | MQTT telemetry ingestion, Redis caching, WebSocket GPS broadcast |
| **M3 — Mobile MVP** | Sprint 5–6 | Flutter app: login, live map, bus tracking, ETA display |
| **M4 — Notifications** | Sprint 7 | FCM push alerts, geofencing, delay detection |
| **M5 — Driver App** | Sprint 8 | Driver navigation screen, boarding checklist, trip start/end |
| **M6 — AI Engine** | Sprint 9–10 | OR-Tools CVRPTW optimizer, ETA ML model, FastAPI service |
| **M7 — Admin Dashboard** | Sprint 11 | React web dashboard, fleet map, analytics, route management |
| **M8 — Hardening** | Sprint 12 | Performance testing, security audit, bug fixes |
| **M9 — Beta Launch** | Sprint 13 | Closed beta with 2 schools, feedback collection |
| **M10 — v1.0 Release** | Sprint 14 | Production deployment, monitoring setup, documentation |

---

## 17. Risks & Mitigations

| # | Risk | Likelihood | Impact | Mitigation |
|---|------|-----------|--------|------------|
| R1 | GPS signal loss in underground/covered routes | Medium | High | Driver app as fallback telemetry source; dead-reckoning via last known speed/heading |
| R2 | AI optimization timeout for large fleets | Low | Medium | Timeout at 8s; return best solution found so far via OR-Tools time limit flag |
| R3 | Push notification delivery failure | Low | Medium | In-app notification backup; webhook retry mechanism via BullMQ |
| R4 | High concurrent WebSocket connections causing memory pressure | Medium | High | Horizontal scaling via Redis adapter + Socket.IO clustering |
| R5 | Student PII data breach | Low | Critical | Field-level encryption, strict RBAC, regular penetration testing |
| R6 | Route optimization producing suboptimal results on first run | Medium | Low | Warm-start with nearest-neighbor heuristic; admin can override any AI route |

---

## 18. Appendix

### A. Glossary

| Term | Definition |
|------|-----------|
| **CVRPTW** | Capacitated Vehicle Routing Problem with Time Windows — the NP-hard optimization problem the AI engine solves |
| **MQTT** | Message Queuing Telemetry Transport — lightweight pub/sub protocol for IoT telemetry |
| **Geofence** | A virtual geographic perimeter that triggers an event when a GPS device enters or exits |
| **ETA** | Estimated Time of Arrival |
| **WGS84** | World Geodetic System 1984 — standard GPS coordinate reference system |
| **FCM** | Firebase Cloud Messaging — Google's cross-platform push notification service |
| **TTL** | Time-To-Live — cache expiry duration |
| **OR-Tools** | Google's open-source Operations Research optimization toolkit |
| **BullMQ** | Redis-backed job queue library for Node.js |
| **PostGIS** | Spatial extension for PostgreSQL enabling geospatial queries |

### B. References

- Google OR-Tools Documentation: https://developers.google.com/optimization
- Flutter Official Docs: https://flutter.dev/docs
- Socket.IO Protocol: https://socket.io/docs/v4
- Leaflet.js Mapping: https://leafletjs.com
- Firebase Cloud Messaging: https://firebase.google.com/docs/cloud-messaging
- MQTT Protocol Spec (v5.0): https://docs.oasis-open.org/mqtt/mqtt/v5.0

---

*Document Status: DRAFT v1.0 | Review Cycle: Bi-weekly | Owner: Product & Engineering Team*
*Last Updated: March 9, 2026*
