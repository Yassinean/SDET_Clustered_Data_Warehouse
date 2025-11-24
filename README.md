
# 📘 FX Deal Import — SDET Assignment (ProgressSoft)

### This project is designed to validate only the testing aspects of the FX Deal Import system described in the ProgressSoft SDET assignment.

### The goal of this repository is to demonstrate test analysis, test design, API behavior expectations, and validation logic, without focusing heavily on implementation details.

#### This README summarizes:

- API behavior
- Positive & negative test scenarios
- Expected HTTP responses
- Valid & invalid request examples
- Links to test case files

# 📌 1. Overview of Requirements

-> The system receives FX deal records with the following fields:

```bash 
Field	                 |                 Description                |
---------------------------------------------------------------------
dealId	               |        Unique identifier for the deal      |
fromCurrency	         |        ISO currency code (source)          |
toCurrency	           |        ISO currency code (target)          |
timestamp	             |        Deal timestamp in ISO8601 format    |
amount	               |        Deal amount in source currency      |
---------------------------------------------------------------------
```
-> System rules:

- ✔ Validate structure
- ✔ Validate required fields
- ✔ Validate types & ISO currency format
- ✔ Prevent duplicate imports
- ✔ No rollback for batch imports (partial success allowed)
- ✔ Use a real DB (Postgres, MySQL, or MongoDB)
- ✔ Provide unit tests, integration tests, API tests (RESTAssured)
- ✔ Provide K6 performance tests
- ✔ Provide 100% coverage for:
      . Parsing
      . Validation
      . Deduplication
      . Import flow

# 📁 2. Test Case Files

-> The test cases are documented separately for clarity.

```bash
Document	               |               Description                      |
---------------------------------------------------------------------------
POSITIVE_TEST_CASES.md	 |     All valid, expected-to-succeed scenarios   |
NEGATIVE_TEST_CASES.md	 |     All invalid, error-producing scenarios     |
---------------------------------------------------------------------------
```

# 🔗 3. API Endpoint
###### POST /api/deals/import

-> This endpoint accepts:

* A single deal object, or
* An array of deal objects

# 🧪 4. Test Case → Expected Response Mapping

-> Below are the HTTP statuses and JSON responses for each test case, clearly referenced so testers know exactly how the system should behave.

##### ✔ Positive Test Scenarios (Happy Path)
-> TC-HP-01 — Valid Single Deal
- Request
* (See POSITIVE_TEST_CASES.md for full JSON)

- Expected Response
* HTTP 201 Created
```bash
{
  "successful": ["D1001"],
  "failed": []
}
```
-> TC-HP-02 — Multiple Valid Deals
- Expected Response

* HTTP 207 Multi-Status
```bash
{
  "successful": ["D2001", "D2002"],
  "failed": []
}
```
-> TC-HP-03 — Very Large Amount

* HTTP 201
```bash
{
  "successful": ["D3001"],
  "failed": []
}
```
-> TC-HP-04 — Rare ISO Currency (XAU)

- HTTP 201
```bash
{
  "successful": ["D3002"],
  "failed": []
}
```
-> TC-HP-05 — Maximum Batch Size

- HTTP 207
```bash
{
  "successfulCount": 1000,
  "failed": []
}
```

-> TC-HP-06 — Performance Valid Load (K6)

- Expected metrics:

* 95th percentile response time < 200ms
* 0% request failure
* Throughput: 200 req/sec

*** (K6 output is not JSON → results will show in console/report.)

##### ❌ Negative Test Scenarios (Unhappy Path)
-> TC-UH-01 — Missing dealId
- Expected Response
* HTTP 400 Bad Request
```bash
{
  "error": "dealId is required"
}
```

-> TC-UH-02 — Invalid Currency Format

* HTTP 400
```bash
{
  "error": "Invalid currency code format"
}
```
-> TC-UH-03 — Amount as String / Wrong Type

* HTTP 400
```bash
{
  "error": "amount must be numeric"
}
```
-> TC-UH-04 — Negative Amount

* HTTP 400
```bash
{
  "error": "Amount must be positive"
}
```
-> TC-UH-05 — Duplicate dealId

* HTTP 409 Conflict
```bash
{
  "error": "Duplicate dealId: D5001"
}
```
-> TC-UH-06 — Mixed Batch (Partial Success)

- Batch includes:

* 1 valid deal
* 1 missing dealId
* 1 duplicate deal

- Expected Response
* HTTP 207 Multi-Status
```bash
{
  "successful": ["D6001"],
  "failed": [
    { "index": 1, "error": "dealId is required" },
    { "index": 2, "error": "Duplicate dealId: D6001" }
  ]
}
```
-> TC-UH-07 — Invalid Timestamp

* HTTP 400
```bash
{
  "error": "Invalid timestamp format"
}
```
-> TC-UH-08 — Oversized Payload (Stress Test)

* HTTP 400 or 413 Payload Too Large
```bash
{
  "error": "Payload too large"
}
```
##### 📊 5. How Tests Are Organized
###### ✔ Unit Tests

Test validation logic, parsing, deduplication, error handling.

###### ✔ Integration Tests

Use an actual DB and verify:

* Persistence
* Unique constraints
* No rollback behavior

###### ✔ RESTAssured API Tests

Verify full request–response cycle with real endpoint.

###### ✔ K6 Performance Tests

Load testing with high volume requests.

##### 📝 6. Coverage Requirements

* JaCoCo configured to fail build if < 100%

* Applies to:
  -  Parsing
  -  Validation
  -  Deduplication
  -  Import flow

###### Excluded items (auto-config, mappers, generated code) are documented with justification.

##### 📌 7. Deliverables Included

###### POSITIVE_TEST_CASES.md

###### NEGATIVE_TEST_CASES.md

###### Complete README

###### Example JSON requests

###### Expected responses for every test

###### Test-to-requirement mapping

##### 🎯 8. Purpose of This Repository

This repo is focused on:

- ✔ Professional test design
- ✔ Structuring test scenarios
- ✔ API expectations
- ✔ Coverage rules
- ✔ Test documentation clarity

Not on full backend implementation — purposely aligned with an SDET-oriented assignment.