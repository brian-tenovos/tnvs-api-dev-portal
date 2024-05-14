# Webhook Authentication
---


This method may not be supported by all OAuth providers, and in general `clientSecretBasic` should be preferred.

<!-- ```json
{
    "grantType": "client_credentials",
    "clientId":  "nbhKdCwuiIotssPjKXc6cjhpJFVy7JbF",
    "tokenUrl": "https://auth.tenovos.io/oauth/token",
    "authMethod": "clientSecretBasic",
    "clientSecret":"ZtjtpXP7p5SdUwNYu1JT9xfQobecM0i5UGOu_F5BU6DdAOPFLZDz7r0xObogttPG",
    "extraParams": {"audience": "http://your.auth.server...."}
} -->

``````

```mermaid
    participant Tenovos
    participant Customer
    Tenovos->>Cusotmer: Authenticate with Application Credentials
    Customer-->>Customer: Validate Credentials
    Customer->>Tenovos: Access Token
    Tenovos->>Cusotmer: Send Webhook Request
    Customer->>Tenovos: Response (200)
```

