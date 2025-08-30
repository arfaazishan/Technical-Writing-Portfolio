# Banking API Documentation

Welcome to the **Banking API**. This API enables secure integration with banking services such as accounts, credit cards, and payments.

Base URL:  
```
https://api.bankexample.com/v1
```

Authentication:  
All requests require an API key in the header:
```
Authorization: Bearer <your_api_key>
```

---

## Endpoints

### 1. Account Details
Retrieve customer account details.

**Endpoint**  
```
GET /accounts/{accountId}
```

**Parameters**  
| Name       | Type   | Required | Description              |
|------------|--------|----------|--------------------------|
| accountId  | string | Yes      | Unique customer account ID |

**Response (200)**  
```json
{
  "accountId": "1234567890",
  "accountType": "Savings",
  "currency": "AED",
  "balance": 15230.75,
  "status": "Active"
}
```

---

### 2. Transaction History
Fetch transactions for a specific account.

**Endpoint**  
```
GET /accounts/{accountId}/transactions
```

**Parameters**  
| Name        | Type   | Required | Description                         |
|-------------|--------|----------|-------------------------------------|
| accountId   | string | Yes      | Customer account ID                 |
| startDate   | string | No       | Filter transactions from this date (YYYY-MM-DD) |
| endDate     | string | No       | Filter transactions until this date (YYYY-MM-DD) |
| limit       | int    | No       | Number of records to return (default: 20) |

**Response (200)**  
```json
{
  "accountId": "1234567890",
  "transactions": [
    {
      "transactionId": "txn001",
      "date": "2025-08-20",
      "amount": -250.00,
      "currency": "AED",
      "description": "ATM Withdrawal",
      "status": "Completed"
    },
    {
      "transactionId": "txn002",
      "date": "2025-08-22",
      "amount": 1000.00,
      "currency": "AED",
      "description": "Salary Credit",
      "status": "Completed"
    }
  ]
}
```

---

### 3. Credit Card Payment
Make a payment towards a credit card bill.

**Endpoint**  
```
POST /credit-cards/{cardId}/payments
```

**Parameters (Body)**  
```json
{
  "amount": 1500.00,
  "currency": "AED",
  "paymentMethod": "BankAccount",
  "accountId": "1234567890"
}
```

**Response (201)**  
```json
{
  "paymentId": "pay98765",
  "cardId": "card12345",
  "amount": 1500.00,
  "status": "Success",
  "timestamp": "2025-08-29T10:00:00Z"
}
```

---

### 4. Funds Transfer
Transfer money between accounts.

**Endpoint**  
```
POST /transfers
```

**Parameters (Body)**  
```json
{
  "fromAccountId": "1234567890",
  "toAccountId": "9876543210",
  "amount": 500.00,
  "currency": "AED",
  "remarks": "Rent Payment"
}
```

**Response (201)**  
```json
{
  "transferId": "trf112233",
  "status": "Pending",
  "amount": 500.00,
  "currency": "AED",
  "timestamp": "2025-08-29T10:05:00Z"
}
```

---

## Error Codes

| Code | Message            | Description                          |
|------|--------------------|--------------------------------------|
| 400  | Bad Request        | Invalid input or missing parameters  |
| 401  | Unauthorized       | API key missing or invalid           |
| 403  | Forbidden          | Insufficient permissions             |
| 404  | Not Found          | Resource not found                   |
| 500  | Internal Error     | Something went wrong on our side     |

---

## Rate Limits
- **1000 requests/minute** per API key  
- Exceeding limits will return `429 Too Many Requests`  

---

## Changelog
- **v1.0.0 (2025-08-29)** – Initial release: Accounts, Transactions, Credit Cards, Transfers
