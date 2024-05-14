# Webhook Authentication
---

Tenovos supports the following authenication types:

- `clientSecretBasic`
- `clientSecretPost`
- Open Authentication - message signature validation only

:::info Signature Validation
Regardless of authentication type used, Tenovos signs each message, and validation of the signature should be performed on every message as detailed in our [section on Validation](validation.md).

:::

## Using OAuth 2.0 Authentication

Tenovos supports autenticating Webhook requests using OAuth 2.0 `clientCredentials` grant type.  Tenovos requires a valid `clientId`, which is required for all authenication types.

Customers will need to provide the URL of their authorization server.  This is the URL from which Tenovos will get authorization tokens from the authenitcation service.

## Using clientSecretBasic

When using `clientSecretBasic`, an `Authorization` header will be added to the request with the value of the secret.

```
Authorization: Basic ZGVtbzpwQDU1dzByZA==
```

This method will authenticate to the OAuth server using an Authorization header in the HTTP request with a value for the client secret.

This is the most common means of authenication.

## Using clientSecretPost

When using `clientSecretPost` the secret will be added to the body in a parameter called `client_secret`.

This method will authenticate to the OAuth server by passing the client secret in a `client_secret` parameter in the body of the HTTP request.

This method may not be supported by all OAuth providers, and in general `clientSecretBasic` should be preferred.

```json
{
    "grantType": "client_credentials",
    "clientId":  "nbhKdCwuiIotssPjKXc6cjhpJFVy7JbF",
    "tokenUrl": "https://auth.tenovos.io/oauth/token",
    "authMethod": "clientSecretBasic",
    "clientSecret":"ZtjtpXP7p5SdUwNYu1JT9xfQobecM0i5UGOu_F5BU6DdAOPFLZDz7r0xObogttPG",
    "extraParams": {"audience": "http://your.auth.server...."}
}

``````

sequenceDiagram
    participant Tenovos
    participant Customer
    Tenovos->>Cusotmer: Authenticate with Application Credentials
    Customer-->>Customer: Validate Credentials
    Customer->>Tenovos: Access Token
    Tenovos->>Cusotmer: Send Webhook Request
    Customer->>Tenovos: Response (200)


