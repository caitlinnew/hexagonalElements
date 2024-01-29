---
stoplight-id: mcpuz10tzeuxl
---

# Data/Cloud API
Cloud API empowers **partners** to upload bike model information like bike specifications and bike images to be served by the Flow app when users are connecting to a specific bike.

<!-- theme: info -->

> #### Please note
>
> No rider login required for Data/Cloud API.

## Getting started

### Prerequisites
In order to use Data/Cloud API, please get in touch with your responsible Bosch contact first to request an OAuth client, which represents your requesting client application.

### How does it work?

#### Client authentication
In order to make successful API requests to Data/Cloud API you need a valid OpenID Connect Access Token (JWT), which must be obtained through our OAuth server using the `Client Credentials OAuth 2 Flow`. Please note, there is no rider login needed. Refer to security scheme `OAuth 2.0 (Client)` on each API for details.
