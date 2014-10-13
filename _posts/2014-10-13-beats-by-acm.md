---
layout: post
title: Beats by ACM, a network jukebox with fair song queueing
---
[Beats by ACM](https://www-s.acm.uiuc.edu/beats/1104/) is a network jukebox that allows [ACM@UIUC](http://www.acm.uiuc.edu/) members to play music out of the speakers in the ACM Office. It's one of my largest personal software projects to date.

[![Beats screenshot](/images/2014/10/beats-screenshot.png)](/images/2014/10/beats-screenshot.png)

## How it works
Users log into the Beats web frontend and queue up songs to be played, which can be either songs that have been loaded into the application, or any video on YouTube. Songs in the queue are scheduled using an algorithm known as [packet-by-packet generalized processor sharing (PGPS)](http://dl.acm.org/citation.cfm?id=159914). This algorithm is intended for scheduling network packets, but I adapted it for music scheduling by equating songs to packets and song length to packet size.

PGPS allows for a service (in this case, play time on Beats) to be distributed fairly to multiple users. It takes into account song length, the user that queued the song, and the time at which they queued it to determine the order in which to play songs on the queue. This scheme allows for different users to be given different weights, which would grant them a larger or smaller proportion of a given amount of play time, but in my implementation all users are given equal weight. Thus, songs queued by different users will be scheduled in such a way that all users on the queue at a given point in time get a similar amount of play time per unit time, or in other words, a similar "rate" of Beats usage.

In Beats, however, users can also vote for songs, which increases their individual weights and can potentially bump them in front of nearby songs in the queue. This functionality is a feature of Beats, not part of the actual PGPS specification.

<center>
[![Beats artist view](/images/2014/10/beats-artist-screenshot.png)](/images/2014/10/beats-artist-screenshot.png)
<small>_Artist view. Album art support has [yet to be implemented](https://www.youtube.com/watch?v=LDzH7ecKt7U)..._</small>
</center>

## [The code](https://github.com/acm-uiuc/beats)
The Beats backend itself is written as a REST API server in Python using Flask. It uses [libVLC](https://wiki.videolan.org/LibVLC/) Python bindings for media player integration, and the Python library [Pafy](http://np1.github.io/pafy/) is used to retrieve YouTube stream URLs and metadata. Song metadata and queue data are stored in a MySQL database. The PGPS scheduler is implemented [here](https://github.com/acm-uiuc/beats/blob/master/scheduler.py).

The frontend is an AngularJS application largely written by [Zach Reizner](https://github.com/zachreizner), an ACM member who graduated last year and narrowly avoided the responsibility of maintaining the system. User authentication is handled by ACM's Atlassian Crowd server, which checks a user's credentials against the UIUC Active Directory server and also verifies that the user is an ACM member.

Beats replaced the Acoustics Media Player, a similar system that became unmaintained over time and was eventually retired last year. I began developing Beats in February, and deployed it at the beginning of this semester. It's been serving ACM faithfully since August, and currently has over 18,000 songs in its library. The software supports multiple players with separate queues and a shared music library, and thus Beats instances are currently deployed in two of the three rooms in the ACM Office. The project itself has already seen a number of pull requests, so I am hopeful that Beats will continue to be maintained after I graduate.
