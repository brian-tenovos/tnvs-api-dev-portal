# Retry and Failure Handling

## Retry Schedule

Tenovos attempts to deliver each webhook message based on a retry schedule with exponential backoff.

### The Schedule

Each message is attempted based on the following schedule, where each period is started following the failure of the preceding attempt:

- Immediately
- 5 seconds
- 5 minutes
- 30 minutes
- 2 hours
- 5 hours
- 10 hours
- 10 hours (in addition to the previous)

If an endpoint is removed or disabled, delivery attempts to the endpoint will be disabled as well.  For example, an attempt that fails three times before eventually succeeding will be delivered roughly 35 minutes and 5 seconds following the first attempt.

## Failed Delivery Handling

After the conclusion of the above attempts, the message will be marked as ==Failed== for this endpoint, and you will get a webhook message of type 
**`message.attempt.exhausted`** notifying you of this error.

### Tenovos Webhooks Admin Dashboard

Tenovos provides users with the Administrative privilege for Webhooks to access an Administrative dashboard in the Tenovos UI.  Via this Dashboard you have access to see all available event types, which ones you're subscribed to, all of your endpoint configurations, and real time webhook message analytics.  From this Dashboard, you can also manually retry failed webhook messages at any time, or automatically retry ("Recover") all failed messages starting from a given date.

### Disabling Failing Endpoints

If all attempts to a specific endpoint fail for a period of 5 days, the endpoint will be disabled by Tenovos automatically, and a webhook **`EndpointDisabledEvent`** will be sent to your Tenovos Webhooks Admin Dashboard (not to the failing endpoint).  If you are having issues with your integration and do not expect your endpoint to be operational for a period of 3 hours or more, it is recommended that you disable your endpoints in the Tenovos Webhooks Admin Dashboard, or via the Tenovos REST API.