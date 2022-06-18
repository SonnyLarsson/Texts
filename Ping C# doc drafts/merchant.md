---
title: "Merchant"
excerpt: "A description of the usage of the Merchant endpoints"
---

# Merchant

The `merchant` resource exposes several methods dedicated to dealing with merchant objects.

Example:

```c#
// using merchants = PingPayments.PaymentsApi.Merchants;

var listMerchants = new PingPaymentsApiClient(httpClient).Merchants.V1.List;
var createMerchant = new PingPaymentsApiClient(httpClient).Merchants.V1.Create;
var getMerchant = new PingPaymentsApiClient(httpClient).Merchants.V1.Get;

var merchantRequest = new merchants.Create.V1.CreateMerchantRequest
{
    Name = "test merchant AB",
    Organization = new merchants.Shared.V1.Organization
    {
        Country = "SE",
        SeOrganizationNumber = "1234567890"
    }
};

MerchantsResponse listResponse = await listMerchants();
GuidResponse merchantId = await createMerchant(merchantRequest);
MerchantResponse getResponse = await getMerchant(merchantId);
```

## Module Name

`merchants`

## Functions

-   [Get Merchants](/doc/api_resources/payments_api/merchant.md#get-merchants)
-   [Create New Merchant](/doc/api_resources/payments_api/merchant.md#create-new-merchant)
-   [Get Specific Merchant](/doc/api_resources/payments_api/merchant.md#get-specific-merchant)

# Get Merchants

Get an list of all the merchant objects connected to a specific tenant.

Using `List()`:

-   Create a HttpClient with a `tenantId` GUID and an `apiUrl` URI pointing to the Ping Payments API.
-   Create a PingPaymentsApiClient using the HttpClient.
-   Use the List endpoint: `Merchants.V1.List`.

List() returns an object with a list of all merchants for a valid tenantId. List() returns an error object if the tenantId is invalid.

```c#
new PingPaymentsApiClient(httpClient).Merchants.V1.List();
```

## Response

```c#
MerchantsResponse response = await new PingPaymentsApiClient(httpClient).Merchants.V1.List();
var status = response.StatusCode;
```

### Code 200

A successful call. `List()` returned a list of all the merchants objects connected to a tenantId

Example:

```json
[
  {
    "email": "contact@merchant.a.com",
    "id": "55555555-5555-5555-5555-555555555555",
    "name": "Merchant A",
    "organization": {
      "country": "NO",
      "no_organization_number": "555555555"
    },
    "phone_number": "0731231234"
  },
  {
    "email": "contact@merchant.b.com",
    "id": "55555555-5555-5555-5555-555555555555",
    "name": "Merchant B",
    "organization": {
      "country": "SE",
      "se_organization_number": "5555555555"
    },
    "phone_number": "0739876543"
  }
]
```

### 403

API error. `List()` returned an error message.

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

MerchantsResponse response = await new PingPaymentsApiClient(httpClient).Merchants.V1.List((to, from));

if (response.IsSuccessful) {
  var body = result.Body;
  // use list of merchants in body
}

if (response.IsFailure) {
  var body = result.Body;
  // use error message in body
}
```

# Create New Merchant

Create and connect a new merchant to a tenant.

Using `Create()`:

-   Create a HttpClient with a `tenantId` GUID and an `apiUrl` URI pointing to the Ping Payments API.
-   Create a PingPaymentsApiClient using the HttpClient.
-   Use the Create endpoint: `Merchants.V1.Create`.

Create() returns an object representing the merchant you created if all parameters are correct.
Create() returns an error object if one or more parameters are invalid.

```c#
new PingPaymentsApiClient(httpClient).Merchants.V1.Create(merchantRequest);
```

| Parameter               | Type                    | Containing                                            | Description                          |
| ----------------------- | ----------------------- | ----------------------------------------------------- | ------------------------------------ |
| `createMerchantRequest` | `CreateMerchantRequest` | merchant_name:`string`<br>organisation:`Organization` | A `CreateMerchantRequest` object     |

## Response Type

```c#
GuidResponse response = await new PingPaymentsApiClient(httpClient).Merchants.V1.Create(merchantRequest);
var status = response.StatusCode;
```

### 200

A successful call. A new merchant was created. `Create()` returned the new merchant object.

Example:

```json
{
  "name": "Merchant",
  "organization": {
    "country": "SE",
    "se_organization_number": "5555555555"
  }
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
// using merchants = PingPayments.PaymentsApi.Merchants;

var apiUrl = Configuration["PingPayments:ApiUrl"];
var tenantId = Configuration["PingPayments:TenantId"];
var httpClient = new HttpClient().ConfigurePingPaymentsClient(apiUrl, tenantId);
var api = new PingPaymentsApiClient(httpClient);

var merchantRequest = new merchants.Create.V1.CreateMerchantRequest
{
    Name = "test merchant AB",
    Organization = new merchants.Shared.V1.Organization
    {
        Country = "SE",
        SeOrganizationNumber = "1234567890"
    }
};

var request = new createMerchantRequest(merchantRequest);

GuidResponse response = await new PingPaymentsApiClient(httpClient).Merchants.V1.Create(request);

if (response.IsSuccessful) {
  var body = result.Body;
  // use guid id in body
}

if (response.IsFailure) {
  var body = result.Body;
  // use error message in body
}
```

# Get Specific Merchant

Get a specific merchant connected to a tenant.

Using `Get()`: 

-   Create a HttpClient with a `tenantId` GUID and an `apiUrl` URI pointing to the Ping Payments API.
-   Create a PingPaymentsApiClient using the HttpClient.
-   Use the Get endpoint: `Merchants.V1.Get`.

Get() needs a `merchantId` as a parameter.
Get() returns a merchant object containing a merchant if the tenantId and merchantId are valid and connected.
Get() returns an error object if the tenantId or the merchantId is invalid.

```c#
new PingPaymentsApiClient(httpClient).Merchants.V1.Get(merchantId);
```

| Parameter     | Type   | Description                                       |
| ------------- | ------ | ------------------------------------------------- |
| `merchant_id` | `Guid` | A Guid containing the ID of a specific merchant |

## Response Type

```c#
MerchantResponse response = await new PingPaymentsApiClient(httpClient).Merchants.V1.Get(merchantId);
var status = response.StatusCode;
```

### 200

A successful call. `Get` returned a merchant object matching the `tenantId` and `merchant_Id`.

Example:

```json
{
  "email": "contact@merchant.com",
  "id": "55555555-5555-5555-5555-555555555555",
  "name": "Merchant",
  "organization": {
    "country": "SE",
    "se_organization_number": "5555555555"
  },
  "phone_number": "0705555555"
}
```

### 403

API error. The merchant endpoint returned an error message.

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

Search error. `Get()` couldn't match the combined `tenantId` and `merchantId`.

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

GuidResponse response = await new PingPaymentsApiClient(httpClient).Merchants.V1.Get(merchantId);

if (response.IsSuccessful) {
  var body = result.Body;
  // use guid id in body
}

if (response.IsFailure) {
  var body = result.Body;
  // use error message in body
}
```
