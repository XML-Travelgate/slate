---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - json
  - csharp
  - go

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

The SWAPPT API is composed by 3 big processes: 

- Payment
- Notification
- Tokenization. 

This document describes all 3 processes and its respective APIs.

# Payment

This is the main process; the process where a payment and/or fraud analysis is done (if the *SWAPPT Switcher* and/or *SWAPPT FDS* solutions are hired and activated). It requires only 2 calls to the SWAPPT API to do a payment: *Get Available Payment Methods*, and *Payment*. More actions can be done related to the payment process, a *Payment Details* call to retrieve the status and details of a payment, and a *Refund* call to refund a payment.

## Payment: GetAvailablePaymentMethods

The first call that you must do is *available_payment_methods*. With this call, you will get the list of payment methods that you have available for the transaction and configuration specified. Note that this step is only mandatory for payments; for fraud analysis is not required.

```json
AvailablePaymentMethodsRQ {
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

1.2 PAYMENT: *Payment*

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





















---

# Introduction

Welcome to the Kittn API! You can use our API to access Kittn API endpoints, which can get information on various cats, kittens, and breeds in our database.

We have language bindings in Shell, Ruby, and Python! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

This example API documentation page was created with [Slate](https://github.com/tripit/slate). Feel free to edit it and use it as a base for your own API's documentation.

# Authentication

> To authorize, use this code:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint retrieves a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

