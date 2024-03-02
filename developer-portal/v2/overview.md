---
showNextButton: false
showPrevButton: false
---

## V2 Overview

:::info V2 Availability

This documentation serves as an early preview in order to retrieve customer feedback. 

:::

### Key Changes:
- Authentication Standards have changed to OAuth.  The use of Basic Authentication (username and password) and Client ID is no longer required.
- Several new endpoints have been added.
- All API Response payloads have changed to simplify the data returned and provide more complete datasets so that fewer subsequent API calls are needed for common use cases.
- Responses no longer include denormalized objects for Metadata and Security.
- The use of Epoch dates has been entirely removed and replaced with UTC date and time.
- All API Responses implement paginated results when more than one result is expected.
- Exponentially improved performance over v1 based on initial testing.
- Updates to the HTTP Methods used, i.e., GET vs POST for certain endpoints.
- Errors returned by Tenovos will always be an HTTP Status Code other than '200'.
- Successful Asynchronous operations return a 202 with a task id that allows you to request status via the Task endpoints.
- Successful Synchronous **DELETE** operations return a 204 status.
- The Base URL is now standardized regardless of customer environment.
- Tenovos API v2 uses the latest Open API Specification.