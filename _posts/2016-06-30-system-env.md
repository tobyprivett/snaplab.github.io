---
layout: post
title: Oh, Phoenix, where is my System.Env?
---

I have a small elixir / phoenix app running on a [dokku](https://github.com/dokku/dokku) instance. Nice and easy to setup and deploy. Much like heroku. But cheaper. [Way cheaper](https://m.do.co/c/cb8aadf33195). Yes, that was / is a [referral link](https://m.do.co/c/cb8aadf33195).

The other day I realised that my little app would be better off behind some basic authentication for a while. At least until it’s time to pull the covers off. So I went around looking for a nice and easy way to add this. I came across [basic_auth](https://github.com/CultivateHQ/basic_auth). And set about rolling it into the app.

In its most basic form, all I needed was to add a plug to the router pipeline:

```
plug BasicAuth, realm: “Under Construction”, username: “admin”, password: System.get_env(“DEV_PWD”)
```

Push, deploy, restart. And add the environment variable to the dokku config

```
dokku config:set myapp DEV_PWD=foobar
```

And restart the app.

But when I go to the home page, type ‘admin’ in the username field and leave the password blank, I am in. Bollocks. Is there something up with the basic_auth library?

Stupidly, but at the same time, educationally, I clone the basic_auth library and try to recreate the empty password behaviour in the test suite. But I can’t.

Let’s rethink the implementation. In the meantime, I went about cleaning up my code and pulled the config out to the [prod.secret file](https://github.com/SnapLab/compassIO/commit/45500ad0a6c373c78e1330949c2cf582d36fb55b). Astute readers (well, those who visit the last link) will notice my comment about looking for the “prod.secret file within the dokku instance”. Yep. I did spend some time grepping around the server, looking for a config file, forgetting that this is dokku, and I’m not meant to know about the file system.

Getting back on track, I now have a configurable username and password:

```
username: System.get_env("DEV_USER"),
password: System.get_env("DEV_PWD")
```

And it’s getting worse. I can log in to the app without __any__ authentication. Hmm. Perhaps I should go and read the github issues. How about the one I ignored about [supporting runtime credential configuration](https://github.com/CultivateHQ/basic_auth/issues/7). Surely that doesn’t apply to me, as I’m running dokku and the configuration tool always restarts the app.

But I’m also running Elixir / Phoenix and the app is compiled on deploy. Compiled as in __compiled along with any environment variables__. If I want to set / unset an environment variable, I have to recompile the app. A restart ain’t gonna do it. This is not rails.
