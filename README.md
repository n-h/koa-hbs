koa-hbs
=======

[Handlebars](http://handlebarsjs.com) Templates via Generators for
[Koa](https://github.com/koajs/koa/)

[![Build Status][travis-badge]][repo-url]

## Forward
Things that are supported:
- Registering helpers
- Registering partials
- Specify a directory or multiple directories of partials to register
- Single default layout
- Alternative layouts
- Template caching (actually, this cannot currently be disabled)
- Content blocks

Things that will be, but are **not** yet supported:
- Asynchronous helpers


## Usage
koa-hbs is middleware. Configure the default instance by passing an options hash
to #middleware or on an independant from #create().

```javascript
var koa = require('koa');
var hbs = require('koa-hbs');

var app = koa();

// koa-hbs is middleware. `use` it before you want to render a view
app.use(hbs.middleware({
  viewPath: __dirname + '/views'
}));

// Render is attached to the koa context. Call `this.render` in your middleware
// to attach rendered html to the koa response body.
app.use(function *() {
  yield this.render('main', {title: 'koa-hbs'});
})

app.listen(3000);

```

After a template has been rendered, the template function is cached. `#render`
accepts two arguements - the template to render, and an object containing local
variables to be inserted into the template. The result is assigned to Koa's
`this.response.body`.

### Registering Helpers
Helpers are registered using the #registerHelper method. Here is an example
using the default instance (helper stolen from official Handlebars
[docs](http://handlebarsjs.com):

```javascript
hbs = require('koa-hbs');

hbs.registerHelper('link', function(text, url) {
  text = hbs.Utils.escapeExpression(text);
  url  = hbs.Utils.escapeExpression(url);

  var result = '<a href="' + url + '">' + text + '</a>';

  return new hbs.SafeString(result);
});
```

registerHelper, Utils, and SafeString all proxy to an internal Handlebars
instance. If passing an alternative instance of Handlebars to the middleware
configurator, make sure to do so before registering your helpers via the koa-hbs
proxies, or just register your helpers directly via your Handlebars instance.

### Registering Partials
The simple way to register partials is to stick them all in a directory, and
pass the `partialsPath` option when generating the middleware. Say your views
are in `./views`, and your partials are in `./views/partials`. Configuring the
middleware as

### Layouts
Passing `defaultLayout` with the a layout name will cause all templates to be
inserted into the `{{{body}}}` expression of the layout. This might look like
the following.

```html
<!DOCTYPE html>
<html>
<head>
  <title>{{title}}</title>
</head>
<body>
  {{{body}}}
</body>
</html>
```

In addition to, or alternatively, you may specify a layout to render a template
into. Simply specify `{{!< layoutName }}` somewhere in your template. koa-hbs
will load your layout from `layoutsPath` if defined, or from `viewPath`
otherwise.

At this time, only a single content block (`{{{body}}}`) is supported.

### Block content
Reserve areas in a layout by using the `block` helper like so.

```html
{{#block "sidebar"}}
  <!-- default content for the sidebar block -->
{{/block}}
```

Then in a template, use the `contentFor` helper to render content into the
block.

```html
{{#contentFor "sidebar"}}
  <aside>
    <h2>{{sidebarTitleLocal}}</h2>
    <p>{{sidebarContentLocal}}</p>
  </aside>
{{/contentFor}}
```

### Options
The plan for koa-hbs is to offer identical functionality as express-hbs
(eventaully). These options are supported _now_.

- `viewPath`: [_required_] Full path from which to load templates
  (`Array|String`)
- `handlebars`: Pass your own instance of handlebars
- `templateOptions`: Hash of
  [handlebars options](http://handlebarsjs.com/execution.html#Options) to pass
  to `template()`
- `extname`: Alter the default template extension (default: `'.hbs'`)
- `partialsPath`: Full path to partials directory (`Array|String`)
- `defaultLayout`: Name of the default layout
- `layoutsPath`: Full path to layouts directory (`String`)
- `contentHelperName`: Alter `contentFor` helper name
- `blockHelperName`: Alter `block` helper name

## Example
You can run the included example via `npm install koa` and
`node --harmony app.js` from the example folder.

## Credits
Functionality and code were inspired/taken from
[express-hbs](https://github.com/barc/express-hbs/).

[travis-badge]: https://travis-ci.org/jwilm/koa-hbs.png?branch=master
[repo-url]: https://travis-ci.org/jwilm/koa-hbs
