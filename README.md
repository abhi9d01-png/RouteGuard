
# SchoolRoute
## School Bus Management, Live Tracking & Intelligent Route Management System

**Project Type:** Full-stack web application  
**Domain:** School transportation management  
**Platform:** Responsive web application  
**Status:** Planning and design

---

## 1. Project Overview

SchoolRoute is a school bus management system designed to improve the safety, visibility, and efficiency of school transportation.

The system connects four main user groups:

- **Administrators:** Manage buses, students, staff, routes, and transportation operations.
- **Parents:** Track their child's assigned bus and receive transport notifications.
- **Drivers:** View assigned trips, routes, and authorised route changes.
- **Helpers:** Verify student boarding and exit events.

SchoolRoute uses GPS tracking, a central backend, a relational database, and in-app notifications to coordinate transportation activities.

One of its key features is **fixed routes with dynamic exceptions**. Buses normally follow school-approved routes, but authorised replacement pickups can be arranged when a bus is delayed.

## 2. Problem Statement

Traditional school transportation often relies on manual registers, phone calls, and messaging applications.

This can cause:

- Uncertainty about bus locations.
- Unpredictable pickup and arrival times.
- Delayed parent notifications.
- Difficulty tracking student boarding and exits.
- Confusion during bus delays.
- Limited visibility into route changes.
- Poor historical records of transport events.

SchoolRoute aims to solve these problems through a centralised and auditable transportation management platform.

## 3. Project Objectives

1. Manage students, parents, buses, staff, routes, and stops.
2. Track buses using GPS and mobile internet.
3. Display estimated pickup and school arrival times.
4. Verify student boarding and exit events.
5. Send in-app notifications to authorised parents.
6. Provide separate interfaces for administrators, parents, drivers, and helpers.
7. Support controlled replacement pickups.
8. Maintain transport history and audit logs.
9. Handle stale GPS data and network failures.
10. Demonstrate practical full-stack software engineering skills.

---

## 4. Technology Stack

The following technologies are proposed for the project.

| Technology | Purpose |
|---|---|
| React | Frontend user interfaces |
| Node.js | JavaScript runtime for the backend |
| Express.js | Backend API framework |
| PostgreSQL | Relational database |
| Prisma | Database access and schema management |
| Leaflet | Interactive maps |
| OpenStreetMap | Map data and tiles |
| HTTP REST API | Communication between clients and backend |
| Server-Sent Events or WebSockets | Live location and status updates |
| Git | Version control |

These are planned technology choices and should be updated if the implementation changes.

---

## 5. System Architecture

SchoolRoute follows a client-server architecture.

```text
                 SCHOOLROUTE SYSTEM
                         |
          +--------------+--------------+
          |              |              |
     Admin Portal   Parent Portal   Staff Portals
          |              |              |
          +--------------+--------------+
                         |
                    HTTP / API
                         |
                +----------------+
                | Backend Server |
                | Node.js        |
                | Express.js     |
                +----------------+
                         |
          +--------------+--------------+
          |              |              |
      Authentication  Trip Logic    GPS / ETA
          |              |              |
          +--------------+--------------+
                         |
                +----------------+
                |   PostgreSQL   |
                |    Database    |
                +----------------+

 Bus Phone GPS ---------> Backend Server
 Backend Server --------> Live Browser Updates
 Backend Server --------> In-App Notifications
```

### 5.1 Frontend

The frontend is the part of the application users see in their browsers.

It is responsible for:

- Displaying dashboards and forms.
- Showing buses on a map.
- Displaying pickup and arrival estimates.
- Showing student-event statuses.
- Displaying notifications.
- Sending user actions to the backend.
- Showing validation errors and connection warnings.

React will be used to build the frontend.

### 5.2 Backend

The backend processes requests and enforces the system's business rules.

It is responsible for:

- Authentication and authorisation.
- Student and bus management.
- Route and trip management.
- GPS update processing.
- ETA calculations.
- Boarding and exit verification.
- Dynamic reassignment.
- Notification generation.
- Audit logging.

Node.js and Express.js are the proposed backend technologies.

### 5.3 Database

PostgreSQL stores the system's permanent information.

The database includes users, students, guardians, buses, routes, trips, assignments, GPS updates, student events, notifications, and audit logs.

Prisma will provide a structured interface between the backend and PostgreSQL.

### 5.4 GPS Device

A dedicated phone on each bus obtains GPS coordinates and sends them to the backend through mobile internet.

The backend validates the update and associates it with the correct bus and trip.

If GPS updates stop arriving, the application must mark the location as stale instead of continuing to display it as live.

