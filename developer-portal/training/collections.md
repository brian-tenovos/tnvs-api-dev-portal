## Creating Collections
This source file makes use of a series of sample helper functions that completes the entire flow of getting data needed to create a collection:

These helper utilities can be found in the [Getting Started Exercise](./authenticating.md) or by accessing them directly:
- [API Configuration Helper](./authenticating.md#api-configuration-helper)
- [Collection Data Helper](./authenticating.md#collection-data-helper)
- [Create Collections Helper](./authenticating.md#create-collections-helper)
- [Asset Search Helper](./authenticating.md#asset-search-helper)
- [Metadata Vocabulary Helper](./authenticating.md#metadata-vocabulary-helper)
- [Metadata Template Helper](./authenticating.md#metadata-template-helper)
- [Security Template Helper](./authenticating.md#security-template-helper)

Each of these Helpers perform a specific function to assist in API calls such as creating collections where data (especially ID's) is needed that isn't accessible otherwise.

### Process

The sequence diagram below shows the typical process to create collections.
```mermaid
sequenceDiagram
    Customer->>+metadata/vocabulary: Get Tokens
    metadata/vocabulary-->>-Customer: Returns
    Customer->>+metadata/template: Look up Metadata Template
    metadata/template-->>-Customer: Returns
    Customer->>+security/template: Look up Security Template
    security/template-->>-Customer: Returns
    Customer->>+/collection: Create Collection
    /collection-->-Customer: Returns
```
***

#### **`service/collection.js`**

```javascript
const HttpUtil = require('../util/http');
const createCollection = async (request) => {
 // Extract Parameters from Request
 const { endpointUrl, apiKey, authorization, accessToken, collection } = request;
 const { collectionType } = collection;
 // Prepare Request Body
 const body = {
   collectionType,
   name: collection.name,
   metadataTemplateId: collection.metadataTemplateId,
   metadataDocument: collection.metadataDocument || '[]',
   securityTemplateIds: collection.securityTemplateIds,
   collectionDocument: collection.collectionDocument || [],
 };
 // Get Body Content Length
 const bodyLength = Buffer.byteLength(JSON.stringify(body));
 // Prepare HTTP Request
 const httpRequest = {
   method: 'POST',
   url: `${endpointUrl}/collection`,
   headers: {
     Accept: 'application/json',
     Authorization: authorization,
     accessToken,
     'Content-Length': bodyLength,
     'Content-Type': 'application/json',
     'x-api-key': apiKey,
   },
   body,
 };
 // Send HTTP Request, Get HTTP Result
 const result = await HttpUtil.sendHttpRequest(httpRequest);
 // Extract Collection from HTTP Result
 const newCollection = result.body;
 // Return Collection
 return newCollection;
};
module.exports = {
 createCollection,
};

```