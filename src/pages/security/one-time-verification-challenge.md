---
title: One time verification challenge
description: Learn why and how webhook providers like Twitter, Okta, and Microsoft implement a verification/challenge request before sending webhook messages
--- 

{% table %}
---
* **Complexity**
* - Medium
---
* **Pros**
* - Validates if the webhook consumer controls a destination before sending webhook notifications
  - Can be easily combined with other webhook authentication controls
---
* **Caveats**
* - Additional implementation complexity
---
* **Examples**
* - [Adobe Sign](https://helpx.adobe.com/sign/using/adobe-sign-webhooks-api.html#VoI)
  - [Airship](https://docs.airship.com/platform/sms/inbound-message-handling/#registering-your-sms-webhook)
  - [Microsoft OneDrive](https://docs.microsoft.com/en-us/onedrive/developer/rest-api/concepts/webhook-receiver-validation-request?view=odsp-graph-online)
  - [Okta](https://developer.okta.com/docs/concepts/event-hooks/#one-time-verification-request)
  - [Smartsheet](https://smartsheet.redoc.ly/tag/webhooksDescription#section/Creating-a-Webhook)
  - [Twitter](https://developer.twitter.com/en/docs/twitter-api/enterprise/account-activity-api/guides/securing-webhooks)
  - [Zoom](https://developers.zoom.us/docs/api/rest/webhook-reference/#validate-your-webhook-endpoint)
{% /table %}
---

While all providers implement authentication and authorization to set up webhooks, that doesn't confirm if the webhook consumer controls the code receiving webhook notifications.

To mitigate this risk, webhook providers like [Twitter](https://developer.twitter.com/en/docs/twitter-api/enterprise/account-activity-api/guides/securing-webhooks), [Okta](https://developer.okta.com/docs/concepts/event-hooks/#one-time-verification-request), and [Microsoft OneDrive](https://docs.microsoft.com/en-us/onedrive/developer/rest-api/concepts/webhook-receiver-validation-request?view=odsp-graph-online) implement a verification/challenge request.

The challenge request is sent during setup and contains a random string that should be relayed back as part of the response:

```curl
GET https://listener.com/my-webhook?validationToken=randomString
Content-Length: 0

HTTP/1.1 200 OK
Content-Type: text/plain

randomString
```

Webhook notifications are not sent until the challenge response is successful.

{% callout title="Additional security concerns for webhook providers" type="warning" %}
Webhook integrations seem simple to secure at the surface. However, webhook URLs can be explored by malicious actors — acting as legitimate webhook consumers — to carry out Distributed Denial of Service (DDoS), Server Site Request Forgery (SSRF), and other attacks to the provider infrastructure.

The [best practices for webhook providers](/best-practices/webhook-providers#implement-security-on-egress-communication) provides guidelines for mitigating these risks in webhook communications. 
{% /callout %}
