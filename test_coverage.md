# Postman Collection: Your Diary API

Navigation: [Project README](../README.md) · [Test Cases](../test-documentation/Test_Cases.md) · [Test Data](../test-documentation/Test_Data.md) · [Test Coverage](../test-documentation/Test_Coverage.md)

| File | Purpose |
|---|---|
| [Your_Diary_API.postman_collection.json](Your_Diary_API.postman_collection.json) | The collection (96 requests) |
| [Your_Diary_Environment.example.json](Your_Diary_Environment.example.json) | Environment template with empty or placeholder values |

> The collection has **not been executed as part of this documentation work**, and no result is claimed. It cannot run successfully without the setup described below: a reachable API, a confirmed test account, and (for some groups) a second account or a mailbox.

## 1. How the collection is organised

Folders run top to bottom. Every request description starts with its test case ID and endpoint, so a request can be traced to [Test_Cases.md](../test-documentation/Test_Cases.md).

| # | Folder | Purpose | Needs |
|---|---|---|---|
| 1 | `Health` | Smoke test: `GET /api/ping` (cold-start tolerant, 60 s limit) | — |
| 2 | `Auth` > `Positive` / `Negative` | Login, refresh, invalid logins. **Creates the session cookies** used by later folders | confirmed account |
| 3 | `Account` | Profile, username round trip, password round trip, e-mail change request | confirmed account; `username` variable; e-mail folder needs `allow_email_tests` |
| 4 | `Diary` > `CRUD flow` / `Negative` | Record lifecycle with verification after every step; not-found cases | session |
| 5 | `Registration` | Negative registration cases (no side effects) and one positive case that sends e-mail | `Positive` needs `allow_email_tests` |
| 6 | `Authorization (cross-user)` | User A against the records of user B | `email_b`, `password_b` |
| 7 | `Potential Issues (expected to fail)` | Unverified or reported problems; assert the *expected* behaviour and fail by design | see [Bug_Reports.md](../test-documentation/Bug_Reports.md) |
| 8 | `Logout & access control` | 403 without cookie, 401 with forged cookie, logout | empty cookie jar at start (see CI-04) |
| 9 | `Destructive (manual)` | Deletes the test account | `allow_destructive=true` |

Guards use `pm.execution.skipRequest()` (a recent Postman version); in older versions a guarded request fails with a "Blocked: ..." error instead of being skipped.

## 2. Import

1. In Postman choose **Import** and select both JSON files.
2. The environment appears as *Your Diary - Environment (example)*. Duplicate it for your own use and select it before running. Do not commit the filled-in copy.

## 3. Configure the environment

Fill the variables in the environment's **Current value** column (Postman does not sync current values, which keeps secrets local).

| Variable | Required | Value |
|---|---|---|
| `base` | yes | API address without trailing slash. Template default: the documented local address `http://localhost:3001`. |
| `email`, `password` | yes | A **confirmed** test account. Create it once on the website and confirm the e-mail. |
| `username` | yes | The account's **current** username. The username test restores this value. |
| `new_password` | for password tests | Any password different from `password` |
| `email_b`, `password_b` | optional | Second confirmed account. Leave empty to skip the cross-user folder. |
| `allow_email_tests` | optional | `true` enables requests that send real e-mails and create unconfirmed accounts |
| `allow_destructive` | optional | `true` enables account deletion |
| `max_response_ms` | optional | Response-time limit, default 6000 |

Variables starting with `rt_` are runtime state set by the scripts; do not set them by hand.

**Cookies.** Login sets HttpOnly cookies, which the Postman cookie jar sends automatically. The OpenAPI file documents that `Secure` cookies are used when the server runs with `PROD=true`; over plain `http://` such cookies may not be stored or sent. Use a backend setting that matches your `base` scheme.

## 4. Run individual requests

Select the folder's first request and run in order. Chains (CRUD flow, username, password) depend on earlier steps; sending a middle request alone fails because its runtime variable is missing. Cookies can be inspected in **Cookies** (below the *Send* button) for the backend's domain.

## 5. Run the whole collection

1. Open the collection > **Run**.
2. Select the environment, keep the folder order, **Run order** unchanged.
3. Recommended first runs: (a) everything except `Potential Issues (expected to fail)` and `Destructive (manual)`; (b) the potential-issues folder alone; (c) destructive last, if wanted.
4. Do not clear cookies between requests; the session depends on the cookie jar.
5. Export the run result (**Export Results**) and capture screenshots; see [evidence/README.md](../evidence/README.md).

## 6. Interpret results

| Observation | Meaning |
|---|---|
| All tests of a request green | The response matched the assertions. This is **not** proof of correct behaviour beyond what the assertions check (see assertion depth in [Test_Coverage.md](../test-documentation/Test_Coverage.md)). |
| Red test in a normal folder | Analyse first: wrong response, wrong assertion, wrong expectation, wrong data or order. Only then decide on a defect. |
| Red test in the potential-issues folder | Expected by design while the behaviour differs from the expectation. Capture the response as evidence, then follow [Bug_Reports.md](../test-documentation/Bug_Reports.md). |
| Request skipped | A guard variable is not set. Intended. |
| "Blocked: ..." error | Guard hit in an old Postman version; update Postman or set the variable. |
| Response-time failure | Limit is 6000 ms (60000 ms for the first request). Check cold start or network before suspecting the API. |

