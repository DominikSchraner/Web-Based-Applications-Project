# Web-Based-Applications-Project

# EasyDrive — Peer-to-Peer Vehicle Rental SaaS (Working Title)

**Current stage:** Milestone 1 — design draft. Update this README throughout the project; do not start a separate document for each milestone.

Later sections will be introduced in the fourth theory session and subsequent classes. For now, document the design draft below.

## Project overview

Our application (**EasyDrive**) is a peer-to-peer and B2C SaaS platform (similar to Airbnb or Turo) that allows individuals and companies to list their vehicles for rent, while enabling other users to easily search, book, and rent these vehicles for temporary use. It addresses the problem of underutilized vehicles sitting idle, while providing flexible, localized, on-demand mobility options for people who do not own a car.

### Team and initial responsibilities

| Member | Initial responsibility | Next action |
|---|---|---|
| Dominik Schraner | Defined domain entities (`User`, `Vehicle`, `Booking`) and example JSON payloads | Draft OpenAPI contract for Milestone 2[cite: 3] |
| Silvan Aichholz | Defined user roles, project scope, user stories, and screen navigation flow | Upload UI sketches to repository[cite: 1, 3] |
| Samuel Albani | Defined business rules, booking workflow steps, and planned CRUD operations | Present open questions in M1 coaching[cite: 3] |---


## 1. Analysis

### Scenario, users and goals

- **Situation or problem:** Individuals and local companies have vehicles that sit unused for most of the week. Conversely, people frequently need temporary access to a vehicle (for weekend trips, moving, or errands) without the high financial burden of car ownership or the rigid, inconvenient processes of traditional rental agencies.
- **Intended users:** 
  - *Visitors (Unauthenticated):* Prospective users browsing available cars, locations, and pricing.
  - *Vehicle Owners / Hosts (Authenticated Providers):* Private individuals or companies looking to monetize their idle vehicles by listing them and managing bookings.
  - *Renters (Authenticated Consumers):* People needing to search, book, and use a vehicle for a specific timeframe.
- **Proposed benefit:** Extra passive income for vehicle owners and flexible, transparent, neighborhood-based mobility for renters.
- **Initial scope:** 
  - **In Scope (MVP):** Public vehicle discovery and filtering (by city and date range), vehicle listing CRUD operations for Owners, booking creation and cancellation for Renters, booking approval/status management for Owners, and role-based access control.
  - **Out of Scope (for Milestone 1 / Prototype):** Real payment gateway processing (e.g., Stripe), automated driver's license OCR verification, live GPS vehicle tracking, and dynamic insurance policy generation.

### User stories and first workflow

#### Adapted Minimum User Stories
- **Device & Navigation:** As a user (Visitor, Renter, or Owner), I want to access the application on desktop and mobile devices with consistent navigation and visual presentation so that I can use the application easily.
- **Public Discovery:** As a **Visitor**, I want to view public vehicle listings and filter by location and dates without logging in, so I can explore available cars without access to protected functions or personal data.
- **Listing Management (CRUD):** As an authenticated **Vehicle Owner**, I want to create, read, update, and delete/deactivate records for my vehicles, subject to the application's business rules (e.g., cannot delete a vehicle with an active booking).
- **Booking Management (CRUD):** As an authenticated **Renter**, I want to create a booking for an available car, inspect my booking details, and update/cancel my reservation if my plans change.
- **Access Control & Privacy:** As an authenticated **User (Renter or Owner)** with protected responsibilities, I want to log in so that access is restricted to the bookings and vehicle data I am authorised to see and manage.

#### Primary Workflow: Searching and Booking a Vehicle
**Core User Story:** *"As a Renter, I want to view available vehicles for my desired dates and location, so that I can book a car for my upcoming trip without double-booking."*

| Step | User / role | Action | Information needed | Expected result or feedback |
|---|---|---|---|---|
| 1 | Visitor / Renter | Enter search criteria | `location` (e.g., Basel), `start_date`, `end_date` | List of available vehicles matching criteria with daily rate |
| 2 | Visitor / Renter | Select a vehicle | `vehicle_id` | Detailed view of the vehicle specifications, availability, and calculated total price |
| 3 | Renter | Confirm booking request | Authenticated `renter_id`, `vehicle_id`, `start_date`, `end_date` | System validates dates; displays Booking Confirmation screen (`PENDING` or `CONFIRMED`) |
| 4 | Owner | Review booking request | `booking_id`, `renter_id`, requested dates | Notification of a new pending booking; Owner approves (`CONFIRMED`) or declines (`DECLINED`) |
| 5 | Renter | Check booking status | Authenticated `renter_id` | Updated booking status and pickup details shown in "My Bookings" |

*Open Question:* What happens if an owner manually cancels an already confirmed booking at the last minute?

---

## 2. Design

### Screens and navigation

*(Add links or embed your sketches in `docs/sketches/` before committing)*

