# Test Cases

Test cases for the **Your Diary** API, derived from the supplied OpenAPI file (`openapi.yaml`) and the supplied Postman collection. Navigation: [README](../README.md) · [Test Plan](Test_Plan.md) · [Test Coverage](Test_Coverage.md) · [Execution Report](Test_Execution_Report.md) · [Bug Reports](Bug_Reports.md) · [Test Data](Test_Data.md)

## How to read this document

- **Actual Result / Status:** no execution results (saved responses, run exports, screenshots) were supplied with the project. Every case is therefore **Not executed**. A Postman assertion existing in the collection is not treated as proof that a request passed.
- **Expected Result** ends with a source tag:
  `[Doc]` stated in the OpenAPI file · `[A#]` a testing assumption listed below, **not** an official requirement · `[Undefined]` the documentation does not define the behaviour, so the case only records actual behaviour.
- **Postman mapping:** every request in the collection carries its test case ID in its description. The request list per case is in [Test_Coverage.md](Test_Coverage.md).
- **Test data** uses placeholders such as `<TEST_EMAIL>`. See [Test_Data.md](Test_Data.md).

### Preconditions used in the tables

| Code | Meaning |
|---|---|
| P1 | API reachable at the configured `base` URL |
| P2 | A confirmed test account exists (`email`, `password`) |
| P3 | Logged in as that account (cookie jar holds `access_token` and `refresh_token`) |
| P4 | No session cookies in the cookie jar (after logout) |

### Assumptions (not official requirements)

| ID | Assumption | Basis / conflict with documentation |
|---|---|---|
| A1 | Usernames are unique, so registering a taken username returns 400. | Not documented. The collection expects 400 (`Register - username already taken`). |
| A2 | A malformed record id returns 404 `Record not found`. | Documentation only describes 404 for an id without a matching record; `id` is a plain string. |
| A3 | The backend should require a password of at least 6 characters and a non-empty username on registration. | Stated by the author as a frontend rule (collection descriptions). OpenAPI documents the opposite: only e-mail and password are validated, no strength rules, username optional. |
| A4 | Deleting a record that does not exist, or that belongs to another user, should return 404. | Author's proposal, consistent with read/edit returning 404. OpenAPI documents 200 for a non-existent id. |
| A5 | A request to `diary-edit` without the `record` wrapper should return a body-validation message (`Invalid json object`). | Author's proposal. OpenAPI documents 401 `Invalid token` for this case. |
| A6 | An invalid access token on `change-email-request` should return 401 `Invalid token`. | Consistent with the security scheme description and all other endpoints. OpenAPI documents 500 `Server error` for this endpoint as an exception. |
| A7 | Duplicate e-mail registration returns 400. | OpenAPI lists 400 for database insert errors, but a unique e-mail constraint is not stated explicitly. |

## Test cases covered by the Postman collection

### Health

| Test Case ID | Feature / Endpoint | Test Scenario | Preconditions | Test Data | Steps | Expected Result | Actual Result | Status |
|---|---|---|---|---|---|---|---|---|
| HLT-01 | GET /api/ping | Health check answers without authentication | P1 | — | 1. Send GET /api/ping | 200; JSON body `{"status":"ok"}` [Doc] | Not executed | Not executed |

### Registration

