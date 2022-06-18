---
title: "Payment Order"
excerpt: "A description of the usage of the Payment Order endpoints"
---
# Payment Order

The `payment order` resource exposes several methods dedicated to dealing with payment objects.

Example:

```c#
var getPaymentOrders = new PingPaymentsApiClient(httpClient).PaymentOrder.V1.List;
var createPaymentOrder = new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Create;
var getPaymentOrder = new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Get;
var updatePaymentOrder = new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Update;
var closePaymentOrder = new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Close;
var splitPaymentOrder = new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Split;
var settlePaymentOrder = new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Settle;

var paymentOrderRequest = new createPaymentOrderRequest(PingPayments.PaymentsApi.Payments.Shared.V1.CurrencyEnum.SEK);

PaymentOrdersResponse listResponse = await listPaymentOrders((from, to));
GuidResponse orderIdResponse = await createPaymentOrder(paymentOrderRequest);
PaymentOrderResponse getResponse = await getPaymentOrder(orderId);
EmptyResponse updateResponse = await updatePaymentOrder((orderId, splitTreeId));
EmptyResponse splitResponse = await splitPaymentOrder(orderId);
EmptyResponse closeResponse = await closePaymentOrder(orderId);
EmptyResponse settleResponse = await settlePaymentOrder(orderId);
```

## Module Name

`paymentOrders`

## Functions

