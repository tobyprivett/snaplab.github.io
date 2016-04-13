---
layout: post
title: Testing Phoenix models
---

Models in [Phoenix](http://phoenixframework.org) can be tested in two ways:

__With side effects__
`use Rumbl.ModelCase` when you want to hit the database. And you need to test the transformations actually happen. 

__Without side effects__
`use Rumbl.ModelCase, asnyc: true` when you don't want to hit the database. When it's enough to test how the queries are composed.
