---
layout: post
title: 'Hackathon project: Khan Academy office dashboard'
---
![KA dashboard photo](/images/2014/07/ka-dashboard-photo.jpg)

This past weekend was the Khan Academy Healthy Hackathon. Check out [Ben Kamens's blog post](http://bjk5.com/post/26810034751/how-we-ran-the-first-khan-academy-healthy-hackathon) for details about what exactly the Healthy Hackathon is and what makes it special, but in a nutshell, we were required to be "healthy hackers." In other words, we had to eat healthily (no junk food) and sleep well every night. To that end, everyone is kicked out of the office at 11:45 pm and let back in the next morning at 9:30 am.

My team's goal was to make "a beautiful new 'KA today at a glance' dashboard" for our soon-to-be new office. Each member of my team was in a different part of the US &mdash; Mountain View, Princeton, and Boston &mdash; but we still managed to be very productive thanks to science (read: HipChat). After three days of long-distance hacking, this is what we ended up with:

## The Khan Academy Dashboard (a.k.a. the KAshboard)

[![KA dashboard screenshot](/images/2014/07/ka-dashboard-screenshot.png)](/images/2014/07/ka-dashboard-screenshot.png)

The KAshboard is essentially a web application optimized for a 1080p display. The web frontend is built entirely with [React](http://facebook.github.io/react/), and the backend uses [Node.js](http://nodejs.org/) and [Express](http://expressjs.com/). The backend exists mainly to make the variety of API calls necessary to retrieve all the relevant information (Twitter, GitHub, Google BigQuery, etc.). It also does some caching for website analytics data obtained using BigQuery.

Here are some details on a few of the widgets on the dashboard.

<dl>
<dt>Fingers crossed</dt>
<dd>All of our commits require <a href="https://secure.phabricator.com/book/phabricator/article/differential_test_plans/">test plans</a> in the commit message. When a change is either trivial or not testable locally, we usually write "Fingers crossed" for the test plan.</dd>
<dt>Khan Academy Stories</dt>
<dd>The widget at the top right cycles through recent <a href="https://www.khanacademy.org/stories">stories</a> submitted to Khan Academy. We did some <a href="https://github.com/cjfuller/KAshboard/blob/400888106aacde36b61a76b9266a8344655b49ba/server/app.js#L181">really simple spam filtering</a> here because no, none of us are interested in applying for a loan at the moment.</dd>
<dt>Current experiments</dt>
<dd>This widget showcases ongoing experiments on <a href="http://www.alangpierce.com/blog/2014/07/07/bigbingo-khan-academys-new-bigquery-backed-a-b-testing-framework/">BigBingo, our A/B testing framework</a>.</dd>
</dl>

The KAshboard source code is available on [GitHub](https://github.com/cjfuller/kashboard). Shout-out to [Colin Fuller](http://cjf.io/) and [Tony Liu](https://github.com/tloinuy) for hacking from the East Coast!

As an aside, I also made my [first real contribution](https://github.com/chrisdickinson/beefy/pull/54) to an open source project during the hackathon. Woohoo!
