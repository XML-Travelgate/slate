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

> **EXAMPLE:**
```curl 
curl https://api.swappt.com/v1/organizations/test/payment
     -H "Authorization: ApiKey YourApiKey"
````

You need to authenticate when performing an API call. Moreover, the API will check your Authorization for every operation, so make sure that you have permissions to do every request that you want to do.

In order to authenticate, you need to place the word 'ApiKey' followed by your API key on the **Authorization** header of your HTTP request.

# GetAvailablePaymentMethods

The first call that you must do is *available_payment_methods*. With this call, you will get the list of payment methods that you have available for the transaction and configuration specified. Note that this step is only mandatory for payments; for fraud analysis is not required.

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


* currency_amount: Payment base amount where taxes will be applied. (See section 1.1.1 for more information).

* departure: 

* cancellation_expenses_beginning:

* excluded_methods:

* fraud_information: This node can indicate previous fraud information so only secure gateways are returned.

* only_secure_payment:

* session_code:

* client_config:

1.1.1 CurrencyAmount:

{

amount (number, mandatory),

currency_code (string, mandatory)

}

Payment amount specified in a concrete currency (*currency_code* must be specified in ISO3 (ISO 4217)).

# Payment

This is the main process; the process where a payment and/or fraud analysis is done (if the *SWAPPT Switcher* and/or *SWAPPT FDS* solutions are hired and activated). It requires only 2 calls to the SWAPPT API to do a payment: *Get Available Payment Methods*, and *Payment*. More actions can be done related to the payment process, a *Payment Details* call to retrieve the status and details of a payment, and a *Refund* call to refund a payment.

As it was previously introduced, the *Payment* procedure is intended to do the main payment process and/or do a previous Fraud analysis. The request must have the following JSON structure:

ProcessRQ {

card_data (*CardData*, optional),

card_type (string, optional)*,

currency_amount (*CurrencyAmount*, mandatory),

sender_details (*SenderDetails*, mandatory),

session_code (string, mandatory),

fraud_process (*FraudProcessRQ*, optional),

payment_process (*PaymentProcessRQ*, optional)

}

*fraud_process *field must be filled if a fraud analysis must be done, as *payment_process *must be filled if a payment must too.

* Valid values are *Explicit*, *Encrypted*, *Tokenized. *A null *card_type* will ignore the *card_data* information.

1.2.1 Card Data:

{

encrypted_data (string, optional),

store_card_info (bool, optional),

tokenized_data (string, optional),

number (string, optional),

expiration_month (integer, optional),

expiration_year (integer, optional),

security_code (string, optional)

}

* _store_card_info:_ indicates whether or not should the information be stored and tokenized for future uses by the *SWAPPT Vault* service.

* _encrypted_data_: card data information encrypted by the *SWAPPT CSE JavaScript*. Mandatory if *card_type* is *Encrypted*.

* _tokenized_data_: token pointing the data previously stored on *SWAPPT Vault*. Mandatory if *card_type* is *Tokenized*.

* _number: _card number. Mandatory if *card_type* is *Explicit*.

* _expiration_month: _card expiration month. Mandatory if *card_type* is *Explicit*.

* _expiration_year: _card expiration year. Mandatory if *card_type* is *Explicit*.

* _security_code: _card security code. Mandatory if *card_type* is *Explicit*.

1.2.2 SenderDetails:

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

* _holder_: Holder name and surname

* _id_: 

* _name_: Sender name

* _surname_: Sender surname

* _email_: Sender email

* _phone_number_: Sender phone number

* _ssn_: Social Security Number

* _address_: Sender address (see "*Address*" structure in 1.2.3.1)

* _billing_address_: Billing address (see "*Address*" structure in 1.2.3.1)

* _sender_bank_details_: Sender bank details(see "*BankDetails*" structure in 1.2.3.2)

* _recipient_bank_details_: Recipent bank details (see "*BankDetails*" structure in 1.2.3.2)

1.2.2.1 Address:

{

country_code (string, optional),

city (string, optional),

address_line1 (string, optional),

address_line2 (string, optional),

zip_code (string, optional)

}

* _country_code_: ISO3 country code

* _city_: City name

* _address_line1_: Address line 1 (main)

* _address_line2:_ Address line 2 (additional information)

* _zip_code: _Adress zip code

1.2.2.2 BankDetails:

{

bic (string, optional),

holder (string, optional),

bank_account (BankAccount, optional),

bank_name (string, optional),

country_code (string, optional),

iban (string, optional)

}

* _bic_: (string, optional),

* _holder_: (string, optional),

* _bank_account_: (BankAccount, optional),

* _bank_name_: (string, optional),

* _country_code_: (string, optional),

* _iban_: (string, optional)

1.2.2.2.1 BankAccount:

{

number (string, optional),

security_code (string, optional)

}

Number and security code of the bank account.

1.2.3 FraudProcessRQ :

{

enabled (boolean, optional),

rules_module (Array[SkeeperRule], optional),

default_result (string, optional),

device_fingerprint (string, optional),

ip_address (string, optional),

session_id (string, optional),

custom_data (Key-Value, optional)

}

* _enabled:_ Indicates whether or not a fraud analysis must be done.

* _rules_module:_ Array of Fraud rules to be applied (if no rules specified, it will be used the configured ones on the *SWAPPT FDS *website.

* _default_result:_ The result that must be returned if no rule is triggered (mandatory if rules provided on the request). Values can be *'Undefined'*, *'Accept'*, *'Reject'* or *'AcceptWithSecurePayment'*.

* _defice_fingerprint: _Customer’s device ID.

* _ip_address: _Customer’s IP Address.

* _session_id: _Customer’s navigation session ID.

* _custom_data: _Additional data that Fraud should know. It is a Key-Value field, where Keys are *SWAPPT FDS*’s tool names and values are the configurations for that tools:

    * *Protect*: Key-Value containing the Custom Fields of the transaction, where Key is the Custom Field name, and Value it’s value (Custom Fields must be previously configured con *SWAPPT Protect*’s website, more info Here).

    * *V5*: Comming soon

    * *Sift_science*: Comming soon

1.2.4 PaymentProcessRQ:

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

1.3 PAYMENT: *Details*

DetailsRQ {

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

* _search_type_: This node specifies what type of search is this request based on. Possible values are *'ByTransaction' *and *'ByDates'*. The first one will launch a transaction search by its transaction id and the node *transaction_id *MUST be specified. For the second value, *transaction_start_time *and *transaction_end_time* nodes MUST be specified for them are the limits of the date search.

* session_code:

* client_config: the client configuration

* payment_method:

* _gateway_code_: indicates the code of the gateway where the payment details should be consulted.

1.4 PAYMENT: *Refund*

TODO

2. NOTIFICATION

This process is the entry point for gateways to deliver his notifications. An usual use case is when the gateway confirms/cancels a payment and informs us about that event. 

3. TOKENIZATION

3.1 TOKENIZATION: *Tokenize*

*SWAPPT Vault*’s tokenization is used to tokenize credit cards. The structure of the body must match the following:

TokenizeRQ {

credit_card {

  number (string),

expiration_month (integer),

expiration_year (integer),

security_code (string)

}

}

This call will provoke the system to save that information, and will return a unique token. The response structure is:

TokenizeRS {

token (string),

error_details (ErrorDetails),

status (string) 

}

Where status takes the values '*Success'* or '*Error'*. If *Success*, *token *contains the unique token, if *Error*, *error_details* contains information related to the error occurred (see *Appendix *for a deep description). Note that the token returned has 1 unique use. When used, the system will provide a new one (if desired), and will make the used one useless.

3.2 TOKENIZATION: *Retrieve*

This call will return the information associated to a token. Moreover, if you wish the data to remain saved, you should set the *retain *field to *True*, other input will cause the data to be invalidated after returning it. The structure of the body is very simple:

RetrieveRQ {

token (string),

retain (boolean)

}

The response is rather as exposed:

RetrieveRS {

data (TokenizeRQ),

token (string),

error_details (ErrorDetails),

status (string) = ['Success', 'Error']

}

As for *Tokenize*, for a *Sucess status*, *data *will contain the data that was introduced during *Tokenize *call and *token *will contain the new token if data was asked to remain saved. For an *Error status*, *error_details* will contain information related to the error occurred (see *Appendix *for a deep description).

4. APPENDIX: _ErrorDetails_

ErrorDetails {

error_type (string)

error_messages (Array[GlobalError])

}

An *ErrorDetails *field will contain 2 fields. *error_types *determines the *SWAPPT *Solution where the error was caused (*'FDS*, *'Switcher'*, *'Vault'*) or *'Technical' *in case of internal error. On the *error_messages *field are gathered all the errors appeared during the execution. It has a simple structure:

GlobalError {

code (string),

description (string),

field (string),

process_moment (string)

}

* _code: _The error code (you can see the possible error codes on section 4.1) 

* _description: _Provides a textual description of the error cause.

* _field: _Contains the field that is related to the error (for example in *400’s *errors), and 

* _process_moment:_ Says when the error was produced (*BeforeCallProvider*, *WhileCallingProvider*, *AfterCallProvider*), which will help us to detect the error and will tell you if the provider call was done.

4.1 APPENDIX: Error Codes

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
