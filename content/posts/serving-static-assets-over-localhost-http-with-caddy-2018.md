+++
author = "J. Austin Hughey"
date = 2020-06-11T08:16:32Z
description = ""
draft = false
slug = "serving-static-assets-over-localhost-http-with-caddy-2018"
title = "Serving Static Assets Over localhost HTTP with Caddy (2018)"

+++


[Caddy](https://caddyserver.com/) is a great HTTP/2 server written in Go that you can set up rather easily on MacOS to serve static assets over `localhost`. This is a quick how-to showing how I personally have it set up for development/testing purposes.

### Install It

I use [Homebrew](http://brew.sh/) to install such things:

```
brew install caddy

```

This will install Caddy, _but won't create a configuration file for it._ By default, Caddy will serve HTTP assets in the directory you start it from if you do so in the foreground, but since I wanted this always running as a service, I needed to create a simple configuration file and use `brew services` to automate loading it with MacOS' `launchctl`.

### Load It As A Service

Let's use `brew services` to start it:

```
brew services start caddy

```

Now if you check on it using `brew services list` you'll probably see it as `started` but written in _yellow_. This is because the `launchd` configuration it uses relies on a configuration file that doesn't yet exist. So let's write one.

### Configure It

Now that it's "running", let's see if we can find where the `launchctl` plist file is that tells it how to start:

```
brew services list

```

You should see all running services under Homebrew here with a path to where the `launchctl` configuration plist is located. In my case, this was `/Users/jah/Library/LaunchAgents/homebrew.mxcl.caddy.plist`.

[...] <key>ProgramArguments</key> <array> <string>/Users/jah/.brew/opt/caddy/bin/caddy</string> <string>-conf</string> <string>/Users/jah/.brew/etc/Caddyfile</string> </array> [...]

The contents of that file reveal that Caddy is being told that the configuration file it needs (`Caddyfile`) is located in `/Users/jah/.brew/etc/Caddyfile`, but looking there, **there's no such file!**

So let's create one. Here's a minimal configuration for how to let it serve static files in a given directory that I just happened to already have on my machine:

```
localhost:8888 {
  gzip
  log /tmp/caddy.8888.log
  root /Users/jah/.www
}

```

#### Quick Tour of Directives

* `localhost:8888`: Tells Caddy where to bind and on what port.
* `gzip`: standard HTTP command telling Caddy to compress the stuff it sends downstream. Widely used and has been for years.
* `log`: I set this to `/tmp/caddy.8888.log` because any issues I have with Caddy, I'll debug in real time and I don't want log files eating up huge amounts of space on disk. Anything under `/tmp` gets nuked every time you reboot, so rebooting my developoment machine (which I do once or twice a week anyway) will clean up the logs for me automatically.
* `root`: This is where Caddy looks for files to serve. In this case, `$HOME/.www` is a symlink to another directory I keep backed up.

### Restart Caddy

Now that our configuration is in place, we can restart Caddy to have it pick things up correctly:

```
brew restart caddy

```

Now if you go to [http://localhost:8888/](http://localhost:8888/) you should see a 404 if you have nothing in `$HOME/.www`, otherwise you should see the contents of `index.html` (create one if you haven't already).

### Symlinks Make Things Easier

I've created a more permanent `.conf` directory somewhere that's regularly backed up, and symlink'd `/Users/jah/.brew/etc/Caddyfile` to `.conf/Caddyfile` so my configuration is always up to date. This makes it easier to work with the configuration without having to hunt down the path again six months from now when you need to make changes and forgot where you had all that to begin with!

