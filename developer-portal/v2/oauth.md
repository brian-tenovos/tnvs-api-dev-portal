---
showNextButton: false
showPrevButton: false
---

## Using OAuth 2.0 with Tenovos API

[OAuth 2.0](https://oauth.net/2/) is an authorization protocol that gives an API client limited access to user data on a web server. GitHub, Google, and Facebook APIs notably use it. OAuth relies on authentication scenarios called flows, which allow the resource owner (user) to share the protected content from the resource server without sharing their credentials. For that purpose, an OAuth 2.0 server issues access tokens that the client applications can use to access protected resources on behalf of the resource owner. For more information about OAuth 2.0, see oauth.net and [RFC 6749](https://tools.ietf.org/html/rfc6749).

:::info Backwards Compatability

Tenovos API v1.5 retains the same Authentication and Authorization flows from v1, and adds OAuth 2.0, making one or the either required, but not both.

:::

### Flows

Tenovos v1.5 and 2.x both implement the OAuth 2.0 authorization code flow.  The flows (also called grant types) are scenarios an API client performs to get an access token from the authorization server. OAuth 2.0 provides several flows suitable for different types of API clients:

- **Authorization code –** The most common flow, mostly used for server-side and mobile web applications. This flow is similar to how users sign up into a web application using their Facebook or Google account.
- **Implicit –** This flow requires the client to retrieve an access token directly. It is useful in cases when the user’s credentials cannot be stored in the client code because they can be easily accessed by the third party. It is suitable for web, desktop, and mobile applications that do not include any server component.
- **Resource owner password credentials (or just password) –** Requires logging in with a username and password. Since in that case the credentials will be a part of the request, this flow is suitable only for trusted clients (for example, official applications released by the API provider).
- **Client Credentials –** Intended for the server-to-server authentication, this flow describes an approach when the client application acts on its own behalf rather than on behalf of any individual user. In most scenarios, this flow provides the means to allow users specify their credentials in the client application, so it can access the resources under the client’s control

### Configure OAuth consent

:::info Coming Soon

Content regarding OAuth 2.0 configuration from Tenovos Admin is Coming Soon!

:::

### Scope Categories

:::info Coming Soon

Content regarding OAuth 2.0 configuration from Tenovos Admin is Coming Soon!

:::

### Creating Access Credentials

To authenticate end users and access user data in your app, you need to create one or more OAuth 2.0 Client IDs. A client ID is used to identify a single app to Tenovos's OAuth servers. If your app runs on multiple platforms, you must create a separate client ID for each platform.  Credentials are used to obtain an access token from Tenovos's authorization servers so your app can call Tenovos API's.  This section describes how to choose and set up the credentials your app needs.

### Application Types

#### Service accounts

A service account is used in an application that calls APIs on behalf of an application that does not access user information. This type of application needs to prove its own identity, but it does not need a user to authorize requests.  

#### Web applications

A web application is accessed by web browsers over a network.

- Applications that use client-side JavaScript to access Tenovos APIs must specify authorized JavaScript origins. The origins identify the domains from which your application can send API requests.

- Applications that access Tenovos APIs from a server (often using languages and frameworks like Node.js, Java, .NET, and Python) must specify authorized redirect URIs. The redirect URIs are the endpoints of your application server to which the OAuth 2.0 server can send responses.

### Rotating your client secrets

Client secrets or credentials should be treated with extreme care, because they allow anyone who has them to use your app's identity to gain access to user information. With the client secret rotation feature, you can add a new secret to your OAuth client configuration, migrate to the new secret while the old secret is still usable, and disable the old secret afterwards. This is useful when the client secret has been inadvertently disclosed or leaked. This also ensures good security practices by occasionally rotating your secrets without causing downtime of your app.

:::info Coming Soon

Client secret rotation feature from Tenovos Admin is Coming Soon!

:::