| Test Case ID | Feature / Endpoint | Test Scenario | Preconditions | Test Data | Steps | Expected Result | Actual Result | Status |
|---|---|---|---|---|---|---|---|---|
| REG-01 | POST /api/register | Valid registration (creates an unconfirmed account) | P1; `allow_email_tests=true` | Unique username, plus-address of `<TEST_EMAIL>`, `<VALID_PASSWORD>` | 1. POST /api/register with the data | 200; `{"message":"Registered! Check your email to confirm."}`. Delivery of the e-mail is not checked by the collection [Doc] | Not executed | Not executed |
| REG-02 | POST /api/register | Required field missing: (a) e-mail, (b) password, (c) empty body | P1 | (a) username + password, (b) username + e-mail, (c) `{}` | 1. POST each data set | 400; `{"message":"Email and password required"}` [Doc] | Not executed | Not executed |
| REG-03 | POST /api/register | E-mail already registered | P1, P2 | Existing `<TEST_EMAIL>`, new username, `<VALID_PASSWORD>` | 1. POST /api/register | 400 with a non-empty message (text is dynamic); no new account [Doc, A7] | Not executed | Not executed |
| REG-04 | POST /api/register | Username already taken | P1, P2 | New e-mail, username of the test account | 1. POST /api/register | 400 with a non-empty message; no new account [A1] uniqueness is not documented | Not executed | Not executed |
| REG-05 | POST /api/login | Login before e-mail confirmation is refused | REG-01 executed | The pending account's e-mail and password | 1. POST /api/login | 403; `{"message":"Email not confirmed"}` [Doc] | Not executed | Not executed |
| REG-06 | POST /api/register | Password shorter than 6 characters is rejected by the backend | P1 | Unique e-mail and username, 5-character password | 1. POST /api/register | Expected by author: 400 `Password must be at least 6 characters long.` **Documented behaviour differs**: OpenAPI says no password rules are checked (200). See PI-01 [A3] | Not executed | Not executed |
| REG-07 | POST /api/register | Empty username is rejected by the backend | P1 | Unique e-mail, `"username":""`, `<VALID_PASSWORD>` | 1. POST /api/register | Expected by author: 400 `Username is required.` **Documented behaviour differs**: `username` is optional, only e-mail and password are validated. See PI-02 [A3] | Not executed | Not executed |

### Authentication and session

| Test Case ID | Feature / Endpoint | Test Scenario | Preconditions | Test Data | Steps | Expected Result | Actual Result | Status |
|---|---|---|---|---|---|---|---|---|
| AUTH-01 | POST /api/login | Valid login sets session cookies | P1, P2 | `<TEST_EMAIL>`, `<TEST_PASSWORD>` | 1. POST /api/login<br>2. Inspect body and Set-Cookie headers | 200; `{"message":"Logged in!"}`; `access_token` (Max-Age=900, HttpOnly) and `refresh_token` (Max-Age=604800, HttpOnly) set; no JWT in the body [Doc] | Not executed | Not executed |
| AUTH-02 | POST /api/login | Unknown e-mail | P1 | Unregistered e-mail, any password | 1. POST /api/login | 400; `Invalid email or password`; no `access_token` cookie [Doc] | Not executed | Not executed |
| AUTH-03 | POST /api/login | Wrong password for an existing account | P1, P2 | `<TEST_EMAIL>`, wrong password | 1. POST /api/login | 400; same message as AUTH-02 (does not reveal which e-mails exist); no `access_token` cookie [Doc] | Not executed | Not executed |
| AUTH-04 | POST /api/login | Credentials missing: (a) no password, (b) no e-mail, (c) empty body | P1 | (a) e-mail only, (b) password only, (c) `{}` | 1. POST each data set | 400; `{"message":"Email and password required"}` [Doc] | Not executed | Not executed |
| AUTH-05 | POST /api/refresh | Refresh with a valid refresh cookie | P3 | — | 1. POST /api/refresh (no body) | 200; `Token refreshed`; new `access_token` cookie (Max-Age=900, HttpOnly) [Doc] | Not executed | Not executed |
| AUTH-06 | POST /api/refresh | No refresh cookie | P4 | — | 1. POST /api/refresh | 401; `No refresh token` [Doc] | Not executed | Not executed |
| AUTH-07 | POST /api/refresh | Invalid refresh cookie | P4 | Header `Cookie: refresh_token=invalid.token.value` | 1. POST /api/refresh | 401; `Invalid refresh token` [Doc] | Not executed | Not executed |
| AUTH-08 | POST /api/logout | Logout clears both cookies | P3 | — | 1. POST /api/logout<br>2. Inspect Set-Cookie | 200; `Logged out`; `access_token` and `refresh_token` returned with empty values. Server-side token revocation does not exist (documented) and is not tested [Doc] | Not executed | Not executed |

### Account

