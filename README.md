# Web-Based-Applications-Project

# DriveShare / Vehicle Rental SaaS (Working Title)

**Current stage:** Milestone 1 — design draft. Update this README throughout the project; do not start a separate document for each milestone.

Later sections will be introduced in the fourth theory session and subsequent classes. For now, document the design draft below.

## Project overview

Our application is a SaaS platform that allows individuals and companies to list their vehicles for rent, while enabling other users to easily search, book, and rent these vehicles for temporary use. It addresses the problem of underutilized vehicles sitting idle, while providing flexible, on-demand mobility options for people who do not own a car.

### Team and initial responsibilities

| Member | Initial responsibility | Next action |
|---|---|---|
| [Name 1] | Coordination and README | Keep decisions, questions and the milestone commit together |
| [Name 2] | Users and workflow | Describe needs and the steps of one workflow |
| [Name 3] | Sketches and interaction | Sketch the screens and feedback for that workflow |
| [Name 4] | Data and API exploration | Prepare sample JSON and clarify the proposed operations |

*These are suggested starting responsibilities, not permanent silos. Discuss and review each other's work; everyone should understand the draft. Adjust or rotate responsibilities as needed.*

## 1. Analysis

### Scenario, users and goals

- **Situation or problem:** Individuals and companies have vehicles that are often unused. Conversely, people frequently need temporary access to a vehicle without the financial burden of ownership or traditional, inflexible rental agencies.
- **Intended users:** 
  - *Vehicle Owners (Providers):* Private individuals or companies looking to monetize their idle vehicles.
  - *Renters (Consumers):* People needing a vehicle for a specific timeframe.
- **Proposed benefit:** Extra income for vehicle owners and flexible, localized mobility for renters.
- **Initial scope:** The initial workflow will focus on the **Renter searching for and booking an available vehicle**. Admin dashboards, payment gateways, and user verification will wait for later iterations.

### User stories and first workflow

**User Story:** “As a Renter, I want to view available vehicles for my desired dates, so that I can book a car for my upcoming trip.”

| Step | User / role | Action | Information needed | Expected result or feedback |
|---|---|---|---|---|
| 1 | Renter | Enter search criteria | Location, Start Date, End Date | List of available vehicles matching criteria |
| 2 | Renter | Select a vehicle | Vehicle ID | Detailed view of the vehicle and total price |
| 3 | Renter | Confirm booking | Renter details, payment preference | Booking confirmation screen |
| 4 | Owner | Review booking | Booking ID, Renter ID | Notification of a new pending/confirmed booking |

*Open Question:* What happens if an owner manually cancels a booking at the last minute? 

## 2. Design

### Screens and navigation

*[Add links or embed your sketches here: e.g., Search screen, Vehicle Details page, Booking Confirmation screen. Paper photos or draw.io diagrams are perfectly fine.]*

### Domain concepts and example data


### Business rules and possible operations

**Rule:** A vehicle cannot be booked by two different users for overlapping dates. 
**Exception:** If a user attempts to book dates that were just taken by someone else milliseconds prior, the system must reject the request and inform the user the vehicle is no longer available.

| User goal | Proposed action | Example input | Expected output | Open question |
|---|---|---|---|---|
| Rent a car | Create a booking | `vehicle_id`, `start_date`, `end_date` | Booking ID and "success" confirmation | Do we auto-confirm bookings, or require owner approval first? |
| List a car | Create a vehicle listing | Make, model, license plate, daily rate | New `vehicle_id` | How detailed does the car description need to be? |

## 3. Project management

### Decisions, open questions and next steps

| Question / decision | Current position | Next step / person |
|---|---|---|
| Should we require owner approval for bookings? | Draft: Instant booking for companies, optional approval for private owners. | Discuss with lecturer during coaching. / [Name] |
| Do we need to handle insurance details now? | Undecided. Might be too complex for the prototype. | Keep out of Milestone 1 scope. / [Name] |

### Milestone progress

| Milestone | Available evidence | Status / next step |
|---|---|---|
| 1 — Design draft | Analysis, workflow, example JSON and proposed operations documented above. | **[Add links to sketches, then submit to Moodle]** |
| 2 — Contract and available implementation | OpenAPI contract and implemented/tested progress | *Update later* |
| Integration — later | Revised feature scope, frontend decision, architecture and a connected workflow | *Update after classroom examples* |

## 4. References and acknowledgements

- Template lineage: [Pizzeria Reference Project](https://github.com/FHNW-INT/Pizzeria_Reference_Project) adapted for the HS26 Python/FastAPI teaching path.
- *[List any other tools, libraries, or existing apps (like Mobility, Turo, or Uber Carshare) you looked at for inspiration here]*

## Worked example — adapt, do not submit unchanged

*From the design-class workflow and sketch exercises (Adapted for Vehicle Rental):*

- **Need:** Renters want to quickly secure a vehicle without double-booking.
- **Workflow:** enter dates → select car → review price → submit → see confirmation. Owners can then see the upcoming rental in their dashboard.
- **Sketches:** *[Link your paper sketches of the search form, car detail view, and confirmation]*
- **Data (exercise 1c):** `{"vehicle_id": 105, "renter_id": 42, "dates": {"start": "2026-10-15", "end": "2026-10-17"}}`. 
- **Proposed operation:** Create a booking; expected result: an identifier and confirmation. This describes intended behaviour, not an implemented endpoint.
- **API observation:** *[Add any observations if you tested weather/mapping APIs for location searches]*
- **Open question:** How should the app respond if a renter returns the car late?

## Friday handoff checklist

- [ ] Commit this README and the available draft material before the milestone.
- [ ] First join the module's MS Team using the link in Moodle. 
- [ ] Submit the GitHub repository link in Moodle by Friday.
- [ ] Ensure the lecturer can access the repository; public visibility is not required.
- [ ] If you do not yet have a group channel and your team composition is not recorded in Moodle's team formation activity, email the lecturer with all team members' names.
- [ ] Keep credentials and personal data out of the repository.