-   [Get Payment Orders](/doc/api_resources/payments_api//paymentOrder.md#get-payment-orders)
-   [Create New Paymkent Order](/doc/api_resources/payments_api//paymentOrder.md#create-new-payment-order)
-   [Get Specific Payment Order](/doc/api_resources/payments_api//paymentOrder.md#get-specific-payment-order)
-   [Update Payment Order](/doc/api_resources/payments_api//paymentOrder.md#update-payment-order)
-   [Close Payment Order](/doc/api_resources/payments_api//paymentOrder.md#close-payment-order)
-   [Split Payment Order](/doc/api_resources/payments_api//paymentOrder.md#split-payment-order)
-   [Settle Payment Order](/doc/api_resources/payments_api//paymentOrder.md#settle-payment-order)

# Get Payment Orders

Gets and returns a list of payment order objects for a tenant.

Using `List()`:

-   Create a HttpClient with a `tenantId` GUID and an `apiUrl` URI pointing to the Ping Payments API.
-   Create a PingPaymentsApiClient using the HttpClient.
-   Use the List endpoint: `PaymentOrder.V1.List`.

You can can use an optional date range arg containing `from` and `to` values to limit the list of payment order objects List() returns, by date. 
List() returns an error object if you have provided an invalid date in either the date_from or date_to parameter or if the tenant_id is invalid.

```c#
new PingPaymentsApiClient(httpClient).PaymentOrder.V1.List((to, from));
```

| Parameter   | Type                 | Required  | Description                                       |
| ----------- | -------------------- | --------- | ------------------------------------------------- |
| `args`      | range                | No        | (`DateTimeOffset`, `DateTimeOffset`)              |


The datetime range consists of a range of two DateTimeOffset parameters, to and from.

| Parameter   | Type                 | Required  | Description                                       |
| ----------- | -------------------- | --------- | ------------------------------------------------- |
| `from`      | `DateTimeOffset`     | Yes       | Start date for the range                          |
| `to`        | `DateTimeOffset`     | Yes       | End date for the range                            |

## Response

```c#
PaymentOrdersResponse response = await new PingPaymentsApiClient(httpClient).PaymentOrder.V1.List((to, from));
var status = response.StatusCode;
```

### 200

A successful call. `List()` returns a response containing a list of payment order objects.

Example:

```json
[
  {
    "created_at": "2021-11-05T10:04:19.275000Z",
    "id": "55555555-5555-5555-5555-555555555555",
    "payments": [
      {
        "currency": "SEK",
        "id": "55555555-5555-5555-5555-555555555555",
        "method": "autogiro",
        "order_items": [],
        "provider": "bankgirot",
        "status": "COMPLETED"
      }
    ],
    "status": "OPEN"
  },
  {
    "created_at": "2021-11-15T09:15:01.400000Z",
    "id": "55555555-5555-5555-5555-555555555555",
    "payments": [
      {
        "currency": "SEK",
        "id": "55555555-5555-5555-5555-555555555555",
        "metadata": {},
        "method": "mobile",
        "order_items": [],
        "provider": "swish",
        "status": "ABORTED"
      }
    ],
    "status": "OPEN"
  }
]
```

### 403

API error. `List()`  returned an error message.

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

### 422

Validation error. `List()` returned an error message because of an invalid value.

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

PaymentOrdersResponse response = await new PingPaymentsApiClient(httpClient).PaymentOrder.V1.List((to, from));

if (response.IsSuccessful) {
  var body = result.Body;
  // use list of payment orders in body
}

if (response.IsFailure) {
  var body = result.Body;
  // use error message in body
}
```

# Create New Payment Order

Creates a new payment order connected to a split tree datastructure.

Using `Create()`:

-   Create a HttpClient with a `tenantId` GUID and an `apiUrl` URI pointing to the Ping Payments API.
-   Create a PingPaymentsApiClient using the HttpClient.
-   Use the Create endpoint: `PaymentOrder.V1.Create`.

Create() takes a request containing a specified `Currency` and an optional `SplitTreeId` and returns an object containing an `orderId`. 
Create() returns an error object if the SplitTreeId or Currecy is invalid,  or if the TenantId in the HttpClient is invalid..

```c#
new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Create(currency);
```

| Parameter       | Type           | Required | Description                                                                                                                                                 |
| --------------- | -------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `currecy`       | `CurrencyEnum` | Yes      | Enum: `SEK`, `NOK` <br>Type of currency used for this payment order. Payments connected to a payment order must have the same currency as the payment order |
| `splitTreeId`   | `Guid`         | No       | String containing the ID of a specific split tree                                                                                                           |

## Response Type

```c#
  GuidResponse response = await new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Create(currency);
  var status = response.StatusCode;
```

### 200

A successful call. `Create()` created a payment order. create_payment_order() returns an object containing a new `orderId`.

Example:

```json
{
  "id": "55555555-5555-5555-5555-555555555555"
}
```

### 403

API error. `Create()` returned an error message.

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

### 422

Validation error. `Create()` returned an error message because of an invalid value.

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

var request = new createPaymentOrderRequest(PingPayments.PaymentsApi.Payments.Shared.V1.CurrencyEnum.SEK);

GuidResponse response = await new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Create(request);

if (response.IsSuccessful) {
  var body = result.Body;
  // use guid id in body
}

if (response.IsFailure) {
  var body = result.Body;
  // use error message in body
}
```

# Get Specific Payment Order

Gets a payment order.

Using `Get()`:

-   Create a HttpClient with a `tenantId` GUID and an `apiUrl` URI pointing to the Ping Payments API.
-   Create a PingPaymentsApiClient using the HttpClient.
-   Use the Get endpoint: `PaymentOrder.V1.Get`.

Get() takes an `orderId` and returns a payment order object. 
Get() returns an error object if the orderId or the tenantId is invalid.

```c#
new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Get(orderId);
```

| Parameter          | Type     | Description                                            |
| ------------------ | -------- | ------------------------------------------------------ |
| `orderId`          | `string` | String containing the ID of a specific payment order   |

## Response Type

```c#
PaymentOrdersResponse response = await new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Get(orderId);
var status = response.StatusCode;
```

### 200

A successful call. `Get()` returned a payment order.

Example:

```json
{
  "created_at": "2021-11-15T09:15:01.400000Z",
  "id": "55555555-5555-5555-5555-555555555555",
  "payments": [
    {
      "currency": "SEK",
      "id": "55555555-5555-5555-5555-555555555555",
      "metadata": {},
      "method": "mobile",
      "order_items": [],
      "provider": "swish",
      "status": "COMPLETED"
    }
  ]
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

Search error. `Get()` couldn't match the `orderId` to a payment order object.

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

```C#
var apiUrl = Configuration["PingPayments:ApiUrl"];
var tenantId = Configuration["PingPayments:TenantId"];
var httpClient = new HttpClient().ConfigurePingPaymentsClient(apiUrl, tenantId);
var api = new PingPaymentsApiClient(httpClient);

PaymentOrdersResponse response = await new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Get(orderId);

if (response.IsSuccessful) {
  var body = result.Body;
  // use payment order in body
}

if (response.IsFailure) {
  var body = result.Body;
  // use error message in body
}
```

# Update Payment Order

Updates a payment order with a new "split tree" datastructure containing a ruleset for the distribution of funds.

The new split tree datastructure is used for splitting payments in the payment order between payment recipients.

Using `Update()`:

-   Create a HttpClient with a `tenantId` GUID and an `apiUrl` URI pointing to the Ping Payments API.
-   Create a PingPaymentsApiClient using the HttpClient.
-   Use the Update endpoint: `PaymentOrder.V1.Update`.

Update() takes an `orderId` and a `SplitTreeId`, and updates a payment order matching the orderId. 
Update() returns an error object if the orderId is invalid or if the tenantId is invalid.

```C#
new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Update(orderId);
```

| Parameter          | Type     | Description                                            |
| ------------------ | -------- | ------------------------------------------------------ |
| `orderId`          | `Guid`   | Guid containing the ID of a specific payment order     |
| `SplitTreeId`      | `Guid`   | Guid containing the ID of a specific split tree        |

## Response Type

```C#
EmptyResponse response = new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Update((orderId, splitTreeId));
var status = response.StatusCode;
```

### 204

A successful call.

### 403

API error. `Update()` returned an error message.

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

Search error. `Update()` couldn't match the `orderId` to a payment order or the `SplitTreeId` to an existing split tree datastructure.

### 422

Validation error. `Update()` returned an error message because of an invalid value.

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

EmptyResponse response = await new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Update((orderId, splitTreeId));

if (response.IsSuccessful) {
  // handle successful response
}

if (response.IsFailure) {
  // handle error response
}
```

# Close Payment Order

Closes a payment order and disables the possibility of updating the payment order with further payments.

Using `Close()`:

-   Create a HttpClient with a `tenantId` GUID and an `apiUrl` URI pointing to the Ping Payments API.
-   Create a PingPaymentsApiClient using the HttpClient.
-   Use the Close endpoint: `PaymentOrder.V1.Close`.

Close() takes an `orderId` and updates a payment order matching the orderId with a "closed" status.
Close() returns an error object if the orderId or tenant_id is invalid.


```c#
new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Close(orderId);
```

| Parameter          | Type     | Required | Description                                            |
| ------------------ | -------- | -------- | ------------------------------------------------------ |
| `orderId`          | `Guid`   | Yes      | Guid containing the ID of a specific payment order     |

## Response Type

```c#
EmptyResponse response = new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Close(orderId);
var status = response.StatusCode;
```

### 204

A successful call.

### 403

API error. `Close()` returned an error message.

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

Search error. `Close()` couldn't match the `orderId` to a payment order.

### 422

Validation error. `Close()` returned an error message because of an invalid value.

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

EmptyResponse response = await new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Close(orderId);

if (response.IsSuccessful) {
  // handle successful response
}

if (response.IsFailure) {
  // handle error response
}
```

# Split Payment Order

Splits a payment order. Used when some payments in a payment order have been fulfilled.

A split payment order can still be partly refunded.

Using `Split()`:

-   Create a HttpClient with a `tenantId` GUID and an `apiUrl` URI pointing to the Ping Payments API.
-   Create a PingPaymentsApiClient using the HttpClient.
-   Use the Split endpoint: `PaymentOrder.V1.Split`.

Split() takes an `orderId` and updates a payment order matching the orderId with a "split" status.
Split() returns an error object if the orderId or tenantId is invalid.

```c#
new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Split(orderId);
```

| Parameter          | Type      | Required | Description                                                                             |
| ------------------ | --------- | -------- | --------------------------------------------------------------------------------------- |
| `orderId`          | `Guid`    | Yes      | Guid containing the ID of a specific payment order                                      |

## Response Type

```c#
EmptyResponse response = new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Split(orderId);
var status = response.StatusCode;
```

### 204

A successful call.

### 403

API error. `Split()` returned an error message.

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

Search error. `Split()` couldn't match the `orderId` to a payment order.

### 422

Validation error. `Split()` returned an error message because of an invalid value.

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

EmptyResponse response = await new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Split(orderId);

if (response.IsSuccessful) {
  // handle successful response
}

if (response.IsFailure) {
  // handle error response
}
```

# Settle Payment Order

Marks a payment order as "settled". A settled payment order only contains processed payments.

Refunds are not possible on a settled payment order.

Using `Settle()`:

-   Create a HttpClient with a `tenantId` GUID and an `apiUrl` URI pointing to the Ping Payments API.
-   Create a PingPaymentsApiClient using the HttpClient.
-   Use the Settle endpoint: `PaymentOrder.V1.Settle`.

Settle() takes an `orderId` and updates a payment order matching the orderId with a "split" status.
Settle() returns an error object if the orderId or tenantId is invalid.


```c#
new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Settle(orderId);
```

| Parameter          | Type      | Required | Description                                                                                                  |
| ------------------ | --------- | -------- | ------------------------------------------------------------------------------------------------------------ |
| `orderId`          | `Guid`    | Yes      | Guid containing the ID of a specific payment order                                                           |

## Response Type

```c#
EmptyResponse response = new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Settle(orderId);
var status = response.StatusCode;
```

### 204

A successful call.

### 403

API error. `Settle()` returned an error message.

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

Search error. `Settle()` couldn't match the `orderId` to a payment order.

### 422

Validation error. `Settle()` returned an error message because of an invalid value.

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

EmptyResponse response = await new PingPaymentsApiClient(httpClient).PaymentOrder.V1.Settle(orderId);

if (response.IsSuccessful) {
  // handle successful response
}

if (response.IsFailure) {
  // handle error response
}
```
