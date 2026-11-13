# Web-Based Train Reservation System — SE2030 Group Project

Spring Boot + Thymeleaf + MySQL. This is a **starter scaffold**: auth,
train search, and booking (Functions 1 & 2) are fully working end-to-end.
Functions 3, 4, 5, 6 have a working skeleton — extend them following the
same pattern (see "Where to go next" below). You all need to be able to
explain your own code in the viva, so build on this rather than treating
it as finished.

## 1. Prerequisites

- JDK 17+ ([Adoptium](https://adoptium.net/) is a good free source)
- Maven (or use your IDE's built-in Maven — IntelliJ and Eclipse both have it)
- MySQL Server running locally (MySQL Workbench makes this easier to manage)
- An IDE: IntelliJ IDEA (Community is free) or Eclipse with Spring Tools

## 2. Setup

1. Open the project folder in IntelliJ/Eclipse as a **Maven project**. It
   will download all dependencies automatically the first time (needs
   internet access).
2. Open `src/main/resources/application.properties` and set your own
   MySQL username/password. You don't need to create the database first —
   `createDatabaseIfNotExist=true` handles that.
3. Run `TrainReservationSystemApplication.java` (right-click → Run, or
   `mvn spring-boot:run` from the project root).
4. Hibernate will auto-create all the tables on first run (`ddl-auto=update`).
5. Visit `http://localhost:8080` → redirects to `/login`.

## 3. Getting test data in

There's no seed data yet. Easiest way to test the full flow:

1. Register a passenger account via `/register`.
2. To add stations/trains/schedules, either:
   - Register a user, then manually update their `role` to `ADMIN` in
     MySQL Workbench (`UPDATE users SET role='ADMIN' WHERE email='...'`),
     then log in and use `/admin`, **or**
   - Insert a few rows directly into `stations`, `trains`, and `schedules`
     via MySQL Workbench to get going faster.

## 4. Project structure

```
model/          JPA entities (User, Station, Train, Schedule, Booking, Payment, Feedback)
repository/     Spring Data JPA repositories (one per entity)
service/        Business logic layer (interface + Impl per feature)
service/payment/  Strategy pattern for payment processing
security/       Spring Security config + user details service
controller/     Handles HTTP requests, maps to Thymeleaf templates
resources/templates/   Thymeleaf HTML pages
resources/static/css/  Stylesheet
```

## 5. Design patterns already wired in (for the Design Document / viva)

- **Builder pattern** — `model/BookingBuilder.java`, used in
  `BookingServiceImpl.createBooking()`. Constructs a `Booking` step by
  step (reference number, timestamp, status) instead of a big constructor.
- **Strategy pattern** — `service/payment/PaymentStrategy.java` +
  `SimulatedCardPaymentStrategy.java`, used in `BookingServiceImpl`. Lets
  you swap in a different payment method later without touching booking
  logic.

The assignment requires **at least 2**, well-integrated and justified —
these two are real, working, and easy to explain (not bolted on for
show), which is what the rubric is looking for.

## 6. Where to go next (map to your 6 major functions)

| Function | Owner (from proposal) | Status | What to do |
|---|---|---|---|
| 1. Train Search & Favourite route management | Gunawardhana N.V.I.N | ✅ Working | Polish UI, add filters (time of day, direct only, etc.),Add favourite routes |
| 2. Seat Reservation & Booking | Naveed M.I.M | ✅ Working | Add seat-class selection, booking history filters |
| 3. Station & Route Management | Gunawardhana N.V.I.N | 🟡 Skeleton in `AdminController` | Split into its own `StationController`; add edit/delete |
| 4. Train Schedule Management | Wanasinghe G.W.M.S.T. | 🟡 Skeleton in `AdminController` | Add delay/cancel actions on schedules, notification trigger |
| 5. Customer Support & Feedback | Gunawardena M.T.A.N | ✅ Working basic flow | Add status filters, maybe categorize feedback |
| 6. Train Management | Jayasekara J.K.R.K.S | 🟡 Skeleton in `AdminController` | Split into `TrainController`; link origin/destination stations in the add-train form |

For each skeleton item: copy the pattern from `TrainController` +
`train-search.html` / `train-results.html` — controller method → service
call → Thymeleaf template. That's the whole loop used everywhere in this
codebase.

## 7. Mapping to non-functional requirements already covered

- Passwords hashed with BCrypt (`SecurityConfig.passwordEncoder()`)
- Role-based access control (`SecurityConfig.filterChain()`, `Role` enum)
- Prevents double-booking: `BookingServiceImpl.createBooking()` checks
  and decrements `availableSeats` inside a `@Transactional` method

## 8. For the Design Document (Week 10)

- **Use Case Diagram**: actors = the 5 stakeholder roles in `Role.java`;
  use cases = the methods on each Controller.
- **Class Diagram**: base it on the `model/` package — that's your
  domain model already.
- **Activity Diagrams**: one per major function — trace the flow through
  Controller → Service → Repository for each (e.g. `BookingController` →
  `BookingServiceImpl.createBooking()` → repositories is your Function 2
  activity diagram).
- **Ethical Considerations**: data privacy (password hashing, role-based
  access already implemented), accessibility (note in report — semantic
  HTML forms are used throughout, no reliance on color alone).
