# room-booking

App lets students book empty rooms safely while locking taken slots. Professors can also schedule lectures. Built with Spring Boot and PostgreSQL.

## Team

| Name | Contributions |
| :--- | :--- |
| **Turgunbay Meiirbek** | Container diagram, repository setup: readme, maven and spring boot configuration (pom.xml) |
| **Daulet Aladi** | Architecture choice: one selected style and one rejected alternative |
| **Jubanissov Daniyar** | ADR 1: context, decision, alternatives, consequences |

---

## Container Diagram (C4 Level 2)

```text
+-------------------------------------------------------------------------------+
|                            Room Booking System                                |
|                                                                               |
|  +---------------------------+        +------------------------------------+  |
|  | Web / Mobile Client       |        | Backend API (Spring Boot)          |  |
|  | (Student & Professor UI)  |------->| - Booking / Lock Management        |  |
|  +---------------------------+  HTTPS | - Room & Schedule Management       |  |
|                                       | - Auth & User Roles                |  |
|                                       +------------------------------------+  |
|                                                          |                    |
+----------------------------------------------------------|--------------------+
                                                           | JDBC
                                                           v
                                                +--------------------+
                                                | Database           |
                                                | (PostgreSQL)       |
                                                +--------------------+
```
---
### Container Responsibilities

- **Web / Mobile Client:** Student and professor interface for searching empty rooms, locking slots, and scheduling lectures.
- **Backend API (Spring Boot):** Handles business rules, slot locking logic, transaction coordination, and room management.
- **Database (PostgreSQL):** Persists room status, slot reservations, user credentials, and lecture schedules.

---

## Architecture Choice

- **Selected Style: Modular Monolith**
  - **Rationale:** Since the project is managed by a small team and deployed as a single unit, a Modular Monolith provides clear boundary enforcement between domains (Booking, Rooms, Users) without the operational overhead and network latency of distributed systems.
- **Rejected Alternative: Microservices Architecture**
  - **Rationale:** Introducing microservices at this stage adds unnecessary complexity regarding distributed transactions, deployment configuration, and service-to-service latency.

---

## ADR 1: Concurrent Room Booking and Slot Locking

### Status:Accepted

### Details
- **Context:** High peak-time traffic causes multiple students to attempt booking the exact same room slot simultaneously. The key priority is preventing double-booking race conditions.
- **Decision:** Applied database-level pessimistic locking (`PESSIMISTIC_WRITE`) alongside Spring Boot transactional boundaries (`@Transactional`) during slot reservation.
- **Alternatives Considered:**
  1. **In-Memory Lock:** Rejected because it cannot scale across multiple server instances.
  2. **Optimistic Lock (`@Version` attribute):** Rejected because high slot contention would trigger excessive exceptions and ruin user experience.
- **Consequences:**
  - **Positive Impact:** Guarantees 100% data consistency and eliminates double-booking hazards under concurrent traffic.
  - **Trade-offs:** Potential lock contention under heavy load; requires careful transaction timeout configuration to prevent deadlocks.
---

## Repository Structure

```text
room-booking/
├── src/            # Spring Boot application code
│   ├── main/java/kz/sdu/roombooking/
│   │   ├── booking/        # Slot reservation & locking
│   │   ├── room/           # Room management
│   │   └── user/           # Auth & Profiles
│   └── main/resources/
│       └── application.yml
├── pom.xml         # Maven dependencies
└── README.md       # Project documentation
```
---

## How to Run

- **Prerequisites:** Java 21, Maven, PostgreSQL.
- **Setup Status:** Detailed execution commands will be updated after the first working version.

