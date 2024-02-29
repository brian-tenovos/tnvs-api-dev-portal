## Uploading Assets into Tenovos

### Requirements

The sequence diagram below shows the typical process to create a new asset within Tenovos using the REST API.  Creation of an Asset using the API requires that you know
the UUID's for the metadata and security templates you wish to use on the asset.  It also requires that you know the metadata attribute types (vocabulary, tables, etc.) and obtain the 
UUID information for each attribute and vocabulary value that you wish to add to the asset.

### Constraints

**Note:** The code examples provided below are specific to uploading files that are larger than 5 GB in size.  For files less than 5 GB in size, the standard upload process should be used.  Multi-part upload requires that an individual file be 5 MB at a minimum.  

When uploading files using multi-part upload, the **maximum part size is 100 MB** and is not configurable.

### Code Dependencies

- Code examples are written in Javascript using Node.js 16.
- Node Package Manager (NPM) modules are used and will need to be installed, including:
    - Lodash
    - Node Fetch

Using npm:
```

npm i -g lodash
npm i -g node-fetch

```

### Process
The process depicted below includes the steps necessary to make additional calls for getting metadata and security templates, as well as metadata attributes and values in the event that this information is not already known when performing an upload.

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

##### **`config/create-asset.js`**
```javascript
const MAX_PART_SIZE = 1024 * 1024 * 100; // 100 MBs
const CREATE_ASSET = {
  MAX_PART_SIZE,
};

module.exports = {
  CREATE_ASSET,
};
```
---
Example data of an asset used to create a request for multi-part upload.  

This is example data that is required by the API, with an additional “filePath” field. “filePath” is used to extract filesize for the first step and to upload the file in the second step.  If you do not have the UUID's for metadata template, security template, and the metadata definitions (attributes), additional calls would be made to get this information as shown in the above sequence diagram.
##### **`data/asset.js`**
```javascript
const createAssetData = {
  "metadataTemplateId": "930247ab-df91-4e9e-bc18-8f1aec179bf2",
  "metadata": [
    {
      "metadataDefinitionId": "e7664ae5-aca8-46a0-8e2e-f907a6f945d9",
      "metadataDefinitionValue": "Testing 7 GB File via Rest API"
    },
    {
      "metadataDefinitionId": "b96a4d1c-9bca-4b18-95bb-3004e386941e"
    },
    {
      "metadataDefinitionId": "a8323fc9-5a9e-40d4-9c3b-02d5157f5b80",
      "metadataDefinitionValue": {
        "valueId": "845fa7b1-19b1-4897-812b-546da15fb6db"
      }
    },
    {
      "metadataDefinitionId": "4d5415a6-87a9-431c-8eb1-93170bb5407a"
    }
  ],
  "securityTemplateIds": [
    "42b5dc04-79af-4911-93aa-96d73b1c2f75"
  ],
  "filename": "filename.mp4",
  "filePath": "<file-path>/filename.mp4"
};

module.exports = {
  createAssetData,
}
```
---
This is entry point for this example. This helper invokes other services and utility functions to create an asset.  This is a sample file to combine all 3 steps of upload file plus authentication.
##### **`helper/create-asset-helper.js`**
```javascript
const { createAssetService } = require('../service/create-asset');
const { authenticate } = require('../service/authentication');
const { createAssetData } = require('../data/asset')

/**
 * Helper function to invoke create-asset example
 * 1. it authenticates user
 * 2. initiates create-asset flow
 * @returns {Promise<*>}
 */
async function createAssetHelper() {
  const endpointUrl = process.env.BASE_URL;
  const apiKey = process.env.API_KEY;
  const clientId = process.env.CLIENT_ID;
  const username = process.env.USER_NAME;
  const password = process.env.PASSWORD;
  const authenticateRequestParams = {
    endpointUrl,
    apiKey,
    clientId,
    username,
    password,
  }
  const authResponse = await authenticate(authenticateRequestParams);
  const {
    authorization,
    accessToken,
  } = authResponse.session;
  const assetRequestParams = {
    endpointUrl,
    apiKey,
    authorization,
    accessToken,
    asset: createAssetData,
  };
  const response = await createAssetService(assetRequestParams);
  console.log(response);
  return response;
}

createAssetHelper()
  .then(console.log)
  .catch(console.error);
```
---
The below example takes parameters (asset object and auth tokens) and follows the create asset flow.
- Initiate Upload (create asset object)
- Upload file to S3 (multipart upload request).
- Invokes upload complete API at the end (marks asset-upload compete)

**Important Notes:**
- MAX_PART_SIZE/PART_SIZE at the moment 100MBs is only supported part size. It is not configurable.
- When file size is more than 5GBs then a multipart request is required.
- If file size is less than 5MBs then a simple request (without multipart) is required.
- If file size is between 5MBs and 5GBs then either a simple upload or multipart request will work.
- If there is a filesize parameter available, the API will return a multipart response (request will be treated as multipart).
- If the request body does not contain a filesize parameter then it will return single response (not multipart).

