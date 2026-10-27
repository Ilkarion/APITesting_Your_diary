# Test Execution Report

Navigation: [Project](https://github.com/Ilkarion/APITesting_Your_diary) · [Bug Reports](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/bugs-report.md) · [Test Coverage](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/test_coverage.md) · [Test Plan](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/Test_Plan.md)

> **Status: pending execution.** The materials supplied for this project contain the Postman collection and the API documentation, but **not ALL run results**: no saved responses, no collection run export **just** screenshots. Thus, this report serves as a brief introduction.

## 1. Project and environment

| Item | Value |
|---|---|
| Project | Your Diary API testing portfolio |
| API under test | Diary API 1.0.0 (Express + Supabase), described by `openapi.yaml` |
| Collection + Env.-Variables | [Your_Diary_API.postman_collection.json](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/Your-Diary%20API.postman_collection.json) |
| Postman version | 12.34.1 |
| Tester | Ilkarion |
| Variables enabled | `allow_email_tests`: *true* · `allow_destructive`: *true* · `second account`: *yes* ...|

## 2. Execution scope

Planned: the 42 test cases in [Test_Cases.md](Test_Cases.md), executed through the 96 requests of the collection. Groups that need extra setup are skipped by the collection unless enabled: e-mail tests (REG-01, REG-05, ACC-07), cross-user tests (SEC-01 to SEC-04) and the destructive test.

## 3. Summary of results

Counts are of **test cases**. Evidence-based counts are zero because nothing has been executed with recorded evidence.

| Result | Count | Note |
|---|---|---|
| Passed | 360 | no evidence |
| Failed | 11 | [Bugs](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/bugs-report.md) |
| Blocked | 0 |  |
| **Total** | **371** | |

## 4. Result log

<img src="../imgs/run results.png" width="400" height="400" alt="Screenshot">

Verdict values: *Passed* 360 · *Failed-Bugs* 11.

## 6. Related documents
- Bugs: [Bug_Reports.md](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/bugs-report.md)

## 7. Known limitations

- E-mail delivery and confirmation links cannot be verified by the collection.
