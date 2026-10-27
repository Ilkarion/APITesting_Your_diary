# Test Plan: Your Diary API

Navigation: [README](../README.md) · [Test Cases](Test_Cases.md) · [Test Coverage](Test_Coverage.md) · [Execution Report](Test_Execution_Report.md) · [Bug Reports](Bug_Reports.md) · [Test Data](Test_Data.md)

This is a personal portfolio project. There is no formal requirements document and no QA team. The plan is written by one person, and it separates what was **planned** from what was **actually achieved**.

## 1. Introduction and objectives

**Application.** "Your Diary" is a personal diary web application. Its backend is an Express service backed by Supabase, described in the supplied OpenAPI file (`openapi.yaml`, "Diary API" 1.0.0). It covers registration with e-mail confirmation, cookie-based sessions, diary records with tags, and account settings.

**Objectives**

- Check the backend behaviour against the documented API contract.
- Check authentication, session cookies and access control.
- Check that diary records are created, read, updated and deleted correctly and are isolated between users.
- Find and document backend validation gaps that the web frontend hides.
- Keep the tests repeatable in Postman, with clear cleanup.

## 2. Scope of testing

API-level, manual-first testing of the backend through Postman, with test scripts that automate the assertions. Includes functional, negative, authentication/authorization, response-structure and basic response-time checks.

## 3. Features and endpoints in scope

| Area | Endpoints |
|---|---|
| System | `GET /api/ping` |
| Registration | `POST /api/register`, `GET /api/confirm` |
| Session | `POST /api/login`, `POST /api/refresh`, `POST /api/logout` |
| Account | `GET /api/me`, `POST /api/change-username`, `POST /api/change-password`, `POST /api/change-email-request`, `GET /api/change-email-confirm`, `DELETE /api/user-delete` |
| Diary | `POST /api/diary`, `POST /api/diary-send`, `POST /api/diary-allTags`, `GET /api/diary-record/{id}`, `POST /api/diary-edit`, `DELETE /api/diary-delete/{id}` |

The two e-mail confirmation endpoints are in scope, but the collection contains no requests for them yet (see [Test_Coverage.md](Test_Coverage.md)).

## 4. Features out of scope

- The web frontend (only referenced where a frontend rule is the stated source of an expectation).
- Performance, load, security penetration and accessibility testing. The only timing check is a smoke-level response-time limit.
- Database internals (Supabase), JWT secrets and server source code. The supplied OpenAPI file mentions source details, but the code itself was not supplied.
- Full e-mail flows that need mailbox access (receiving and opening confirmation links).
- Test automation frameworks beyond Postman scripts.

## 5. Test basis and available documentation

| Source | Use |
|---|---|
| `openapi.yaml` (OpenAPI 3.0.3) | Primary source of expected behaviour. Hosted at the link in [API_Documentation_Link.md](../docs/API_Documentation_Link.md). |
| Postman collection (see [postman/](../postman/README.md)) | Existing requests, test scripts and the author's notes on known problems. |
| Author's notes in the collection descriptions | Frontend validation rules for registration (6-character password, required username). These are **not** in the API documentation. |

No formal requirements, user stories or acceptance criteria were supplied. Where expected behaviour is not documented, the test case says so and uses a labelled assumption (A1 to A7 in [Test_Cases.md](Test_Cases.md)).

## 6. Test environment and tools

| Item | Detail |
|---|---|
| Tool | Postman (Collection Runner, cookie jar, JavaScript tests) |
| Server | Configured through `base`. The OpenAPI file documents `http://localhost:3001` as the local default. The address of any deployed backend is not part of the supplied materials. |
| Accounts | One confirmed test account; optionally a second one for cross-user tests |
| Session handling | HttpOnly cookies `access_token` (15 min) and `refresh_token` (7 days) kept by the Postman cookie jar |
| Data | See [Test_Data.md](Test_Data.md) |

## 7. Test approach and techniques

