---
showNextButton: false
showPrevButton: false
---

## v1.5 Overview

:::info V1.5 Availability

This documentation is in draft and provided for the intent of assisting early preview beta users.  This documentation will be updated once this new version is available for general customer usage. 

:::

#### Tenovos has introduced version 1.5 to provide non-breaking improvements over v1, so that v1 clients can easily and safely update to use v1.5.

### Key Changes:
* Authentication Standards now include OAuth 2.0 authorization code flow.  
* Client ID is no longer required to be passed when requesting an Authorization token using our standard auth from v1. 
* New endpoints have been added and will continue to be added until v2 is generally available to all customers.
    * Adaptive Template endpoints
    * A new Get Asset endpoint with a simplied Response
    * A new Create Asset endpoint with simplified payloads for the Request and Response
    * A new Update Asset endpoint with simplified payloads for the Request and Response
* We've made some performance improvements to most endpoints

## Staying up to date with changes

Tenovos sends regular product updates to all customer points of contact as well as to our partners.  API changes are documented in the Product Update for which the change is deployed, 
and you may also see a consolidated list of updates and changes in the [v1.5 Changelog](changelog.md).