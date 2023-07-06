---
layout: post
title:  "Sometimes Shifting Left Is The Wrong Direction"
date:   2023-07-04 07:35:27 +0100
categories: tech
---

![Fish swimming right against the stream](https://github.com/Geoff-Lillis/geoff-lillis.github.io/blob/main/docs/assets/images/ShiftLeft.jpeg?raw=true)# When Shifting Left Is The Wrong Direction

Some time ago I worked on an app with a 40 minute integration test cycle. This is more than ample time to forget what you were working on and why. A reminder would come in the form of a red build status, letting you know that something somewhere in your changeset had broken the build and your teammates would very much appreciate you focusing your efforts on restoring a more calming green.

"Shift left!" would ring throughout the halls. Whenever a test would fail, we were told, we should seek to move the test earlier in the cycle. Doing so would make bugs be found earlier, faster, and cheaper.

I almost always agree.

We live in an imperfect world. Sometimes integration tests can't be replaced by unit tests or linting. Sometimes you're at the mercy of past architectural choices that make retrofitting unit tests intractable. Sometimes you have to evaluate other options.

## Shift Out

Resolving to reduce the runtime we started with historical analysis. There were multiple teams using this test pack and every run for the past year had been kept (to the detriment of storage costs somewhere no doubt): we had hundreds and hundreds of runs of many of the tests.

It's easy to get lost in a dataset this size so a couple of days passed before I realised some tests passed all the time. Every time. No matter what code changed, no matter if key infrastructure was down, over scores of branches and a whole year these tests always returned green.

Looking more closely I grouped them in categories:

* **Faulty tests**: These tests weren't working and always returned true
* **Obsolete tests**: These were testing for conditions that no longer existed
* **Fake tests**: Tests designed to always pass in an effort to increase code coverage statistics

Naturally items in the final category prompted a frank conversation with the committer and the reviewers involved. There's probably another blog post in how technical controls aren't the right tool to bring about cultural change, but I digress.

These tests added no value. They were shifted out of existence. Minutes were regained from the test run. Testing gaps were added to the backlog. Overall we recorded a win for the test pack duration.

## Shift Right

There were something in the region of 400 tests contributing to the 40 minute runtime. Digging into these, we found that half of this runtime was consumed by nine tests.

50% of the runtime was devoted to less than 2.5% of the tests.

We shifted these right.

One could argue that we shifted 391 tests left, but it's a stretch, and won't convince me to change this post's title.  
We created a second test run called 'slow tests', placed the offending nine in there, and ran it after the main pack. This led to an intended win. If anything failed in the main test pack folks got their feedback 20 minutes faster, and with a moderately higher chance of them recalling what they were working on when they committed.

## The Unintended Win

A week later found us still monitoring duration but with  no clear next focus. We were no longer working on improvements. With some surprise we saw the total pipeline duration drop  by four minutes. We decided to monitor for the remainder of the week, assuming we'd see reversion to the mean, then found ourselves again surprised when the duration dropped by a further five.                          
For a year the developers had known that they were losing 40 minutes multiple times a day to slow test runs. Reviewing 400 or so tests had seemed insurmountable, but we'd inadvertently painted a target on a much more manageable nine. The pent up frustration of waiting for feedback coupled with a clear, achievable target led to the formation of an impromptu crossfunctional team with a drive that initially bordered on fanatical, then crossed that border, established a settlement, and adopted the local customs and practices as their own.

All slow tests were replaced within ten days. When we were congratulated on our cunning use of psychology to enlist free help I had the awkward job of explaining that we weren't that smart. That said, I have learned from this happy accident.
