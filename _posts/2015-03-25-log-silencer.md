---
published: true
title: Rails Log Silencer Rack Middleware
layout: post
---

After enabling `debug` logging on some Rails apps at work, I found the logs
were getting filled up by health checks. So I wrote this simple rack middleware
to silence the logs:

```ruby
class LogSilencer
  def initialize(app, opts = {})
    @silenced = opts.delete(:silenced)
    @app = app
  end

  def call(env)
    if env['X-SILENCE-LOGGER'] || @silenced.match(env['PATH_INFO'])
      Rails.logger.silence do
        @app.call(env)
      end
    else
      @app.call(env)
    end
  end
end
```

I was inspired by (a stackoverflow
answer)[http://stackoverflow.com/a/10310064], but wanted to improve on it. It's
configured with the following in the `config/application.rb`:

```ruby
require 'log_silencer'
config.middleware.insert_before Rails::Rack::Logger,
  LogSilencer,
  silenced: /^\/options/

```

The `silenced:` key takes a RegEx to match on the path. In this case, I want to
silence any requests to a `/options` path.

The `#silence` method on logger is a Rails addition. So if you're using Rails
4+ you have to make sure to use the `ActiveSupport::Logger` logger.
