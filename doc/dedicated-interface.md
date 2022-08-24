# MultiPass - PSD2 Dedicated Interface - documentation

1. [Introduction](./dedicated-interface.md#introduction)
   1. [General information](./dedicated-interface.md#general-information)
   2. [Workflow](./dedicated-interface.md#workflow)
2. [Actions](./dedicated-interface.md#actions)
   1. [Authorization](./dedicated-interface.md#authorization)
      1. [Init](./dedicated-interface.md#init)
      2. [Generate token](./dedicated-interface.md#generate-token)
      3. [Revoke token](./dedicated-interface.md#revoke-token)
   2. [Account](./dedicated-interface.md#account)
      1. [Get account list](./dedicated-interface.md#get-account-list)
      2. [Get account](./dedicated-interface.md#get-account)
      3. [Get balance](./dedicated-interface.md#get-balance)
3. [Appendix](./dedicated-interface.md#appendix)
   1. [Available scopes](./dedicated-interface.md#available-scopes)

## Introduction

### General information

This document provides specification of API endpoints and webhooks for interacting with MultiPass Open Banking solution.
It allows Third Party Providers (TPPs) to interact with MultiPass in accordance with Payment Service Directive 2 (PSD2).

Users of TPP applications (PSU) can grant access to their account and payments data in the partner ASPSPs, which TPP application can then access and
use through unified REST API interfaces.

Main focus points of this documentation are:

Securing of API calls to ensure only authorized TPP providers can access API.
Authorization process for user using Oauth2.
Account and payments information API endpoints.
To see procedure flow charts and sequence diagrams that describe API flows, please see the Workflow.

### Workflow

## Actions

### Authorization

#### Init

This begins the authorization process. Users should be redirected to the URL supplied in the response.

```
POST /api/tpp/authorize
```

Supported query parameters:


| **Name of parameter** | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| secret                  | TPP auth secret key                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| scope                   | Required scope list. [Available scopes](./dedicated-interface.md#available-scopes) |
| redirect_uri            | URI to which users will be redirected back when the authorization process is completed. Mandatory field.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| username              | TPP client id|
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                
#### Sample Response

```
HTTP/1.1 200 Found
{
    "redirectUrl": "https://frontend_redirect.url?clientId=username__1&x-request-id=XHGFmOO0KhgM4zPi3nto"
}
```

#### Generate token

Allows to get access/refresh tokens, which should be used in further requests for getting account information or making payments for the respective PSU. Authorization code has to be sent to receive following tokens.

```
POST /api/client/token
```

Supported query parameters:


| **Name of parameter** | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| accessCode                  | TPP auth secret key                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| clientId                   | Authorization code |


#### Sample Response

```
HTTP/1.1 200 Found
{
    "accessToken": "gbOyNBXkxceJDGBsz9onYsXK5",
    "expiresIn": 315360000,
    "scope": "account.details account.list"
}
```

```
HTTP/1.1 404 Not found
{
    "message": "Entity not found or Invalid access code"
}
```

#### Revoke token

In order to revoke Client token by TPP, request with currently used access token is used.

```
POST /api/client/token/revoke
```

Supported query parameters:


| **Name of parameter** | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| accessToken             | Access token used with Client account                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


#### Sample Response

```
HTTP/1.1 200 Found
{
    "status": "success"
}
```

```
HTTP/1.1 400 Bad request
{
    "status": "failure"
}
```

### Account

#### Get account list

"Get accounts" message is initiated by the TPP. The account.list consent (scope) needs to be approved to receive a successful response.

```
POST /api/tpp/accounts
```

Supported query parameters:

No parameters required.

#### Sample Response

```
HTTP/1.1 200 Found
{
  "accounts": [
    {
      "id": 47669570047,
      "name": "John Doe",
      "iban": "GB01TCCL82495709311980",
      "currency": "EUR",
      "additional_data": []
    },
    {
      "id": 82948889462,
      "name": "Jane Doe",
      "iban": "GB77DNNA04135500001698",
      "currency": "EUR",
      "additional_data": []
    }
  ]
}
```

#### Get account

"Get account" action is initiated by the TPP. The account.details consent (scope) needs to be approved to receive a successful response.

```
POST /api/tpp/account
```

Supported query parameters:


| **Name of parameter** | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| accountId             | Account id                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |



#### Sample Response

```
HTTP/1.1 200 Found
{
  "account": 
    {
      "id": 47669570047,
      "name": "John Doe",
      "iban": "GB01TCCL82495709311980",
      "currency": "EUR",
      "additional_data": []
    }
}
```

#### Get balance

"Get account balance" action is initiated by the TPP. The account.balance consent (scope) needs to be approved to get the successful response.

```
POST /api/tpp/account/balance
```

Supported query parameters:


| **Name of parameter** | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| accountId             | Account id                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |



#### Sample Response

```
HTTP/1.1 200 Found
{
  "account": 
    {
      "id": 47669570047,
      "name": "John Doe",
      "balance" [
         {
            "currency": "EUR",
            "available": 30.40
         },{
            "currency": "GBP",
            "available": 420.74
         } 
      ]
    }
}
```

## Appendix

### Available scopes


| **Scope name** | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| account.list             | Get account list                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| account.details          | Get account information                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| account.balance          | Get account balance                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

