---
published: true
title: What I learned this week #1
---

On this week's Ruby Rogues on [evaluating yourself](http://rubyrogues.com/171-rr-evaluating-yourself/), [Saron](https://twitter.com/saronyitbarek) mentioned blogging about what she learned during the week. So I'll give a go:

This week I started a new job at [Avvo](http://www.avvo.com), so a lot of what I learned was about them and their process. But even though there was nothing ground breaking, it was still awesome.

Here's what I learned this week:

* How to use the bus system! I haven't ridden on a bus since high school (10+ years), so this was my biggest source of anxiety. It went great, except for that day it took 2.5 hours to get to work. Woops.
* Used minitest. I don't like it yet, I'm missing a lot of my happiness from RSpec. Not sure what to do here.
* Set the database name in your mysql prompt. Add to your `.my.cnf` file under the `[client]` section: `prompt=\\d>\\_`. Presto! Your current database as the prompt. This should be the default.

What I want to learn more about:

* How to test services. We've got two rails projects, one with a JSON API and a database connection, the second that consumes the API and renders HTML. The problem I have is when I want to add a feature and want an integration test that uses the API. Like if I add a field to an existing resource. Currently we've got a mocked service locally. I want to know if there's a better way to integration test internal APIs.