### 5.5 Map System

Leaflet displays bus locations, routes, and stops.

OpenStreetMap can provide the underlying map data.

A map library does not automatically provide accurate traffic-aware ETAs. Those require separate calculation logic and potentially an external traffic-data provider.

---

## 6. How the System Works

### Step 1: Administrator Configures Transportation

The administrator creates student records, registers buses, assigns drivers and helpers, defines routes, and associates students with pickup and drop-off stops.

### Step 2: A Trip Begins

An authorised user starts the trip.

The backend checks the bus, route, staff assignments, and trip status before accepting the operation.

### Step 3: The Bus Sends GPS Updates

The bus phone sends location updates to the backend.

The backend validates and stores the location and makes authorised information available to the frontend.

### Step 4: Parents View the Bus

The parent signs in and views the assigned bus.

The parent can see the latest reported location, location freshness, estimated pickup time, and estimated school arrival.

Parents can only access information for their linked children.

### Step 5: Student Boards the Bus

The student code is entered on the shared school-managed device.

The backend verifies:

1. The code identifies a valid student.
2. The trip is active.
3. The student is assigned to that bus for the trip.
4. The requested event is allowed.
5. The event is not a duplicate or conflicting operation.

If validation succeeds, the backend records the event and creates the appropriate in-app notification.

The helper must supervise the real-world boarding event. Code entry alone does not prove that a student physically boarded.

### Step 6: Student Exits the Bus

At the approved drop-off stop, the student code is entered again under helper supervision.

The backend validates and records the exit event.

A recorded exit does not automatically prove that the child has been safely handed over to an authorised person. School procedures must handle that separately.

### Step 7: Trip Ends

The system records trip completion and preserves relevant student events, route changes, and operational exceptions.

---

## 7. User Roles

### Administrator

- Manage students and guardians.
- Register buses.
- Manage drivers and helpers.
- Create routes and stops.
- Assign students to buses.
- Monitor live bus locations.
- Review delays and incomplete events.
- Approve exceptional reassignments.
- Review audit logs.

### Parent or Guardian

- View linked children.
- Track the assigned bus.
- View pickup and arrival estimates.
- Receive boarding notifications.
- Receive exit notifications.
- View approved transport changes.
- Review relevant trip history.

### Driver

- View assigned trips.
- View ordered stops.
- Review route progress.
- Receive authorised route changes.
- View operational warnings.

### Helper

- View the assigned trip and expected students.
- Supervise student code entry.
- Record boarding and exit events.
- Identify incomplete events.
- Report exceptions.

---

## 8. GPS Tracking and ETA Calculation

### GPS Data

A location update may include:

- Bus identifier.
- Latitude.
- Longitude.
- Device timestamp.
- Server receipt timestamp.
- Optional speed and heading.
- Associated trip identifier.

The backend should validate the data before accepting it.

### Location Freshness

The system should distinguish among:

- **Live:** A sufficiently recent location is available.
- **Delayed:** The latest update is older than the configured threshold.
- **Unavailable:** No usable current location exists.
- **Trip inactive:** The bus is not reporting an active trip.

### ETA Calculation

A basic estimate can be calculated using:

ETA = Remaining route distance / Estimated average speed

This is only an approximation. Traffic, road conditions, and stop durations affect the actual arrival time.

More advanced ETA calculations can incorporate route geometry, observed travel times, and suitable traffic data.

---

## 9. Dynamic Bus Reassignment

SchoolRoute normally uses fixed routes but can support authorised exceptions when a bus is delayed.

### Reassignment Workflow

1. Detect a significant delay.
2. Check whether GPS information is sufficiently recent.
3. Identify potential replacement buses.
4. Check passenger capacity.
5. Check route compatibility and detour time.
6. Check driver and helper assignments.
7. Check whether affected students have already boarded.
8. Apply school-approved rules.
9. Automatically commit an eligible routine change or request administrator approval.
10. Update the trip-specific assignment.
11. Notify affected parents and staff.
12. Record the decision in the audit history.

### Important Rules

- Never exceed approved passenger capacity.
- Never rely on stale GPS for an automatic decision.
- Never silently assign one student to conflicting buses.
- Never silently transfer a student who has already boarded.
- Preserve the original assignment.
- Require human review when eligibility cannot be established confidently.

The regular student assignment should remain separate from the assignment for a specific trip.

---

## 10. Database Design

The proposed database contains the following entities.

