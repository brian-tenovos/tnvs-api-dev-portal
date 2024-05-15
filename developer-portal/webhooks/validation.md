# Verifying Webhook Signatures

Each webhook call includes three headers with additional information that are used for verification:

- `webhook-id`: the unique message identifier for the webhook message.  This indentifier is unique across all messages, but will be the same when the same webhook is being resent (e.g. due to a previous failure).
- `webhook-timestamp`: timestamp in [seconds since epoch](https://en.wikipedia.org/wiki/Unix_time).
- `webhook-signature`: the [Base64](https://en.wikipedia.org/wiki/Base64) encoded list of signatures (space deliminated).


## Constructing the signed content

The content to sign is composed by concatenating the id, timestamp and payload, seperated by the full-stop character `(.)`.  In code, it would look something like:

```js
signedContent = "${webhook_id}.${webhook_timestamp}.${body}"
```

Where `body` is the raw body of the request.  The signature is sensitive to any changes, so even a small change in the body will cause the signature to be completly different.  This means that you should *not* change the body in any way before verifying.

## Determining the expected signature

Tenovos used an [HMAC](https://en.wikipedia.org/wiki/HMAC) with [SHA-256](https://en.wikipedia.org/wiki/SHA-2) to sign its webhooks.

To calculate the expected signature, you should HMAC the `signed_content` from above using the base64 portion of your signing secret (this is the part after the `whsec_` prefix) as the key.  For example, given the secret `whsec_MfKQ9r8GKYqrTwjUPD8ILPZIo2LaLaSw` you will want to use `MfKQ9r8GKYqrTwjUPD8ILPZIo2LaLaSw`.

Below is code example of how you can calculate the signature in Node.js:

```js
const crypto = require('crypto');

signedContent = `${webhook-id}.${webhook-timestamp}.${body}`
const secret = "whsec_5WbX5kEWLlfzsGNjH64I8lOOqUB6e8FH";

// Need to base64 decode the secret
const secretBytes = new Buffer(secret.split('_')[1], "base64");
const signature = crypto
  .createHmac('sha256', secretBytes)
  .update(signedContent)
  .digest('base64');
console.log(signature);
```

This generated signature should match one of the ones sent in the `webhook-signature` header sent by Tenovos.

The `webhook-signature` header is composed of a list of comma delimited signatures and their corresponding version identifiers.  The signature list is most commonly of length one.  Though there could be any number of signatures.  For example:

```
v1,g0hM9SsE+OTPJTGt/tmIKtSyZlE3uFJELVlNIOLJ1OE= v1,bm9ldHUjKzFob2VudXRob2VodWUzMjRvdWVvdW9ldQo= v2,MzJsNDk4MzI0K2VvdSMjMTEjQEBAQDEyMzMzMzEyMwo=
```
:::attention Attention

Make sure to remove the version prefix and delimiter (e.g. `v1`) before verifying the signature.

Please note that to compare the signatures it's recommended to use a constant-time string comparison method in order to prevent timing attacks.

:::

## Verify timestamp

As mentioned above, Tenovos also send the timestamp of the attempt in the `webhook-timestamp` header.  You should compare this timestamp against your system timestamp and make sure it's within your tolerance in order to prevent timestamp attacks.