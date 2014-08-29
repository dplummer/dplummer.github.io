---
published: false
title: What I learned this week #2
layout: post
---

Here's what I learned this week:

* The `$&` variable in Ruby contains the last full match of a regular expression:
  ```
  [1] pry(main)> str = "cats 1234"
  => "cats 1234"
  [2] pry(main)> str =~ /\d+$/
  => 5
  [3] pry(main)> $&
  => "1234"
  ```
* Don't use open buffers in vim as a stash when merging. Just save the file and use `git stash`.

What I want to learn more about:

* 
