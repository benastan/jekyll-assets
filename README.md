# Jekyll::AssetsPlugin

[![Build Status](https://secure.travis-ci.org/ixti/jekyll-assets.png)](http://travis-ci.org/ixti/jekyll-assets)
[![Dependency Status](https://gemnasium.com/ixti/jekyll-assets.png)](https://gemnasium.com/ixti/jekyll-assets)
[![Code Climate](https://codeclimate.com/badge.png)](https://codeclimate.com/github/ixti/jekyll-assets)

Jekyll plugin, that adds Rails-alike assets pipeline, that means that:

- It allows you to write javascript/css assets in other languages such as
  CoffeeScript, Sass, Less and ERB.
- It allows you to specify dependencies between your assets and automatically
  concatenates them.
- It allows you to minify/compress your JavaScript and CSS assets using
  compressor you like: YUI, SASS, Uglifier or no compression at all.
- It supports JavaScript templates for client-side rendering of strings or
  markup. JavaScript templates have the special format extension `.jst` and are
  compiled to JavaScript functions.
- Automaticaly adds MD5 fingerprint suffix for _cache busting_. That means
  that your `app.css` will become `app-908e25f4bf641868d8683022a5b62f54.css`.

Jekyll-Assets uses fabulous [Sprockets][sprockets] under the hood, so you may
refer to Rails guide about [Asset Pipeline][rails-guide] for detailed
information about amazing features it gives you.

*Notice:* You must have an [ExecJS][extjs] supported runtime in order to use
CoffeeScript.


[rails-guide]:  http://guides.rubyonrails.org/asset_pipeline.html
[sprockets]:    https://github.com/sstephenson/sprockets#readme
[extjs]:        https://github.com/sstephenson/execjs#readme


## Installation

Add this line to your application's Gemfile:

    gem 'jekyll-assets'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install jekyll-assets


## How to Use Jekyll-Assets

First of all make sure to require it. Common practice is to add following line
into `_plugins/ext.rb` file:

``` ruby
require "jekyll-assets"
```

Once plugin installed, you'll have following liquid tags available:

- `javascript app`: Generates `<script>` tag for `app.js`
- `stylesheet app`: Generates `<link>` tag for `app.css`
- `asset_path logo.png`: Returns _resulting_ URL for `logo.png`
- `asset app.css`: Returns _compiled_ body of `app.css`

All compiled assets will be stored under `assets/` dir of generated site.

Pipeline assets should be under your sources directory of Jekyll site. When a
file is referenced with liquid tag or with helper from another asset, Sprockets
searches the three default asset locations for it: `_assets/images`,
`_assets/javascripts` and `_assets/stylesheets`.

For example these files:

```
_assets/stylesheets/app.css
_assets/javascripts/app.js
_assets/javascripts/vendor/jquery.js
```

would be referenced like this:

``` html
{% stylesheet app %}
{% javascript app %}
{% javascript vendor/jquery %}
```

You might want to require `vendor/jquery.js` into your `app.js`. To do so, just
put following line in the beginning of your `app.js` to get it concatenated:

``` javascript
//= require vendor/jquery

$(function () {
  alert('I love BIG BOOKS!');
});
```

Now, if you want to use CoffeScript, just add `.coffee` suffix to the file you
want and you good to go. For example, here's how your `app.js.coffe` might look
like:

``` coffeescript
#= require vendor/jquery

$ ->
  alert 'I love BIG BOOKS! And small ones too!'
```

Now, you might want your stylesheets and javascripts to be minified. In this
case just install `uglifier` gem and add following lines into your `config.yml`:

``` yaml
assets:
  compress:
    js:   uglifier
    css:  sass
```

You might want to use YUI compressor for minification. In this case install
`yui-compressor` gem and put `yui` in place of `uglifier` and/or `sass` in the
config file.

Let's go crazy now! Assume you want your blog's `body` background color to be
white all the time, but red in December. Just add `.erb` suffix extension and
you can use ruby to "pre-process" asset, something like this:

```
# file: _assets/stylesheets/app.css.sass.erb

body
  background-color: <%= (12 == Date.today.month) ? "red" : "white" %>
```

Want more? Sure, here you are. You can use JavaScript templating with EJS or ECO
for example. Create a file `_assets/javascripts/hello.jst.ejs` with following
contents:

``` html
<p>Hello, <span><%= name %></span>!</p>
<p><%= info %></p>
```

Then use it in your `app.js` file like this:

``` coffeescript
#= require vendor/jquery
#= require hello

$ ->
  $("body").html JST["hello"]
    name: "ixti"
    info: "I love BIG BOOKS! And small ones too!"
```

That's all. Feel free to ask questions if any on [twitter][twitter],
[jabber][jabber] or [e-mail][e-mail].

[twitter]:  https://twitter.com/zapparov
[jabber]:   xmpp://zapparov@jabber.ru
[e-mail]:   mailto://ixti@member.fsf.org


## Configuration

You can fine-tune configuration by editing your `_config.yml`:

    #
    # Plugin: jekyll-assets
    #
    assets:
      #
      # Pathname of the destination of generated (bundled) assets relative
      # to the destination of the root.
      #
      dirname: assets
      #
      # Base URL of assets paths.
      #
      baseurl: /assets/
      #
      # Pathnames where to find assets relative to the root of the site.
      #
      sources:
        - _assets/javascripts
        - _assets/stylesheets
        - _assets/images
      #
      # Sets compressors for the specific types of file: `js`, or `css`.
      # No compression by default.
      #
      # Possible variants:
      #
      #     css  => 'yui', 'sass', nil
      #     js   => 'yui', 'uglifier', nil
      #
      compress:
        js:   ~
        css:  ~


## "Ben, I need help!" (c) Brother 2

Feel free to send me any comments, recommendations, suggestions. Improve this
README, as I really suck in documenting things (thanks @imathis for pointing
this out).


## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Added some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request


## License

Copyright (C) 2012 Aleksey V Zapparov (http://ixti.net/)

The MIT License

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the “Software”), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
of the Software, and to permit persons to whom the Software is furnished to do
so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.