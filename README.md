# DummyJSON API Automation (Apidog CLI + GitHub Actions)

[![API Tests](https://github.com/AbdelrahmanGamal1998/dummyjson-api-automation-apidog/actions/workflows/api-tests.yml/badge.svg)](https://github.com/AbdelrahmanGamal1998/dummyjson-api-automation-apidog/actions/workflows/api-tests.yml)

Automated API test suite for the public [DummyJSON](https://dummyjson.com) API. It covers authentication
(login, login failure, the current user) and product CRUD, runs from the command line with the
**Apidog CLI**, produces HTML and JUnit reports, and runs on every push through **GitHub Actions**.
The API is described in an OpenAPI 3 spec that is linted in CI. No API key or account is needed.

## What is tested

8 requests and 30 assertions, run in order. The login token is stored by request 01 and reused by
request 03.

| #   | Request                          | What it checks                                           |
| --- | -------------------------------- | -------------------------------------------------------- |
| 01  | `POST /auth/login`, valid user   | 200, response time, `accessToken` and user info returned |
| 02  | `POST /auth/login`, wrong user   | 400 and an error message                                 |
| 03  | `GET /auth/me`                   | 200, returns the logged-in user (uses the stored token)  |
| 04  | `GET /products`                  | 200, products array, required fields on each product     |
| 05  | `GET /products/1`                | 200, id is 1, required fields                            |
| 06  | `POST /products/add`             | 201, product id returned, title matches the request      |
| 07  | `PUT /products/1`                | 200, title reflects the update                           |
| 08  | `DELETE /products/1`             | 200 and the `isDeleted` flag                             |

Every request also checks that the response time is under 2000 ms.

## Run it locally

You only need Node.js 18 or newer.

```bash
git clone https://github.com/AbdelrahmanGamal1998/dummyjson-api-automation-apidog.git
cd dummyjson-api-automation-apidog
npm install
npm test
```

The command prints the results in the terminal and writes an HTML and a JUnit report to `reports/`.

Other commands:

```bash
npm run lint:spec      # validate apis/openapi.yaml with Redocly
npm run preview:spec   # open the API docs in the browser
```

## Continuous integration

`.github/workflows/api-tests.yml` runs on every push and pull request:

1. **Validate** lints `apis/openapi.yaml`.
2. **Test** runs the suite, uploads the HTML report as an artifact and publishes the JUnit results to the
   run summary.

## Project structure

```
apis/
  openapi.yaml                           OpenAPI 3 description of the endpoints under test
tests/
  dummyjson-api-tests.apidog-cli.json    The test suite that `npm test` runs (Apidog CLI format)
.github/workflows/api-tests.yml          CI pipeline
```

## Notes

- The login user (`emilys` / `emilyspass`) is one of DummyJSON's public demo users.
- DummyJSON simulates write requests: they return realistic responses but nothing is stored, so the suite
  can run repeatedly.
- A similar suite for another public API is in
  [reqres-api-automation-apidog](https://github.com/AbdelrahmanGamal1998/reqres-api-automation-apidog).

## License

[MIT](LICENSE)
