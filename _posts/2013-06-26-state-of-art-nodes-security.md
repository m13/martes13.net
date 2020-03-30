---
layout: post
title: "State of Art :: node's security"
post_id: 201
categories: 
- challenges
- fun
- graph
- history
- nodejs
- shrinkwrap
- tools
---

These days I have been working on designing an API REST in NodeJS, discovering some security bugs in our third party libraries. I am very worried because you cannot avoid using them, and you know that if you discover some bugs, there will be other, more dangerous bugs there.

I am surprised how fast NodeJS spread, but it is easy to understand when you watch some 
[Google.IO talks](http://www.youtube.com/watch?v=FrufJFBSoQY). The V8 engine is wonderful. It was developed from scratch, focusing on performance as much as possible. If we compare it against an Apache webserver (multi-process/multi-thread), they have compressed all the different independent layers into one, being able to optimize the whole stack, but removing all the "sometimes useful" features (like "data isolation").

From my point of view, we have to move in this direction (reducing your hardware costs is a must), but it comes with new challenges for developers. It reminds me of Android when I was researching the platform, because in the end, both of them have bugs well-known in """deprecated""" software (like Apache?).

And that is why last week I joined to the group 
[nodesecurity.io](http://nodesecurity.io/) . I want to contribute to and give advice to the community of NodeJS about the common mistakes and not so common when you develop an application. Very often they are technical bugs, just because we could not use an ORM or good enough Authentication/Authorization layers, or because we used functions like EVAL, but in some occasions they are functional bugs that you cannot solve with a patch because it depends on the behavior/workflow of your application.

I think that security is very close to read metrics, apply automation, testing, and then developing tools that help you to discover bottlenecks or shortcomings. Thanks to github, the dependency manager and NodeJS itself, it is easy to discover potential risks (and exploit them).

So, because this is the first post, I am going to start writing about the dependency manager.


# npm registry

The 
[project](https://github.com/isaacs/npmjs.org) uses a 
[CouchDB](https://npmjs.org/doc/registry.html) database to save all the 
[applications](http://isaacs.iriscouch.com/registry) & 
[users](http://isaacs.iriscouch.com/_users/). Because we lack privileges if we try to retrieve information from these databases directly, we can 
[take profit of the proxy](https://github.com/isaacs/npmjs.org/blob/master/registry/rewrites.js) and download both database with these urls: https://registry.npmjs.org/-/all/ && https://registry.npmjs.org/-/users/ . Why would you want >44.000 emails address from the same place? I propose a 'spear phishing' experiment, but not today.

We also can draw a graph from the application database like this:

[![Graph by Libreoffice4](/images/2013/06/graph.png)](/images/2013/06/graph.png)

We can see that the probability to use non-updated software is considerable, and I am sure that you agree that it directly increases the risk. We should check other factors like "time of live", "versions released", "community", but the graph gives a first idea. Some of you will think that a stable version is good enough to be used, but remember that we are speaking about NodeJS. One year ago did we have v0.6 or v0.4? For example, I could not use node-gd/gd because they did not compile.


## 0.0.1

Because there are some tricks in npm that we should look at when we want to deploy and run our application, I developed a script that we could use whenever to check about some information of our packages. Feel free to use and append to your hooks of git (pre-commit), and... leave a comment if you think that other features would be good :) really appreciated!


[https://gist.github.com/m13/0c564f93eefb97155302](https://gist.github.com/m13/0c564f93eefb97155302)

Features:

- Check npm-shrinkwrap.json file

- Check if you have change the npm config register variable

- Check how many dependencies and sort them by last updated