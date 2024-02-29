## API Reference

The Tenovos API is organized around [REST](http://en.wikipedia.org/wiki/Representational_State_Transfer).  Our API has predictable resource-oriented URLs, accepts JSON request bodies,
returns [JSON-encoded](http://www.json.org/) responses, and uses standard HTTP response codes, authentication, and verbs.

You can use the Tenovos API in non-production and production accounts.  The base url differs between non-production and production accounts.

The Tenovos API Supports batch requests in all versions, however v2 has the most support for batching.

### Introduction

Tenovos REST API currently contains three versions, v1, v1.5, and v2.

#### Version 1.0-1.4 (v1)

- Is the current version of the Tenovos API that production customers are on, as of Feb. 2024.
- Currently in Maintenace mode.  No additions will be made to v1 of the Tenovos API.
- Scheduled for end of support July, 2025.

#### Version 1.5 (v1.5)

- **Provides a fast and easy upgrade path for v1 customers.**
- Currently in a limited Beta and undergoing testing.
- Scheduled for inclusion in a broader Beta early April, 2024.
- Introduces OAuth2 as an additional authentication option.
- Provides some additional endpoints for Adaptive Templates customers, as well as alternative endpoints for getting assets and performing searches.
- Eliminates high latency on initial requests
- Improves overall performance on response latency

#### Version 2.x (v2)

:::attention Contains Breaking Changes

Contains breaking changes when migrating from v1 or v1.5.

:::

- Significant performance differences.
- Support for OAuth2 Authentication.
- Much broader functional coverage.
- Streamlined Response payloads resulting in much lighter Responses that are easier to implement.
- Standardization across all data types.
- 100% standardization of Response Codes and error message handling.
- Support for long running jobs.
- Webhook subscription management support.
- More detailed OpenAPI specification.

---

### Version 1 API Reference Documenation

[Version 1 - Latest Stable Version](./v1/index.md)

---

### Version 1.5 API Reference Documenation

[Version 1.5 - Beta Release/Private Preview](./hermes/index.md)

---

### Version 2 API Reference Documenation

[Version 2 - In Development](./v1/index.md)
Version 2 of the Tenovos API is currently in development and expected for early customer access in June 2024.

---