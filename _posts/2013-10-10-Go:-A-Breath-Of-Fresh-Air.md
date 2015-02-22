---
layout: post
title: "Go: A breath of fresh air"
tags : [programming,go,algorithms]
---

I've been experimenting with [Go](http://golang.org/) on and off for the past couple of weeks. I come from a Web Development background but C has always been my language of choice for pet projects as I like the "bare-metal" experience,
even though most applications I would write would most likely perform just as well in other higher level languages&hellip; I just like C.

I had seen a lot of buzz around Go over the past couple of years and with a recent change in career path I decided to spend some time and start to play around with it.

![](/assets/images/gopher.png)

To get familiar with Go, I started to implement common algorithms and data structures. You can find them in my [goalg](https://github.com/eddie/goalg) repository on github. As the README says, be-ware.. This code isn't production ready and in most cases likely to be broken or half-implemented.

# Slap on the wrists

One of the many benefits of coding in Go is the strict rules enforced when running or building code. Granted, this exists with the majority of other languages in the form of lint programs. But the fact it's built into the core is great. After a few slaps on the wrist you eventually stop making messy mistakes (unused variables, incorrect typings, unused imports) and the reward is substantial. You get cleaner, streamlined code and learn to improve your techniques as a programmer. 

# Hit the ground running

![](/assets/images/gorun.png)

When I get an idea for something I want to work on, if time permits I usually just dive straight in and start working on a prototype. My usual process is sketch something out on paper, write a small prototype in some interpreted language and then when I'm happy with structure, depending on the project I'll move on over to C (probably crazy but who cares!)


I would then find my self going through a set of hurdles and common mistakes to get my project up and running, part of me would want to go and write it in another language, the other part was stubborn. After eventually getting up and running, I'd fall into the usual SIGSEGV territory (Uh-oh, pointer assignment on automatic variable), Once again I should have known better.

# Best Friends

![](/assets/images/friends.png)

This is where Go steps in and wins my heart. I no longer have to worry (too much) about memory management and go has a fantastic [standard library](http://golang.org/pkg/) with the source quickly available for each package. A great learning resource and I can start building projects with different requirements often using only the standard library. If I can't find the solution built-in, the [go-wiki](https://code.google.com/p/go-wiki/wiki/Projects) usually contains what I'm looking for.

# Using Go Packages

A little off-focus but thought I would share something I initially had problems with. Package management was a little confusing but once I got to grips with it everything started to fall into place. There is extensive documentation on the correct way to manage and setup go projects, I just wanted to get stuck in. [How to Write Go Code](http://golang.org/doc/code.html) covers the recommended way of starting a project.

Given that I had imports from a VCS repository, I would import them into my project like so:

{% highlight go %}

package main

import (
  "fmt"
  "github.com/huandu/facebook"
  // ... 
)

{% endhighlight %}

From there, I set the GOPATH to my current project directory and ask go to retrieve packages specified in my imports. I like to contain my projects dependencies with the rest of my project. We can now run our program with dependencies satisfied.

<a class="post-category post-category-vim" href="#">Warning</a> This probably isn't the best way to go about project setup, but for now it works whilst I spend time familiarizing with Go.

{% highlight bash %}
cd my-go-project

# Change go bath to project path (I like to keep dependencies along side project)

export $GOPATH=`pwd`

# Pull any VCS imports
# Package source will be downloaded to `pwd`/src

go get .
go run my-program.go
{% endhighlight %}

# Whats next?

There's still lots of exciting areas I need to cover with Go and have only scratched the surface. My main areas of study when I have time will be:

- Concurrency and Channels
- [Interfaces](http://golang.org/doc/effective_go.html#interfaces)
- The build process and packages in more depth
- [Memory Model](http://golang.org/ref/mem)
- Explore more of the [standard library](http://golang.org/pkg/)

Go really has been a breath of fresh air (excuse the Cliché) I can write flexible code with great support and still maintain that "bare-metal" feeling. Go is also developed by some of my favorite engineers and I look forward to digging deeper and learning more. I can see Go eventually become my new language of choice for small and large scale projects. If you haven't yet experimented with go, checkout the interactive tutorial over at [golang.org](http://golang.org/).



