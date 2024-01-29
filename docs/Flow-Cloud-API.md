---
stoplight-id: y99tjhwo0azcc
---

# Flow/Cloud API
Cloud API provides **riders** read-only access to their data, collected via the eBike Flow app.
<!-- theme: info -->

> #### Please note
>
> Flow/Cloud API requires a rider login via [SingleKey ID](https://singlekey-id.com/de/home/).

## eBike profile
With eBike profile, riders gain insights on their eBike data like detailed specifications, configurations and changes.

## Activity records
Activity records allows riders to query past activities which have been recorded using the Flow app. It provides access to activity details as well as a comprehensive activity summary after a ride is successfully finished.

## Getting started

### Prerequisites
There are two prerequsites in order to use Flow/Cloud API:
1. **Partners** need to get in touch with their responsible Bosch contact first to request an OAuth client, which represents your requesting client application. Note that the authorization code flow requires at least one client to be configured with a whitelisted redirect-URL.
2. **Riders** need to install the eBike Flow app on their smartphone and follow the registration and pairing process using a [SingleKey ID](https://singlekey-id.com/de/home/). Need help? Please follow this [documentation](https://www.bosch-ebike.com/en/products/ebike-flow-app).

### How does it work?
- The eBike Flow app collects data from a smart system eBike and sends it to the Bosch cloud. Cloud API enables riders to access their data, for example via a partner app or website. The following picture shows the simplified data flow:

  ```mermaid
  flowchart LR      
    eb["Smart System eBike #128690;"] --> app["Flow app #128241;"];
    app --> api["Cloud API #9729;"];
    api --> partner[Partner custom solution];
  ```

- In order to make successful API requests riders need a valid OpenID Connect Access Token (JWT), which must be obtained through SingleKey ID using the `Authorization Code OAuth 2 Flow`. Please refer to security scheme `OAuth 2.0 (SingleKeyID_User)` for details.
- Riders are asked once for consent to grant a partner client application access to their eBike data.