| Entity | Purpose |
|---|---|
| Users | Account identity and status |
| Roles | User permissions |
| Guardians | Parent or guardian records |
| Students | Student transport information |
| GuardianStudent | Links guardians to students |
| Buses | Vehicle records and capacity |
| StaffAssignments | Driver and helper assignments |
| Routes | Regular route definitions |
| Stops | Pickup and drop-off locations |
| RouteStops | Ordered stops within a route |
| StudentAssignments | Regular bus and stop assignments |
| Trips | Individual bus journeys |
| TripAssignments | Student assignments for a specific trip |
| GPSUpdates | Location history and timestamps |
| StudentEvents | Boarding and exit records |
| Reassignments | Authorised trip-specific changes |
| Notifications | In-app notifications |
| AuditLogs | History of important operations |

### Important Relationships

- A guardian can be linked to multiple students.
- A route contains multiple ordered stops.
- A bus can operate many trips over time.
- A trip uses a particular route plan.
- A student can have a regular assignment and a separate trip-specific assignment.
- A trip can contain multiple student events.
- A reassignment records the original and replacement assignments.
- A notification is associated with a relevant event or operational change.

Database constraints and transactions should protect important operations from duplicate or conflicting updates.

---

## 11. API Design

The frontend and GPS device communicate with the backend through APIs.

The following are example endpoint names for planning purposes.

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/api/auth/login` | Sign in |
| GET | `/api/students` | Retrieve authorised students |
| POST | `/api/students` | Create a student |
| GET | `/api/buses` | Retrieve buses |
| POST | `/api/buses` | Register a bus |
| GET | `/api/routes` | Retrieve routes |
| POST | `/api/trips` | Create or schedule a trip |
| POST | `/api/gps/updates` | Submit GPS data |
| GET | `/api/trips/:id/status` | Retrieve trip status |
| POST | `/api/student-events` | Submit a boarding or exit event |
| POST | `/api/reassignments/proposals` | Evaluate a reassignment |
| POST | `/api/reassignments/:id/approve` | Approve a proposal |
| GET | `/api/notifications` | Retrieve notifications |
| GET | `/api/audit-logs` | Retrieve authorised audit records |

These endpoints are illustrative, not implemented.

Every endpoint must validate inputs, enforce permissions, and return clear success or error responses.

---

## 12. Suggested Project Structure

### Frontend

```text
frontend/
  src/
    components/
    pages/
      admin/
      parent/
      driver/
      helper/
    layouts/
    features/
      authentication/
      students/
      buses/
      routes/
      trips/
      live-map/
      student-events/
      notifications/
      reassignments/
    services/
    hooks/
    utilities/
```

### Backend

```text
backend/
  src/
    auth/
    users/
    students/
    guardians/
    buses/
    staff/
    routes/
    stops/
    trips/
    assignments/
    gps/
    eta/
    student-events/
    reassignments/
    notifications/
    audit/
    middleware/
    database/
