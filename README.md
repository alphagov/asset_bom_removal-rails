# Asset::Bom::Removal::Rails

This gem hooks into [Rails `assets:precompile` task](http://guides.rubyonrails.org/asset_pipeline.html#precompiling-assets) and removes the BOM from any CSS files (and their gzipped versions) in the asset root.

We do this because we want to use [SRI](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity) on our assets, but [a bug in Firefox versions < 52](https://bugzilla.mozilla.org/show_bug.cgi?id=1269241) means it calculates the hash incorrectly when the CSS asset has a [BOM](https://en.wikipedia.org/wiki/Byte_order_mark#UTF-8) and refuses to load the asset.  The BOM is [generated by SASS versions > 3.4](https://github.com/sass/sass/blob/3.4.0/lib/sass/tree/visitors/to_css.rb#L131-L140) and in the versions of rails, sass, sprockets, sass-rails, and sprockets-rails we use it is impossible to configure SASS to stop doing this (as mentioned [in the sass-rails readme](https://github.com/rails/sass-rails/blob/v4.0.0/README.md#configuration) from version 4.0+).

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'asset-bom-removal-rails'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install asset_bom_removal-rails

## Usage

The gem hooks itself into rails assets pipeline so when you run `assets:precompile` it will be used.  When a css file is compiled with this gem in the app any BOM added by sass will be removed before the compiled CSS is passed to the rest of the asset pipeline to be persisted and added to the manifest.

It should only affect production environments because sass is (by default) only configured to use `:compressed` style (and thus generate BOMs) in production mode.  In development it uses `:expanded` which does not trigger the BOM insertion.

It replaces the default `css_compressor` (as configured in rails via `config.assets.css_compressor`) with a version that strips the BOM.  It won't do it if the `css_compressor` is configured and is not `:sass` or `:scss`.

## Development

After checking out the repo, run `bundle install` to install dependencies. Then, run `rake spec` to run the tests.

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/alphagov/asset-bom-removal-rails.

## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

