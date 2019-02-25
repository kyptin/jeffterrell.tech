{:title "Resources for Learning Clojure"
 :layout :post
 :author "Jeff Terrell"
 :tags ["clojure"]
 :toc false}

I'm suddenly finding myself in a situation where I need to help a lot of people get a crash-course in Clojure in preparation for a project we'll be doing this semester. Rather than prepare teaching materials myself, fun though that would be, I'll lean on the excellent resources already available. This post contains my current recommendations for how to learn Clojure.

<!-more->

_Note: I'm assuming a prerequisite of some programming experience. I'm not sure how much of a hard prerequisite that is. If you have a sense of that, I'd be interested to hear what you think._

## Install Clojure

Start with the official [Clojure Getting Started page](https://clojure.org/guides/getting_started) to install Clojure. Launch a Clojure REPL (read-evaluate-print-loop) with `clj`. Type `(+ 1 2 3)` and hit enter. You should see a result of `6`. You now have a (rudimentary but useful) tool for running Clojure code, which many experienced Clojurians still rely on as a central part of their workflow ([1](https://github.com/matthiasn/talk-transcripts/blob/master/Halloway_Stuart/REPLDrivenDevelopment.md) [2](http://blog.cognitect.com/blog/2017/6/5/repl-debugging-no-stacktrace-required)).

## Read _Clojure for the Brave and True_

I've seen the book _[Clojure for the Brave and True](https://www.braveclojure.com/)_ recommended a lot over the past year or two. It seems to be the recommended resources these days for learning Clojure. Perhaps a big part of that is that it's free to read online in its entiretly. It's also a lively, fun read.

Read the front matter, and read Chapter 1: Building, Running, and the REPL. Skip Chapter 2: How to Use Emacs, an Excellent Clojure Editor. Although I really enjoy Emacs (well, more specifically the Spacemacs distribution of Emacs), I've heard that this chapter is significantly out of date. I have alternate instructions for using an editor in the next section.

Next, Chapters 3 (a Clojure crash course), 4 (core functions), 5 (functional programming), and 6 (namespaces) are essential. Chapter 7 (evaluation) is important, but probably not essential. Chapter 8 is about macros, which I find fascinating---but you probably don't need it, at least not in the beginning.

The advanced topics (Chapters 9-13) are probably fine to skip for now, but it might be worthwhile to at least understand what they're about, so that you can recognize later if you encounter a situation where reading them might help.

## Picking an editor

Picking an editor is definitely a matter a personal taste. You can see what editors Clojurians use in the [State of Clojure 2018 Results](http://blog.cognitect.com/blog/2017/1/31/clojure-2018-results) (second question).

If you prefer a more full-featured IDE, [Cursive](https://cursive-ide.com/) is probably your best bet. It's free for noncommercial student use.

If you're more of a hacker or power user, you can try your hand at Emacs, Spacemacs, or Vim. Vim is what I cut my teeth on, and I still believe it has the best and most powerful text editing experience around with its modal editing interface. That said, there's a definite learning curve to get used to it. However, Vim's support for Clojure usually lags significantly behind that of Emacs. Fortunately, if you want Vim-style editing but with Emacs packages, there's [Spacemacs](http://spacemacs.org/), which is what I use.

In any case, don't get too bogged down on this step. A nice editor setup can make it really convenient to develop Clojure, because you can send bits of code to a running REPL process in a nicely integrated fashion. But it can also be tricky to set one up. If you get stuck, ask for help.

## Clojurians Slack

The [Clojurians Slack](http://clojurians.net/) is where lots of Clojurians hang out. I especially recommend the `#beginners` channel if you're starting out. You can get lots of help that way.

## 4Clojure

It's important to combine all the reading with some experience actually coding. A fun way to do that is with the exercises on [4Clojure.com](http://www.4clojure.com/). Once you solve a problem, you can see solutions from other users you follow, which is a great way to learn about other techniques and other core functions. (You can follow me, user `jeff_terrell`. I've solved all the problems, often prioritizing legibility over concision, although I don't necessarily stand by all my solutions.)

## Clojure by Example (added 2019-02-25)

[Aditya Athalye](https://github.com/adityaathalye) got in touch to recommend
[Clojure by Example](https://github.com/adityaathalye/clojure-by-example). I've
looked it over, and I agree that it deserves a mention in this post. Besides
the up-to-date [setup
instructions](https://github.com/adityaathalye/clojure-by-example#setup-instructions)
(and also some more resources on [choosing and setting up an
editor](https://github.com/adityaathalye/clojure-by-example#code-editor-and-tooling)),
it includes a series of tutorial examples teaching Clojure one piece at a time
in a well-organized way. Check it out.

## Anything else?

What would you add to my list? Any corrections, updates, or different opinions? Have you learned Clojure a different way that you would recommend? Please comment below if so.
