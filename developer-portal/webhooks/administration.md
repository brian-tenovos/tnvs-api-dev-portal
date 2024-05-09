## Tenovos Webhooks [BETA]

### Introduction

In the Admin section of Tenovos, Webhooks allow you to easily subscribe to system events directly through the Tenovos UI. Within the Webhooks page, there are four accessible pages: Endpoints, Events Catalog, Logs, and Activity.

### Endpoints

The *Endpoints* tab allows you to create new, and manage existing, endpoints that are currently receiving event notifications from Tenovos.
To create a new endpoint, click *Add Endpoint* in the top right hand corner of the screen. This will open up a window where you can proceed to enter the endpoint that the events will be sent to, add a description to the newly created endpoint, activate filters to only be notified of specific events and more. 

**Note: If no filters are applied to the endpoint, that endpoint will receive notifications for all events.**

To manage an existing endpoint, click on any previously created endpoint. When managing an existing endpoint, you are able to make changes to the endpoint where notifications are being sent, add/remove event types from notifications, and review statistics on notification delivery.

### Events Catalog

The Events Catalog is where you can view all event types that can be subscribed to.

### Logs

The logs page will show all events that have been invoked from your dedicated Tenovos system. Your configured endpoints will show only messages for event types that have been requested, whereas logs will show all event activity, regardless of event type.

### Activity

The Activity page provides a visual representation of the last 6 hours of notification activity, including the count of successful anf failed delivery attempts, across all endpoints.

### Message Contents

Refer to the Payload examples.

