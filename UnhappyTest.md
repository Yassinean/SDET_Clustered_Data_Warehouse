# NEGATIVE_TEST_CASES.md

### These scenarios confirm that the system rejects invalid data correctly.

#### TC-UH-01 — Missing dealId
- Request
```bash
{
  "fromCurrency": "USD",
  "toCurrency": "EUR",
  "timestamp": "2025-01-01T10:00:00Z",
  "amount": 1500.00
}
```
- Expected

* HTTP 400 Bad Request

* Error: "dealId is required"

#### TC-UH-02 — Invalid Currency Format
- Request
```bash
{
  "dealId": "D1003",
  "fromCurrency": "USDD",
  "toCurrency": "E",
  "timestamp": "2025-01-01T10:00:00Z",
  "amount": 1200
}
```
- Expected

* HTTP 400
* "Invalid currency code format"

#### TC-UH-03 — Invalid Amount Type
- Request
```bash
{
  "dealId": "D1004",
  "fromCurrency": "USD",
  "toCurrency": "JPY",
  "timestamp": "2025-01-01T10:00:00Z",
  "amount": "abc"
}
```
- Expected

* HTTP 400
* "amount must be numeric"

#### TC-UH-04 — Negative Amount
- Request
```bash
{
  "dealId": "D1005",
  "fromCurrency": "USD",
  "toCurrency": "EUR",
  "timestamp": "2025-01-01T10:00:00Z",
  "amount": -500
}
```
- Expected

* HTTP 400
* "Amount must be positive"

#### TC-UH-05 — Duplicate dealId
- First Request
```bash
{
  "dealId": "D5001",
  "fromCurrency": "USD",
  "toCurrency": "CAD",
  "timestamp": "2025-01-01T10:00:00Z",
  "amount": 300
}
```
- Second Request (duplicate)
```bash
Same as above.
```
- Expected

- HTTP 409 Conflict
- "Duplicate dealId: D5001"

#### TC-UH-06 — Invalid Timestamp
- Request
```bash
{
  "dealId": "D7001",
  "fromCurrency": "USD",
  "toCurrency": "EUR",
  "timestamp": "NOT_A_DATE",
  "amount": 100
}
```
- Expected

* HTTP 400
* "Invalid timestamp format"
