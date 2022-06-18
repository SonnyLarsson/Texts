---
title: "Payments API"
excerpt: "A general description of the Ping Payments API C# SDK"
---

# Ping Payments API Documentation

## The PaymentsApiClient Class

The `PaymentsApiClient` class exposes endpoints that let you create and interact with instances or resources

| Parameter     | Type         | Description                                                                   |
| ------------- | ------------ | ----------------------------------------------------------------------------- |
| `httpClient`  | `HttpClient` | A HttpClient configured with the ConfigurePingPaymentsClient function         |

Using an endpoint in the PaymentsApi class:

-   Create a HttpClient with a `tenantId` GUID and an `apiUrl` URI pointing to the Ping Payments API.
-   Create a PingPaymentsApiClient using the HttpClient.
-   Use an appropriate endpoint through the PaymentsApi object, for example: `Merchants.V1.Create`.

Initializing the Ping Payments API:

```c#
// using PingPayments.PaymentsApi;

var apiUrl = Configuration["PingPayments:ApiUrl"];
var tenantId = Configuration["PingPayments:TenantId"];
var httpClient = new HttpClient().ConfigurePingPaymentsClient(apiUrl, tenantId);
var api = new PingPaymentsApiClient(httpClient);
```

## Calls to the Ping Payments API

Example using the merchant resource:

```c#
// using merchants = PingPayments.PaymentsApi.Merchants;

var createMerchant = new PingPaymentsApiClient(httpClient).Merchants.V1.Create;

var merchantRequest = new merchants.Create.V1.CreateMerchantRequest
{
    Name = "test merchant AB",
    Organization = new merchants.Shared.V1.Organization
    {
        Country = "SE",
        SeOrganizationNumber = "1234567890"
    }
};

Guid merchantId = await createMerchant(merchantRequest);
```

## API Resources

| Name          | Description                                                          |
| ------------- | -------------------------------------------------------------------- |
| merchant      | Gets all merchant methods from the `MerchantV1` class                |
| payment_order | Gets all payment order methods from the `PaymentOrderV1` class       |
| payment       | Gets all payment methods from the `PaymentsV1` class                 |
