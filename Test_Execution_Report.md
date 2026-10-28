# Test Execution Report

Navigation: [Project](https://github.com/Ilkarion/APITesting_Your_diary) · [Bug Reports](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/bugs-report.md) · [Test Coverage](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/test_coverage.md) · [Test Plan](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/Test_Plan.md)

> **Status: executed.  Date: 24.10.2026. Postman 12.34.1** The materials supplied for this project contain the Postman collection and the API documentation, but **not ALL run results**: no saved responses, no collection run export **just** screenshots. Thus, this report serves as a brief introduction.

## 1. Project and environment

| Item | Value |
|---|---|
| Project | Your Diary API testing portfolio |
| API under test | Diary API 1.0.0 (Express + Supabase), described by `openapi.yaml` |
| Collection + Env.-Variables | [Your_Diary_API.postman_collection.json](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/Your-Diary%20API.postman_collection.json) |
| Postman version | 12.34.1 |
| Tester | Ilkarion |
| Variables enabled | `allow_email_tests`: *true* · `allow_destructive`: *true* · `second account`: *yes* ...|

## 2. Summary of results

Counts are of **assertions**.

| Result | Count | Note |
|---|---|---|
| Passed | 360 | |
| Failed | 11 | [Bugs](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/bugs-report.md) |
| Blocked | 0 |  |
| **Total** | **371** | |

11 failed assertions belong to 6 known issues (see [bugs_report.md]((https://github.com/Ilkarion/APITesting_Your_diary/blob/main/bugs-report.md)))

## 3. Result log

<img src="imgs/run results.png" width="400" height="400" alt="Screenshot">

Verdict values: *Passed* 360 · *Failed-Bugs* 11.

## 4. Related documents
- Bugs: [Bug_Reports.md](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/bugs-report.md)

## 5. Known limitations

- E-mail delivery and confirmation links cannot be verified by the collection.
