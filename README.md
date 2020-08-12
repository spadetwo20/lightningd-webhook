## The `webhook` plugin.

This plugin sends a webhook to any URL when a payment is received, sent or forwarded. It supports filtering just some of these events and dispatching the webhooks to multiple URLs (each can have its own filtering policies).

It supports [all of the notification types lightningd produces](https://lightning.readthedocs.io/PLUGINS.html#notification-types). However, by default it will only dispatch `invoice_payment` and `sendpay_success`. The others are available only if you specify them explicitly in the URL querystring (see below).

The payload sent for each webhook is the _inner object_ of the payload given by lightningd. For example, for `invoice_payment` it will be `{"label": "unique-label-for-invoice", "preimage": "0000000000000000000000000000000000000000000000000000000000000000", "msat": "10000msat" }`.

## How to install

This is distributed as a single binary for your delight (or you can compile it yourself with `go get`).

[Download it](https://github.com/fiatjaf/lightningd-webhook/releases), call `chmod +x <binary>` and put it in `~/.lightning/plugins` (create that directory if it doesn't exist).

You only need the binary you can get in [the releases page](https://github.com/fiatjaf/lightningd-webhook/releases), nothing else.

## How to use

Initialize `lightningd` passing the option `--webhook=https://your.url/here` and that's it. You can also write that in your `~/.lightning/config` file. A nice place to get webhook endpoints for a quick test is https://beeceptor.com/.

### Multiple URLs

To dispatch webhooks to multiple URLs, write them separated by commas, like `--webhook=https://first.url/,https://second.url/`.

### Filter events

You can opt to receive only `payment`, `invoice` or `forward` events. To filter (in each URL) append a querystring parameter like `?filter-event=invoice` to the URL. You can repeat that parameter. If no parameters are given, `invoice_payment` and `sendpay_success` events will be dispatched.

### Example

With the following line in `~/.lightning/config`:

```
webhook=https://myurl.com/myhandler?filter-event=invoice_payment
```

You'll get payloads like these:

```json
{
  "invoice_payment": {
    "label": "unique-label-for-invoice",
    "preimage": "0000000000000000000000000000000000000000000000000000000000000000",
    "msat": "10000msat"
  }
}
```

## Built with [github.com/fiatjaf/lightningd-gjson-rpc](https://pkg.go.dev/github.com/fiatjaf/lightningd-gjson-rpc/plugin?tab=doc)
