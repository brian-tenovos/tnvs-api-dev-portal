## Creating Relationships in Bulk
This source file includes a series of sample helper functions that completes the following scenario:
- Start with an Array of Relationship Records.
    - We will assume that a preprocessor has already read the records from a file into an Array.  In this example, we have stored the Array in a file for use.
- Authenticate and Get Authorization Token.
- Aggregate the Array of Relationship Records, referenced by Association ID, into a series of Create Relationship requests.
- Extract the Association IDs from the Array of Relationship Records and retrieve the corresponding Asset Object IDs using the Asset Keyword Search REST API.
    - An Association ID to Object ID map will be cached for later reference.
- Iterate over the Array of Create Relationship Requests to create Asset Relationships using the Create Relationship REST API.
    - Before creation, the Primary and Secondary Association IDs will be converted into Primary and Secondary Object IDs.
- Any errors during processing will be logged to the console.

### Application Configurations
This source file stores the configurations needed to run the example application.

#### Requirements
- Replace the following variables in < > with real values:
    - endpointUrl
    - apiKey
    - clientId
    - username
    - password
    - association_id - The Association ID Search Field Name
- Credentials and API Key are stored in a file here for simplicity, but should normally be stored in a secure location, such as in a Secrets Manager.

#### **`config/config.js`**

```javascript
    const getApiConfig = () => {
        const endpointUrl = '<endpointUrl>';
        const apiKey = '<apiKey>';
        const config = {
            endpointUrl,
            piKey,
        };
        return config;
    };
    const getCredentials = () => {
        const clientId = '<clientId>';
        const username = '<username>';
        const password = '<password>';
        const credentials = {
            clientId,
            username,
            password,
        };
        return credentials;
    };
    const metadata = {
        ASSOCIATION_ID: {
        RECORD_NAMES: [
            'primaryAssociationId',
            'secondaryAssociationId',
        ],
        ATTRIBUTE_NAME: '<association_id>',
        },
    };
    module.exports = {
        getApiConfig,
        getCredentials,
        metadata,
    };
```

### Sample Relationship Data
This source file stores sample Relationship data for simplicity.  Normally, this data would be read from a spreadsheet, file, or other repository.

In this sample data, we point out potential reasons for errors, such as duplicate relationships, circular dependencies, invalid IDs, or invalid link types.

#### **`data/relationship.js`**

```javascript
const createRelationshipRecords = [
 {
    primaryAssociationId: 'f47a3d97-eeed-47af-aed2-48c37dd8cbf9-1',
    secondaryAssociationId: '7f3d3d79-effc-4ecc-9259-74a99f0331da-2',
    linkType: 'placed_graphic',
    },
    {
    primaryAssociationId: 'f47a3d97-eeed-47af-aed2-48c37dd8cbf9-1',
    secondaryAssociationId: '92aa1ad3-e6d7-4676-a962-c70f277a28b8-2',
    linkType: 'child',
    },
    {
    // Circular Relationship
    primaryAssociationId: '92aa1ad3-e6d7-4676-a962-c70f277a28b8-2',
    secondaryAssociationId: 'f47a3d97-eeed-47af-aed2-48c37dd8cbf9-1',
    linkType: 'child',
    },
    {
    // Duplicate Relationship
    primaryAssociationId: 'd38c9c7a-efeb-400d-8b3f-222eb2395e5e-1',
    secondaryAssociationId: '48ced887-c2ea-402c-8fff-a5213a5f8efb-2',
    linkType: 'derivative',
    },
    {
    primaryAssociationId: 'd38c9c7a-efeb-400d-8b3f-222eb2395e5e-1',
    secondaryAssociationId: '92aa1ad3-e6d7-4676-a962-c70f277a28b8-2',
    // Invalid Link Type
    linkType: 'child',
    },
    {
    primaryAssociationId: 'd38c9c7a-efeb-400d-8b3f-222eb2395e5e-1',
    // Invalid Secondary Association ID
    secondaryAssociationId: '92aa1ad3-e6d7-4676-a962-c70f277a28b8-3',
    linkType: 'child',
    },
    {
    // Invalid Primary Association ID
    primaryAssociationId: 'f47a3d97-eeed-47af-aed2-48c37dd8cbf9-2',
    secondaryAssociationId: '3919382b-7b5b-4b6f-93af-8d36247cb68e-2',
    linkType: 'child',
    },
];

module.exports = {
 createRelationshipRecords,
};

```

