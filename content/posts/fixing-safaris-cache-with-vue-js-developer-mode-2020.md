+++
author = "J. Austin Hughey"
categories = ["frontend", "javascript", "vuejs"]
date = 2020-06-11T08:19:10Z
description = ""
draft = false
slug = "fixing-safaris-cache-with-vue-js-developer-mode-2020"
tags = ["frontend", "javascript", "vuejs"]
title = "Fixing Safari's Cache With Vue.js (Developer Mode) (2020)"

+++


I happen to like Apple's [Safari](https://www.apple.com/safari/) browser, and I'd like to use it when developing stuff with Vue.js. I ran into a problem with this, however: Safari would cache the response from the live-reload development server on localhost, so that when I made changes it didn't update, even when hitting the reload button. I _think_ I've solved this though with a `Cache-Control` header configued in the project's `vue.config.js` file.

### tl;dr

Put this in a file at project root called `vue.config.js`:

module.exports = {  devServer: {    headers: {      'Cache-Control': 'no-store'    }  },  chainWebpack: (config) => {    if (process.env.NODE_ENV === 'development') {      config.plugins.delete('preload')    }  }}

Kill the running `npm run serve` process then relaunch it, and reload the page in Safari. Live reload now seems to be giving us correct information.

### The problem

Say you did this:

1. `npm run serve`
2. Load `localhost:8080` in Safari, see your Vue app
3. Make some changes and save

You would _expect_ live reload to fire and those new changes to be picked up. I mean hey, it works in like _every other browser out there_, why would Safari be any different?

Well, it _is_ different, for some damn reason. Doesn't really matter why, all that matters is that it won't do what you want it to do. If you do the exact same thing, but substitute "Firefox", "Opera" or "Chrome" for "Safari" above, it works as expected. What the hell?

### The Cache-Control Header

Caching strategies obviously differ in the aforementioned browsers vs. Safari for something running in development mode. I'm not interested in the specifics right now; all I care about is telling Safari **not to cache anything** from my dev server running on `localhost:8080`. In theory this should force Safari to re-initialize everything in the DOM when changes are made.

My theory is that this will solve the problem. At least, I hope it will.

So to do that, we need to configure [webpack-dev-server](https://webpack.js.org/configuration/dev-server/), which `vue-cli` is using under the hood to run our live-reload dev server. Turns out you can do this with a JavaScript configuration file named **vue.config.js** at the project root. It needs to export a module for `devServer` with a `headers` object setting `Cache-Control` to `no-store`:

module.exports = {  devServer: {    headers: {      'Cache-Control': 'no-store'    }  }}

You can read more about the [Cache-Control Header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control) on MDN. Basically it's an HTTP header that tells the client (browser, whatever) how to cache the information it gets back. In this situation, we're setting it to `no-store` which is the configuration equivalent of "don't store anything at all, dammit!".

### Deleting the 'preload' Plugin

I also ran across [this comment](https://github.com/vuejs/vue-cli/issues/2509#issuecomment-474959168) in `vue-cli`'s GitHub Issues that suggests deleting the `preload` plugin also helps. So to do that, just add another object to `module.exports`:

module.exports = {  devServer: {    headers: {      'Cache-Control': 'no-store'    }  },  chainWebpack: (config) => {    if (process.env.NODE_ENV === 'development') {      config.plugins.delete('preload')    }  }}

### Verifying the Header Change

Create the configuration file with the above code, save it, then CTRL+C your running `npm run serve` process, then start it again (`npm run serve`). Now in another terminal tab/window, run:

```
$ curl -I http://localhost:8080

```

You should see output similar to the following:

```
HTTP/1.1 200 OK
X-Powered-By: Express
Cache-Control: no-store
Accept-Ranges: bytes
Content-Type: text/html; charset=UTF-8
Content-Length: 785
ETag: W/"311-JwxZQsNJwurfpMdtZree0SxO5ro"
Date: Fri, 03 Apr 2020 19:32:33 GMT
Connection: keep-alive

```

As you can see here, the third line shows our `Cache-Control` header with the configured value.

### Don't do this in prod.

It should go without saying that doing this in production is a BadIdea™. You'll want to implement more sophisticated caching strategies for production-release code so that you can minimize the volume of unnecessary network traffic going into your app.

