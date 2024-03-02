---
showNextButton: false
showPrevButton: false
---

## Tenovos API v1.5 changelog

### Expected Future Changes

---

### 1.5.0-beta (2024-04-15)

- Available for customers in Beta

### 1.5.0-beta-24.3.20 (2024-03-20)

- Added new endpoints to the Asset category and API references for an alternative endpoints to get assets and thier metadata.  This new alternative endpoint returns a streamlined
payload by removing technical metadata, normalized security, customer id, and other data that was not necessary.  In addition, a new data structure has been introduced, particularly for asset metadata.  
    - `/assets/{id}`: A GET call that allows clients to return a single asset with an alternative response payload.

### 1.5.0-beta-24.3.18 (2024-03-18)

- Added new endpoint to the Search category and API reference for an alternative endpoint for performing a keyword search an asset.  This new alternative endpoint returns a streamlined
payload by removing technical metadata, normalized security, customer id, and other data that was not necessary.  
    - `/search`: A POST call that allows clients to search for assets.

### 1.5.0-beta-24.3.10 (2024-03-10)

- Added new endpoint to the Security category and API reference for getting all roles
    - `/roles`: A GET call that allows clients to retrive a listing of Roles available in thier system

### 1.5.0-beta-24.3.1 (2024-03-04)

- Added new endpoint category and API references for Adaptive Templates
    - `/template`: A POST call that allows clients to search for existing Adaptive Templates in thier system
    - `/template/launch`: A POST call that returns the url for launching the template via a web browser

---

### Previous Changes

### 1.5.0-beta-0 (2023-10-12)

v1.5 (Hermes) was born.