1. **Home & Search Screen (`docs/sketches/01-search.png`):** Search bar with inputs for Location, Start Date, and End Date, plus a grid of available vehicle cards showing make/model, city, and daily rate in CHF.
2. **Vehicle Detail & Booking View (`docs/sketches/02-vehicle-detail.png`):** Full vehicle specs (transmission, seats, fuel type), date range picker, automatic price breakdown (`rental_days * daily_rate_chf`), and a "Request Booking" button.
3. **Renter Dashboard — My Bookings (`docs/sketches/03-renter-bookings.png`):** Overview of the logged-in renter's reservations with status badges (`PENDING`, `CONFIRMED`, `CANCELLED`) and a cancellation action.
4. **Owner Dashboard — My Fleet & Requests (`docs/sketches/04-owner-dashboard.png`):** Interface for owners to add/edit/remove vehicle listings and approve or decline incoming booking requests.

#### Screen & Navigation Flow
```text
[Home / Search Screen (Public)]
       │
       ▼
[Vehicle Details Page (Public)] ──(Click "Book Vehicle")──► [Authentication / Login Check]
                                                                    │
       ┌────────────────────────────────────────────────────────────┴──────────────────────────┐
       ▼ (Role: Renter)                                                                        ▼ (Role: Owner)
[Booking Confirmation & "My Bookings"]                                        [Owner Dashboard: Fleet CRUD & Approvals]
```

### Domain concepts and example data

#### Core Entities
- **User:** Platform account (`user_id`, `name`, `email`, `role` [`renter`, `owner`, `admin`]).
- **Vehicle:** Car listed for rent (`vehicle_id`, `owner_id`, `make`, `model`, `year`, `license_plate`, `location`, `transmission`, `fuel_type`, `seats`, `daily_rate_chf`, `is_active`).
- **Booking:** Rental transaction linking a Renter to a Vehicle (`booking_id`, `vehicle_id`, `renter_id`, `start_date`, `end_date`, `total_price_chf`, `status` [`PENDING`, `CONFIRMED`, `CANCELLED`, `COMPLETED`]).

#### Sample JSON Payloads

**1. Vehicle Resource (`GET /api/v1/vehicles/105`)**
```json
{
  "vehicle_id": 105,
  "owner_id": 12,
  "make": "VW",
  "model": "ID.3 Pro",
  "year": 2024,
  "license_plate": "BS-48291",
  "location": "Basel",
  "transmission": "automatic",
  "fuel_type": "electric",
  "seats": 5,
  "daily_rate_chf": 75.00,
  "is_active": true
}
```

**2. Create Booking Request (`POST /api/v1/bookings`)**
```json
{
  "vehicle_id": 105,
  "start_date": "2026-10-15",
  "end_date": "2026-10-17",
  "notes": "Weekend trip, pickup at 09:00."
}
```

**3. Create Booking Response (`201 Created`)**
```json
{
  "booking_id": 9012,
  "vehicle_id": 105,
  "renter_id": 42,
  "start_date": "2026-10-15",
  "end_date": "2026-10-17",
  "rental_days": 2,
  "total_price_chf": 150.00,
  "status": "PENDING",
  "created_at": "2026-10-01T14:30:00Z"
}
```

**4. Overlapping Booking Error Response (`409 Conflict`)**
```json
{
  "error": "VEHICLE_UNAVAILABLE",
  "message": "Vehicle 105 is already booked for the selected dates (2026-10-15 to 2026-10-17)."
}
```

### Business rules and possible operations

#### Business Rules & Validation
- **Rule 1 (No Double-Booking):** A vehicle cannot be booked by two different users for overlapping dates if an existing booking has status `PENDING` or `CONFIRMED`.
  - *Exception Handling:* If a user attempts to book dates that were just taken milliseconds prior, the system rejects the request with `409 Conflict` and informs the user that the vehicle is no longer available.
- **Rule 2 (Valid Date Ranges):** `start_date` cannot be in the past, and `end_date` must be after `start_date` (minimum rental duration of 1 day).
- **Rule 3 (Server-Side Pricing):** `total_price_chf` is always calculated by the backend service (`rental_days * daily_rate_chf`) to prevent client-side price tampering.
- **Rule 4 (Ownership & Self-Booking Restriction):** Only the Owner of a vehicle may edit or delete its listing. Owners cannot create a booking for their own vehicle.
- **Rule 5 (Deletion Protection):** A vehicle with upcoming `CONFIRMED` bookings cannot be hard-deleted; it can only be deactivated (`is_active: false`) to prevent future bookings.

#### Planned Operations Table

