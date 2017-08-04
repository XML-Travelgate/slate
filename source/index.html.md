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


**NOTE:** All the endpoints work around HTTPS. HTTP request **won't** work.


## Authentication


```curl 
curl https://api.swappt.com/v1/organizations/test/payment
     -H "Authorization: ApiKey YourApiKey"
````

You need to authenticate when performing an API call. Moreover, the API will check your Authorization for every operation, so make sure that you have permissions to do every request that you want to do.

In order to authenticate, you need to place the word 'ApiKey' followed by your API key on the **Authorization** header of your HTTP request.

# AvailablePaymentMethods

The first call that you must do is *available_payment_methods*. With this call, you will get the list of payment methods that you have available for the transaction and configuration specified. Note that this step is **mandatory** for payments but for fraud analysis is not required.

> AvailablePaymentMethodsRQ:

```json
 {
  currency_amount (CurrencyAmount),
  departure (string,ptional),
  cancellation_expenses_beginning (string),
  excluded_methods (Array[string]),
  fraud_information (FraudInformation, optional),
  only_secure_payment (boolean),
  session_code (string),
  client_config (Key-Value)
}
```

Node | Type | Description
---- | ---- | -----------
currency_amount | CurrencyAmount | Payment base amount where taxes will be applied. (See section 1.1.1 for more information).
departure | String |  
cancellation_expenses_beginning | String | 
excluded_methods | Array[String] | 
fraud_information | FraudInformation | This node can indicate previous fraud information so only secure gateways are returned.
only_secure_payment | Boolean | 
session_code | String | 
client_config | Key-Value | 


1.1.1 CurrencyAmount:

The CurrencyAmount specifies the amount and the currency used for this payment. The currency code must be specified in ISO3 (ISO 4217)) format

```json
{
  amount (number, mandatory),
  currency_code (string, mandatory)
}
```

# Payment

This is the main process; the process where a payment and/or fraud analysis is done (if the *SWAPPT Switcher* and/or *SWAPPT FDS* solutions are hired and activated). It requires only 2 calls to the SWAPPT API to do a payment: *Get Available Payment Methods*, and *Payment*. More actions can be done related to the payment process, a *Payment Details* call to retrieve the status and details of a payment, and a *Refund* call to refund a payment.

As it was previously introduced, the *Payment* procedure is intended to do the main payment process and/or do a previous Fraud analysis. The request must have the following JSON structure:

```json
ProcessRQ {
  card_data (*CardData*, optional),
  card_type (string, optional)*,
  currency_amount (*CurrencyAmount*, mandatory),
  sender_details (*SenderDetails*, mandatory),
  session_code (string, mandatory),
  fraud_process (*FraudProcessRQ*, optional),
  payment_process (*PaymentProcessRQ*, optional)
}
```

*fraud_process* field must be filled if you wish to perform a fraud analysis.
Likewise the *payment_process* must be filled if you're performing a payment.

* Valid values are:

- *Explicit*
- *Encrypted*
- *Tokenized. 

> A null *card_type* will ignore the *card_data* information.


1.2.1 Card Data:

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

- store_card_info: indicates whether or not should the information be stored and tokenized for future uses by the *SWAPPT Vault* service.
- encrypted_data: card data information encrypted by the *SWAPPT CSE JavaScript*. Mandatory if *card_type* is *Encrypted*.
- tokenized_data: token pointing the data previously stored on *SWAPPT Vault*. Mandatory if *card_type* is *Tokenized*.
- number: card number. Mandatory if *card_type* is *Explicit*.
- expiration_month: card expiration month. Mandatory if *card_type* is *Explicit*.
- expiration_year: card expiration year. Mandatory if *card_type* is *Explicit*.
- security_code: card security code. Mandatory if *card_type* is *Explicit*.

1.2.2 SenderDetails:

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

- holder: Holder name and surname
- id: 
- name_: Sender name
- surname: Sender surname
- email: Sender email
- phone_number: Sender phone number
- ssn: Social Security Number
- address: Sender address (see "*Address*" structure in 1.2.3.1)
- billing_address: Billing address (see "*Address*" structure in 1.2.3.1)
- sender_bank_details: Sender bank details(see "*BankDetails*" structure in 1.2.3.2)
- recipient_bank_details: Recipent bank details (see "*BankDetails*" structure in 1.2.3.2)

1.2.2.1 Address:

```json
{
  country_code (string, optional),
  city (string, optional),
  address_line1 (string, optional),
  address_line2 (string, optional),
  zip_code (string, optional)
}
```

- country_code: ISO3 country code
- city: City name
- address_line1: Address line 1 (main)
- address_line2: Address line 2 (additional information)
- zip_code: Adress zip code

1.2.2.2 BankDetails:

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

- bic: (string, optional),
- holder: (string, optional),
- bank_account: (BankAccount, optional),
- bank_name: (string, optional),
- country_code: (string, optional),
- iban: (string, optional)

1.2.2.2.1 BankAccount:

```json
{
  number (string, optional),
  security_code (string, optional)
}
```

Number and security code of the bank account.

1.2.3 FraudProcessRQ :

```json
{
  enabled (boolean, optional),
  rules_module (Array[SkeeperRule], optional),
  default_result (string, optional),
  device_fingerprint (string, optional),
  ip_address (string, optional),
  session_id (string, optional),
  custom_data (Key-Value, optional)
}
```

- enabled: Indicates whether or not a fraud analysis must be done.
- rules_module: Array of Fraud rules to be applied (if no rules specified, it will be used the configured ones on the *SWAPPT FDS *website.
- default_result: The result that must be returned if no rule is triggered (mandatory if rules provided on the request). Values can be *'Undefined'*, *'Accept'*, *'Reject'* or *'AcceptWithSecurePayment'*.
- defice_fingerprint: Customer’s device ID.
- ip_address: Customer’s IP Address.
- session_id: Customer’s navigation session ID.
- custom_data: Additional data that Fraud should know. It is a Key-Value field, where Keys are *SWAPPT FDS*’s tool names and values are the configurations for that tools:

- *Protect*: Key-Value containing the Custom Fields of the transaction, where Key is the Custom Field name, and Value it’s value (Custom Fields must be previously configured con *SWAPPT Protect*’s website, more info Here).
- V5: Comming soon
- Sift_science: Comming soon

1.2.4 PaymentProcessRQ:

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

- search_type: This node specifies what type of search is this request based on. Possible values are *'ByTransaction' *and *'ByDates'*. The first one will launch a transaction search by its transaction id and the node *transaction_id *MUST be specified. For the second value, *transaction_start_time *and *transaction_end_time* nodes MUST be specified for them are the limits of the date search.
- session_code:
- client_config: the client configuration
- payment_method:
- gateway_code: indicates the code of the gateway where the payment details should be consulted.

## Refund

> TODO


# Tokenization

This is the core process of *SWAPPT*s *Vault* solution. As you may know, this process is used to tokenize credit cards and, of course, retrieve them. 


## Tokenize

*SWAPPT Vault*’s tokenization is used to tokenize credit cards. The structure of the body must match the following:

> TokenizeRQ

```json
{
  credit_card {
    number (string),
    expiration_month (integer),
    expiration_year (integer),
    security_code (string)
  }
}
```

This call will provoke the system to save that information, and will return a unique token. The response structure is:

> TokenizeRS 

```json
{
  token (string),
  error_details (ErrorDetails),
  status (string) 
}
```

Where status takes the values '*Success'* or '*Error'*. If *Success*, *token *contains the unique token, if *Error*, *error_details* contains information related to the error occurred (see *Appendix *for a deep description). Note that the token returned has 1 unique use. When used, the system will provide a new one (if desired), and will make the used one useless.


## Retrieve

This call will return the information associated to a token. Moreover, if you wish the data to remain saved, you should set the *retain *field to *True*, other input will cause the data to be invalidated after returning it. The structure of the body is very simple:

```json
RetrieveRQ {
  token (string),
  retain (boolean)
}
```

The response is rather as exposed:

```json
RetrieveRS {
  data (TokenizeRQ),
  token (string),
  error_details (ErrorDetails),
  status (string) = ['Success', 'Error']
}
```

As for *Tokenize*, for a *Sucess status*, *data *will contain the data that was introduced during *Tokenize *call and *token *will contain the new token if data was asked to remain saved. For an *Error status*, *error_details* will contain information related to the error occurred (see *Appendix *for a deep description).

4. APPENDIX: _ErrorDetails_

```json
ErrorDetails {
  error_type (string)
  error_messages (Array[GlobalError])
}
```

An *ErrorDetails *field will contain 2 fields. *error_types *determines the *SWAPPT *Solution where the error was caused (*'FDS*, *'Switcher'*, *'Vault'*) or *'Technical' *in case of internal error. On the *error_messages *field are gathered all the errors appeared during the execution. It has a simple structure:

```json
GlobalError {
  code (string),
  description (string),
  field (string),
  process_moment (string)
}
```

- code: The error code (you can see the possible error codes on section 4.1) 
- description: Provides a textual description of the error cause.
- field: Contains the field that is related to the error (for example in *400’s *errors), and 
- process_moment: Says when the error was produced (*BeforeCallProvider*, *WhileCallingProvider*, *AfterCallProvider*), which will help us to detect the error and will tell you if the provider call was done.

4.1 APPENDIX: Error Codes


