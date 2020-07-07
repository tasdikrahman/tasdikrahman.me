---
layout: post
title: "Rails logging"
description: "Rails logging"
tags: [rails, logging]
comments: true
share: true
cover_image: '/content/images/2020/07/rails_logging.png'
---

If you are on rails, you would have noticed that the rails logs which you get by default are quite verbose and spread across multiple lines, even if the context is of processing just one line of log. 

Let's take the example of a simple health check controller which you would add to just make the health check pass for your rails app deployed on kubernetes

```ruby
# app/controllers/health_check_controller.rb
class HealthCheckController < ActionController::Base
  def ping
    render json: { success: true, errors: nil, data: 'pong' }, status: :ok
  end
end
```

and for the config (shown for the development evironment for )

```ruby
# config/environments/developement.rb
Rails.application.configure do
    config.log_tags = [:request_id]
    config.log_level = :debug
end
```

A simple route for the `GET` verb, to call the #ping action in the HealthCheckController

```ruby
# config/routes.rb
Rails.application.routes.draw do
    get '/ping', to: 'health_check#ping
end
```

This is what the logs would look like for the route `ping`

```
[my-app-fbf8d7bfc-wk5cd] I, [2020-07-01T07:19:05.007174 #1]  INFO -- : [ec0ad0ba-dfb2-419b-bd81-5feb7dacb308] Processing by HealthCheckController#ping as HTML
[my-app-fbf8d7bfc-wk5cd] I, [2020-07-01T07:19:05.007874 #1]  INFO -- : [ec0ad0ba-dfb2-419b-bd81-5feb7dacb308] Completed 200 OK in 0ms (Views: 0.2ms)
[my-app-fbf8d7bfc-wk5cd] I, [2020-07-01T07:19:05.290929 #1]  INFO -- : [86332306-62e4-412e-a690-eee8253ab1c8] Started GET "/ping" for 10.177.3.1 at 2020-07-01 07:19:05 +0000
[my-app-fbf8d7bfc-wk5cd] I, [2020-07-01T07:19:05.292363 #1]  INFO -- : [86332306-62e4-412e-a690-eee8253ab1c8] Processing by HealthCheckController#ping as HTML
```

You can notice a few things here, 