```

The backend can initially be a single application organised into modules. This approach is called a modular monolith and is generally simpler to develop and deploy than multiple independent microservices for an initial project.

---

## 13. Security and Privacy

Because SchoolRoute handles children's transportation information, security is a core requirement.

### Authentication

- Use secure password hashing.
- Protect authenticated sessions.
- Expire or revoke access appropriately.
- Rate-limit repeated login failures.

### Authorisation

- Enforce permissions on the backend.
- Allow parents to view only linked children.
- Restrict drivers and helpers to authorised trips.
- Protect administrative actions.
- Prevent access to unrelated records by changing identifiers in API requests.

### GPS Security

- Authenticate GPS devices.
- Validate incoming coordinates.
- Track update timestamps.
- Restrict live and historical location access.
- Avoid unnecessary location collection outside transport periods.

### Student Code Security

- Avoid predictable codes.
- Rate-limit failed attempts.
- Reject duplicate and conflicting events.
- Minimise exposure of student information.
- Supervise real-world boarding and exit verification.

### Audit Logging

Important changes should record the actor, time, operation, affected record, and relevant reason.

Examples include route edits, assignment changes, approvals, administrative overrides, and event corrections.

### Privacy

Define appropriate access policies, data-retention periods, consent and notice procedures, and incident-handling processes before using real student information. Review applicable Indian data-protection requirements before deployment.

---

## 14. Error Handling and Reliability

### GPS Failure

Mark the last position as stale and avoid displaying it as live.

### Network Failure

Do not assume a request succeeded merely because it was submitted.

If offline event capture is implemented, queued events must be marked as pending until the backend validates and accepts them.

### Duplicate Requests

Use database constraints and idempotency mechanisms where appropriate.

### Notification Failure

Preserve the underlying transport event even if notification delivery fails.

### Reassignment Conflict

Validate the latest assignment state and use appropriate database transaction controls.

### Invalid Student Code

Reject the event and provide a clear error without exposing unnecessary student information.

---

## 15. Testing Strategy

### Unit Testing

Test individual rules such as:

- Authentication and permissions.
- GPS freshness.
- ETA calculations.
- Student-event validation.
- Capacity checks.
- Reassignment eligibility.
- Duplicate prevention.

### Integration Testing

Test interactions between backend modules and PostgreSQL.

Examples:

- Creating a student and linking a guardian.
- Assigning a student to a route.
- Starting a trip.
- Recording a boarding event.
- Creating a notification.
- Approving a reassignment.

### End-to-End Testing

Test complete workflows from the user interface to the database.

Example:

1. Administrator configures a route.
2. Driver starts a trip.
3. GPS updates reach the backend.
4. Parent views the assigned bus.
5. Helper records student boarding.
6. Backend validates the event.
7. Parent receives an in-app notification.
8. Administrator reviews the event history.

Security tests should verify that each role can access only permitted information and operations.

---

## 16. Development Roadmap

### Phase 1: Core Management

- Authentication and role permissions.
- Student and guardian management.
- Bus and staff management.
- Routes and stops.
- Regular assignments.
- Basic trip management.

### Phase 2: GPS and Parent Portal

- GPS update ingestion.
- Live bus map.
- Location freshness.
- Basic ETAs.
- Parent portal.
- In-app notifications.

### Phase 3: Student Verification

- Student code workflow.
- Boarding and exit events.
- Duplicate prevention.
- Event history.
- Offline-state design.

### Phase 4: Driver and Helper Operations

- Assigned trip views.
- Ordered stops.
- Expected student lists.
- Route-change instructions.
- Operational warnings.

### Phase 5: Dynamic Reassignment

- Delay detection.
- Alternative bus evaluation.
- Capacity and route checks.
- Approval workflow.
- Trip-specific reassignment.
- Notifications and audit records.

### Phase 6: Reliability and Deployment

- Automated tests.
- Security review.
- Failure simulation.
- Performance improvements.
- Deployment planning.
- Documentation and demonstration scenarios.

---

## 17. Deployment Architecture

A potential deployment setup includes:

- A static hosting service for the React frontend.
- A server hosting environment for the Node.js backend.
- A managed PostgreSQL database.
- HTTPS for network communication.
- Environment variables and secure secret management.
- Database backups.
- Application logging and error monitoring.
- Controlled database migrations.

The bus phone must be configured for appropriate background-location permissions, battery management, and mobile connectivity.

Hosting choices should be made after considering costs, provider availability, expected traffic, and service policies.

---

## 18. Project Limitations

- GPS accuracy depends on the device and environment.
- Mobile internet may be unreliable.
- ETAs are estimates, not guarantees.
- Traffic-aware estimates require suitable traffic data.
- Code entry alone cannot prove physical boarding or safe handover.
- A real deployment requires operational testing and privacy safeguards.
- Native mobile applications and SMS or WhatsApp notifications are not part of the initial scope.

---

## 19. Future Improvements

Possible future additions include:

- Native Android and iOS applications.
- SMS or WhatsApp notifications.
- Additional Indian languages.
- Improved traffic-aware ETAs.
- Better offline operation.
- Route-performance reports.
- Advanced fleet optimisation.
- Secure school attendance integration.
- Multi-school support.

These features should be introduced only after the core transport workflow is reliable.

---

## 20. Definition of Done

The first dependable release should allow the school to:

- Manage students, guardians, buses, routes, and stops.
- Assign students to regular transport arrangements.
- Start and monitor a trip.
- Receive GPS updates from a bus phone.
- Display location freshness and approximate ETAs.
- Restrict parent access to linked children.
- Record validated boarding and exit events.
- Generate in-app notifications.
- Preserve trip and event history.
- Handle stale GPS and network failures honestly.
- Pass automated tests for the main workflows.

Dynamic reassignment can be delivered as a later milestone.

---

## 21. Conclusion

SchoolRoute is planned as a full-stack school transportation platform built around reliable trip data, controlled access, verified student events, transparent route changes, and timely communication.

Its architecture separates the frontend, backend business logic, database, and bus GPS mechanism. This separation makes the system easier to test, maintain, and expand.

The recommended approach is to implement the project incrementally, starting with core management and adding advanced live operations after the foundational workflows are reliable.

**Project status:** Planning and architecture design.  
**Implementation status:** To be updated as development progresses.
