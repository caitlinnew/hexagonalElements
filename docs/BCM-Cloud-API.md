---
stoplight-id: ldyqhqagd8el7
---

# BCM/Cloud API
Cloud API empowers **partners** to manage BCM configurations and BCM notifications.

<!-- theme: info -->

> #### Please note
>
> No rider login required for BCM/Cloud API.

## Getting started

### Prerequisites
In order to use BCM/Cloud API, please get in touch with your responsible Bosch contact first to request an OAuth client, which represents your requesting client application.

### How does it work?

#### Client authentication
In order to make successful API requests to BCM/Cloud API you need a valid OpenID Connect Access Token (JWT), which must be obtained through our OAuth server using the `Client Credentials OAuth 2 Flow`. Please note, there is no rider login needed. Refer to security scheme `OAuth 2.0 (Client)` on each API for details.

#### Configuration steps
- The BCM will be activated for remote configuration by default. This requires the software version >= `BES3_12`.
- As soon as the BCM is activated, it will register itself in the Bosch cloud with next power-on of the eBike. You can start configuring (enable/disable) features immediately.
- As last step subscribe to available event types to receive eBike data notifications via webhooks.
#### Runtime behaviour
- As soon as a BCM feature is enabled it sends data to the Bosch cloud. Cloud API pushes it to a subscribed partner webhook listener. The following picture shows the simplified data flow:
  ```mermaid
      flowchart LR      
      eb["Smart System eBike #128690;"] --> BCM["Bosch ConnectModule"];          
      BCM --> api["Cloud API #9729;"];
      api --> partner[Partner webhook listener];
  ```

## BCM configurations
Enabling and disabling BCM features is provided by BCM configurations to **partners**. 

Two features are supported:
  - Location Tracking
  - Bike Status

For each feature you can select in addition a predefinied profile (`DEFAULT` or `FLEET`) which has an impact on the collection and transmission rate of the data.

> #### Please note
>
> A BCM is only remotely configurable if it was flashed to software version >= `BES3_12` before.

## BCM notifications
Cloud API uses webhooks for BCM event notifications. Webhooks are push API calls, using the HTTP `POST` method, that notifies your service an event has happened for your managed BCMs.

#### How to use it?
First provide an internet facing webhook listener, then [create a webhook](../openapi/notification.v1.yaml/paths/~1webhooks/post) to subscribe to one or more events.\
In order for your app to be able to receive event notifications, it must:
* Provide one or more internet facing HTTP `POST` endpoint(s) to receive notifications of various [event types](#event-types) in JSON format.
* Verify that the event notification came from Bosch and was not altered or corrupted during transmission. See [Message signature](#message-signature).
* Respond with a HTTP `200` status code.

#### How to create a Webhook?
You can subscribe to one or more types of events, but each event type can be only subscribed once. The subscription is only created when a [test notification](../openapi/notification.v1.yaml/components/schemas/WebhookTest) has been sent successfully to the specified URL. Static authentication credentials can be added optionally.

In case of connection failures, Cloud API performs 5 retries (after 1, 2, 5, 180 and 480 seconds) for the current event. The event is skipped if the total of retries is reached without success.

#### Message signature
We sign the request payload of each event notification and send it over HTTPS (TLS) only. The request headers contain the signature and additional information that you have to use to validate the signature.

##### HTTP header
| Header Name | Description |
|:------|:------------|
| X-Bosch-Sig | The Bosch generated asymmetric signature. |
| X-Bosch-Sig-Algo | The algorithm that Bosch used to generate the signature and that you can use to verify the signature. (e.g. SHA-256 with RSA encryption) |
| X-Bosch-Cert-Url | The X509 public key certificate. Download the certificate from this URL and use it to verify the signature. |

##### Signature validation
The following example shows, how to verify the signature using OpenSSL:
- tls-pub.pem - the downloaded public X509 certificate
- sig - the signature from the HTTP request header X-Bosch-Sig
- msg - the notification message from the HTTP request body
```
openssl dgst -sha256 -verify tls-pub.pem -signature sig msg
Verified OK
```

#### Event types
Partners can subscribe to the following type of events:
| Event Name | Event Model | Prerequsites |
|:------|:------------|:------------|
| BIKE_STATUS | [BikeStatus](../openapi/notification.v1.yaml/components/schemas/BikeStatus) | Feature is enabled on BCM |
| LOCATION_TRACKING | [LocationTracking](../openapi/notification.v1.yaml/components/schemas/LocationTracking) | Feature is enabled on BCM |
