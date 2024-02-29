# Webhook Event Types

Tenovos provides webhooks specific to the following topics and events.  The majority of **topics** provide created, updated, and deleted **events**, and additional events are provided for some topics, when relevant, such as shared, downloaded, and promoted.

When creating a webhook subscription to your endpoint, the full **Event Type** will be used, such as **asset.downloaded**, and this value will appear in the event payload of each call.  For examples of the payloads Tenovos sends, please reference the [Webhook Payloads](https://api.tenovos.com/developer-portal/webhooks/reference/overview/) API reference.


| **Topic**                   | **Event**  | **Event Type**               | **Event Occurs When...** |
|-----------------------------|------------|------------------------------|---------------------------------------------------------------------------------------------|
| Asset                       | Created    | asset.created                | an assets metadata and content are fully created and available                              |
| Asset                       | Deleted    | asset.deleted                | an asset has been deleted/sent to the Recycle Bin                                           |
| Asset                       | Downloaded | asset.downloaded             | the download process for a single asset has started                                         |
| Asset                       | Shared     | asset.shared                 | an asset is shared internally or externally from Tenovos                                    |
| Asset                       | Purged     | asset.purged                 | an asset is permanently deleted from the Recycle Bin                                        |
| Asset Metadata              | Updated    | asset.metadata.updated       | only the metadata of an asset is updated.                                                   |
| Asset Renditions            | Created    | asset.renditions.created     | system renditions of the asset uploaded have completed and able to be viewed in Tenovos     |
| Asset Security              | Updated    | asset.security.updated       | only the Security Templates applied to an asset are updated/changed                         |
| User                        | Created    | user.created                 | a new user account is created                                                               |
| User                        | Updated    | user.updated                 | a user account is updated                                                                   |
| Collection                  | Created    | collection.created           | a new Collection is created                                                                 |
| Collection                  | Updated    | collection.updated           | a Collection is updated, including adding assets, folders, and changes to metadata          |
| Collection                  | Deleted    | collection.deleted           | a Collection is permanently deleted                                                         |
| Collection                  | Shared     | collection.shared            | a Collection is shared externally                                                           |
| Collection                  | Downloaded | collection.downloaded        | the download process for a collection has started                                           |
| Collection Metadata         | Updated    | collection.metadata.updated  | only the metadata of a Collection is updated                                                |
| Collection Security         | Updated    | collection.security.updated  | only the Security Templates applied to a Shared Collection are updated/changed              |
| Relationship                | Created    | relationship.created         | a new relationship, of any type, between assets is created                                  |
| Relationship                | Updated    | relationship.updated         | the relationship type is updated/changed for an existing relationship between assets        |
| Relationship                | Deleted    | relationship.deleted         | a relationship between assets is deleted/removed                                            |
| Rights Management Project   | Created    | rm.project.created           | a new RM project is created                                                                 |
| Rights Management Project   | Updated    | rm.project.updated           | the metadata of an RM project is updated                                                    |
| Rights Management Project   | Deleted    | rm.project.deleted           | a RM project is deleted                                                                     |
| Rights Management Agreement | Created    | rm.agreement.created         | a new RM agreement is created                                                               |
| Rights Management Agreement | Updated    | rm.agreement.updated         | the metadata of a RM agreement is updated                                                   |
| Rights Management Agreement | Deleted    | rm.agreement.deleted         | a RM agreement is deleted                                                                   |
| Rights Management Agreement | Expired    | rm.agreement.expired         | the rights expiration date of an agreement is reached                                       |
| Rights Management Entity    | Created    | rm.entity.created            | a new RM entity is created                                                                  |
| Rights Management Entity    | Updated    | rm.entity.updated            | the metadata of a RM entity is updated                                                      |
| Rights Management Entity    | Deleted    | rm.entity.deleted            | a RM entity is deleted                                                                      |
| Story Board                 | Created    | storyboard.created           | a new Storyboard is created, whether enabled or disabled                                    |
| Story Board                 | Updated    | storyboard.updated           | metadata changes or when new assets/collections/searches are added                          |
| Story Board                 | Deleted    | storyboard.deleted           | a Storyboard is deleted                                                                     |
| Story Board                 | Shared     | storyboard.shared            | a Storyboard is shared internally or externally                                             |
| Version                     | Created    | version.created              | an asset is versioned with new content                                                      |
| Version                     | Promoted   | version.promoted             | a previous version of an asset is promoted to the latest version                            |