| User goal | Proposed action | Example input | Expected output | Open question |
|---|---|---|---|---|
| Search available cars | `GET /vehicles` | `?location=Basel&start_date=2026-10-15&end_date=2026-10-17` | `200 OK` — List of available `Vehicle` JSON objects | Should booked cars be hidden completely or displayed as unavailable? |
| View car details | `GET /vehicles/{id}` | `vehicle_id: 105` | `200 OK` — Single `Vehicle` object with full specs | Should we return a list of blocked date ranges in this endpoint? |
| List a car | `POST /vehicles` | Make, model, year, license plate, location, `daily_rate_chf` | `201 Created` — New `vehicle_id` and created resource | How detailed does the car description need to be for the MVP? |
| Update car listing | `PUT /vehicles/{id}` | `daily_rate_chf: 85.00`, `is_active: true` | `200 OK` — Updated `Vehicle` object | Confirmed: Rate changes only apply to new bookings, not existing ones. |
| Remove car listing | `DELETE /vehicles/{id}` | `vehicle_id: 105` | `200 OK` (or `409 Conflict` if active bookings exist) | Should we use soft-delete (`is_active = false`) instead of hard-delete? |
| Rent a car | `POST /bookings` | `vehicle_id`, `start_date`, `end_date` | `201 Created` — `booking_id`, `total_price_chf`, and confirmation | Do we auto-confirm bookings, or require owner approval (`PENDING`) first? |
| View my bookings | `GET /bookings` | Authenticated user token/session | `200 OK` — List of bookings belonging to the logged-in Renter/Owner | Should owners and renters use the same endpoint filtered by role? |
| Approve or cancel booking | `PATCH /bookings/{id}` | `{"status": "CONFIRMED"}` or `{"status": "CANCELLED"}` | `200 OK` — Updated `Booking` status | What is the cutoff time before `start_date` for a free cancellation? |

---

## 3. Project management

### Decisions, open questions and next steps

| Question / decision | Current position | Next step / person |
|---|---|---|
| Should we require owner approval for bookings? | Draft: Start with `PENDING` status so Owners can approve/decline, demonstrating a multi-role workflow. | Discuss with lecturer during coaching. / Samuel Albani |
| Do we need to handle insurance and payment details now? | Out of scope for Milestone 1 & prototype. We calculate `total_price_chf` in business logic without real payment processing. | Keep out of Milestone 1 scope. / Dominik Schraner |
| Which frontend approach will we use? | Considering server-rendered templates (FastAPI + Jinja2) or lightweight JS frontend paired with our documented REST API. | Finalize after Milestone 1 coaching. / Silvan Aichholz |
| Group size (3 members vs. 4 members) | Currently 3 members (Dominik, Silvan, Samuel) with clear split across Backend, Frontend, and API/QA. | Confirm 3-person group setup with lecturer during coaching. / All |

### Milestone progress

| Milestone | Available evidence | Status / next step |
|---|---|---|
| 1 — Design draft | Analysis, user stories, workflow, domain concepts, example JSON, and proposed operations documented above. | Add sketch files to repo, commit, and submit link to Moodle. |
| 2 — Contract and available implementation | OpenAPI contract and implemented/tested progress | *Update later* |
| 3 — Integration | Revised feature scope, frontend decision, architecture and a connected workflow | *Update after classroom examples* |
| 4 — Submission readiness | Substantially completed application, security checks, tests, and setup instructions | *Update before final submission* |

---

## 4. References and acknowledgements

- **Template lineage:** [Pizzeria Reference Project](https://github.com/FHNW-INT/Pizzeria_Reference_Project) adapted for the HS26 Python/FastAPI teaching path.
- **Domain & UX Inspiration:**
  - [Turo](https://turo.com/) & [Getaround](https://getaround.com/) — Peer-to-peer car sharing workflows, vehicle attributes, and host approval mechanics.
  - [Mobility Carsharing](https://www.mobility.ch/) — Swiss localized vehicle availability and transparent rate structures.
  - [Airbnb](https://www.airbnb.com/) — Two-sided marketplace layout (Host vs. Guest/Renter perspective) and date-range search pattern.

---

## Worked example — adapt, do not submit unchanged

*From the design-class workflow and sketch exercises (Adapted for EasyDrive Vehicle Rental):*

- **Need:** Renters want to quickly secure an available vehicle for specific dates without double-booking.
- **Workflow:** Enter location & dates → select car → review calculated price → submit booking → see confirmation. Owners can then see and approve the upcoming rental in their dashboard.
- **Sketches:** Linked in Section 2 (`docs/sketches/01-search.png` to `04-owner-dashboard.png`).
- **Data (exercise 1c):** `{"vehicle_id": 105, "renter_id": 42, "start_date": "2026-10-15", "end_date": "2026-10-17", "status": "PENDING"}`.
- **Proposed operation:** Create a booking (`POST /bookings`); expected result: a unique `booking_id`, calculated `total_price_chf`, and confirmation status (or `409 Conflict` if already booked). This describes intended behaviour, not an implemented endpoint.
- **API observation:** Searching vehicles by date range works naturally with query parameters (`GET /vehicles?location=Basel&start_date=...`), while creating a reservation requires a validated JSON body (`POST /bookings`).
- **Open question:** How should the app respond if a renter returns the car late and impacts the next reservation?

---

## Friday handoff checklist

- [x] Complete Milestone 1 README design draft.
- [ ] Add UI sketches to `/docs/sketches` and verify links in Section 2.
- [ ] Commit this README and the available draft material before the milestone.
- [ ] First join the module's MS Team using the link in Moodle. 
- [ ] Submit the GitHub repository link in Moodle by Friday.
- [ ] Ensure the lecturer can access the repository; public visibility is not required.
- [ ] If you do not yet have a group channel and your team composition is not recorded in Moodle's team formation activity, email the lecturer with all team members' names.
- [ ] Keep credentials and personal data out of the repository.
