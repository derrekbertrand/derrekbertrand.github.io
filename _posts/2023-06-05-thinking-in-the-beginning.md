---
layout: post
title: Thinking in the Beginning
banner_caption: Fox channel 5 asking if you know where your children are
---

It's 10PM on a Saturday and your application is unresponsive. Do you know where your engineers are?

A flurry of Slack messages and video calls ensues. After a short while, an engineer restores service with a temporary
fix. A meeting is put on the calendar for first thing Monday morning. Wouldn't it be great if we had known about the
problem in advance? Well... we did.

## The Queue

In one particular incident I've experienced, we wrote up a spec and had multiple meetings out of concern for our queues.
It is possible that the queues could become overloaded and that the application would become slow as a result.

One fine day, overloaded they became. The queues became backed up, yes. The website, however, was **completely offline**.
Impatient users had caused such a traffic spike, that we ran out of CPU cores on our database server. The fallout was a
service blackout, thousands of dollars in emergency upgrades, and a massive loss of faith from clients.

We prioritized features instead and didn't fix the problem, even though we knew about it months in advance. Nice. ✨

> *The sage anticipates things that are difficult while they are easy,  
> and does things that would become great while they are small.*  
> &#8212; Laozi, Dao De Jing (63)

## The Invoices

In another life we had a service that was billed by metered usage. Our upstream provider was rather particular about how
we used sub-accounts on their service. Our clients were even more particular about how their bills were divided, and
this caused some friction in our schema design. With so many conflicting ways to slice the data, we hemmed and hawed
over this issue for months. In favor of expedience, though, we went ahead and launched our new feature set.

Some time later, our provider started enforcing regulations with zero notice. The result was a very long weekend, angry
clients, and **many** emergency fixes to our invoicing system. Again, we knew in advance, we just failed to act.

## The Solution

> One who lightly promises is sure to keep but little faith;  
> one who is continually thinking things easy is sure to find them difficult.  
> Therefore the sage sees difficulty even in what seems easy,  
> and so never has any difficulties.  
> &#8212; Laozi, Dao De Jing (63)

In the case of the queues, feature work should have been removed from the roadmap and replaced with stress testing and
performance upgrades. In the case of the invoices, we could have prepared a secondary pipeline in advance - it only took
about 6 tries to hotfix. The problem here is short-term bias by stakeholders that do not understand the stakes. The solution is engineering
teams owning their software. It is an engineering team's job to ensure the stability and performance of their systems.

Failure to take ownership of your codebase is not an option, because you'll still be the one rewriting invoice queries
at 10PM on a Saturday. It isn't enough to be available for failure, you must plan for it and prepare yourself
accordingly.

---

You can find translations from the Dao De Jing on the [Chinese Text Project](https://ctext.org/dao-de-jing#n11654). The
quotes in this post are from chapter 63: "Thinking in the Beginning".
