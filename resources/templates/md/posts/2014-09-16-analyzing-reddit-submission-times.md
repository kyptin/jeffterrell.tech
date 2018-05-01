{:title "Analyzing Reddit Submission Times"
 :layout :post
 :author "Jeff Terrell"
 :tags ["analysis" "data" "reddit" "tool" "visualization"]
 :toc false}

I enjoy reading the [Data is Beautiful](http://www.reddit.com/r/dataisbeautiful/) sub-Reddit, which often has interesting and useful visualizations. The [official guide](http://www.reddit.com/r/dataisbeautiful/wiki/greatpost) has some tips for making a great post, including this one:

> The best time to post is generally between 12pm and 5pm EST (UTC–5). Other times also work well, but most of the successful posts on /r/dataisbeautiful were posted in that time range.

I thought I'd test that assertion. So I wrote a [simple tool](https://bitbucket.org/altometrics/reddit-scores) in Clojure and gnuplot that queries the Reddit API for a particular subreddit, groups recent submissions by the submission hour (UTC), and creates a chart displaying the percentage of high-scoring submissions per hour. Here's the current chart for DataIsBeautiful:

<img src="/img/reddit-high-scores-highlighted.png" alt="The percentage of /r/dataisbeautiful Reddit scores that were above some value, per hour." width="640" height="480" />

As you can see, the guide, highlighted in orange, was wrong! The best time to post, at least according to recent data, is actually between 8am and 12pm EDT (UTC–4), which is highlighted in green.

You can run the same analysis yourself on any subreddit by using [the tool I wrote](https://bitbucket.org/altometrics/reddit-scores). Pull requests welcome!

<h3>Update 2014-09-16 2:07 PM</h3>

The Data is Beautiful sub-Reddit has updated their guide to reflect the new data. Cheers!
