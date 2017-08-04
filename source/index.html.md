---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - curl
  - csharp
  - go

includes:
  - errors

search: true
---

# Introduction

Welcome to the SWAPPT API Documentation! We are glad to have you here, come and take a walk to see what can our API offer to you.
> We also have *Swagger*, check it out!
> <br> <b><a href ='https://api.swappt.com/swagger'>https://api.swappt.com/swagger</a></b>

SWAPPT API is a REST API that has an http POST call for each operation. It is composed by 3 big processes:

- **Available payment methods:** Returns the available payment methods.
- **Payment:** Runs a paymend and/or a fraud analysis.
- **Tokenization:** Tokenization/retrieval of card data information.


## Endpoints

The endpoints for each operation follow the same structure:

> **EXAMPLE:**
> Let's say that you belong to the organization named "test" and you want to use the version 1 of the SWAPPT API to do a **payment**. Your request would be the following:

>`POST https://api.swappt.com/v1/organizations/test/payment`

`POST https://api.swappt.com/v<VERSION_ID>/organizations/<ORGANIZATION_ID>/<OPERATION>`


Parameter | Description
--------- | -----------
VERSION_ID | API Version.
ORGANIZATION_ID | Your identifier as organization (that you will get when live).
OPERATION | The operation to realize.


<aside class="notice">
All the endpoints work around HTTPS. HTTP request <b>won't</b> work.
</aside>


## Authentication


```curl 
curl https://api.swappt.com/v1/organizations/test/payment
     -H "Authorization: ApiKey YourApiKey"
````

You need to authenticate when performing an API call. Moreover, the API will check your Authorization for every operation, so make sure that you have permissions to do every request that you want to do.

In order to authenticate, you need to place the word 'ApiKey' followed by your API key on the **Authorization** header of your HTTP request.

# AvailablePaymentMethods

The first call that you must do is *available_payment_methods*. With this call, you will get the list of payment methods that you have available for the transaction and configuration specified. Note that this step is **mandatory** for payments but for fraud analysis is not required.

> AvailablePaymentMethodsRS example:

```json
{
    "currency_amount" : {
        "amount" : 10,
        "currency_code" : "EUR"
    },
    "departure" : "2017-08-04T06:50:05.498Z",
    "cancellation_expenses_beginning" : "2017-08-04T06:50:05.498Z",
    "excluded_methods" : [
        "Mastercard"
    ],
    "fraud_information" : {
        "departure_datetime_utc" : "2017-08-04T06:50:05.498Z",
        "customer_has_trusted_bookings" : true,
        "customer_is_fraud_suspicious" : true,
        "product_predisposed_to_be_fraud" : true
    },
    "only_secure_payment" : true,
    "session_code" : "string",
    "client_config" : {
            "my_config_key_1": "my_value1"
    }
}