| Test Case ID | Feature / Endpoint | Test Scenario | Preconditions | Test Data | Steps | Expected Result | Actual Result | Status |
|---|---|---|---|---|---|---|---|---|
| ACC-01 | GET /api/me | Current user profile | P3 | — | 1. GET /api/me | 200; `user.username`, `user.email`, `user.created_at` (valid date); e-mail equals the login e-mail; no password fields [Doc] | Not executed | Not executed |
| ACC-02 | POST /api/change-username | Change username, verify, restore | P3; variable `username` = current username | Temporary username `qa_<timestamp>` | 1. POST /api/change-username<br>2. GET /api/me<br>3. POST original username back<br>4. GET /api/me | 200 `Username updated`; /me shows the new value; after restore /me shows the original [Doc] | Not executed | Not executed |
| ACC-03 | POST /api/change-username | Username empty or missing | P3 | `{"username":""}` and `{}` | 1. POST each data set | 400; `Username required` [Doc] | Not executed | Not executed |
| ACC-04 | POST /api/change-password | Password field missing: (a) no new, (b) no current | P3 | (a) `currentPassword` only, (b) `newPassword` only | 1. POST each data set | 400; `Passwords required` [Doc] | Not executed | Not executed |
| ACC-05 | POST /api/change-password | Wrong current password | P3 | Wrong `currentPassword`, `<NEW_PASSWORD>` | 1. POST /api/change-password | 400; `Current password incorrect` [Doc] | Not executed | Not executed |
| ACC-06 | POST /api/change-password | Password round trip (change, verify, restore) | P3 | `<TEST_PASSWORD>` -> `<NEW_PASSWORD>` -> back | 1. Change password<br>2. Login with new<br>3. Login with old<br>4. Restore<br>5. Login with original | 200 `Password updated`; new password logs in; old password gives 400 `Invalid email or password`; restore and final login succeed [Doc] | Not executed | Not executed |
| ACC-07 | POST /api/change-email-request | Request an e-mail change (sends e-mail) | P3; `allow_email_tests=true` | `newEmail` = the account's own e-mail | 1. POST /api/change-email-request | 200; `Confirm new email first`; the address is not changed until the link is opened [Doc] | Not executed | Not executed |
| ACC-08 | POST /api/change-email-request | Invalid access token | P4; manual `Cookie: access_token=invalid.token.value` | `newEmail` any | 1. POST /api/change-email-request | Expected by author: 401 `Invalid token` (as on other endpoints). **Documented behaviour differs**: 500 `Server error`. See PI-06 [A6] | Not executed | Not executed |
| ACC-09 | DELETE /api/user-delete | Delete account (destructive) | P3; `allow_destructive=true` | — | 1. DELETE /api/user-delete<br>2. Login again<br>3. GET /api/me | 200 `Account deleted`, cookies cleared; login gives 400 `Invalid email or password`; /me gives 403 `No token`. Deletion of related diary data is database-dependent (documented) and not verified [Doc] | Not executed | Not executed |

### Diary records and tags

| Test Case ID | Feature / Endpoint | Test Scenario | Preconditions | Test Data | Steps | Expected Result | Actual Result | Status |
|---|---|---|---|---|---|---|---|---|
| DIA-01 | POST /api/diary | List records and tags: structure and order | P3 | — | 1. POST /api/diary (no body) | 200; `diaryRecords` array and `diaryAllTags`; records have `id_record`, `id_user`, `title`, `date`, `created_at`; newest first [Doc] | Not executed | Not executed |
| DIA-02 | POST /api/diary-send | Create a record and save tag lists | P3 | Unique title, date `2026-10-26`, feels, tags, colors, highlights, tag lists | 1. POST /api/diary-send<br>2. POST /api/diary | 200 `Record and tags added`; record count +1; new record is first; stored fields and tag lists equal the sent values [Doc] | Not executed | Not executed |
| DIA-03 | GET /api/diary-record/{id} | Read own record by id | DIA-02 executed | `id_record` from DIA-02 | 1. GET /api/diary-record/{id} | 200; fields present; `id_record` and title equal the created values [Doc] | Not executed | Not executed |
| DIA-04 | POST /api/diary-edit | Edit own record | DIA-02 executed | `record` object with new title and feels | 1. POST /api/diary-edit<br>2. GET the record | 200 `Record updated`; GET shows the new title and feels [Doc] | Not executed | Not executed |
| DIA-05 | POST /api/diary-allTags | Get the user's tag lists | DIA-02 executed | — | 1. POST /api/diary-allTags (no body) | 200; `all_Tags` and `all_Color_Tags` arrays containing the saved tags [Doc] | Not executed | Not executed |
| DIA-06 | DELETE /api/diary-delete/{id} | Delete own record and verify | DIA-02 executed | `id_record` | 1. DELETE<br>2. GET the record<br>3. POST /api/diary | 200 `Record deleted`; GET gives 404 `Record not found`; list size back to the original; id absent [Doc] | Not executed | Not executed |
| DIA-07 | GET /api/diary-record/{id} | Id that does not exist (valid UUID format) | P3 | `00000000-0000-0000-0000-000000000000` | 1. GET the record | 404; `Record not found` [Doc] | Not executed | Not executed |
| DIA-08 | GET /api/diary-record/{id} | Malformed id | P3 | `not-a-valid-id` | 1. GET the record | 404 `Record not found`, not a 5xx. The documentation does not define malformed-id handling [A2] | Not executed | Not executed |
| DIA-09 | POST /api/diary-edit | Edit a record that does not exist | P3 | `record.id_record` = nil UUID | 1. POST /api/diary-edit | 404; `Record not found`. Documentation notes that tag lists are still updated first [Doc] | Not executed | Not executed |
| DIA-10 | POST /api/diary-edit | `record` wrapper missing | P3 | Record fields at the top level of the body | 1. POST /api/diary-edit | Expected by author: message `Invalid json object`. **Documented behaviour differs**: 401 `Invalid token`. See PI-03 [A5] | Not executed | Not executed |
| DIA-11 | DELETE /api/diary-delete/{id} | Delete a record that does not exist | P3 | Nil UUID | 1. DELETE the record | Expected by author: 404 `Record not found`. **Documented behaviour differs**: 200 `Record deleted`. See PI-05 [A4] | Not executed | Not executed |

