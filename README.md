# apigrate-slack
A simple utility to post messages to Slack using inbound webhooks. Useful for logging transactions to a channel where others can see the messages and take action if needed.

## Usage

### Instantiation
```javascript
var webhook = 'your inbound webhook here';

var slack_logger = new SlackLogger(webhook,
  "test environment",
  "SlackLogger Test"
);
```
In the above simple example, the username for **each slack entry** will be 'test environment' and the author will be "SlackLogger Test". By convention, we use username to store our environment or host name and use the author to identify the app making the logging call from that environment. You are, of course, free to implement your own conventions.

Here is a more detailed instantiation that includes additional information to be recorded on every logging call.
```javascript
var webhook = 'your inbound webhook here';

var slack_logger = new SlackLogger(webhook,
  "test environment",
  "Invoice Integration",
  {
    author_url: "http://documentation.about.invoice.integration",
    fields: {
      account_id: 24601,
      environment: "test"
    }
  }
);
```
In this case, `account_id` and `environment` fields will be added to the slack message on every log entry. The "Invoice Integration" author will be hyperlinked as well with the value of `author_url`. This is a nice way to tie documentation together with your log entries, or to hyperlink to an app that is logging activity.

### Logging
Each log message **must** have the following parameters:

1. true/false indicating success or failure
1. the log message (i.e. a summary)
1. a more detailed message (optional). This could be log details, stack trace or other detailed information for the message (typically more useful to provide troubleshooting info on errors).


Here's how to post a simple "success" message.
```javascript
slack_logger.log(
  true,
  'That worked.'
);
```

It is possible include more detail in log messages. Provide a more detailed message (up to 7500 characters) if you like. Note that this detailed message will be formatted in fixed-font code block immediately following the summary message for readability (newline characters are respected by Slack in the formatting). Additionally, the fields parameter allows you to specify additional data that may be helpful for reporting or troubleshooting **on specific log messages**.
```javascript
slack_logger.log(
  true,
  'Invoice Created.',
  'Found customer.\nThere are 3 invoice lines.\nThe total amount is $107.80',
  {
    customer_id: 28390,
    invoice_id: 123789
  }
);
```

Error log messages (as shown below) are more useful when they include detailed information to allow consumers to troubleshoot more effectively.
```javascript
slack_logger.log(
  false,
  'Invoice creation failed.',
  'Found customer.\nException processing invoice lines.\nThe quantity is missing for the line with product 1234879.',
  {
    customer_id: 28390,
    product_id: 1234879,
    product_sku: 'TS4921'
  }
);

```
