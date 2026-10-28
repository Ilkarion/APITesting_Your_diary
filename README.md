## API Documentation

- API Documentation: [Swagger UI](https://ilkarion.github.io/APITesting_Your_diary/)
- Postman Collection: [Postman](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/Your-Diary%20API.postman_collection.json)
- Test Plan: [Plan](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/Test_Plan.md)
- [Steps to run my collection](#steps-to-run-my-collection)


# API Testing — Your Diary

<div align="center">
  <img src="imgs/bugs-example.png" width="350" />
  <img src="imgs/no-register-postman.png" width="350" />
  <img src="imgs/run results.png" width="350" />
</div>


## Project Overview

This project demonstrates manual API testing with Postman test scripts (JavaScript) API testing of a diary application using Postman.

The goal was to verify API functionality, validate responses, test authentication, and identify potential issues in backend validation.

## Tools & Technologies

- **Postman** — API requests, test scripts, and assertions
- **JavaScript** — automated checks within Postman
- **OpenAPI / Swagger UI** — API documentation
- **GitHub** — version control and project documentation

## Testing Scope

- User registration and login
- Authentication and session cookies
- User profile endpoints
- Diary entry operations
- Positive and negative scenarios
- Response status, body, and schema validation
- Backend input validation

## Steps to run my collection:

1. Download my collection --> [collection](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/Your-Diary%20API.postman_collection.json)
2. Create 2 Gmail accounts (for testing). Or use your own accounts at your own risk (**risk**: I can see all registered accounts in my database, so I will know your Gmail addresses).
3. Open Postman.
4. Import my collection.
5. Click on the main folder of my collection - `'Your-Diary API'`.
6. Click on the `Variables` tab.
7. Change 2 fields with your created accounts (`email` & `email_b` fields). Just paste the addresses of your created accounts. Don't touch other variables.
8. Open my collection (`'Your-Diary API'`) folder.
9. Open the `'Health'` folder. Run the `'Ping - server is up'` request. **Result:** message in the response: `"status: ok"`. This is for waking up my server (you will wait ~1 minute).
10. Find and open the `'Auth'` folder, then the `'Positive'` folder.
11. Run the `'register - **user A**'` request. **Result:** message in the response: `"Registered! Check your email to confirm."`
12. Run the `'register - **user B**'` request. **Result:** message in the response: `"Registered! Check your email to confirm."`
13. Confirm your emails (in your mailbox, you will see a link from the sender `'Light'`). If you don't see the link, check your spam folder.
14. Right-click on the main folder of my collection.
15. Choose the option `'Run'`.
16. **UNCHECK** the first 3 requests: `'Ping - server is up'`, `'register - user A'`, `'register - user B'`.
17. Click the button `"Start Run"`.
18. You will see some errors - these are bugs. They are all documented [here](https://github.com/Ilkarion/APITesting_Your_diary/blob/main/bugs-report.md).
