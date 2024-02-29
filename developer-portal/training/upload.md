## Uploading Assets into Tenovos

### Requirements

The sequence diagram below shows the typical process to create a new asset within Tenovos using the REST API.  Creation of an Asset using the API requires that you know
the UUID's for the metadata and security templates you wish to use on the asset.  It also requires that you know the metadata attribute types (vocabulary, tables, etc.) and obtain the 
UUID information for each attribute and vocabulary value that you wish to add to the asset.

### Constraints

### Process

```mermaid
sequenceDiagram
    participant Customer
    Note right of Customer: Username, Password, ClientId, API Key
    Customer->>+auth/token: Get Tokens
    auth/token-->>-Customer: Return Tokens
    Customer->>+metadata/template: Request Metadata Templates
    metadata/template-->>-Customer: Return all Templates and Attribute ID's
    Customer->>+metadata/vocabulary: Get ID's and Values for Vocabulary
    metadata/vocabulary-->>-Customer: Return Vocabulary
    Customer->>+security/template: Get all Security Templates
    security/template-->>-Customer: Return templates and ID's
    Customer->>+/asset: Pass Security Template(s), Metadata Template, and attribute ID's/Values
    /asset-->>-Customer: Return Asset Creation Status
```

### Code Example