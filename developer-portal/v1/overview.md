---
showNextButton: false
showPrevButton: false
---

# v1 Overview

## Welcome to the Tenovos API

The Tenovos API is built to allow you to easily and quickly integrate Tenovos's features and capabilities in your own application or create an integration between Tenovos and any other application. This API is the same one we use to power Tenovos! The API is a RESTful interface, essentially providing programmatic access to the features and data in the system. If you’ve interacted with a RESTful API before, many of the concepts will be familiar - it provides predictable URLs for accessing resources and uses built-in HTTP features. The API accepts JSON or form-encoded content in requests, and returns JSON content in all of its responses, including errors. Only the UTF-8 character encoding is supported for both requests and responses.

## Base Domain

API calls to Tenovos should be made to the base domain configured for the specific environment.  This varies from customer to customer.  If you are unaware of what the base domain is for specific UAT and/or Production environments, you can contact [Tenovos Support](https://tenovoshelp.zendesk.com)


## Prerequisites
- Basic [Web API](https://www.w3schools.com/js/js_api_intro.asp) interface understanding
- Understanding of how to make API calls either via code or utilities such as [Postman](https://www.postman.com/)
- Basic understanding of HTTP [Request Methods](https://www.w3schools.com/tags/ref_httpmethods.asp)
- Understanding of [JSON data structures](https://www.w3schools.com/js/js_json_intro.asp)

## Security & Authentication

The Tenovos API is an SSL-only API. You must be a verified user to make API requests. Tenovos currently supports basic authentication using an API token. Currently, you can request your API Key from Tenovos Support.

***Note: In the near future, the API token will be found on your “Admin > Configuration” page. To access, click on your avatar in the top right and select "Admin". From there, click on Configuration and then "API key."***

## JSON Validation

If you get some errors on POST or PUT requests, please use this [JSON Validator](https://jsonformatter.curiousconcept.com/) to check if your request's body is properly formatted.  Code generation from the documentation is possible, however we don't support any modifications or required fixes for output code.

## Code Examples

Follow the training exercises sequentially, or cherry-pick them independently if you're limited on time.

- [Getting Started with Authentication and Helper Utilities](training/authenticating.md)
- [Search for Assets](training/search.md)
- [Create Asset Relationships](training/relationships.md)
- [Create Relationships in Bulk](training/bulk-relationships.md)
- [Uploading Large Assets](training/large-upload.md)
- [Creating Collections](training/collections.md)

## Asking for help

Don't be shy... [Tenovos Support](https://tenovoshelp.zendesk.com) is here to help.