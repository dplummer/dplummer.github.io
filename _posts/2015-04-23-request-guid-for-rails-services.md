---
published: true
title: Request guids for rails services and logging
layout: post
---

One of the most important aspects of a running system are it's logs. At work we
have many difference Rails apps running, communicating to each other over a
JSON HTTP API. To troubleshoot problems, I've developed some standards for
setting up logging.

The important points:

* A unique RequestID is used across all services for a single request. So if a
  customer hit FrontendApp-A that uses BackendApp-B, the logs share the same
  RequestID. Since we use [Faraday](https://github.com/lostisland/faraday) for
  our http client, it's simple to add a middleware to inject the `X-Request-ID`
  header.
* Any errors reported to [honeybadger](https://honeybadger.io) contain the
  RequestID.
* Logs go to STDOUT so the system can deal with the logging, not the
  application.
* Log HTTP client requests like SQL.
* Use debug logging in production so troubleshooting is easy.

## Setup

In the `Gemfile`, add these gems:

```ruby
gem 'http_logger'
gem 'request_id'
gem 'rack_set_request_id'
gem 'lograge'
```

Assuming you're using honeybadger, configure `http_logger`'s initializer to
ignore those requests. I've set `log_headers` to false, though that is the
default. It's helpful sometimes to turn that on when debugging this, just to
make sure the ReqeustID is being passed properly.

```ruby
if defined?(HttpLogger)
  HttpLogger.ignore = [/honeybadger\.io/, /:8080/]
  HttpLogger.log_headers = false
end
```

For any Faraday client connections, use the request_id gem:

```ruby
require 'faraday/request_id'

connection = Faraday.new 'http://example.com/api' do |builder|
  builder.use Faraday::RequestId
end
```

Configure your environments to use tagged logging and `lograge`, like
`config/environments/production.rb`:

```ruby
  # Configure lograge
  config.log_level = :debug
  config.lograge.enabled = true
  config.log_tags = [
    ->(_request) { "%s#%s".freeze % [Time.now.strftime("%Y-%m-%dT%H:%M:%S.%6N ".freeze), Process.pid] },
    :uuid ]
  config.logger = ActiveSupport::TaggedLogging.new(ActiveSupport::Logger.new(STDOUT))
```

If you use a load balancer, or have a monitoring service that hits a specific
path, and you don't want that logged, check out my [previous blog post about a
log silencer](/2015/03/25/log-silencer.html).
