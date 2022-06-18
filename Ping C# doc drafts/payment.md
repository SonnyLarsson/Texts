---
title: "Payment"
excerpt: "A description of the usage of the Payment endpoints"
---

# Payments

The `payment` resource exposes several methods dedicated to dealing with payment objects.

Example:

```c#
var initiatePayment = new PingPaymentsApiClient(httpClient).Payments.V1.Initiate;
var getPayment = new PingPaymentsApiClient(httpClient).Payments.V1.Get;

var orderItems = new payments.Shared.V1.OrderItem
(
    minorCurrencyUnitValue,
    "A test article",
    payments.Shared.V1.SwedishVat.Vat25,
    merchantId
).InList();

var initiatePaymentRequest = createPaymentRequest.Swish.Ecommerce
(
    orderItems,
    "07012345678",
    "Thanks for your Swish Payment!",
    new Uri("https://my-callback.my-api.com")
);

InitiatePaymentResponse initiatePaymentResponse = await initiatePayment(orderId, initiatePaymentRequest);
PaymentResponse getPayment = await getPayment(orderId, paymentId);
```

## Module Name

`payments`

## Functions

-   [Initiate Payment](/doc/api_resources/payments_api/payment.md#initiate-payment)
-   [Get Payment](/doc/api_resources/payments_api/payment.md#get-payment)

# Initiate Payment

Initiates a payment for a payment order.

Using `Initiate()`:

-   Create a HttpClient with a `tenantId` GUID and an `apiUrl` URI pointing to the Ping Payments API.
-   Create a PingPaymentsApiClient using the HttpClient.
-   Use the Initiate endpoint: `Payments.V1.Initiate`.

Initiate() takes a `paymentOrderId` and a payment object and returns an object containing data needed to fulfill the next step of a payment.
Initiate() returns an error object if the paymentOrderId or payment object is invalid, or if the tenantId is invalid.

```c#
new PingPaymentsApiClient(httpClient).Payments.V1.Initiate(orderId, initiatePaymentRequest);
```

| Parameter                | Type                     | Description                                                       |
| ------------------------ | ------------------------ | ----------------------------------------------------------------- |
| `orderId`                | `Guid`                   | Guid containing the ID of a specific Payment Order                |
| `initiatePaymentRequest` | `InitiatePaymentRequest` | Object containing all information needed to initiate a payment    |

## Payment Base

When you initiate a payment, you have the choice of several different types of payment methods.

All payment types contain the following parameters:

| Containing                   | Type               | Required | Description                                                                                                                                                                                                                                                                         |
| ---------------------------- | -------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `orderItems`                 | `IEnumerable<T>`           | Yes      | List of the items of purchase. The object contains an `amount`(an integer representing minor currency units), a string with a `merchantId` (of the merchant that is paying for that item), a `name`(name of the item) and a `vat` value (the vat rate of the item, integer) |
| `statusCallbackUrl`          | `Uri`                      | No       | URL for callbacks containing status updates on a payment. Read more under [Callback](/doc/api_resources/payments_api/paymentmd#Callback)                                                                                                                                    |
| `metadata`                   | `IDictionary`              | No       | Set of key-value pairs for storing additional information about the payment. IDictionary<string, dynamic>. Default: null                                                                                                                                                    |

## Additional Payment Parameters 

Different payment methods require different parameters.

### Dummy - method: Dummy

Use dummy payments when testing. You can test if a payment is possible with a dummy payment.

| Containing               | Type                | Required | Description                                                                                                                                                                 |
| ------------------------ | ------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `currency`               | `CurrencyEnum`      | Yes      | Enum: `SEK`, `NOK` <br>Type of currency used for this payment. Payments connected to a payment order must have the same currency as the payment order                       |
| `desiredPaymentStatus`   | `PaymentStatusEnum` | Yes      | Desired payment status for the dummy payment. <br>Enum: `INITIATED`, `PENDING`, `DECLINED`, `CANCELLED`, `CRASHED`, `COMPLETED`, `EXPIRED`, `ABORTED`. Default: `COMPLETED` |

### Swish - Swish.Ecommerce

| Containing     | Type     | Required | Description                               |
| -------------- | -------- | -------- | ----------------------------------------- |
| `phoneNumber`  | `string` | Yes      | Payer's swish-connected phone number      |
| `message`      | `string` | Yes      | Message associated with the payment       |

### Swish - Swish.Mcommerce

| Containing    | Type                | Required | Description                              |
| ------------- | ------------------- | -------- | ---------------------------------------- |
| `message`     | `string`            | Yes      | Message associated with the payment      |
| `SwishQrCode` | `SwishQrCode`       | No       | QR-code image                            |

### Verifone - Verifone.Card

| Containing   | Type                           | Required | Description                                                                    |
| ------------ | ------------------------------ | -------- | ------------------------------------------------------------------------------ |
| `firstName`  | `string`                       | Yes      | Payer's first name                                                             |
| `lastName`   | `string`                       | Yes      | Payer's last name                                                              |
| `email`      | `string`                       | Yes      | Payer's email address                                                          |
| `successUrl` | `string`                       | Yes      | URL to which the payer is directed to upon successful completion of a payment  |
| `cancelUrl`  | `string`                       | Yes      | URL to which the payer is directed to when a payment gets canceled             |

### Billmate - Billmate.Invoice

| Containing            | Type      | Required | Description                                                      |
| --------------------- | --------- | -------- | ---------------------------------------------------------------- |
| `firstName`           | `string`  | Yes      | Payer's first name                                               |
| `lastName`            | `string`  | Yes      | Payer's last name                                                |
| `nattionalIdNumber`   | `string`  | Yes      | Payer's national ID number                                       |
| `email`               | `string`  | Yes      | Payer's email address                                            |
| `phoneNumber`         | `string`  | Yes      | Payer's phone number                                             |
| `country`             | `string`  | Yes      | Payer's country of residence                                     |
| `ipAddress`           | `string`  | Yes      | IP address for the device that the payment is being made from    |
| `customerReference`   | `string`  | Yes      | Customer reference                                               |
| `isCompanyCustomer`   | `boolean` | Yes      | Payer's status as a company or an individual                     |


### Payment Iq - PaymentIq.Card

| Containing    | Type           | Required | Description                                                                                                                                           |
| ------------- | ---------------| -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| `currency`    | `CurrencyEnum` | Yes      | Enum: `SEK`, `NOK` <br>Type of currency used for this payment. Payments connected to a payment order must have the same currency as the payment order |
| `successUrl`  | `Uri`          | Yes      | URL to which the payer is directed to upon successful completion of a payment                                                                         |
| `cancelUrl`   | `Uri`          | Yes      | URL to which the payer is directed to when a payment gets canceled                                                                                    |

### Payment Iq - PaymentIq.Vipps

| Containing    | Type           | Required | Description                                                                                                                                           |
| ------------- | ---------------| -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| `currency`    | `CurrencyEnum` | Yes      | Enum: `SEK`, `NOK` <br>Type of currency used for this payment. Payments connected to a payment order must have the same currency as the payment order |
| `successUrl`  | `Uri`          | Yes      | URL to which the payer is directed to upon successful completion of a payment                                                                         |
| `cancelUrl`   | `Uri`          | Yes      | URL to which the payer is directed to when a payment gets canceled                                                                                    |

### Ping Deposit - PingDeposit.Ocr

| Containing      | Type                            | Required | Description                                 |
| --------------- | ------------------------------- | -------- | ------------------------------------------- |
| `referenceType` | `PingDepositReferenceTypesEnum` | Yes      | Enum: `OCR`, `KID` <br>Type of this deposit |

### Ping Deposit - PingDeposit.Kid

| Containing      | Type                            | Required | Description                                 |
| --------------- | ------------------------------- | -------- | ------------------------------------------- |
| `referenceType` | `PingDepositReferenceTypesEnum` | Yes      | Enum: `OCR`, `KID` <br>Type of this deposit |

## Response Type

```c#
InitiatePaymentResponse response = await new PingPaymentsApiClient(httpClient).Payments.V1.Initiate(orderId, initiatePaymentRequest);
var status = response.StatusCode;
```

### 200

A successful call. `Initiate()` initiated a payment. Initiate() returns a `paymentIdd` and an object containing data needed to fulfill the next step of a payment.

Example:

```json
{
  "id": "55555555-5555-5555-5555-555555555555",
  'provider_method_response': {}
}
```

### 403

API error. `Initiate()` returned an error message.

Example:

```json
{
  "errors": [
    {
      "description": "Cannot initiate new Payments when PaymentOrder has been closed",
      "error": "payment_order_closed"
    }
  ]
}
```

### 404

Search error. `Initiate()` couldn't find a matching Payment order for the given payment `orderId`.

### 422

Validation error. The payment endpoint returned an error message because of an invalid value.

Example:

```json
{
  "errors": [
    {
      "description": "null value where string expected",
      "error": "null_value",
      "property": "open_banking.success_url"
    }
  ]
}
```

## Callback

A payment goes through different stages.

The payment status starts as `INITIATED`, continues as `PENDING` and last becomes either `COMPLETED`, `DECLINED`, `ABORTED`, `EXPIRED` or `CRASHED`.

Callbacks from Ping to the `statusCallbackUrl` sends updates containing payment status.

| Payment Status | Description                                                                 |
| -------------- | --------------------------------------------------------------------------- |
| `INITIATED`    | Payment is initiated and Ping starts sending status updates                 |
| `PENDING`      | Payment awaits the next action                                              |
| `COMPLETED`    | Successful payment                                                          |
| `DECLINED`     | Payment could not be processed                                              |
| `ABORTED`      | Payment was canceled by payer                                               |
| `EXPIRED`      | Payment timed out                                                           |
| `CRASHED`      | Payment caused an unexpected error                                          |

## Example Usage

```c#

var orderItems = new payments.Shared.V1.OrderItem
(
    minorCurrencyUnitValue,
    "A test article",
    payments.Shared.V1.SwedishVat.Vat25,
    merchantId
).InList();

var initiatePaymentRequest = createPaymentRequest.Swish.Ecommerce
(
    orderItems,
    "07012345678",
    "Thanks for your Swish Payment!",
    new Uri("https://my-callback.my-api.com")
);

var initiatePayment = new PingPaymentsApiClient(httpClient).Payments.V1.Initiate;
InitiatePaymentResponse response = await initiatePayment(orderId, initiatePaymentRequest);

if (response.IsSuccessful) {
  var body = result.Body;
  // use list of merchants in body
}

if (response.IsFailure) {
  var body = result.Body;
  // use error message in body
}
```

# Get Payment

Gets a payment from a payment order.

Using `Get()`:

-   Create a HttpClient with a `tenantId` GUID and an `apiUrl` URI pointing to the Ping Payments API.
-   Create a PingPaymentsApiClient using the HttpClient.
-   Use the Get endpoint: `Payments.V1.Get`.

Initiate() returns an error object if the paymentOrderId or payment object is invalid, or if the tenantId is invalid.


Get() takes a payment `orderId` and a `paymentId` connected to a matching payment order and returns a payment object. 
Get() returns an error object if the payment `orderId` or paymentId is invalid.
Get() returns an error object if the paymentId doesn't match any payments connected to the payment order matching the payment orderId.
Get() returns an error object if the tenantId is invalid.

```c#
new PingPaymentsApiClient(httpClient).Payments.V1.Get(orderId, paymentId);
```

| Parameter   | Type   | Description                                |
| ----------- | ------ | ------------------------------------------ |
| `orderId`   | `Guid` | Guid cointaining the ID of a payment order |
| `paymentId` | `Guid` | Guid cointaining the ID of a payment       |

## Response Type

```c#
PaymentResponse response  =  await new PingPaymentsApiClient(httpClient).Payments.V1.Get(orderId, paymentId);
var status = response.StatusCode;
```

### 200

A successful call. `Get()` returns a payment object.

Example:

```json
{
  "currency": "SEK",
  "id": "55555555-5555-5555-5555-555555555555",
  "metadata": {
    "delivery_id": "230955"
  },
  "method": "mobile",
  "order_items": [
    {
      "amount": 850,
      "name": "Delivery, Pizza",
      "vat_rate": 12
    }
  ],
  "provider": "swish",
  "status": "PENDING"
}
```

### 403

API error. `Get()` returned an error message.

Example:

```json
{
  "errors": [
    {
      "description": "This operation cannot be completed under certain conditions",
      "error": "operation_forbidden"
    }
  ]
}
```

### 404

Search error. `Get()` couldn't match the `paymentId` to the payment `orderId` or the payment orderId is invalid.

### 422

Validation error. `Get()` returned an error message because of an invalid value.

Example:

```json
{
  "errors": [
    {
      "description": "null value where string expected",
      "error": "null_value",
      "property": "open_banking.success_url"
    }
  ]
}
```

## Example Usage

```c#
var apiUrl = Configuration["PingPayments:ApiUrl"];
var tenantId = Configuration["PingPayments:TenantId"];
var httpClient = new HttpClient().ConfigurePingPaymentsClient(apiUrl, tenantId);
var api = new PingPaymentsApiClient(httpClient);

PaymentResponse response  =  await new PingPaymentsApiClient(httpClient).Payments.V1.Get(orderId, paymentId);

if (response.IsSuccessful) {
  var body = result.Body;
  // use list of merchants in body
}

if (response.IsFailure) {
  var body = result.Body;
  // use error message in body
}
```
