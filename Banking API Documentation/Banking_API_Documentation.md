# Banking API Documentation (Sample Doc for Portfolio)

Welcome to the **Banking API**. This API enables secure integration with banking services such as accounts, viewing transactions, credit cards payments, and fund transfers.

### Base URL:  
```
https://api.bankexample.com/v1
```

### Authentication:  
This API uses API Key for authentication. You can include the API key in the request header:


### Authorization: 
Bearer <YOUR_API_KEY>

---

## Table of Contents  
1. [Introduction](#introduction-to-the-document)
2. [Intended Audience](#intended-audience)
3. [Endpoints](#endpoints)
    - [Account Details](#1-account-details)
    - [Transaction History](#2-transaction-history)
    - [Credit Card Payment](#3-credit-card-payment)
    - [Funds Transfer](#4-funds-transfer)
    - [Delete Scheduled Payment](#5-delete-scheduled-payment) 
7. [Error Responses](#error-responses)  
8. [Rate Limits](#rate-limits)  

---

## Introduction to the Document

This document provides comprehensive details about the Banking API, which enables developers to integrate their applications with core banking systems.
The API provides secure access to account details, payments, and transaction history. It includes information on authentication methods, available endpoints, request/response formats, and error handling to help developers build reliable financial applications.

---

## Intended Audience  
This documentation is designed for:  
- **Developers** integrating banking services into applications  
- **Product Managers** defining requirements for payment solutions  
- **QA Engineers** testing APIs for reliability and accuracy  
- **Business Analysts** reviewing workflows for compliance and efficiency  

---

## Endpoints

### 1. Account Details
Retrieve customer account details.

**URL:** /accounts/{accountId}

**Method:** GET

**Description:**
Retrieve detailed information about a specific bank account, including account holder name, type, balance, and currency.

**Path Parameters**  
| Name       | Type   | Required | Description              |
|------------|--------|----------|--------------------------|
| accountId  | string | Yes      | Unique customer account ID |

**Query Parameters:**

- includeTransactions (optional) – Set to true to include recent transactions in the response
- currency (optional) – Convert balance to a specific currency (e.g., USD, AED, INR)

**Response (200)**  
```json
{
  "accountId": "ACC123456",
  "accountHolder": "John Doe",
  "accountType": "Savings",
  "balance": 5000.75,
  "currency": "AED",
  "createdAt": "2023-05-12T08:25:00Z",
  "status": "Active"
}
```
    
| Sr No | Key           | Value                  | Description                                                                    |
| ----- | ------------- | ---------------------- | ------------------------------------------------------------------------------ |
| 1     | accountId     | "ACC123456"            | Unique identifier for the account. String, max 20 characters                   |
| 2     | accountHolder | "John Doe"             | Full name of the account owner. String, max 100 characters                     |
| 3     | accountType   | "Savings"              | Type of the account. Accepted values: `Savings`, `Current`, `Checking`         |
| 4     | balance       | 5000.75                | Current balance in the account. Decimal number with 2 decimal places           |
| 5     | currency      | "AED"                  | Currency code in ISO 4217 format (e.g., AED, USD, INR)                         |
| 6     | createdAt     | "2023-05-12T08:25:00Z" | Timestamp of account creation in ISO 8601 UTC format                           |
| 7     | status        | "Active"               | Current status of the account. Possible values: `Active`, `Inactive`, `Closed` |

### Notes
- **accountId:** Must be unique across the banking system. Should only contain alphanumeric characters, max 20 characters. Cannot be empty  
- **accountHolder:** Full name of the customer. Supports alphabets, spaces, hyphens, and periods. Max 100 characters. Required for all accounts 
- **accountType:** Indicates the type of account. Accepted values are `Savings`, `Current`, or `Checking`. Must match one of the predefined product types in the bank  
- **balance:** Decimal number representing the available funds in the account. Positive values only. Maximum of 2 decimal places. Negative values are not allowed unless the account permits overdraft  
- **currency:** ISO 4217 currency code. Must be valid and supported by the banking system (e.g., AED, USD, INR). Used to format the balance field
- **createdAt:** ISO 8601 UTC timestamp of when the account was created. Format: `YYYY-MM-DDThh:mm:ssZ`. Important for audit trails and compliance reporting  
- **status:** Reflects the operational status of the account. Possible values: `Active` (usable for transactions), `Inactive` (temporarily disabled), `Closed` (account is permanently closed)  
- All fields are mandatory unless marked optional. API will return `400 Bad Request` if required parameters are missing or invalid  

---

### 2. Transaction History
Retrieve recent transactions for a specific bank account.

**URL:** /accounts/{accountId}/transactions

**Method:** GET

**Description:**  
Fetch a list of transactions for a given account. You can filter by date range, transaction type, or status.

**Path Parameters**  
| Name       | Type   | Required | Description              |
|------------|--------|----------|--------------------------|
| accountId  | string | Yes      | Unique customer account ID |

**Query Parameters:**  
| Name        | Type   | Required | Description |
|-------------|--------|----------|-------------|
| startDate   | string | No       | Start date for transaction history (YYYY-MM-DD) |
| endDate     | string | No       | End date for transaction history (YYYY-MM-DD) |
| type        | string | No       | Filter by transaction type (`Credit`, `Debit`) |
| status      | string | No       | Filter by transaction status (`Completed`, `Pending`, `Failed`) |
| limit       | int    | No       | Number of transactions to return (default: 10) |
| page        | int    | No       | Page number for pagination (default: 1) |

**Response (200)**  
```json
{
  "accountId": "ACC123456",
  "transactions": [
    {
      "transactionId": "TXN1001",
      "date": "2025-08-01T10:15:00Z",
      "amount": 150.50,
      "currency": "AED",
      "type": "Debit",
      "status": "Completed",
      "description": "Payment to ABC Store"
    },
    {
      "transactionId": "TXN1002",
      "date": "2025-08-02T14:20:00Z",
      "amount": 2000.00,
      "currency": "AED",
      "type": "Credit",
      "status": "Completed",
      "description": "Salary Deposit"
    }
  ]
}
```

| Sr No | Key                            | Value                  | Description                                                   |
| ----- | ------------------------------ | ---------------------- | ------------------------------------------------------------- |
| 1     | accountId                      | "ACC123456"            | Unique identifier for the account. String, max 20 characters  |
| 2     | transactions                   | Array                  | List of transactions for the account                          |
| 3     | transactions\[0].transactionId | "TXN1001"              | Unique transaction identifier. String, max 20 characters      |
| 4     | transactions\[0].date          | "2025-08-01T10:15:00Z" | ISO 8601 timestamp of the transaction                         |
| 5     | transactions\[0].amount        | 150.50                 | Decimal value of the transaction amount, max 2 decimal places |
| 6     | transactions\[0].currency      | "AED"                  | ISO 4217 currency code for transaction amount                 |
| 7     | transactions\[0].type          | "Debit"                | Transaction type: `Debit` or `Credit`                         |
| 8     | transactions\[0].status        | "Completed"            | Transaction status: `Completed`, `Pending`, or `Failed`       |
| 9     | transactions\[0].description   | "Payment to ABC Store" | Optional description of the transaction, max 255 characters   |

### **Notes:**

- **transactionId:** Must be unique across all transactions in the system
- **date:** ISO 8601 UTC format; important for sorting and filtering transactions
- **amount:** Always positive; actual transaction effect is determined by type field (Credit or Debit)
- **currency:** Must match the account currency or a supported conversion
- **type:** Determines whether the balance increases (Credit) or decreases (Debit)
- **status:** Completed means the transaction is finalized; Pending means it’s still processing; Failed means the transaction was rejected

---

### 3. Credit Card Payment
Make a payment towards a customer’s credit card account.

**URL:** /accounts/{accountId}/credit-card/payments

**Method:** POST

**Description:**  
Process a payment for a credit card account. Supports specifying amount, currency, and payment method.

**Path Parameters**  
| Name       | Type   | Required | Description              |
|------------|--------|----------|--------------------------|
| accountId  | string | Yes      | Unique customer account ID |

**Request Body:**  
| Name            | Type   | Required | Description |
|-----------------|--------|----------|-------------|
| amount          | number | Yes      | Payment amount. Decimal, max 2 decimal places |
| currency        | string | Yes      | Currency code in ISO 4217 format (e.g., AED, USD, INR) |
| paymentMethod   | string | Yes      | Payment method (`BankTransfer`, `DebitCard`, `CreditCard`) |
| reference       | string | No       | Optional reference or note for the payment, max 255 characters |

**Response (200)**  
```json
{
  "paymentId": "PAY123456",
  "accountId": "ACC123456",
  "amount": 1500.75,
  "currency": "AED",
  "paymentMethod": "BankTransfer",
  "status": "Completed",
  "processedAt": "2025-08-30T10:30:00Z",
  "reference": "August Credit Card Payment"
}
```

| Sr No | Key           | Value                        | Description                                                              |
| ----- | ------------- | ---------------------------- | ------------------------------------------------------------------------ |
| 1     | paymentId     | "PAY123456"                  | Unique identifier for the payment transaction. String, max 20 characters |
| 2     | accountId     | "ACC123456"                  | Unique identifier for the account. String, max 20 characters             |
| 3     | amount        | 1500.75                      | Payment amount. Decimal, max 2 decimal places                            |
| 4     | currency      | "AED"                        | ISO 4217 currency code                                                   |
| 5     | paymentMethod | "BankTransfer"               | Payment method used: `BankTransfer`, `DebitCard`, or `CreditCard`        |
| 6     | status        | "Completed"                  | Status of the payment. Possible values: `Completed`, `Pending`, `Failed` |
| 7     | processedAt   | "2025-08-30T10:30:00Z"       | Timestamp when payment was processed, ISO 8601 UTC format                |
| 8     | reference     | "August Credit Card Payment" | Optional reference or note for the payment, max 255 characters           |

### **Notes:**

- **paymentId:** Must be unique across all payment transactions
- **amount:** Positive decimal number; must not exceed available account balance if using account funds
- **currency:** Must match account currency or a supported conversion
- **paymentMethod:** Defines how the payment is processed; unsupported methods will return 400 Bad Request
- **status:** Completed means payment is successful; Pending means it is processing; Failed indicates payment rejection
- **processedAt:** ISO 8601 UTC timestamp of processing; useful for auditing and reconciliation
- **All mandatory fields must be included; missing or invalid fields will return 400 Bad Request

---

### 4. Funds Transfer
Transfer funds from one account to another within the same bank or to external bank accounts.

**URL:** /accounts/{accountId}/transfers

**Method:** POST

**Description:**  
Initiate a fund transfer from the specified account to a beneficiary account. Supports internal and external transfers with optional notes.

**Path Parameters**  
| Name       | Type   | Required | Description              |
|------------|--------|----------|--------------------------|
| accountId  | string | Yes      | Unique customer account ID |

**Request Body:**  
| Name             | Type   | Required | Description |
|------------------|--------|----------|-------------|
| beneficiaryAccountId | string | Yes      | Account ID of the recipient (internal or external) |
| amount           | number | Yes      | Amount to transfer. Decimal, max 2 decimal places |
| currency         | string | Yes      | Currency code in ISO 4217 format (e.g., AED, USD, INR) |
| transferType     | string | Yes      | Type of transfer: `Internal` (same bank) or `External` (different bank) |
| reference        | string | No       | Optional reference or note for the transfer, max 255 characters |

**Response (200)**  
```json
{
  "transferId": "TRF987654",
  "fromAccountId": "ACC123456",
  "toAccountId": "ACC654321",
  "amount": 2500.00,
  "currency": "AED",
  "transferType": "Internal",
  "status": "Completed",
  "processedAt": "2025-08-30T11:00:00Z",
  "reference": "Monthly Rent"
}
```

| Sr No | Key           | Value                  | Description                                                                |
| ----- | ------------- | ---------------------- | -------------------------------------------------------------------------- |
| 1     | transferId    | "TRF987654"            | Unique identifier for the transfer transaction. String, max 20 characters  |
| 2     | fromAccountId | "ACC123456"            | Account ID of the sender. String, max 20 characters                        |
| 3     | toAccountId   | "ACC654321"            | Account ID of the recipient. String, max 20 characters                     |
| 4     | amount        | 2500.00                | Transfer amount. Decimal, max 2 decimal places                             |
| 5     | currency      | "AED"                  | ISO 4217 currency code                                                     |
| 6     | transferType  | "Internal"             | Type of transfer: `Internal` or `External`                                 |
| 7     | status        | "Completed"            | Status of the transfer. Possible values: `Completed`, `Pending`, `Failed`  |
| 8     | processedAt   | "2025-08-30T11:00:00Z" | Timestamp when transfer was processed, ISO 8601 UTC format                 |
| 9     | reference     | "Monthly Rent"         | Optional note for tracking the purpose of the transfer, max 255 characters |

### **Notes:**

- **transferId:** Must be unique for every transfer transaction
- **fromAccountId / toAccountId:** Must be valid account IDs; for external transfers, the system may require bank code or IBAN
- **amount:** Must be positive and ≤ available balance in the sender’s account; max 2 decimal places
- **currency:** Must be supported by both sending and receiving accounts; mismatch may return an error
- **transferType:** Determines internal vs external processing; external transfers may incur additional fees or longer processing times
- **status:** Completed means successful transfer, Pending indicates it’s still processing, Failed indicates rejection or insufficient funds
- **processedAt:** ISO 8601 UTC timestamp; useful for audit and reconciliation

---

### 5. Delete Scheduled Payment
Remove a scheduled payment or transaction.

**URL:** /payments/{paymentId}

**Method:** DELETE

**Description:**  
Cancel or delete a scheduled payment. Once deleted, the payment will not be processed.

**Path Parameters**  
| Name       | Type   | Required | Description              |
|------------|--------|----------|--------------------------|
| paymentId  | string | Yes      | Unique ID of the scheduled payment |

**Response (200)**  
```json
{
  "message": "Scheduled payment deleted successfully.",
  "paymentId": "PAY123456"
}
```

---

### Error Responses
All error responses have the following structure:

- **Example Error Response:**

    ```json
    {
        "error": {
            "code": 404,
            "message": "The requested account does not exist."
        }
    }
    ```

    | Sr No | Key     | Value                              | Description                                                                 |
    |-------|---------|------------------------------------|-----------------------------------------------------------------------------|
    | 1     | code    | 404                                | HTTP status code for the error. Must be a valid code (e.g., 404 for Not Found).|
    | 2     | message | The requested account does not exist. | Error message explaining the issue with the request.                        |

- **Error Codes:**

| **Sr. No.** | **HTTP Status Code** | **Error Type**           | **Description**                                                                 |
|-------------|--------------------|--------------------------|---------------------------------------------------------------------------------|
| 1           | 400                | Bad Request              | The server could not understand the request due to invalid syntax or parameters |
| 2           | 401                | Unauthorized             | Authentication failed, likely due to an invalid API key or missing credentials  |
| 3           | 403                | Forbidden                | The client is authenticated but does not have permission to access the resource |
| 4           | 404                | Not Found                | The requested resource could not be found on the server                          |
| 5           | 405                | Method Not Allowed       | The requested HTTP method is not allowed for the resource                        |
| 6           | 408                | Request Timeout          | The server timed out waiting for the request to be completed                     |
| 7           | 429                | Too Many Requests        | The user has sent too many requests in a given amount of time ("rate limiting")  |
| 8           | 500                | Internal Server Error    | The server encountered an unexpected condition that prevented it from fulfilling the request |
| 9           | 502                | Bad Gateway              | The server, while acting as a gateway, received an invalid response from the upstream server |
| 10          | 503                | Service Unavailable      | The server is temporarily unavailable, often due to being overloaded or maintenance |
| 11          | 504                | Gateway Timeout          | The server, while acting as a gateway, did not receive a timely response from the upstream server |

---

## Rate Limits
- **1000 requests/minute** per API key  
- Exceeding limits will return `429 Too Many Requests`
- Response on Rate Limit Exceeded:
  
    ```json
    {
    "error": {
        "code": 429,
        "message": "Too many requests. Please try again later."
    }
    }
    ``` 

---

## Changelog
- **v1.0.0 (2025-08-29)** – Initial release: Accounts, Transactions, Credit Cards, Transfers