- the logs are spread over multiple lines, adding to the difficulty in debugging the whole request/response flow.
    - given you would be pushing to your logging platform, let's say EFK, which would allow you to do full text search, the configuration to have the request-id for each log would come handy. A little better than not having anything at all. 
    - if you are not pushing to any logging platform, then you would be debugging this by tailing the logs of the rails app somewhere, if deployed to kubernetes, doing a [kubetail](https://github.com/johanhaleby/kubetail) or if in a VM, sitting inside the VM and then tailing the logs of each and every application server instance. But then the first step here would be to obviously have centralized logging.
- extremely verbose, hindering debugging.
- no clear way to parse the logs as the logs format are non-standard.

## Ways to improve this

Taking things one at a time, you can compact out the log so that it stays meaningful at the same time too, by using something like [lograge](https://github.com/roidrage/lograge). There are a bunch of other alternatives like [https://logger.rocketjob.io/](https://logger.rocketjob.io/) and [https://github.com/shadabahmed/logstasher](https://github.com/shadabahmed/logstasher), but I went ahead with lograge since it has been around for sometime now and has a larger adoption, my use case of what I required to do worked, hence I didn't dig too much on this, more on this later. 

Add lograge in your `Gemfile`

```
...
gem 'lograge', '~> 0.11.2'
...
```

and do a `$ bundle`

```ruby
# config/environments/developement.rb
Rails.application.configure do
  config.lograge.formatter = Lograge::Formatters::Json.new
  config.lograge.enabled = true
  config.lograge.base_controller_class = ['ActionController::Base']
  config.lograge.custom_options = lambda do |event|
    exceptions = %w(controller action format id)
    {
      request_time: Time.now,
      application: Rails.application.class.parent_name,
      process_id: Process.pid,
      host: event.payload[:host],
      remote_ip: event.payload[:remote_ip],
      ip: event.payload[:ip],
      x_forwarded_for: event.payload[:x_forwarded_for],
      params: event.payload[:params].except(*exceptions).to_json,
      rails_env: Rails.env,
      exception: event.payload[:exception]&.first,
      request_id: event.payload[:headers]['action_dispatch.request_id'],
    }.compact
  end
end
```

`config.lograge.base_controller_class = ['ActionController::Base']`, this assumes that each controller will be inheriting from `ActionController::Base`, you can include any other controller which doesn't inherit from Base

along with 

```ruby
class ApplicationController < ActionController::Base
    protect_from_forgery with: exception

    def append_info_to_payload(payload)
        super
        payload[:host] = request.host
        payload[:remote_ip] = request.remote_ip
        payload[:ip] = request.ip
    end
end
```

now if you do a

```
$ curl -I localhost:3000/ping | grep -i "request"                                                                                               
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
X-Request-Id: 4967a677-ab86-4a10-8a01-ea520951cf46
```

and check the logs of your rails app

```json
{"level":"INFO","progname":null,"message":"{\"method\":\"GET\",\"path\":\"/ping\",\"format\":\"*/*\",\"controller\":\"HealthCheckController\",\"action\":\"ping\",\"status\":200,\"duration\":8.36,\"view\":0.22,\"db\":0.0,\"request_time\":\"2020-07-07 16:10:17 +0530\",\"application\":\"MyApplication\",\"process_id\":7869,\"params\":\"{}\",\"rails_env\":\"development\",\"request_id\":\"4967a677-ab86-4a10-8a01-ea520951cf46\"}"}
```

Now the whole log line captures a lot of metadata so that you can debug for each log line, you can also notic that one of keys present in the log line is the `request_id` present, in this way, what is happening is, you have a unique trace id for for your particular request, which you can do a full text search on your logging platform for to trace the whole flow. 

To extend this further, what one can do is capture the `request_id`, and pass it along the controller's flow, to capture it, you can simply do a `request.request_id` if you are doing a `Rails.logger.{info|debug}` you can use to log certain things, when you are logging, you can also log the request-id captured, this way, the request_id's generated would now also be propagated to the custom logs which you would be adding to your application. 

You would also be able to capture additional information like host, remote_ip, ip of the rails app servicing it. by simply doing `request.host`, `request.remote_ip`, `request.ip`, `request.request_id`

### Why not use lograge for even the custom logger?

Lograge doesn't support [this](https://github.com/roidrage/lograge/issues/136). 

### What should I do now to add structured logging for my custom logs

You can add a custom logger for your application and initialize it in your application config for the environments wherever you want it. 

only thing you need to add is 

```ruby
# app/logger/log_formatter.rb
class LogFormatter < ::Logger::Formatter
  def call(severity, time, program_name, message)
    message = '' if message.blank?
    severity = '' if message.blank?

    {
      level: severity,
      progname: program_name,
      message: message,
    }.to_json + "\r\n"
  end
end
```

```ruby
# config/environments/developement.rb
Rails.application.configure do
    config.log_formatter = LogFormatter.new
end
```

and just to test the above out

```ruby
# app/controllers/health_check_controller.rb
class HealthCheckController < ActionController::Base
  def ping
    Rails.logger.info("bazbar")
    render json: { success: true, errors: nil, data: 'pong' }, status: :ok
  end
end
```

## How will my logs look like after this? 

```
{"level":"INFO","progname":null,"message":"bazbar"}
{"level":"INFO","progname":null,"message":"{\"method\":\"GET\",\"path\":\"/ping\",\"format\":\"*/*\",\"controller\":\"HealthCheckController\",\"action\":\"ping\",\"status\":200,\"duration\":8.36,\"view\":0.22,\"db\":0.0,\"request_time\":\"2020-07-07 16:10:17 +0530\",\"application\":\"MyApplication\",\"process_id\":7869,\"params\":\"{}\",\"rails_env\":\"development\",\"request_id\":\"4967a677-ab86-4a10-8a01-ea520951cqw3\"}"}
```

That's all folks

## References

- [https://github.com/roidrage/lograge](https://github.com/roidrage/lograge)
- [https://www.paperplanes.de/2012/3/14/on-notifications-logsubscribers-and-bringing-sanity-to-rails-logging.html](https://www.paperplanes.de/2012/3/14/on-notifications-logsubscribers-and-bringing-sanity-to-rails-logging.html)
- [https://github.com/BaritoLog](https://github.com/BaritoLog)
- [https://stripe.com/blog/canonical-log-lines](https://stripe.com/blog/canonical-log-lines)
- [https://github.com/roidrage/lograge/issues/136](https://github.com/roidrage/lograge/issues/136)
- [https://medium.com/better-programming/ruby-on-rails-single-line-logging-5a76852de1d2/](https://medium.com/better-programming/ruby-on-rails-single-line-logging-5a76852de1d2/)

## Credits

- Image credits [https://pixabay.com/photos/logs-timber-wood-logging-lumber-690888/](https://pixabay.com/photos/logs-timber-wood-logging-lumber-690888/) and [https://pixabay.com/photos/logs-timber-wood-logging-lumber-690888/](https://pixabay.com/photos/logs-timber-wood-logging-lumber-690888/)