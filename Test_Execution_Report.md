# Test Execution Report

Navigation: [README](../README.md) · [Test Cases](Test_Cases.md) · [Bug Reports](Bug_Reports.md) · [Test Coverage](Test_Coverage.md) · [Test Plan](Test_Plan.md)

> **Status: pending execution.** The materials supplied for this project contain the Postman collection and the API documentation, but **no run results**: no saved responses, no collection run export, no screenshots. This report is therefore a template. No result in it has been invented, and no test is classified as passed because a script exists.

## 1. Project and environment

| Item | Value |
|---|---|
| Project | Your Diary API testing portfolio |
| API under test | Diary API 1.0.0 (Express + Supabase), described by `openapi.yaml` |
| Collection | [Your_Diary_API.postman_collection.json](../postman/Your_Diary_API.postman_collection.json) |
| Environment | *To be filled in:* base URL (not part of the supplied materials; documented local default `http://localhost:3001`) |
| Postman version | *To be filled in* |
| Execution period | *Not known* |
| Tester | *To be filled in* |
| Variables enabled | `allow_email_tests`: *to be filled in* · `allow_destructive`: *to be filled in* · second account: *yes / no* |

## 2. Execution scope

Planned: the 42 test cases in [Test_Cases.md](Test_Cases.md), executed through the 96 requests of the collection. Groups that need extra setup are skipped by the collection unless enabled: e-mail tests (REG-01, REG-05, ACC-07), cross-user tests (SEC-01 to SEC-04) and the destructive test (ACC-09).

## 3. Summary of results

Counts are of **test cases**. Evidence-based counts are zero because nothing has been executed with recorded evidence.

| Result | Count | Note |
|---|---|---|
| Passed | 0 | no evidence |
| Failed | 0 | no evidence |
| Blocked | 0 | no evidence |
| Not executed / not verified | 42 | all cases |
| **Total** | **42** | |

### Expected outcome of known-issue tests

Six cases assert the *expected* behaviour of potential issues and are designed to fail while the API behaves as documented: REG-06, REG-07, ACC-08, DIA-10, DIA-11, SEC-04. A failure there is not a test-suite problem; it is the signal that must be examined and captured in [Bug_Reports.md](Bug_Reports.md). Run these folders separately from the main regression run so that the main summary stays readable.

## 4. Result log (to be completed after the run)

Record one row per test case. Distinguish three things: what the API returned, whether the assertion passed, and whether a defect is confirmed.

| Test case | API response observed (status, message) | Assertions (passed / total) | Verdict | Evidence file | Bug / PI link |
|---|---|---|---|---|---|
| HLT-01 | | | | | |
| REG-01 to REG-05 | | | | | |
| REG-06 | | | | | [PI-01](Bug_Reports.md) |
| REG-07 | | | | | [PI-02](Bug_Reports.md) |
| AUTH-01 to AUTH-08 | | | | | |
| ACC-01 to ACC-07, ACC-09 | | | | | |
| ACC-08 | | | | | [PI-06](Bug_Reports.md) |
| DIA-01 to DIA-09 | | | | | |
| DIA-10 | | | | | [PI-03](Bug_Reports.md) |
| DIA-11 | | | | | [PI-05](Bug_Reports.md) |
| SEC-01 to SEC-03 | | | | | |
| SEC-04 | | | | | [PI-04](Bug_Reports.md) |
| SEC-05, SEC-06 | | | | | |

Verdict values: *Passed* (response and assertions as expected) · *Failed - defect* · *Failed - potential issue* · *Failed - test or data problem* · *Blocked* · *Skipped by guard*.

## 5. Failed test details

None recorded. After the run, copy each failure here with the request name, the actual response, the failed assertion and the classification.

## 6. Related documents

- Test cases: [Test_Cases.md](Test_Cases.md)
- Potential issues and defects: [Bug_Reports.md](Bug_Reports.md)
- Collection review findings that can cause failures that are not product defects (CI-01 to CI-10): [Postman README](../postman/README.md#collection-review-findings)

## 7. Known limitations

- No execution evidence; no execution period.
- The deployed backend address is not part of the supplied materials.
- E-mail delivery and confirmation links cannot be verified by the collection.
- Expected results marked `[A#]` in the test cases are assumptions.

## 8. Final assessment

**Not possible yet.** The tested functionality cannot be rated until the collection has been run. What can be stated from the supplied materials is limited to test design quality: documented behaviour is mapped to test cases, six expectations that conflict with the documentation are isolated as potential issues, and two documented endpoints have no tests.
