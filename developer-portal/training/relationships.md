## Creating Asset Relationships in Tenovos
This source file includes a function createRelationship() that performs the following steps:
- Prepares an authorized HTTP request to create Asset relationships between a Primary Asset and one or more Secondary Assets.
- Submits the API request and receives a response.
- Extracts the create relationship result from the response.
- Returns the create relationship result.

### Requirements

**Primary Asset ID's:** The Identifier for the asset which is to be the primary (parent) is the relationship is referred to as the Primary Asset ID

**Secondary Asset ID's:** The Identifier(s) for the assets which are to be related to the Primary/Parent asset. These are the assets which will be children, derivatives or placed graphics.

### Constraints
- The user account used to obtain the auth token must have access to the Metadata Template applied to the asset.  Access Templates when applied may revoke this.
- The user account used to obtain the auth token must be part of a user group that has the ***Apply Security Template*** permission on the Security Template being used.
- Valid relationship types are: ***"child"***, ***"derivative"***, and ***"placed_graphic"***.  **These values are case sensitive**


### Process

The sequence diagram below shows the typical process to search for assets using keywords and/or filters within Tenovos using the REST API.

```mermaid
sequenceDiagram
    Customer->>+auth/token: Get Tokens
    auth/token-->>-Customer: Return Tokens
    Customer->>+/asset/{primaryId}/link: Create Relationships
    /asset/{primaryId}/link-->>-Customer: Return Results
```
***

### Code Example

The below example assumes use of the HTTP Utility and that you have a valid token returned from the authentication service, as described in [Getting Started with Authentication and HTTP Utility](authenticating.md)

#### **`service/relationship.js`**
```javascript
    const HttpUtil = require('../util/http');
    const createRelationship = async (request) => {
    // Extract Parameters from Request
    const { endpointUrl, apiKey, authorization, primaryId, secondaryIds } = request;
    // Validate Parameters
    if (!primaryId) {
        throw new Error('Missing primaryId');
    }
    if (!(Array.isArray(secondaryIds) && secondaryIds.length)) {
        throw new Error('secondaryIds Array must have at least 1 entry');
    }
    for (let i = 0; i < secondaryIds.length; i += 1) {
        const secondaryId = secondaryIds[i];
        const { id, linkType } = secondaryId;
        if (!(id && linkType)) {
            throw new Error(`Missing id or linkType in secondaryId ${i}`);
        }
    }
    // Prepare Request Body
    const body = {
        secondaryIds,
    };
    // Get Body Content Length
    const bodyLength = Buffer.byteLength(JSON.stringify(body));
    // Prepare HTTP Request
    const httpRequest = {
    method: 'POST',
    url: `${endpointUrl}/asset/${primaryId}/link`,
    headers: {
        Accept: 'application/json',
        Authorization: authorization,
        'Content-Length': bodyLength,
        'Content-Type': 'application/json',
        'x-api-key': apiKey,
    },
    body,
};

 // Send HTTP Request, Get HTTP Result
 const result = await HttpUtil.sendHttpRequest(httpRequest);
 // Extract Search Result from HTTP Result
 const createResult = JSON.parse(result.body);
 // Return Create Relationship Result
 return createResult;
};

module.exports = {
 createRelationship,
};

```

### Create Asset Relationship Response
Multiple operations to create Asset Relationships can be submitted in the same request.  For this reason, while a request may have been processed successfully, the individual operations will succeed or fail independently.  The status of the individual operations are itemized in the response.  The list of returned statuses corresponds to the list of operations submitted in the request.

#### Success
The following example shows a response body where Asset Relationships were successfully created between a primary Asset and two secondary Assets.  **HTTP Status Code** on this Response will be **200**

```json
[
   {
     "status": "success",
     "message": "Relationship created successfully",
     "relationshipId": "7f3d3d79-effc-4ecc-9259-74a99f036896-92aa1ad3-e6d7-4676-a962-c70f277a28b8"
   },
   {
     "status": "success",
     "message": "Relationship created successfully",
     "relationshipId": "7f3d3d79-effc-4ecc-9259-74a99f036896-3919382b-7b5b-4b6f-93af-8d36247cb68e"
   }
 ]

```

#### Error - Relationship Already Exists
The following example shows a response body where an Asset Relationship failed to be created between a primary Asset and secondary Asset due to an Asset Relationship already existing with the same link type.  Duplicate Asset Relationships are not permitted.  **HTTP Status Code** on this Response will be **200**

```json
[
   {
     "status": "error",
     "message": "Relationship exists"
   }
 ]
```

#### Error - Circular Dependency

The following example shows a response body where an Asset Relationship failed to be created between a primary Asset and secondary Asset due to the potential to create  a circular Relationship loop. A circular Relationship loop is where Asset Relationships of the same link type are created for a series of Assets in such a way that the Relationships lead back to the primary Asset where the new Relationship is being created.  Currently, circular Relationship loops are not permitted.  **HTTP Status Code** on this Response will be **200**

**Example:** Asset A &rarr; Asset B &rarr; Asset C &rarr; Asset A

```json
[
   {
     "status": "error",
     "message": "Circular dependency"
   }
 ]
```

#### Error - Generic
The following example shows a response body where an error occurred during the Create Asset Relationship processing, and a single, generic message was returned, instead of a message specific to a Relationship being created.  **HTTP Status Code** on this Response will be **400**

```json
{
   "message": "Failed to create Relationship"
}

```