# POSITIVE_TEST_CASES.md

### These scenarios validate that the system behaves correctly when valid FX deal data is submitted.

#### TC-HP-01 — Valid Single Deal

##### Description:
Verify that one valid deal is successfully imported.

- Request JSON
``` bash
{
  "dealId": "D1001",
  "fromCurrency": "USD",
  "toCurrency": "EUR",
  "timestamp": "2025-01-01T10:00:00Z",
  "amount": 1500.00
}
```

- Expected:

* HTTP 201 Created
* Deal inserted into DB

#### TC-HP-03 — Valid Large Amount

##### Description:
Verify that very large numeric amounts are accepted.

- Request JSON
```bash
{
  "dealId": "D3001",
  "fromCurrency": "CHF",
  "toCurrency": "USD",
  "timestamp": "2025-03-01T10:00:00Z",
  "amount": 999999999999.99
}
```

-Expected:

* HTTP 201
* Deal inserted

#### TC-HP-04 — Valid Rare ISO Currency

##### Description:
Validate acceptance of rare but correct ISO currency codes.

- Request JSON

```bash
{
  "dealId": "D3002",
  "fromCurrency": "XAU",
  "toCurrency": "USD",
  "timestamp": "2025-03-03T09:00:00Z",
  "amount": 50
}
```

- Expected:

* HTTP 201
* Deal inserted

#### TC-HP-05 — Batch with Maximum Allowed Size

##### Description:
Verify system performance & stability with the largest acceptable batch size (ex: 1000 deals).

- Request JSON: (Example snippet)
```bash
[
  {
    "dealId": "MAX001",
    "fromCurrency": "USD",
    "toCurrency": "EUR",
    "timestamp": "2025-05-01T10:00:00Z",
    "amount": 10
  }
  // + 999 more
]
```

- Expected:

* HTTP 207
* All valid deals inserted

#### TC-HP-06 — Performance Valid Load (K6)

##### Description:
Verify system stability under 200 req/sec.

- Expected:

* No errors
* Response time under acceptable limits
