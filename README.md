# keystone-email

Email helper for KeystoneJS Apps.

Makes it easier to send dynamic emails from templates with ether Mandrill or Mailgun, and includes support for automatically inlining CSS.

## Background

This module is currently WIP. We're moving the built-in email functionality out of KeystoneJS core into its own package, so that it's not bundled by default and can be updated independently.

If you're interested in helping out, please open an issue!

## Usage

Create a new Email instance with a template and options:

```
new Email(template, options)
```

HTML from your template will automatically be converted to text using the [html-to-text](https://www.npmjs.com/package/html-to-text) package.

CSS will be automatically inline with [Juice](https://www.npmjs.com/package/juice) or natively by Mandrill if you are using that service.

#### Options

- `engine` (optional, String or Function) the template engine you are using. Defaults to the extension in the template argument.
- `transport` (String) the email service you wish to use. Supported options are `mailgun` and `mandrill`. Required for the `send()` method to work, but not for `render()`
- `mailgun` (optional, Object) options to pass to mailgun. See [mailgun-js](https://www.npmjs.com/package/mailgun-js)
- `mandrill` (optional, Object) options to pass to mandrill. See the [Mandrill API Docs](https://mandrillapp.com/api/docs/index.nodejs.html)
- `root` (optional, String) the root path to look in when resolving template paths. Defaults to the current working directory.

#### Engines

Strings are treated as package names, and loaded with `require()`.

Functions are the template engine itself.

If you don't want to parse your template, set your engine to `"html"`.

### Sending Email

Once you have created an Email instance, use the send method to send it:

```js
new Email('template.pug', { transport: 'mailgun' }).send(locals, options, (err) => { /* sent */ })
```

#### Arguments

- `locals` (Object) local variables / options for the template engine
- `options` (Object) options for the transport, extends options set in the `mailgun` or `mandrill` options passed to the Email constructor
- `callback` (Function) called when the email has been sent, passed an error if one occurred

### Rendering Email

You can also render the contents of an email without sending it, useful for providing a "view email in your browser" feature or previewing email templates during development.

```js
new Email('template.pug').render(locals, (err, { html, text }) => { /* rendered */ })
```

- `locals` (Object) local variables / options for the template engine
- `callback` (Function) called when the email has been sent, passed an error if one occurred

## Usage with Mailgun

The following `send()` options are applicable when using `mailgun` as the transport:

- `apiKey` (required, String) Your API Key, defaults to `process.env.MAILGUN_API_KEY`
- `domain` (required, String) Your sending domain, defaults to `process.env.MAILGUN_DOMAIN`
- `inlineCSS` (optional, Boolean) whether to inline CSS classes in your template, defaults to `true`

See [mailgun-js](https://www.npmjs.com/package/mailgun-js) and [the Mailgun API Docs](https://documentation.mailgun.com/api-sending.html#sending) for the full set of supported options.

## Breaking changes from Keystone.Email

### Template helpers

The built-in template helpers have been removed. If you used these are are interested in helping create their replacement, let us know.

### Default locals

Default locals have been removed. If you need them, please pass them explicitly. They were:

```
pretty: true,
_: require('lodash'),
moment: require('moment'),
utils: require('keystone-utils),
subject: '(no subject)',
theme: {}
```

### Mandrill template support

Mandrill template support has been dropped. If you are going to use this, you're not using most of the functionality of this package anyway, and we suggest you simply use Mandrill's API directly.

### Automatic tagging

Keystone would previously automatically tag emails with the sent date and template name. That's pretty opinionated and of questionable usefulness, if you still want this behaviour it's easy to add tags yourself in the `send()` options.

### Path changes

You could previously use a directory as the template name, and Keystone.Email would look for an `email.{ext}` file (where ext is your template language's default extension). This is no longer supported, please provide the full path to your email template.


## License

MIT Licensed. Copyright (c) 2016 Jed Watson.

Portions of this library are based on Express, see https://github.com/expressjs/express for copyright.