### Authorization and access control

| Test Case ID | Feature / Endpoint | Test Scenario | Preconditions | Test Data | Steps | Expected Result | Actual Result | Status |
|---|---|---|---|---|---|---|---|---|
| SEC-01 | GET /api/diary-record/{id} | User A cannot read the record of user B | P3 for A and B (two accounts) | Record id owned by B | 1. B creates a record<br>2. A requests it | 404; `Record not found` [Doc] | Not executed | Not executed |
| SEC-02 | POST /api/diary-edit | User A cannot edit the record of user B | As SEC-01 | B's record id, A's own tag lists | 1. A sends diary-edit for B's id | 404; `Record not found`; B's record unchanged (checked in SEC-03) [Doc] | Not executed | Not executed |
| SEC-03 | DELETE /api/diary-delete/{id} | User A cannot delete the record of user B (data integrity) | As SEC-01 | B's record id | 1. A sends DELETE<br>2. B reads the record<br>3. B deletes it (cleanup) | A's response is below 500 (status itself is not asserted here); B's record still exists with the original title [Doc] | Not executed | Not executed |
| SEC-04 | DELETE /api/diary-delete/{id} | Cross-user delete is answered with 404 | As SEC-01 | B's record id | 1. A sends DELETE | Expected by author: 404 `Record not found`. Reported actual: 200. Documentation does not define the status for this case. See PI-04 [A4] | Not executed | Not executed |
| SEC-05 | 11 protected endpoints | No session cookie is refused | P4 | No cookies | 1. Call each endpoint without cookies: `GET /api/me`, `POST /api/diary`, `POST /api/diary-send`, `POST /api/diary-allTags`, `GET /api/diary-record/{id}`, `POST /api/diary-edit`, `DELETE /api/diary-delete/{id}`, `POST /api/change-username`, `POST /api/change-password`, `POST /api/change-email-request`, `DELETE /api/user-delete` | 403; `{"message":"No token"}` on every endpoint [Doc] | Not executed | Not executed |
| SEC-06 | 6 protected endpoints | Invalid access token is refused | P4 | `Cookie: access_token=invalid.token.value` | 1. Call `GET /api/me`, `POST /api/diary`, `POST /api/diary-allTags`, `POST /api/change-username`, `POST /api/change-password`, `DELETE /api/user-delete` | 401; `{"message":"Invalid token"}` [Doc] | Not executed | Not executed |

## Proposed additional test cases (designed, not yet in the collection)

These cases close gaps identified in [Test_Coverage.md](Test_Coverage.md). They are **not implemented in Postman and have not been run**. Where the documentation does not define the expected behaviour, the case says so instead of inventing a requirement.

