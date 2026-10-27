# Test Plan: Your Diary API

Navigation:  · [Test Coverage](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/test_coverage.md) · [Execution Report](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/Test_Execution_Report.md)

This is a personal portfolio project. There is no formal requirements document and no QA team. The plan is written by one person.

## 1. Introduction and goals

**Application.** ["Your Diary"](https://github.com/Ilkarion/diary_cosmic) is a personal diary web application. Its [backend](https://github.com/Ilkarion/your_book_backend) is an Express service backed by Supabase, described in the supplied OpenAPI file (`openapi.yaml`, ["Diary API"](https://ilkarion.github.io/APITesting_Your_diary/) 1.0.0). It covers registration with e-mail confirmation, cookie-based sessions, diary records with tags, and account settings.

**Goals**

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

The email confirmation endpoint is within the scope, but there are no requests for it in the sample data. This is because they can only be confirmed manually using the link sent to your email.

## 4. Features out of scope

- The web frontend (only referenced where a frontend rule is the stated source of an expectation).
- Performance, load, security penetration and accessibility testing. The only timing check is a smoke-level response-time limit.
- Database internals (Supabase), JWT secrets and server source code.
- Full e-mail flows that need mailbox access (receiving and opening confirmation links).
- Test automation frameworks beyond Postman scripts.

## 5. Test basis and available documentation

| Source | Use |
|---|---|
| `openapi.yaml` (OpenAPI 3.0.3) | Primary source of expected behaviour. Hosted at the link in [API_Documentation_Link.md](https://ilkarion.github.io/APITesting_Your_diary/). |
| Postman collection (see [collection](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/Your-Diary%20API.postman_collection.json)) | Existing requests, test scripts and the author's notes on known problems. |
| Author's notes in the collection descriptions | Frontend validation rules for registration (6-character password, required username). These are **not** in the API documentation. |

## 6. Test environment and tools

| Item | Detail |
|---|---|
| Tool | Postman (Collection Runner, cookie jar, JavaScript tests) |
| Server | Configured through variable `base` in postman. 
| Accounts | 2 registered & confirmed test account(HOWEVER, you should create your own test accounts, since in that case you won't be able to register those accounts after calling the DELETE endpoint) |
| Session handling | HttpOnly cookies `access_token` (15 min) and `refresh_token` (7 days) kept by the Postman cookie jar |

## 7. Test approach and techniques

| Technique | Where it is applied |
|---|---|
| Positive testing | Registration, login, refresh, CRUD flow, profile and password changes |
| Negative testing | Wrong, missing and empty credentials and fields; unknown and malformed ids |
| Equivalence partitioning | One representative per class: missing e-mail, missing password, empty body; no cookie vs invalid cookie |
| Authentication and session testing | Cookie attributes, refresh, logout, no token (403), invalid token (401) |
| Authorization testing | Cross-user read, edit and delete of diary records |
| Response validation | Status code, `Content-Type`, exact message, field types, JSON schema checks, sort order |
| State-based / round-trip testing | Change then verify then restore (username, password, record lifecycle) |

## 8. Entry criteria

Planned criteria (all must hold before a run):

- The API is reachable and `GET /api/ping` answers.
- A confirmed test account exists; `email`, `password` and the **current** `username` are configured.
- Optional groups only when their prerequisites exist: a second confirmed account for cross-user tests, and a mailbox for e-mail tests.
- The collection and environment are imported and variables are filled in.

## 9. Exit criteria

Planned criteria:

- All collection test cases(in postman) executed at least once and results recorded.
- Every failure analysed and classified as defect or potential issue.
- Potential issues either confirmed with evidence or closed.
- Coverage gaps listed in [Test_Coverage.md](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/test_coverage.md).

## 11. Test deliverables

| Deliverable | File |
|---|---|
| Test plan | this file |
| Coverage and traceability | [Test_Coverage.md](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/test_coverage.md) |
| Execution report | [Test_Execution_Report.md](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/Test_Execution_Report.md) |
| Bug and potential issue reports | [Bug_Reports.md](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/bugs-report.md) |
| Postman collection and environment template | [collection](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/Your-Diary%20API.postman_collection.json) |

