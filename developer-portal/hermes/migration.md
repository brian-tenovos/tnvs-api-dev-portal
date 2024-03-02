---
showNextButton: false
showPrevButton: false
---

## Updating your integrations to Tenovos API (v1.5)

:::info Coming Soon

If we told you now, you'd start using it before it's ready.

:::

### How to upgrade

Version 1 and 1.5 both implement the exact same endpoints with the same request and response structures.  This makes updating from v1 to v1.5 extremely simple.

Integrations should implement a constant base url for each API call made to Tenovos, allowing this value to be defined once and reused throughout the integration.  If you've implemented your integration in this way, then upgrading to v1.5 is as easy as changing the value of the constant value for the base url.

#### Understanding v1 Base URLs

In v1 of the Tenovos REST API, each customer is provided a base url.  This url is specific to the account, and includes base urls such as:
- `https://entuat.services.tenovos.io/content-store-v1`
- `https://enterprise-2.services.tenovos.io/content-store-v1`
- `https://enterprise3.services.tenovos.io/content-store-v1`

With v1.5 of the Tenovos REST API, the base url is now the same for every customer, respective to UAT and Production accounts.
- All UAT accounts: `https://api.uat.tenovos.io/v1`
- All Production accounts: `https://api.tenovos.io/v1`

---

**Upgrading from v1 to v1.5 is as easy as updating the base url in your integration!**