```

Node | Type | Description
---- | ---- | -----------
currency_amount | CurrencyAmount | Payment base amount where taxes will be applied. (More information below).
departure | String |  
cancellation_expenses_beginning | String | 
excluded_methods | Array[String] | 
fraud_information | FraudInformation | This node can indicate previous fraud information so only secure gateways are returned.
only_secure_payment | Boolean | 
session_code | String | 
client_config | Key-Value | 


## CurrencyAmount

Payment amount specified in a concrete currency.

Node | Type
---- | ----
amount | Integer 
currency_code | String 


<aside class="notice">
<i>currency_code</i> must be specified in ISO3 (ISO 4217)
</aside>

# Payment

This is the main process; the process where a payment and/or fraud analysis is done (if the *SWAPPT Switcher* and/or *SWAPPT FDS* solutions are hired and activated). It requires only 2 calls to the SWAPPT API to do a payment: *Get Available Payment Methods*, and *Payment*. More actions can be done related to the payment process. Additionaly you can do a *Payment Details* call to retrieve the status and details of a payment, and a *Refund* call to refund a payment.

## Payment
As it was previously introduced, the *Payment* procedure is intended to do the main payment process and/or do a previous Fraud analysis. The request must have the following JSON structure:

> ProcessRS example:

```json
{
  "fraud_response": {
    "result": "Success"
  },
  "payment_response": {
    "transaction": {
      "id": "string",
      "type": "Expirable",
      "recurring_payment_details": {
        "start_date": "2017-08-04T06:50:05.513Z",
        "schedule_details": {
          "profile_reference": "string",
          "reason": {
            "id": "string",
            "content": "string"
          },
          "payment_period": {
            "billing_period": "Day",
            "billing_frequency": 0,
            "amount": {
              "amount": 0,
              "currency_code": "string"
            },
            "shipping_amount": {
              "amount": 0,
              "currency_code": "string"
            },
            "tax_amount": {
              "amount": 0,
              "currency_code": "string"
            }
          }
        },
        "profile_id": "string",
        "profile_status": "Active",
        "auto_bill_type": "NoAutoBill",
        "max_failed_payments": 0,
        "recurring_payment_summary_details": {
          "next_billing_date": "2017-08-04T06:50:05.514Z",
          "number_cycles_completed": 0,
          "number_cycles_remaining": 0,
          "failed_payment_count": 0,
          "last_payment_date": "2017-08-04T06:50:05.514Z",
          "last_payment_amount": {
            "amount": 0,
            "currency_code": "string"
          }
        },
        "recurring_payment_profile_details": {
          "billing_start_date": "2017-08-04T06:50:05.515Z",
          "profile_reference": "string"
        },
        "aggregate_amount": {
          "amount": 0,
          "currency_code": "string"
        },
        "aggregate_optional_amount": {
          "amount": 0,
          "currency_code": "string"
        },
        "credit_card_details": {
          "credit_card_type": "Undefined",
          "credit_card_number": "string",
          "exp_month": 0,
          "exp_year": 0
        }
      },
      "expiration_datetime": "2017-08-04T06:50:05.518Z",
      "billing_type": "None",
      "status": "Unknown",
      "status_description": "string",
      "merchant_reference": "string",
      "currency_amount": {
        "amount": 0,
        "currency_code": "string"
      },
      "authorization_code": "string",
      "sender_details": {
        "holder": "string",
        "id": "string",
        "name": "string",
        "surname": "string",
        "email": "string",
        "phone_number": "string",
        "ssn": "string",
        "address": {
          "country_code": "string",
          "city": "string",
          "address_line1": "string",
          "address_line2": "string",
          "zip_code": "string"
        },
        "billing_address": {
          "country_code": "string",
          "city": "string",
          "address_line1": "string",
          "address_line2": "string",
          "zip_code": "string"
        },
        "sender_bank_details": {
          "bic": "string",
          "holder": "string",
          "bank_account": {
            "number": "string",
            "security_code": "string"
          },
          "bank_name": "string",
          "country_code": "string",
          "iban": "string"
        },
        "recipient_bank_details": {
          "bic": "string",
          "holder": "string",
          "bank_account": {
            "number": "string",
            "security_code": "string"
          },
          "bank_name": "string",
          "country_code": "string",
          "iban": "string"
        }
      },
      "reasons": [
        {
          "id": "string",
          "content": "string"
        }
      ],
      "provider_transaction_object": {},
      "pending_reason": "Unknown",
      "payment_method": "Undefined",
      "entity": "string",
      "transaction_datetime": "2017-08-04T06:50:05.519Z",
      "payment_method_details": [
        {
          "authorized": {
            "amount": 0,
            "currency_code": "string"
          },
          "authorizing": {
            "amount": 0,
            "currency_code": "string"
          },
          "brand": "string",
          "debited": {
            "amount": 0,
            "currency_code": "string"
          },
          "id": "string",
          "processor": "string",
          "recredited": {
            "amount": 0,
            "currency_code": "string"
          },
          "type": "string"
        }
      ],
      "agency_code_partner": "string",
      "fees": {
        "fee": [
          {
            "id": "string",
            "currency_amount": {
              "amount": 0,
              "currency_code": "string"
            },
            "exchange_rate": "string",
            "description": "string"
          }
        ]
      },
      "payment_items_details": {
        "item": [
          {
            "id": "string",
            "name": "string",
            "description": "string",
            "item_date": {
              "start_date": "2017-08-04T06:50:05.520Z",
              "end_date": "2017-08-04T06:50:05.520Z"
            },
            "quantity": "string",
            "currency_amount": {
              "amount": 0,
              "currency_code": "string"
            },
            "other_amount": {
              "type": "GiftCard",
              "amount": 0,
              "currency_code": "string"
            },
            "image_url": "string"
          }
        ]
      },
      "payment_transaction": {
        "id": "string",
        "id_type": "Expirable",
        "expiry_date": "2017-08-04T06:50:05.521Z"
      },
      "other_amount": {
        "type": "GiftCard",
        "amount": 0,
        "currency_code": "string"
      },
      "refunded_currency_amount": {
        "amount": 0,
        "currency_code": "string"
      }
    },
    "redirect": {
      "method": "string",
      "protocol": "string",
      "form_inputs": {
        "form_input": [
          {
            "key": "string",
            "value": "string"
          }
        ]
      }
    },
    "gateway_information": {
      "code": "string",
      "platform_user": "string",
      "platform_merchant_id": "string",
      "platform_mode": "string",
      "cost": 0,
      "settlement_day": 0
    },
    "multibanco_reference": "string",
    "status": "Success"
  },
  "card_data_token": "string",
  "status": "Success"
}
```

<aside class="success">
Remember: <a href ="#fraudprocessrq"><i>fraud_process</i></a> field must be filled if a fraud analysis must be done, as <i>payment_process</i> must be filled if a payment must too.
</aside>

* Valid values are *Explicit*, *Encrypted*, *Tokenized*. A null *card_type* will ignore the *card_data* information.

### Card Data:

```json
{
  encrypted_data (string, optional),
  store_card_info (bool, optional),
  tokenized_data (string, optional),
  number (string, optional),
  expiration_month (integer, optional),
  expiration_year (integer, optional),
  security_code (string, optional)
}
```

Node | Type | Description
---- | ---- | -----------
store_card_info | String | Indicates whether or not should the information be stored and tokenized for future uses by the *SWAPPT Vault* service.
encrypted_data | Boolean | Card data information encrypted by the *SWAPPT CSE JavaScript*. Mandatory if *card_type* is *Encrypted*.
tokenized_data | String | Token pointing the data previously stored on *SWAPPT Vault*. Mandatory if *card_type* is *Tokenized*.
number | String |  Card number. Mandatory if *card_type* is *Explicit*.
expiration_month | Integer | Card expiration month. Mandatory if *card_type* is *Explicit*.
expiration_year | Integer | Card expiration year. Mandatory if *card_type* is *Explicit*.
security_code | String | Card security code. Mandatory if *card_type* is *Explicit*.

### SenderDetails:

```json
{
  holder (string, optional),
  id (string, optional),
  name (string, optional),
  surname (string, optional),
  email (string, optional),
  phone_number (string, optional),
  ssn (string, optional),
  address (Address, optional),
  billing_address (Address, optional),
  sender_bank_details (BankDetails, optional),
  recipient_bank_details (BankDetails, optional)
}
```

Node | Type | Description
---- | ---- | -----------
holder | String | Holder name and surname
id | String | 
name | String | Sender name
surname | String | Sender surname
email | String | Sender email
phone_number | String | Sender phone number
ssn | String | Social Security Number
address | Address | Sender address (see "*Address*" structure in 1.2.3.1)
billing_address | Address | Billing address (see "*Address*" structure in 1.2.3.1)
sender_bank_details | BankDetails Sender bank details(see "*BankDetails*" structure in 1.2.3.2)
recipient_bank_details | BankDetails | Recipent bank details (see "*BankDetails*" structure in 1.2.3.2)

#### Address:

```json
{
  country_code (string, optional),
  city (string, optional),
  address_line1 (string, optional),
  address_line2 (string, optional),
  zip_code (string, optional)
}
```

Node | Type | Description
---- | ---- | -----------
country_code | String | ISO3 country code
city | String | City name
address_line1 | String | Address line 1 (main)
address_line2 | String | Address line 2 (additional information)
zip_code | String | Adress zip code

#### BankDetails:

```json
{
  bic (string, optional),
  holder (string, optional),
  bank_account (BankAccount, optional),
  bank_name (string, optional),
  country_code (string, optional),
  iban (string, optional)
}
```

Node | Type | Description
---- | ---- | -----------
bic |  | 
holder |  | 
bank_account |  | 
bank_name |  | 
country_code |  | 
iban |  | 

##### BankAccount:

```json
{
  number (string, optional),
  security_code (string, optional)
}
```

Number and security code of the bank account.

### FraudProcessRQ :

> FraudProcessRQ

```json
{
  enabled (optional),
  rules_module (optional),
  default_result (optional),
  device_fingerprint (optional),
  ip_address (optional),
  session_id (optional),
  custom_data (optional)
}
```

Node | Type | Description
---- | ---- | -----------
enabled | Boolean | Indicates whether or not a fraud analysis must be done.
rules_module | Array[SkeeperRule] | Array of Fraud rules to be applied (if no rules specified, it will be used the configured ones on the *SWAPPT FDS *website.
default_result | String | The result that must be returned if no rule is triggered (mandatory if rules provided on the request). Values can be *'Undefined'*, *'Accept'*, *'Reject'* or *'AcceptWithSecurePayment'*.
defice_fingerprint | String | Customer’s device ID.
ip_address | String | Customer’s IP Address.
session_id | String | Customer’s navigation session ID.
custom_data | Key-Value | Additional data that Fraud should know. It is a Key-Value field, where Keys are *SWAPPT FDS*’s tool names and values are the configurations for that tools:

    * *Protect*: Key-Value containing the Custom Fields of the transaction, where Key is the Custom Field name, and Value it’s value (Custom Fields must be previously configured con *SWAPPT Protect*’s website, more info Here).

    * *V5*: Comming soon

    * *Sift_science*: Comming soon

### PaymentProcessRQ:

```json
{
  enabled (boolean, optional),
  tokenization (Tokenization, optional),
  reference_token (string, optional),
  payment_details (PaymentDetails, optional),
  callback_url_fail (string, optional),
  callback_url_success (string, optional),
  client_config (inline_model_1, optional),
  source (Source, optional),
  merchant_reference (string, optional),
  payment_method (string, optional) = ['Undefined', 'Visa', 'Mastercard', 'American_express', 'JCB', 'Union_pay', 'Diners_club', 'Hiper', 'Discover', 'Paysafecard', 'Sofort', 'Trustly', 'Offline_transference', 'Paga_mas_tarde', 'Instant_credit', 'Cetelem', 'Klarna', 'Paypal', 'Masterpass', 'Safety_pay', 'IUPay', 'Multibanco', 'Braspag', 'Limonetik', 'Cash'],
  gateway_code (string, optional)
}
```

## Details

> DetailsRQ

```json
{
  search_type (string),
  transaction_id (string),
  transaction_start_time (string),
  transaction_end_time (string),
  session_code (string),
  client_config (Key-Value),
  source (Source),
  merchant_reference (string),
  payment_method (string) = ['Undefined', 'Visa', 'Mastercard', 'American_express', 'JCB', 'Union_pay', 'Diners_club', 'Hiper', 'Discover', 'Paysafecard', 'Sofort', 'Trustly', 'Offline_transference', 'Paga_mas_tarde', 'Instant_credit', 'Cetelem', 'Klarna', 'Paypal', 'Masterpass', 'Safety_pay', 'IUPay', 'Multibanco', 'Braspag', 'Limonetik', 'Cash'],
  gateway_code (string)
}
```
Node | Type | Description
---- | ---- | -----------
search_type | String | This node specifies what type of search is this request based on. Possible values are *'ByTransaction' *and *'ByDates'*. The first one will launch a transaction search by its transaction id and the node *transaction_id *MUST be specified. For the second value, *transaction_start_time *and *transaction_end_time* nodes MUST be specified for them are the limits of the date search.
session_code | string | 
client_config | Key-Value |  The client configuration
payment_method | String | 
gateway_code | String | Indicates the code of the gateway where the payment details should be consulted.


## Refund

TODO


# Tokenization

This is the core process of *SWAPPT*s *Vault* solution. As you may know, this process is used to tokenize credit cards and, of course, retrieve them. 


## Tokenize

*SWAPPT Vault*’s tokenization is used to tokenize credit cards. The system will save that information, and will return a unique token. The structure of the body must match the following:

> TokenizeRS 

```json
{
  "token": "K7WMBFTB5VAQPJWVHBZLFQWCPAYQAQWWS5U4F4NS6MKN4LWDX74EVRZLUOQS6FQOQKXHIXTY2WXH2===",
  "status": "Success"
}
```

Node | Type | Description
---- | ---- | -----------
number | String | Card number. 
expiration_month | Integer | Card expiration month.
expiration_year | Integer | Card expiration year.
security_code | String |  Card security code.


Where status takes the values '*Success'* or '*Error'*. If *Success*, *token *contains the unique token, if *Error*, *error_details* contains information related to the error occurred (see *Appendix *for a deep description). Note that the token returned has 1 unique use. When used, the system will provide a new one (if desired), and will make the used one useless.


## Retrieve

This call will return the information associated to a token. Moreover, if you wish the data to remain saved, you should set the *retain* field to *True*.

> RetrieveRS example:

```json
{
  "data": {
    "credit_card": {
      "number": "0000000000000000",
      "expiration_month": 1,
      "expiration_year": 3000,
      "security_code": "000"
    }
  },
  "token": "POYSIGHFLRA7XO64XIFUFUEB3PHCABVZE63ATEWBV6VV6UTL6EJU34I2KOP4N4CB6QM2FJODUK4A4===",
  "status": "Success"
}
```

Node | Type | Description
---- | ---- | -----------
token | String | Token with the information to retrieve.
retain | Boolean | True if data must remain saved, False if don't (Default: False).

For an *Error status*, *error_details* will contain information related to the error occurred (see *Appendix* for a deep description).


<aside class="warning">
<i>token</i> field will only be returned if the field <i>retain</i> was sent, and its value was true. Other input will cause the data to be invalidated after returning it.
</aside>


# Appendix

## ErrorDetails

<code>
{
  error_type (string)
  error_messages (Array[GlobalError])
}
</code>

> Error example:

An *ErrorDetails* field will contain 2 fields. *error_types* determines the *SWAPPT* Solution where the error was caused (*'FDS*, *'Switcher'*, *'Vault'*), or *'Technical' *in case of internal error. On the *error_messages* field are gathered all the errors appeared during the execution. It has a simple structure:

Node | Description
---- | -----------
code | The error code (you can see the possible error codes on section 4.1) 
description | Provides a textual description of the error cause.
field | Contains the field that is related to the error (for example in *400*’s errors), and 
process_moment | Says when the error was produced (*BeforeCallProvider*, *WhileCallingProvider*, *AfterCallProvider*), which will help us to detect the error and will tell you if the provider call was done.


## Error Codes

<table>
  <tr>
    <td>Code</td>
    <td>Name</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Bad_Request</td>
    <td>Request does not match the specification.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Unauthorized</td>
    <td>User not authorized for that operation.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Forbidden</td>
    <td>Invalid user.</td>
  </tr>
  <tr>
    <td>404</td>
    <td>Not_Found</td>
    <td>Something requested was not found.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>Request_Timeout</td>
    <td>Timeout (tipically on provider’s side).</td>
  </tr>
  <tr>
    <td>409</td>
    <td>Conflict</td>
    <td>Conflict with the configuration (e.g. FDS returns SecurePayment and referencing a non secure gateway).</td>
  </tr>
  <tr>
    <td>410</td>
    <td>Gone</td>
    <td>Resource not yet avaliable.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Internal_Server_Error</td>
    <td>System internal error (contact SWAPPT team).</td>
  </tr>
  <tr>
    <td>1000</td>
    <td>Undefined</td>
    <td>Error could not be traced.</td>
  </tr>
</table>
