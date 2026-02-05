# TechCorp Payments CI Pipeline

Week 3 Capstone - CI/CD Pipeline for TechCorp Payments Domain testing.

## Overview

This repository demonstrates a complete CI/CD test automation pipeline using Postman CLI and GitHub Actions, created as part of the CSE Technical Bootcamp Week 3 Capstone.

## Pipeline Status

![GitHub Actions](https://github.com/sean-riney/techcorp-payments-ci/actions/workflows/techcorp-payments-ci.yml/badge.svg)

## Test Collection

**Collection**: User Verification E2E (Azure Users API)

| Request | Endpoint | Purpose |
|---------|----------|---------|
| 1. Check Azure Health | `GET /health` | Smoke test - service availability |
| 2. Get All Users | `GET /users` | Extract user data for chaining |
| 3. Get User by ID | `GET /users/{id}` | Fetch specific user with chained ID |
| 4. Verify User Match | `GET /users/{id}` | Verify data consistency |
| 5. Cleanup | `GET /health` | Clear workflow variables |

**Total**: 11 assertions across 5 requests

## CI/CD Pipeline

**Triggers**:
- Push to `main` branch
- Pull requests to `main`
- Manual trigger (workflow_dispatch)

**Pipeline Steps**:
1. Checkout repository
2. Install Node.js and Postman CLI
3. Authenticate with Postman API
4. Run test collection with environment
5. Upload JUnit test results as artifacts

## Running Locally

### Prerequisites
- Postman CLI
- Postman API Key

### Steps

1. Install Postman CLI:
```bash
curl -o- "https://dl-cli.pstmn.io/install/linux64.sh" | sh
```

2. Login:
```bash
postman login --with-api-key YOUR_API_KEY
```

3. Run collection:
```bash
postman collection run COLLECTION_ID --environment ENVIRONMENT_ID
```

## Troubleshooting Documentation

### Failure #1: DNS Resolution Error

**Run**: [View Failed Run](https://github.com/sean-riney/techcorp-payments-ci/actions/runs/21731978284/job/62688501734)

**Symptom**:
```
getaddrinfo ENOTFOUND api-dev.techcorp.internal
```

**Root Cause**: The TechCorp - Dev environment pointed to an internal domain (`api-dev.techcorp.internal`) that is not accessible from GitHub Actions runners on the public internet.

**Resolution**: Switched to a collection using publicly accessible APIs (User Verification E2E with Azure bootcamp APIs) while documenting the infrastructure limitation.

**Lesson**: When setting up CI/CD for internal APIs, ensure the CI environment has network access to the target endpoints (VPN, private runners, or public mock servers).

---

### Failure #2: 401 Unauthorized

**Run**: [View Failed Run](https://github.com/sean-riney/techcorp-payments-ci/actions/runs/21731978284/job/62689297859)

**Symptom**:
```
GET https://cse-bootcamp-api-dev.azurewebsites.net/api/users [401 Unauthorized]
AssertionError: expected response to have status code 200 but got 401
```

**Root Cause**: The Postman environment's `azureAccessToken` value was not synced to Postman Cloud. The CLI uses cloud-stored values, not local values.

**Resolution**: Used the **Share** button in Postman to sync the environment values to the cloud.

**Lesson**: Postman CLI uses "Initial/Shared" values from the cloud, not "Current/Local" values. Always verify environment values are synced before running in CI.

---

### Successful Run

**Run**: [View Successful Run](https://github.com/sean-riney/techcorp-payments-ci/actions/runs/21731978284/job/62689878281)

**Result**: 11/11 assertions passed, all 5 requests completed successfully.

## Repository Secrets Required

| Secret | Description |
|--------|-------------|
| `POSTMAN_API_KEY` | Postman API key for CLI authentication |
| `POSTMAN_COLLECTION_ID` | Collection ID to run |
| `POSTMAN_ENVIRONMENT_ID` | Environment ID with variables |

## Resources

- [Postman CLI Documentation](https://learning.postman.com/docs/postman-cli/postman-cli-overview/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