| Technique | Where it is applied |
|---|---|
| Positive testing | Registration, login, refresh, CRUD flow, profile and password changes |
| Negative testing | Wrong, missing and empty credentials and fields; unknown and malformed ids |
| Equivalence partitioning | One representative per class: missing e-mail, missing password, empty body; no cookie vs invalid cookie |
| Boundary value analysis | Planned only (password length 5/6/7, username lengths: NEW-05, NEW-07). Not yet implemented because the required limits are undefined (A3). |
| Authentication and session testing | Cookie attributes, refresh, logout, no token (403), invalid token (401) |
| Authorization testing | Cross-user read, edit and delete of diary records |
| Response validation | Status code, `Content-Type`, exact message, field types, JSON schema checks, sort order |
| State-based / round-trip testing | Change then verify then restore (username, password, record lifecycle) |
| Exploratory testing | Planned: invalid types and formats, token reuse after logout, tag side effects (NEW-06 to NEW-13). Not yet performed. |

Not applied: pagination, filtering and rate-limit techniques, because the documented API has none.

## 8. Entry criteria

Planned criteria (all must hold before a run):

- The API is reachable and `GET /api/ping` answers.
- A confirmed test account exists; `email`, `password` and the **current** `username` are configured.
- Optional groups only when their prerequisites exist: a second confirmed account for cross-user tests, and a mailbox for e-mail tests.
- The collection and environment are imported and variables are filled in.

## 9. Exit criteria

Planned criteria:

- All collection test cases executed at least once and results recorded.
- Every failure analysed and classified as defect, potential issue, test defect or environment problem.
- Potential issues either confirmed with evidence or closed.
- Coverage gaps listed in [Test_Coverage.md](Test_Coverage.md).

**Actually satisfied:** none of the exit criteria are satisfied yet. No execution evidence was supplied, so the run, the failure analysis and the evidence capture remain to be done. The documentation, test design and coverage analysis are complete.

## 10. Risks, assumptions and limitations

| Risk / limitation | Effect |
|---|---|
| No execution evidence available | All results are *Not executed*; no defect is confirmed. |
| Some expected results are assumptions | Tests such as REG-04 and DIA-08 may fail only because the assumption is wrong. |
| Registration expectations come from the frontend | The documentation says the backend does not validate password length or username. |
| Real e-mails and accounts are created | Guarded by `allow_email_tests`; unconfirmed accounts cannot be removed through the API. |
| Run-order dependencies | Some groups rely on session state created earlier (see [Postman README](../postman/README.md)). |
| Shared test account state | An interrupted run can leave a changed username or password; see cleanup notes. |
| Live documentation not validated | The hosted page renders client-side; only the supplied `openapi.yaml` was analysed. |

## 11. Test deliverables

| Deliverable | File |
|---|---|
| Test plan | this file |
| Test cases | [Test_Cases.md](Test_Cases.md) |
| Coverage and traceability | [Test_Coverage.md](Test_Coverage.md) |
| Test data description | [Test_Data.md](Test_Data.md) |
| Execution report | [Test_Execution_Report.md](Test_Execution_Report.md) |
| Bug and potential issue reports | [Bug_Reports.md](Bug_Reports.md) |
| Postman collection and environment template | [postman/](../postman/README.md) |
| Evidence guidance | [evidence/README.md](../evidence/README.md) |

## 12. Defect reporting approach

- **Confirmed defect:** the expected behaviour is supported by the documentation or an explicit requirement, the actual behaviour is captured (response, screenshot or run export), and the steps are reproducible. Logged as `BUG-nnn`.
- **Potential issue:** a plausible problem where evidence is incomplete, for example the expectation is an assumption or only the author's note exists. Logged as `PI-nn` in a separate section until verified.
- **Documented behaviour that may need a design decision** is recorded as an observation, not as a defect.
- Severity and priority are assigned only after verification and only with a stated reason.
- A failing Postman assertion is *not* a defect by itself: first decide whether the response, the assertion or the expectation is wrong.
