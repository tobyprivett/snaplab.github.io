---
layout: post
title: Resurrecting an old app
---

Today I did some work upgrading an old application to Rails 4 / Ruby 2. It was reasonably easy and there were only a couple of gotchas.

1. Rails 4 no longer uses the mass_assignment_sanitizer so I disabled any attributes set in the model, removed the config element and, well, it worked, which is kind of worrying because I want the attribute assignment to be protected but for no - it's not - something I will have to return to.

2. Postgres installation on Mac OS hasn't got any easier - actually this has nothing to do with a Rails upgrade and everything to do with working on my own machine rather than a corporate laptop. Which is a lovely feeling and deep mountain crevasses away from being a gotch.

So the app is resurrected and may well form part of my MVP.
