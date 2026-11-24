# Job Portal Project Instructions

## Project goals
- Build the assignment as a simple full-stack app: ASP.NET Core Web API, React, PostgreSQL, and Docker Compose.
- Keep code easy for a fresher to read: use clear names, small methods, basic controllers/services, and minimal abstractions.
- Follow the assignment clarification note when it differs from the original assignment.

## Technology and structure
- Backend: ASP.NET Core with EF Core and ASP.NET Core Identity.
- Frontend: React, with a shared API helper for all HTTP requests.
- Database: PostgreSQL.
- Use a clear folder structure. Suggested backend folders: `Controllers`, `Services`, `Models`, `Data`, and `Tests`.
- Use a clear frontend structure. Suggested frontend folders: `pages`, `components`, `services`, and `tests`.
- Do not add an advanced framework or library unless it is needed for a requirement.

## Authentication and authorization
- Use ASP.NET Core Identity for users, password hashing, and roles.
- Login creates a JWT signed with the configured key.
- Put JWT settings in configuration: `Jwt:Key`, `Jwt:Issuer`, `Jwt:Audience`, and token lifetime.
- Use the `role` claim for the Candidate, Employer, or Admin role consistently.
- Add `[Authorize]` and `[Authorize(Roles = "...")]` where endpoints require login or a role.
- Take the user id from the token for ownership checks. Never trust a user id or role from the request body or URL.
- Check both role and ownership; use 401 for no valid login and 403 for a valid but unauthorized user.
- Never store plain-text passwords or commit keys, passwords, `.env`, or other secrets.

## API and data rules
- Return `ProblemDetails` for expected 400, 401, 403, 404, and 409 responses.
- Add a service check and unique database constraint to prevent duplicate applications.
- A candidate may apply only to an Open job.
- Only the current employer may manage its company and jobs, and only the employer may view applications for its jobs.
- Candidate profile changes may be made only by that candidate.
- Job search and filtering must run on the server. Default page size is 10 and maximum is 50.
- Sort public job results newest first.
- Enforce application status transitions in service code:
  - Applied: Employer -> UnderReview or Rejected; Candidate -> Withdrawn.
  - UnderReview: Employer -> Shortlisted or Rejected; Candidate -> Withdrawn.
  - Shortlisted: Employer -> Hired or Rejected.
  - Hired, Rejected, and Withdrawn are final.
- Use 409 Conflict for an invalid status transition.

## Frontend
- Send JWT requests through one API helper as `Authorization: Bearer <token>`.
- Clear the stored token and send the user to login on 401.
- Add protected routes, loading states, error states, and basic form validation.
- Keep components small and put API calls in the API service, not in every component.

## Tests and verification
- Add backend tests for job creation, successful and duplicate applications, closed jobs, unauthorized access, and status updates.
- Add at least one frontend component test and one frontend API mock test.
- Test at least: missing token -> 401, Candidate using Employer endpoint -> 403, cross-employer application access -> 403, and cross-candidate profile change -> 403.
- Before finishing a change, run the narrowest relevant tests, then build or run the app if practical.

## Docker and configuration
- The project must start with `docker compose up --build`.
- Add backend and frontend Dockerfiles and PostgreSQL to Docker Compose.
- Keep local secrets in dotnet user-secrets.
- Keep Docker secrets in an uncommitted `.env`.
- Commit `.env.example` with fake values only.

## Git and changes
- Work on feature branches and merge through reviewed pull requests.
- Make small, meaningful commits.
- Do not commit directly to main or make a single final bulk commit.
- Update the README when setup or demo behavior changes.