## 7. Test data and cleanup

See [Test_Data.md](../test-documentation/Test_Data.md). Key points:

- Most groups clean up after themselves (record deleted, username and password restored).
- **If a run stops inside the password chain**, send `Change password - restore original` manually.
- **Unconfirmed accounts** created by registration tests cannot be removed through the API.
- Saving a record overwrites the account's saved tag lists.

## 8. Known limitations and prerequisites

- No result evidence exists yet; all cases are *Not executed*.
- `GET /api/confirm` and `GET /api/change-email-confirm` have no requests.
- E-mail receipt and confirmation links are not verified.
- Order dependencies listed in CI-04 and in the table above.
- HttpOnly cookies are not readable in the cookie jar by value for scripts (to be confirmed on your Postman version); most cookie checks therefore use the `Set-Cookie` headers (see CI-10).

## Changes made to the supplied collection

The supplied collection was kept intact: 96 requests, same methods and URLs, same test scripts, except for the changes below. No tests were added.

| ID | Change | Reason |
|---|---|---|
| M-1 | Collection name, description and variable descriptions rewritten; `username` added to the variable table | The original table omitted a variable that the username test needs; wording referred to a folder `01 Auth` that does not exist |
| M-2 | Folder `Bugs` renamed `Potential Issues (expected to fail)`, sub-folder `cross-user` renamed `Cross-user delete`; its description rewritten | The name implied confirmed defects, but none is confirmed on the available evidence |
| M-3 | Request names normalised (for example `User_B - login` to `User B - login`, `register with paswrd < 6 symbols` to `Register - password shorter than 6 characters`, the first `register` to `Register - valid data (fixed username, legacy)`) | Consistent naming. Scripts do not reference request names |
| M-4 | Test case ID, endpoint and potential-issue ID prepended to every request description; the `Get current user` description corrected | Traceability; the old text claimed that the request stores the username, which its script does not do |
| S-1 | `List records and tags - before`: `to.have.all.keys(...)` changed to `to.include.all.keys(...)` | The exact-key check contradicts the documented record fields and the later assertions on `feels` and `tags` of list items (CI-02). Static analysis only; confirm on a run |
| S-2 | `Get current user - username restored`: `pm.collectionVariables.get("username")` changed to `pm.variables.get("username")` | The old call ignores a value set in the environment, so the check compares against an empty string (CI-03) |
| S-3 | `Delete account`: removed a leftover `console.log(val)` | Debug output |
| S-4 | `Register - password shorter than 6 characters`: body username changed from a fixed value to `{{unique_username}}` | Keeps the request to a single variable: a taken username could otherwise produce a 400 that hides the password result (PI-01) |

All edited scripts were syntax-checked with Node.js. Requests, URLs, methods and bodies are otherwise identical to the supplied file (compared programmatically). The variable name `base` was kept instead of renaming it to `baseUrl`, because renaming would break existing environments and gain nothing.

## Collection review findings

Findings about the test suite itself (not the API). "Not changed" items are recommendations.

| ID | Finding | Status |
|---|---|---|
| CI-01 | `Register - valid data (fixed username, legacy)` (first request of `Auth > Positive`) uses a fixed username, is not guarded, and expects 200, which can only happen for an unregistered e-mail. On the confirmed account described in the prerequisites the API is expected to answer 400. It duplicates `Registration > Positive`. | Note added to description. Recommended: remove or guard it. |
| CI-02 | Exact-key assertion on list records contradicts the documented schema | Fixed (S-1) |
| CI-03 | `username` variable undocumented and read through the wrong scope | Fixed (S-2). Recommended: capture the original username in `Get current user` |
| CI-04 | `No token (403)` needs an empty cookie jar, which currently comes from `Setup - Logout` in the potential-issues folder. Running the folder alone fails. | Not changed. Recommended: log out at the start of the folder |
| CI-05 | Cookie-attribute checks use one regex over both `Set-Cookie` headers, so `access_token=.*?HttpOnly` can match the attribute of `refresh_token`. A missing flag on one cookie could go unnoticed | Not changed. Recommended: assert per cookie |
| CI-06 | The two registration requests in the potential-issues folder are not guarded by `allow_email_tests`; if the problems exist they create unconfirmed accounts and send e-mail | Not changed (S-4 only). Recommended: add the guard |
| CI-07 | The same pre-request snippet is copied into 6 requests; `unique_email` and `unique_username` are computed but unused in some | Not changed |
| CI-08 | Leftover debug output | Fixed (S-3) |
| CI-09 | The potential-issues folder fails by design and distorts the summary of a full run. Some of its expectations contradict the OpenAPI file | Renamed and documented (M-2); run it separately |
| CI-10 | `Delete account` checks that cookies are cleared with `pm.cookies.get(...)`. If Postman does not expose HttpOnly cookies to scripts, `undefined` is always returned and the check passes vacuously. `Logout` checks the `Set-Cookie` headers instead, which is the sturdier approach. *Suspicion from static review, not tested.* | Not changed. Verify on a run |
