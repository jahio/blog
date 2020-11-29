+++
author = "J. Austin Hughey"
categories = ["javascript", "reactjs", "vuejs", "frontend"]
date = 2020-06-11T08:19:36Z
description = ""
draft = false
slug = "react-vs-vue-an-explorative-comparison-2020"
tags = ["javascript", "reactjs", "vuejs", "frontend"]
title = "React vs. Vue: An Explorative Comparison (2020)"

+++


I recently wrote an article for [Josh Software](http://www.joshsoftware.com/), a software consultancy that I began working for in April of this year. It's about the differences between React.js and Vue.js (version 2) and explores those differences from a variety of perspectives for the benefit of front-end developers.

Coming into the research phase of this article, I hadn't _seriously_ done anything with front-end since the days of [Netscape Navigator](https://en.wikipedia.org/wiki/Netscape_Navigator)! I've of course kept up with changes over the years, but I haven't really worked in front end exclusively or heavily since the mid to late 2000's. Since that point in time I've been building APIs, cloud and server automation, and designing databases - not making pretty things. This project was a refreshing change of pace that piqued my interest.

In short, there's no clear "X is better than Y" argument between these two technologies that makes any sense to an adult/seasoned developer whatsoever. There are ways in which React is definitely better positioned than Vue, but jumping to using React based purely on the comparisons for those specific factors is definitely jumping the gun. You'd be missing out on a lot of really great features that Vue brings to the table.

While as of this writing I haven't yet built even a small (full) app in React, I have done so with Vue.js, and I found Vue to be a very easy to use framework (whereas React is more of a _library_). Vue handles concerns like routing, UI rendering, and state management pretty-much out of the box, whereas React only handles one of those all the way, one some of the way and has zero facility for the third (that being routing). This makes it more complex to use because now you have to introduce at least one additional library with a DSL that has nothing to do with React (at least not necessarily), and this of course requires you to _know_ about that library, whatever it is, ahead of time, meaning that a person like me, coming from a strictly back-end background, wouldn't really know what library to reach for in this case (turns out for state management you want Redux, and for routing you want react-router).

You can see the original post on the Josh Software blog [here](https://blog.joshsoftware.com/2020/05/06/react-vs-vue-js-an-explorative-comparison/), and I've captured a PDF for long-term preservation and uploaded it to my blog [here](https://github.com/jahio/jahio.github.io/blob/master/assets/pdf/react-vs-vue.pdf).

