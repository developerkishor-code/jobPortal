# Job Portal — Simple Design Note

## 1. Entities

| Entity | Important fields / relationships |
|---|---|
| User | Identity user id, email, role (`Candidate`, `Employer`, or `Admin`), active flag |
| CandidateProfile | User id, name, phone, headline, skills, experience summary |
| Company | Name, description, location, employer user id |
| Job | Company id, title, description, location, type, created date, status (`Open`/`Closed`) |
| Application | Candidate id, job id, status, applied date |

Rules: an Employer owns one Company; a Job belongs to that Company. One Candidate has one Profile. One Candidate can apply to a Job only once, enforced by service code and a unique `(CandidateId, JobId)` database constraint.

## 2. API Endpoints

| Area | Endpoints |
|---|---|
| Auth | `POST /api/auth/register`, `POST /api/auth/login` |
| Profile | `GET/PUT /api/candidates/me`, optional `GET/PUT` resume endpoints |
| Public jobs | `GET /api/jobs?keyword=&location=&type=&page=&pageSize=`, `GET /api/jobs/{id}` |
| Employer company/jobs | `GET/PUT /api/employer/company`, `POST /api/employer/jobs`, `GET/PUT/DELETE /api/employer/jobs/{id}` |
| Employer applications | `GET /api/employer/jobs/{jobId}/applications`, `PUT /api/employer/applications/{id}/status` |
| Candidate applications | `GET /api/candidates/me/applications`, `POST /api/jobs/{id}/applications`, `PUT /api/candidates/me/applications/{id}/withdraw` |
| Admin | `GET /api/admin/users`, `GET /api/admin/companies`, `GET /api/admin/jobs`, `PUT /api/admin/jobs/{id}/close`, `PUT /api/admin/users/{id}/deactivate` |

Employer update/close, and all application access, require ownership checks. Invalid requests use standard HTTP codes and `ProblemDetails`.

## 3. Authentication

Use ASP.NET Core Identity and PostgreSQL EF Core. Register allows Candidate or Employer only; the Admin is seeded from configuration at startup. Login checks Identity credentials and JWT lifetime/deactivation.

Create a 60-minute signed JWT with `sub`, email, and `role` claims. Validate issuer, audience, signing key, lifetime, and role claim on each protected request. Keep `Jwt:*` settings in configuration, local user-secrets, and an uncommitted Docker `.env`; never commit secrets.

React uses one API helper to add the Bearer token. Protected routes hide screens, but the API remains the authority. A 401 clears the token and opens login. Role checks decide endpoint access; service ownership checks decide whether the user may access specific data.

## 4. Pull Request Plan

1. **PR 1 — Project setup:** solution, React app, PostgreSQL, Docker Compose, `.env.example`, and README start instructions.
2. **PR 2 — Identity foundation:** registration/login, Admin and demo seed data, JWT middleware, roles, and auth tests.
3. **PR 3 — Company and jobs:** employer company, job CRUD/close, public search with paging and filters, and job tests.
4. **PR 4 — Candidate features:** profile, public job pages, apply, application history, withdraw, and frontend tests.
5. **PR 5 — Applications:** employer application list and status transitions, validation, ownership checks, and status tests.
6. **PR 6 — Admin and finish:** admin lists and actions, README, Docker verification, final bug fixes, and a short midpoint status note.