##### **`service/create-asset.js`**
```javascript
const fs = require('fs/promises');
const HttpUtil = require('../util/http');
const { CREATE_ASSET } = require('../config/create-asset')
const initiateUpload = async (request) => {
  const {
    endpointUrl,
    apiKey,
    authorization,
    accessToken,
    asset,
  } = request;
  const {
    metadataTemplateId,
    securityTemplateIds,
    metadata,
    filename,
    filesize,
  } = asset;
  const createAssetRequestBody = {
    metadataTemplateId,
    securityTemplateIds,
    metadata,
    filename,
    filesize,
  }
  const httpRequest = {
    method: 'POST',
    url: `${endpointUrl}/asset`,
    headers: {
      Authorization: authorization,
      AccessToken: accessToken,
      Accept: 'application/json',
      'Content-Type': 'application/json',
      'x-api-key': apiKey,
    },
    body: createAssetRequestBody,
  };
  console.log(httpRequest);
  const response = await HttpUtil.sendHttpRequest(httpRequest)
  console.log(response);
  return  response.body;
}
/**
 * It is second step of create-asset flow i.e. upload file-parts to S3
 * @param {Array<S3Part>} s3Parts
 * @param {FileHandle} fileHandle
 * @param {Number} bufferSize number of bytes to read for every multipart request. This is 100MBs at the moment.
 * @returns {Promise<Array<{etag:String, partNumber:Number}>>}
 */
const uploadAssetFile = async (s3Parts, fileHandle, bufferSize) => {
  const uploadParts = [];
  const buffer = Buffer.alloc(bufferSize)
  for (let index = 0; index < s3Parts.length; index++) {
    const {
      url,
      partNumber,
    } = s3Parts[index];
    const filePartStart = (partNumber - 1) * bufferSize;
    const fileBinary = await fileHandle.read(buffer, 0, bufferSize, filePartStart);
    const uploadFileParams = {
      method: 'PUT',
      body: fileBinary.buffer.buffer.slice(0, fileBinary.bytesRead),
    };
    const s3Response = await fetch(url, uploadFileParams);
    const etag = s3Response.headers.get('etag');
    uploadParts.push({
      etag,
      partNumber,
    })
  }
  console.log({
    uploadParts,
  });
  return uploadParts;
};
/**
 * It is 3rd step of create-asset flow,
 * it marks uploading of the file complete,
 * and it must be called after file upload is complete
 * @param {Object} assetCompleteRequest
 * @returns {Promise<*>}
 */
const sendAssetCompleteRequest = async (assetCompleteRequest) => {
  const {
    endpointUrl,
    authorization,
    accessToken,
    apiKey,
    assetCompleteBody,
  } =  assetCompleteRequest;
  const completeAssetHttpRequest = {
    method: 'POST',
    url: `${endpointUrl}/asset/complete`,
    headers: {
      Authorization: authorization,
      AccessToken: accessToken,
      Accept: 'application/json',
      'Content-Type': 'application/json',
      'x-api-key': apiKey,
    },
    body: assetCompleteBody,
  };
  const completeResponse = await HttpUtil.sendHttpRequest(completeAssetHttpRequest);
  console.log(completeResponse);
  return completeResponse;
};
/**
 * Creates an asset in 3 main steps:
 * 1. initiate create request,
 * 2. upload file
 * 3. marks upload complete
 * @param {CreateAssetRequest} request
 * @returns {Promise<*>}
 */
const createAssetService = async (request) => {
  const {
    endpointUrl,
    apiKey,
    authorization,
    accessToken,
    asset,
  } = request;
  const {
    filePath,
    filename,
  } = asset;
  const fileHandle = await fs.open(filePath, 'r');
  const stats = await fileHandle.stat();
  const filesize = stats.size;
  request.asset.filesize = filesize;
  const createAssetResponseBody = await initiateUpload(request);
  const s3Parts = createAssetResponseBody.urls;
  const {
    fileId,
    uploadId,
  } = createAssetResponseBody;
  const bufferSize = stats.size > CREATE_ASSET.MAX_PART_SIZE ? CREATE_ASSET.MAX_PART_SIZE : stats.size;
  const uploadParts = await uploadAssetFile(s3Parts, fileHandle, bufferSize);
  const assetCompleteBody = {
    filename,
    fileId,
    filesize,
    uploadId,
    parts: uploadParts,
    action: 'complete',
  }
  const assetCompleteRequest = {
    endpointUrl,
    apiKey,
    authorization,
    accessToken,
    assetCompleteBody,
  }
  return sendAssetCompleteRequest(assetCompleteRequest)
}
module.exports = {
  createAssetService,
}
```