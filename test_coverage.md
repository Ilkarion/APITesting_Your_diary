# Postman Collection: Your Diary API

Navigation: [Project](https://github.com/Ilkarion/APITesting_Your_diary) · [Test Coverage](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/test_coverage.md) · [Execution Report](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/Test_Execution_Report.md)

| File | Purpose |
|---|---|
| [Your_Diary_API.postman_collection.json](Your_Diary_API.postman_collection.json) | The collection (96 requests) and with environment variables |


## 1. How the collection is organised

Folders run top to bottom.

| # | Folder | Purpose | Needs |
|---|---|---|---|
| 1 | `Health` | Smoke test: `GET /api/ping` (cold-start tolerant, 60 s limit) | — |
| 2 | `Auth` > `Positive` / `Negative` | Login, refresh, invalid logins. **Creates the session cookies** used by later folders | confirmed account |
| 3 | `Account` | Profile, username round trip, password round trip, e-mail change request | confirmed account; `username` variable; e-mail folder needs `allow_email_tests` |
| 4 | `Diary` > `CRUD flow` / `Negative` | Record lifecycle with verification after every step; not-found cases | session |
| 5 | `Registration` | Negative registration cases (no side effects) and one positive case that sends e-mail | `Positive` needs `allow_email_tests` |
| 6 | `Authorization (cross-user)` | User A against the records of user B | `email_b`, `password_b` |
| 7 | `Bugs` | Unverified or reported problems; assert the *expected* behaviour and fail by design | see [Bug_Reports.md](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/bugs-report.md) |
| 8 | `Logout & access control` | 403 without cookie, 401 with forged cookie, logout | empty cookie jar at start |
| 9 | `Destructive (manual)` | Deletes the test account | `allow_destructive=true` |

## 2. Import

1. In Postman choose **Import**.
2. Select `Your-Diary API.postman_collection.json` file.
3. The environment appears as *Your Diary*.

## 3. Configure the environment

Fill the variables in the environment's **Current value** column. (For good testing experience use ur own accounts).

| Variable | Required | Value |
|---|---|---|
| `base` | yes | API address without trailing slash. Template default: the documented local address `http://localhost:3001`. |
| `email`, `password` | yes | A **confirmed** test account. Create it once and confirm the e-mail. |
| `username` | yes | The account's **current** username. The username test restores this value. |
| `new_password` | for password tests | good password already created in your env-variables |
| `email_b`, `password_b` | optional | Second confirmed account. Leave empty to skip the cross-user folder. |
| `allow_email_tests` | optional | `true` enables requests that send real e-mails and create unconfirmed accounts |
| `allow_destructive` | optional | `true` enables account deletion |
| `max_response_ms` | optional | Response-time limit, default 6000 |

Variables starting with `rt_` are runtime state set by the scripts; do not set them by hand.

**Cookies.** Login sets HttpOnly cookies, which the Postman cookie jar sends automatically. The OpenAPI file documents that `Secure` cookies are used when the server runs with `PROD=true`; over plain `http://` such cookies may not be stored or sent.

## 4. Run individual requests

Select the folder's first request and run in order. Chains (CRUD flow, username, password) depend on earlier steps; sending a middle request alone fails because its runtime variable is missing. Cookies can be inspected in **Cookies** (below the *Send* button) for the backend's domain.

## 5. Run the whole collection

1. Right click on collection folder > **Run Collection**.
2. Keep the folder order.
3. Recommended first runs: (a) everything except `Destructive (manual)`; Destructive last, if wanted.
4. Do not clear cookies between requests; the session depends on the cookie jar.
5. See result; see [Example](https://github.com/Ilkarion/APITesting_Your_diary/tree/main/imgs).

## 6. Interpret results

| Observation | Meaning |
|---|---|
| All tests of a request green | The response matched the assertions. |
| Red test in a normal folder | Analyse first: wrong response, wrong assertion, wrong expectation, wrong data or order. Only then decide on a defect. |
| Red test in the potential-issues folder | Expected by design while the behaviour differs from the expectation. Capture the response as evidence, then follow [Bug_Reports.md](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/bugs-report.md). |
| "Blocked: ..." error | Guard hit in an old Postman version; update Postman or set the variable. |
| Response-time failure | Limit is 6000 ms (60000 ms for the first request). Check cold start or network before suspecting the API. |


## 8. Known limitations and prerequisites

- `GET /api/confirm` and `GET /api/change-email-confirm` have no requests.
- E-mail receipt and confirmation links are not verified.
