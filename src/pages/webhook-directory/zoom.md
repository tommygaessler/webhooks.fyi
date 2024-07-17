---
title: Zoom Webhook Specs, Events, and Examples
pageTitle: Zoom Webhooks Specs | Examples| How to integrate
description: Webhook specificactions for Zoom including Supported Events, Example Apps, Security Specs, and Documentations.
--- 

Zoom uses webhooks to notify third-party apps of events such as Meetings, Webinars, Recordings, User Activity, Billing, Chat Messages created, and Video SDK

{% table %}
---
* ## Specifications
* - **[HMAC Authentication](/security/hmac)**
  - **[Replay Prevention](/security/replay-prevention)**: ✅
  - **[Forward Compatibility](/ops-experience/versioning)**: ✅
  - **[Zero Downtime Rotation](/ops-experience/key-rotation)**: ❌
---
* ## Supported Events
* - **[Official Doc ↗](https://developers.zoom.us/docs/api/)**
---
* ## Security Headers
* - **Signature Header**: `x-zm-signature`
  - **Hash**: sha256
  - **Encode**: hex
  - **payload**: `v0:timestamp:request_body`
  - **Timestamp Header**: `x-zm-request-timestamp`
  - **Timestamp Format**: Unix Date
---
* ## Documentation
* - [Official Doc ↗](https://developers.zoom.us/docs/api/rest/webhook-reference/)
  - [IP Origins for whitelist ↗](https://developers.zoom.us/docs/api/rest/webhook-reference/#ip-addresses)
---
* ## SDKs and Sample Code
* - [NodeJS Sample ↗](https://github.com/zoom/webhook-sample)
{% /table %}

---

## Sample Validation

```js
const crypto = require('crypto')
const timeHeader = 'x-zm-request-timestamp'
const sigHeader = 'x-zm-signature'
const hashAlgo = 'sha256'
const encode = 'hex'
const hmacSecret = process.env.WEBHOOK_SECRET
app.post('/zoom-webhook', (req, res) => {
    //01: Validate replay prevention with 5 minute timeframe
    const requestTimestamp = req.headers[timeHeader] * 1000;
    const tolerance = Date.now() - (5 * 60 * 1000);
    if (requestTimestamp < tolerance) {
        res.status(403).send('Request expired')
    }else{
        //02: Validate signature
        const message = `v0:${req.headers[timeHeader]}:${JSON.stringify(req.body)}`
        const digest = crypto.createHmac(hashAlgo, hmacSecret).update(message).digest(encode)
        if (request.headers[sigHeader] !== digest) {
            res.status(401).send('Request unauthorized')
        }else{
            //03: Process message
            res.json({ message: "Success" })
        }
    }
})
```