### Bulk Create Relationships
In this source file, we include the following helper functions:
- **aggregateAssocationRelationshipRecords** - Aggregate an Array of Relationship Records into an Array of Create Relationship Requests.
    - The Relationship record includes a primaryAssociationId, secondaryAssociationId, and linkType.
    - The Create Relationship Request includes a primaryAssociationId and an Array of secondaryAssociationId / linkType entries, where each entry is a separate Relationship.
- **retrieveAssetsByAssociationId** - Given an Array of Create Relationship Requests referenced by Association ID, retrieve the Assets corresponding to each Association ID, using the Asset Keyword Search API, and return an Association ID to Asset Object ID Map.
- **convertAssociationToAssetRelationships** - Given an Array of Create Relationship Requests referenced by Association ID, convert the Association IDs to Asset Object IDs in each Request and return the modified Array.
- **createRelationships** - Using the above helper functions, get Authorization data, convert the Relationship Records into an Array of Create Asset Relationship Requests, and process the Requests using the Create Relationship API.  Log any errors during processing.
During the Create Relationship API processing, errors may include Arrays of Relationship-specific errors or consolidated error messages that may occur during Request validation by the API.

#### **`helper/create-relationships.js`**
```javascript
const _ = require('lodash');
const Config = require('../config/config');
const AuthenticationService = require('../service/authentication');
const AssetSearchService = require('../service/asset-search');
const RelationshipService = require('../service/relationship');
const RelationshipData = require('../data/relationship');
/**
* @description Aggregate a Tabular Array of Association ID-based Relationship Records into
*  an Array of Association ID-based Create Relationship Objects.
* @param {Array} records Array of Association ID-based Records
* @returns {Array} Array of Create Relationships by Association ID
*/
const aggregateAssociationRelationshipRecords = (records) => {
 // Validate Records
 if (!Array.isArray(records)) {
   throw new Error('Create Relationship Records must be an Array');
 }
 // Prepare Primary Association ID to Secondary Association IDs Relationship Map
 const relationshipMap = {};
 // Loop Over Records to Aggregate by Primary ID
 records.forEach((record) => {
   // Extract Association IDs and Link Type
   const { primaryAssociationId, secondaryAssociationId, linkType } = record;
   // Check If Primary Association ID Mapping Exists
   if (relationshipMap[primaryAssociationId]) {
     // Add Relationship to Existing Mapping List
     relationshipMap[primaryAssociationId].push({
       secondaryAssociationId,
       linkType,
     });
   } else {
     // Else Create new Relationship Mapping List
     relationshipMap[primaryAssociationId] = [
       {
         secondaryAssociationId,
         linkType,
       },
     ];
   }
 });
 // Convert Mapping to Array of Create Relationship Entries
 const associationRelationships = [];
 Object.keys(relationshipMap).forEach((primaryAssociationId) => {
   associationRelationships.push({
     primaryAssociationId,
     secondaryAssociations: relationshipMap[primaryAssociationId],
   });
 });
 // Return Array of Create Relationship Records using Association IDs
 return associationRelationships;
};
/**
* @description Retrieve Assets given an Array of Association IDs.
*  Generate and Return an Association ID to Asset Object ID Map.
* @param {{records, associationIdNames, requestContext}} request
* @returns {*} Association ID to Object ID Map
*/
const retrieveAssetsByAssociationId = async (request) => {
 const { records, associationIdNames, requestContext } = request;
 const associationIdObjectIdMap = {};
 let searchTerms = [];
 // Validate Parameters
 if (!Array.isArray(records)) {
   throw new Error('records must be an Array');
 }
 if (!Array.isArray(associationIdNames) && associationIdNames) {
   throw new Error('associationIdNames must be an Array');
 }
 if (!requestContext) {
   throw new Error('Missing options.requestContext');
 }
 // Loop to Extract Association IDs from Records
 records.forEach((record, i) => {
   // For Each Record, Extract the Association ID Values
   associationIdNames.forEach((name) => {
     const value = record[name];
     // Value Must be Populated
     if (!value) {
       throw new Error(`Missing [${name}] value in Record ${i}`);
     }
     // Save Association ID as Search Term
     searchTerms.push(value);
   });
 });
 // Remove Duplicate Association IDs
 searchTerms = _.uniq(searchTerms);
 // Chunk Assets into Batches
 const batchSize = 100;
 const searchTermChunks = _.chunk(searchTerms, batchSize);
 // Get Association ID Search Field Name
 const associationIdFieldName = Config.metadata.ASSOCIATION_ID.ATTRIBUTE_NAME;
 // Search for Assets by Association ID
 for (let i = 0; i < searchTermChunks.length; i += 1) {
   const searchTermChunk = searchTermChunks[i];
   // Prepare Search Request
   const search = {
     searchTerm: searchTermChunk,
     limit: batchSize,
     operation: 'OR',
   };
   const searchRequest = {
     ...requestContext,
     search,
   };
   // Run Asset Search
   // eslint-disable-next-line no-await-in-loop
   const searchResult = await AssetSearchService.runKeywordSearch(searchRequest);
   // Extract Association ID Values
   for (let j = 0; j < searchResult.result.length; j += 1) {
     const asset = searchResult.result[j];
     const { objectId } = asset;
     const metadataValue = _.get(asset, `metadataDenormalized[${associationIdFieldName}]`);
     // Add Association ID / Object ID Mapping If Not Not Exist
     if (metadataValue && !associationIdObjectIdMap[metadataValue]) {
       associationIdObjectIdMap[metadataValue] = objectId;
     }
   }
 }
 console.log('Association ID / Object ID Map:', associationIdObjectIdMap);
 // Return Association ID / Object ID Map
 return associationIdObjectIdMap;
};
/**
* @description Convert an Array of Association ID-based Relationships into an Array of Asset Relationships.
*  Request Context is included to support the authorized calls to the REST API.
* @param {{associationRelationships, requestContext}} request
* @returns {Array} Array of Asset Relationships
*/
const convertAssociationToAssetRelationships = async (request) => {
 const { associationRelationships, associationIdObjectIdMap } = request;
 const newRelationships = [];
 // Validate Association Relationships Array
 if (!Array.isArray(associationRelationships)) {
   throw new Error('Association Relationships must be an Array');
 }
 // Loop Over Association Relationships to convert to Asset Relationship
 for (let i = 0; i < associationRelationships.length; i += 1) {
   const associationRelationship = associationRelationships[i];
   const { primaryAssociationId, secondaryAssociations } = associationRelationship;
   try {
     // Convert Primary Association ID to Object ID
     const primaryId = associationIdObjectIdMap[primaryAssociationId];
     // Validate Primary ID
     if (!primaryId) {
       throw new Error('Primary Asset Not Found by Association ID:', {
         primaryAssociationId,
       });
     }
     // Prepare New Secondary Relationships
     const secondaryIds = [];
     // Loop Over Secondary Associations to Convert to Asset Relationships
     for (let j = 0; j < secondaryAssociations.length; j += 1) {
       const secondaryAssociation = secondaryAssociations[j];
       try {
         // Prepare Secondary Association ID Asset Search Request
         const { secondaryAssociationId } = secondaryAssociation;
         // Get Secondary Object ID
         const secondaryObjectId = associationIdObjectIdMap[secondaryAssociationId];
         // Validate Secondary Object ID
         if (!secondaryObjectId) {
           throw new Error('Secondary Asset Not Found by Association ID for Primary Asset Relationship:', {
             primaryId,
             secondaryAssociationId,
           });
         }
         // Add New Secondary ID Entry
         const secondaryId = {
           // id: objectId,
           id: secondaryObjectId,
           linkType: secondaryAssociation.linkType,
         };
         secondaryIds.push(secondaryId);
       } catch (error) {
         console.error(
           'Failed to Locate Secondary Association Assets for Relationship Conversion:',
           error.message,
           {
             primaryAssociationId,
             secondaryAssociation,
           },
           error.stack,
         );
       }
     }
     // Check for Relationships to Create
     if (!secondaryIds.length) {
       throw new Error('No Secondary IDs generated to create new Asset Relationship');
     }
     // Add Asset Relationship
     const newRelationship = {
       primaryId,
       secondaryIds,
     };
     newRelationships.push(newRelationship);
   } catch (error) {
     console.error(
       'Failed to Convert Association to Asset Relationships:',
       error.message,
       {
         primaryAssociationId,
       },
       error.stack,
     );
   }
 }
 // Return New Relationships to Create
 return newRelationships;
};
/**
* @description Create Asset Relationships given a List of Association ID-based Relationship Records.
*  Authorization Token is generated to invoke REST API calls.
*/
const createRelationships = async () => {
 try {
   // Get API Config
   const apiConfig = Config.getApiConfig();
   // Get Credentials
   const credentials = Config.getCredentials();
   // Prepare Authentication Request
   const authenticateRequest = {
     ...apiConfig,
     ...credentials,
   };
   // Authenticate Request and Get Authorization
   const authenticateResult = await AuthenticationService.authenticate(authenticateRequest);
   const { authorization } = authenticateResult.session;
   // Get Bulk Create Relationship Records
   const { createRelationshipRecords } = RelationshipData;
   // Aggregate Association Relationships from Table to Mapping by Primary Association ID
   const associationRelationships = aggregateAssociationRelationshipRecords(createRelationshipRecords);
   // Prepare Authorized Request Context
   const requestContext = {
     ...apiConfig,
     authorization,
   };
   // Retrieve Association ID to Object ID Map
   const retrieveRequest = {
     records: createRelationshipRecords,
     associationIdNames: Config.metadata.ASSOCIATION_ID.RECORD_NAMES,
     requestContext,
   };
   const associationIdObjectIdMap = await retrieveAssetsByAssociationId(retrieveRequest);
   // Convert Association Relationships to Asset Relationships
   const convertRequest = {
     associationRelationships,
     associationIdObjectIdMap,
   };
   const newRelationships = await convertAssociationToAssetRelationships(convertRequest);
   // Loop to Create New Relationships
   for (let i = 0; i < newRelationships.length; i += 1) {
     const newRelationship = newRelationships[i];
     const { primaryId } = newRelationship;
     try {
       // Prepare Create Relationship Request
       const request = {
         ...apiConfig,
         authorization,
         ...newRelationship,
       };
       // Create Relationship
       // eslint-disable-next-line no-await-in-loop
       const result = await RelationshipService.createRelationship(request);
       // Check for Errors
       if (Array.isArray(result)) {
         for (let j = 0; j < result.length; j += 1) {
           const item = result[j];
           const secondaryId = newRelationship.secondaryIds[j];
           if (item.status === 'error') {
             console.error('Failed to Create Asset Relationship:', {
               message: item.message,
               primaryId,
               secondaryId,
             });
           }
         }
       } else {
         // Log Error Result
         console.error('Failed to Create Asset Relationship:', {
           message: result,
           primaryId,
         });
       }
     } catch (error) {
       console.error('Failed to Create Relationship:', error.message, error.stack);
     }
   }
 } catch (error) {
   console.error('Failed to Process Create Relationships:', error.message, error.stack);
 }
};
createRelationships();
```