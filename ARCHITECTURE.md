# Architectural Decisions
 
Decisions that are fixed before the project starts. Everything else is decided when we reach it (see the end of this file).
 
---
 
## ADR-001: Permission-based authorization

**Decision:**
- Policies check **permissions**, never role names. Roles are only bundles of permissions, mapped in one place.
- Policies are called in the `FormRequest` (`authorize()`). Endpoints without a FormRequest (e.g. GET) use `$this->authorize()` or `can:` middleware.
- Permission names follow `resource-action-scope`:
  - `responses-view-all`: all responses
  - `responses-view-created`: responses of surveys the user created
  - `responses-view-own`: responses the user submitted
  - `surveys-edit-all`, `surveys-edit-own`
- `-all` skips the ownership check, the other scopes check ownership inside the policy.
**Why:** Role changes do not touch policy code, and ownership rules live in one testable place.
 
---
 
## ADR-002: Thin controllers, logic in services
 
**Decision:** Controllers only receive validated input, call a service and return a resource. Business logic lives in dedicated service classes.
 
**Why:** Services are testable without HTTP.
 
---
 
## ADR-003: Validation in FormRequests

**Decision:**
- Every endpoint validates in a dedicated `FormRequest`.
- Type-specific rules (e.g. `scale`: valid range, `min < max`) live in one dedicated class (e.g. `AnswerRules`) that returns the rules for a question via `match` on the question type (a PHP enum). The `FormRequest` calls this class.
- Submitted answers are validated against their question's definition.

**Why:** Keeps validation out of controllers and services, and the type rules are unit-testable on their own. 
 
---
 
## ADR-004: API Resources as response format

**Decision:** All responses use API Resources, never raw models. Author and admin views of responses use resources that never include `user_id`.
 
**Why:** Explicit fields, and ADR-008 is enforced by structure.
 
---
 
## ADR-005: API versioning

**Decision:** All routes live under `/api/v1/`.
 
---
 
## ADR-006: Only logged-in users respond, once per survey

**Decision:** Only authenticated users can submit a response. A unique constraint on `(survey_id, user_id)` allows one response per user and survey. The duplicate case is handled explicitly (catch the violation, return a clear error).
 
**Why:** The constraint is safe against double-clicks and parallel requests.
 
---
 
## ADR-007: Survey lifecycle

**Decision:** A survey goes `draft` → `published` → `closed`. `draft` to `published` Transitions are one-way and irreversible while a `closed` survey can get reopened again. A published and closed survey can only change its `end_at` (other than the status). The rule is enforced in the service layer.
 
**Why:** Responses always match the questions they were given for. Mistakes in a published survey could mean creating a new one.
 
---
 
## ADR-008: Respondents are anonymous to author and admin

**Decision:** Author and admin never see which user submitted a response (see ADR-004).
 
**Why:** privacy towards the respondents 
 
---
 
## ADR-009: Responses survive user deletion

**Decision:** `responses.user_id` is nullable with `nullOnDelete()`, no cascade. The unique constraint from ADR-006 still works because MySQL allows multiple `NULL` values.
 
**Why:** Deleting a user must not change survey results.
 
---
 
## ADR-010: Testing strategy

**Decision:**
- Pest with model factories. Tests run against MySQL
- Feature (HTTP) tests are the main layer: per endpoint happy path, 401/403, 422 and the domain rule. Unit tests for services, policy tests per permission.
- Tests are written before each feature implementation and run in CI on every push.

## ADR-011: Effect on survey status when reaching `end_at` 

**Decision** 
- A survey gets automatically closed when it reaches the `end_at` date

**Why:** A survey can get extended while it's published and also reopened with a different `end_at` date so manually closing a survey when its `end_at` is exeeded brings unwanted complexity for the end user 

## ADR-012: Rules for 'end_at'

**Decision:**
- If set... `end_at` has to be in the future and after the `start_at` date

**Why** setting `end_at` at a past date would automatically lead to the survey beeing closed as of ADR-011.
 
## Decided / documented later

- response submission, whether checks are needed if every answer belongs to a question of the survey
- Whether published or closed surveys can be deleted, and what happens to their responses
- Aggregated results as a separate endpoint
- Pagination, error format, rate limiting on submission