| Test Case ID | Feature / Endpoint | Test Scenario | Preconditions | Test Data | Steps | Expected Result | Actual Result | Status |
|---|---|---|---|---|---|---|---|---|
| NEW-01 | GET /api/confirm | Confirmation without token / with invalid token | P1 | No `token`; `token=<INVALID_TOKEN>` | 1. GET without token<br>2. GET with an invalid token | 400 text/html: `The token is missing`; 400 text/html: message starting `Invalid or expired token`. No mailbox needed [Doc] | — | Not implemented |
| NEW-02 | GET /api/change-email-confirm | Confirmation without token / with invalid token | P1 | No `token`; `token=<INVALID_TOKEN>` | 1. GET without token<br>2. GET with an invalid token | 400 `Token missing`; 401 `Invalid token` (text/html). No mailbox needed [Doc] | — | Not implemented |
| NEW-03 | diary-send, diary-record, diary-edit, diary-delete | Invalid access token on the remaining protected endpoints | P4 | `Cookie: access_token=invalid.token.value` | 1. Call the four endpoints | 401 (message `Invalid token`; for diary-send the message is dynamic) [Doc] | — | Not implemented |
| NEW-04 | POST /api/diary, POST /api/diary-allTags | Account without records or tags | New confirmed account | — | 1. POST /api/diary<br>2. POST /api/diary-allTags | `diaryRecords: []` and `diaryAllTags: []`; allTags answers 200 `{"message":"No records to edit"}` [Doc] | — | Not implemented |
| NEW-05 | POST /api/register | Password length boundaries: 5, 6, 7 characters | P1 | Three passwords, unique e-mails | 1. Register with each | Undefined until the requirement is confirmed (A3). If 6 is the minimum: 5 rejected, 6 and 7 accepted [A3] | — | Not implemented |
| NEW-06 | POST /api/register | Invalid e-mail format and wrong data types | P1 | `not-an-email`; number, array, `null` for each field | 1. Register with each data set | Documentation: e-mail format is not checked; type handling undefined. Record the actual behaviour and check for 5xx [Undefined] | — | Not implemented |
| NEW-07 | POST /api/register | Username boundaries | P1 | Whitespace only, 1 character, 256 characters | 1. Register with each | Undefined (username optional, no rules documented). Record the actual behaviour [Undefined] | — | Not implemented |
| NEW-08 | POST /api/diary-send | Missing, empty or wrong-type fields | P3 | No title; `feels` as string; invalid date | 1. POST each variant | Documentation: the server checks no field. Record actual behaviour; clean up created records [Undefined] | — | Not implemented |
| NEW-09 | POST /api/diary-edit | Tag lists change even when the record is not found | P3 | Nil UUID id with a changed `all_Tags` | 1. POST diary-edit<br>2. POST /api/diary-allTags<br>3. Restore tags | 404 `Record not found` (documented); tags updated first (documented). Verifies the side effect [Doc] | — | Not implemented |
| NEW-10 | POST /api/change-username | Username rules: duplicate, whitespace, 256 characters | P3 | As listed | 1. POST each; restore username | Undefined (documentation: no rules). Record actual behaviour [Undefined] | — | Not implemented |
| NEW-11 | POST /api/change-password | Short new password is accepted | P3 | `newPassword` of 1 character, then restore | 1. Change, 2. Login, 3. Restore | 200 (documentation: no strength rules). Confirms documented behaviour and informs PI-01 [Doc] | — | Not implemented |
| NEW-12 | Protected endpoints, POST /api/refresh | Expired access token | Environment with short `JWT_EXPIRE`, or a 15+ minute wait | Expired token | 1. Call a protected endpoint<br>2. POST /api/refresh<br>3. Retry | 401 `Invalid token`; refresh issues a new access token; retry succeeds. Not automatable against an uncontrolled backend [Doc] | — | Not implemented |
| NEW-13 | Protected endpoints | Access token still works after logout (exploratory) | P3 | Token copied from the cookie jar | 1. Log in, copy token<br>2. Logout<br>3. Replay token in a Cookie header | Documentation: tokens are not invalidated server-side, so the request is expected to succeed until expiry. Observation, not a documented defect [Doc] | — | Not implemented |
| NEW-14 | All endpoints | CORS: allowed vs other origin | P1 | `Origin: https://diary-cosmic-liard.vercel.app` vs `Origin: https://example.org` | 1. Send a request or preflight with each Origin | Documentation: credentials CORS is enabled for exactly two origins; no CORS allowance for other origins [Doc] | — | Not implemented |

## Summary

| Group | Count |
|---|---|
| Cases covered by the collection (all *Not executed*) | 42 |
| Proposed additional cases (*Not implemented*) | 14 |
| Of the collection cases, expected behaviour that conflicts with the OpenAPI file (potential issues) | 6 (REG-06, REG-07, ACC-08, DIA-10, DIA-11, SEC-04) |
