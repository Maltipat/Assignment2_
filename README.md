# Assignment2_
# Mini Leave Management System (MVP)

**Author:** Malti Pathak  
**Tech Stack:** Java · Spring Boot · Maven · SQL · Postman  


This repository contains the *design-first* deliverables for a minimal Leave Management System (MVP)
for a 50-employee startup. It includes:

- ER Diagram (`/docs/er-diagram.md`)
- Low-Level Design + Class Diagram (`/docs/lld-and-class-diagram.md`)
- SQL schema (`/db/schema.sql`
- Pseudocode for leave approval logic (`/docs/leave-approval-pseudocode.md`)
- Postman Collection (`/api/postman_collection.json`)
- Sample requests & responses (`/api/samples.http`)



## 1) Setup Steps (Backend)

> You can use these steps when you turn the design into a runnable Spring Boot service.

1. **Create project**  
   ```bash
   mvn -v  # ensure Maven installed
   spring init --dependencies=web,data-jpa,validation,h2,postgresql --build=maven lms
   ```
   Or use Spring Initializr UI to generate a project with:
   - Spring Web
   - Spring Data JPA
   - Validation
   - H2 (local dev) or PostgreSQL/MySQL for prod

2. **Database**  
   - Local/dev: H2 or PostgreSQL
   - Prod: PostgreSQL (recommended), MySQL also fine  
   - Run the DDL in `/db/schema.sql` (or let Hibernate create via JPA, then align with DDL).

3. **Config**  
   Add `application.yml`:
   ```yaml
   spring:
     datasource:
       url: jdbc:postgresql://localhost:5432/lms
       username: lms_user
       password: lms_pass
     jpa:
       hibernate:
         ddl-auto: validate
       properties:
         hibernate:
           format_sql: true
       show-sql: true
   server:
     port: 8080
   ```

4. **Run**  
   ```bash
   mvn spring-boot:run
   ```

5. **Test via Postman**  
   - Import `/api/postman_collection.json` into Postman
   - Hit endpoints described in `/api/openapi.yaml`

---

## 2) Assumptions

- Company size ~50; single approval step by HR/Manager for MVP.
- Supported leave types: `ANNUAL`, `SICK`, `UNPAID` (extensible).
- Business days vs weekends/holidays is **out of scope** for MVP; date range counts **calendar days**. (Future: `holidays` table and working-day calc).
- Leave balance is tracked yearly; allocation happens per employee per year.
- Overlapping leave requests for the same employee are disallowed (if any part overlaps with an approved or pending range).
- Fractional/half-day leaves are **not** supported in MVP (future enhancement).

---

## 3) Edge Cases Handled

- Invalid date ranges (`end_date < start_date`).
- Applying leave with **zero or negative** days.
- Applying leave when **balance is insufficient** for `ANNUAL` and `SICK` (not enforced for `UNPAID`).
- Duplicate email on employee creation.
- Overlapping leaves:
  - Pending vs new request overlap blocked to keep queues clean.
  - Approved vs new request overlap blocked.
- Double actions: approve/reject/cancel on a terminal request is blocked (idempotent safeguards).
- Back-dated approvals (allowed only if no overlap & balances suffice).
- Year boundary requests (e.g., Dec 30–Jan 2): split logic recommended (MVP rejects cross-year to simplify; see improvements).

---

## 4) Potential Improvements

- Multi-level approval workflows; delegation; comments on approval actions.
- Public holidays & weekend calendars, business-day calculation.
- Half-day and hourly leaves.
- Carry-forward rules, accrual per month, encashment workflows.
- Notifications (email/Slack) with templates.
- Role-based access control (Admin/HR/Manager/Employee).
- Audit logs & observability (request tracing, metrics).
- Robust overlapping detection using database constraints + exclusion indexes (e.g., PostgreSQL `btree_gist`).

---

## 5) Suggested Java Package Layout

```
com.example.lms
├── LmsApplication.java
├── config/
├── controller/
├── dto/
├── entity/
├── exception/
├── mapper/
├── repository/
├── service/
│   ├── EmployeeService.java
│   ├── LeaveService.java
│   └── BalanceService.java
└── util/
```

---

## 6) Deploy (Bonus)

- **Render**: Free web services; connect GitHub, set build command `mvn -DskipTests package` and run `java -jar target/*.jar`.
- **Railway/Fly.io/Heroku** (if available): Similar setup with environment variables for DB.
- Use managed PostgreSQL add-on or a free-tier DB; apply `/db/schema.sql`.
- Expose base URL and demo credentials in README once deployed.

---

## 7) How to Read This Package

- Start with `/docs/er-diagram.md`
- Then `/api/openapi.yaml` for all request/response contracts
- Skim `/docs/leave-approval-pseudocode.md`
- Validate DDL in `/db/schema.sql`
- Try the endpoints via Postman import
