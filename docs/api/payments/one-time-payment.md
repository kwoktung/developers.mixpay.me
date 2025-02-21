# Using Short Link

MixPay API for creating a one-time payment.

## POST /one_time_payment

This payment method needs to request the API to generate a code before it can be used, and is suitable for scenarios where only one payment is allowed.


> This is the recommended way to use in production. Please checkout the [Security Guidelines](/guides/security-guidelines).

### Endpoint URL

```
https://api.mixpay.me/v1/one_time_payment
```

### Parameters

|  Param | Optional | Type | Description |
| --- | --- | --- | --- |
| `payeeId` | <span class="required">*required</span> | String | Account ID for receiving crypto, pls see [Five types of account](/guides/getting-started#account) and [How to get payeeId](/guides/getting-started#payee-id). |
| `quoteAmount` | <span class="required">*required</span> | Numeric | Corresponding to the amount of `quoteAssetId`. For example, the current commodity value is 10 USDT |
| `quoteAssetId` | <span class="required">*required</span> | String | `assetId` of quote cryptocurrency. You can see the supported asset id in [Quote Assets](/api/assets/quote-assets).|
| `settlementAssetId` | <span class="required">*required</span> | String | `assetId` of settlement cryptocurrency. Settlement assets you prefer. But you need to pay attention to the `strictMode` field. For more options, see [here](/api/assets/settlement-assets). |
| `strictMode` | optional | Boolean | Default `false`. `true` means that the payment must be settled strictly according to the currency set by settlementAssetId. See [here](/api/strict-mode) for more details.|
| `paymentAssetId` | optional | String | `assetId` of payment cryptocurrency. You can see the supported asset id in [Payment Assets](/api/assets/payment-assets). |
| `remark` | optional | String | maximum 50. Payment remark viewable by the payee. |
| `traceId` | optional | String |  UUID, used to prevent double payment and checking the payment result. It is also used to set the `code` you will get.|
| `orderId` | <span class="required">*required</span> if no `traceId` | String | Unique in your system. String lengths **between 6-36** must be letters, numbers, dashes and underscores and NOT space. `orderId` and `payeeId` make a payment unique. |
| `settlementMemo` | optional | String | maximum 200. A memo is similar to Mixin Snapshots, this parameter you can customize. |
| `returnTo` | optional | String | After successful payment, the URL page will want to redirect to. Useful when you are in a browser JavaScript environment. |
| `failedReturnTo` | optional | String | After payment failure, the URL page will want to redirect to. Useful when you are in a browser JavaScript environment. |
| `callbackUrl` | optional | String | After payment successfully, MixPay will issue a POST request to this URL on our server-side. For security reasons, URLs only support HTTPS and has to be [URL encoded](https://www.w3schools.com/tags/ref_urlencode.ASP). Please refer to [Payment Callback](/api/payments/payment-callback). |
| `callbackEvent` | optional | String | Send  `settle` or `pending` to subscribe more callback event. Please refer to [Payment Callback](/api/payments/payment-callback#callback-event).|
| `expiredTimestamp` | optional | int | Set a expired [timestamp](https://en.wikipedia.org/wiki/Unix_time). This value must be greater than 10s and less than 2880min. After this period, the payment result status field will be marked as `failed`, and the `failureReason` will be `Payment overtime`. If you are not setting this value, the payer can have unlimited time to complete this payment. |


### Example request - One-Time Payment

```bash
curl -i -X POST https://api.mixpay.me/v1/one_time_payment \
  -d "payeeId"="8e69e534-d0c4-3e04-8b61-37a73cd9e7d7" \
  -d "settlementAssetId"="c6d0c728-2624-429b-8e0d-d9d19b6592fa" \
  -d "quoteAssetId"="usd" \
  -d "quoteAmount"="10" \
  -d "isTemp"="1" 
```

```json
// title: Response
{
    "code": 0,
    "success": true,
    "message": "",
    "data": {
        "code": "525502dd-e552-4505-b114-32fe32ff23c2"
    },
    "timestampMs": 1645411665245

}
```

:::info
This interface response is a short code; you can access it by `https://mixpay.me/code/525502dd-e552-4505-b114-32fe32ff23c2`.
:::
