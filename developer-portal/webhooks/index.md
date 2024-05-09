# Webhooks

:::info Coming Soon

This documentation serves as an early preview in order to provide early access previews and perfrom Product Discovery. 

:::

## Overview

Tenovos webhooks are lightweight, event-driven HTTPS calls from the Tenovos DAM. The Webhooks API allows you to subscribe to these events happening in a Tenovos account.  Within your Tenovos account, when certain events occur, such as the creation of an asset or updating a collection, a webhook can make an HTTPS call to your web service and provide a lightweight message in JSON format.  A secure signature will also be provided to validate that the message came from Tenovos.  Webhook messages only contain contextual data to understand what event triggered the call, the topic that the event applies to, and summary-level information.  If the information contained in the message is not encompassing enough for your integration, you can call back into the Tenovos REST API in order to get additional data needed.

Follow an event and topic pattern, webhook subscription message are standardized across all events.  For example, the webhook message for an event of "Create" will follow the same message format regardless of the topic (User, Asset, Collection, etc.).  This makes implementing Tenovos webhooks in your integration even easier.  Webhooks may be enabled for specific ***Events*** to be triggered on various ***Topics***.  Events describe the type of action that was performed within your Tenovos account, 
such as **Create, Update, Download, Share, and Delete**.  When one of these events occurs against a given Topic, Tenovos will send a message to an endpoint you define.  Topics are inclusive of the various types of records in your account, such as **Assets, Users, Collections, RM Projects, RM Agreements, RM Entities, Story Boards**, etc. 

## Prerequisites

Using the Webhooks API requires the following:

- You must deploy a publicly available and secure (HTTPS) endpoint that can handle webhook payloads specified in this documentation.  **If you do not have the ability to create and maintain a highly available service to listen for and process webhook messages, please ask the Tenovos Customer Success team about Tenovos Connect.** 
- Webhooks only support anonymous authentication, basic authentication (username and password), or header authentication.
- You must load test your endpoints to understand your rate limits and provide this information to Tenovos.

## Designing for Webhooks

An essential design concept to understand is that webhooks are not guaranteed to be delivered in chronological order of the events which triggered them. For example, if a user creates an asset, then updates it, but the create message fails in transit to the receiving endpoint and is queued for retry, but the update message immediately succeeds, you may receive the update message prior to the create message. The design of your integration should always assume that messages can be received out of order, and it’s best practice to always call back into the REST API to get the latest real-time data out of Tenovos when receiving a webhook message and reacting appropriately.

It is also important to note that unless Tenovos receives a successful reply from your endpoint, i.e. a status 200 OK response within less than 30 seconds, it will assume the call failed and will automatically begin the retry reschedule. You should always respond immediately upon receipt of the webhook message, and indicate successful transmission.

Please do not provide status responses to Tenovos from other REST API calls you may be making to other integration endpoints, as they are not relevant to the operation of the service.

## Event Types and Topics

Read our [Event Types](event-types.md) documentation to learn more about what events and topics are available as webhook subscriptions within Tenovos.

The Event Types page also contains example payload for each event type, or you can access them directly from the [Webhook Payloads](https://api.tenovos.com/developer-portal/webhooks/responses) section.

## Rate Limiting

As a prerequisite to enabling webhooks, you should have conducted testing to understand how many requests your endpoints can handle.  While Tenovos can handle however many messages you subscribe to, your endpoints may not be able to efficiently receive large volumes of requests. This is why Tenovos Webhooks includes rate-limiting calls to customer endpoints.  

### How does it work?

Prior to enabling Webhooks for a customer, Tenovos will use the rate limiting test data provided by you to set a rate limit for your webhook subscriptions in one of two ways:

- Rate limit will be applied on all endpoints configured for a customer, or 
- Rate limits will be applied to specific endpoints

The rate limit is defined as a limit for the number of messages per second to send to the endpoint. After the limit is reached, requests will get throttled so to keep a consistent rate under the limit.

Due to the nature of distributed systems the actual rate of messages can sometimes be slightly above the enforce rate limit. So for example, if you set a rate limit of 1,000 per second, an endpoint may potentially get messages at a rate of 1,050 or even higher.

## Failed Messages

Tenovos attempts to deliver each webhook message based on a retry schedule with exponential backoff.  Read our [Retry Schedule and Failed Delivery Handling](retry-schedule.md) documentation for more information about failed messages.
