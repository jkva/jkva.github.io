---
layout: post
title: Event Sourcing at 010PHP Meetup in Rotterdam
category: meetup
tags: php, eventsourcing, meetup, rotterdam
---

At [meetup.com](http://www.meetup.com) I was alerted to a meetup on the subject of Event Sourcing 
at the local [PHP](http://www.meetup.com/010PHP) group. 

I'm not familiar with PHP, but we use Event Sourcing with Akka and Scala at work, so I figured it
would be an interesting evening nonetheless.

Upon arriving at the venue at [YouWe](http://www.youwe.com) (thanks for hosting!) I was surprised
by the apparent amount of non-regulars, it seemed I was not the only one just checking the group
out for the interesting talk subject.

After some interesting discussions on PHP, NodeJS and programming in general, the first speaker was
announced. [Herman Peeren](http://www.meetup.com/010PHP/members/13036883/), through a variety of
metaphors, explained the basics of Event Sourcing.

He started by mentioning the de facto reading material on the subject: 
[Implementing Domain Driven Design by Vaughn Vernon](https://vaughnvernon.co/?page_id=168) and
[Domain Driven Design by Eric Evans](http://books.google.nl/books/about/Domain_driven_Design.html?id=7dlaMs0SECsC&redir_esc=y).
After explaining the idea behind (The Law of Demeter)[http://en.wikipedia.org/wiki/Law_of_Demeter] and 
(Aggregate Roots)[http://martinfowler.com/bliki/DDD_Aggregate.html] within 
your domain, he told a story about a morning run through the park.

As he approached a tree, he noticed that that tree had lost numerous leaves the last time he passed it.
He mentioned this to the tree, and the tree, being magical, responded - "No I haven't, I'm a different tree!".
If our existence is a series of changes, where is the immutable self?

After being stopped by a police officer for crossing through a red light and claiming that "That wasn't him, that was
another state" and being asked to show his identification papers, it dawned - our *identity* is our permanent
immutable reference point. Equality is decided by the identity of an entity, not by its values at a point in time.

So if the state of an entity is a sequence of states `S[]`, from `S0` to `SN`, where each state is the difference between
the previous state and itself, we can record these changes as *events*. This answers the question stated by 
(Theseus' Paradox)[http://en.wikipedia.org/wiki/Ship_of_Theseus] - Theseus' ship is defined by its identity, and
any changes to it are state mutations applied to it.

Herman continued onto Functional Programming, known for not having mutable state (some hybrid languages excepted, but
let's not get into that) and explained that immutable state processing through recorded events can be used
together with a concept called *(CQRS)[http://en.wikipedia.org/wiki/Command%E2%80%93query_separation]* (coined by
Greg John) which sums up as:

* We define our aggregate roots
* We process commands to modify these through a *Command Side*
* Command validation can trigger zero or more *Events*
* These Events are persisted and can be replayed to achieve state at that point in time
* The Events are used to provide a separate *Read Side* which provides public state
* Read Side is never used by the *Command Side* for verification purposes, only internal event state

After speaking more on some differences between classic OO, (Emergent Behaviour)[http://en.wikipedia.org/wiki/Emergence]
and Functional Programming in PHP, it was question time.

A short break was called in, and afterwards it was time for (Frits-Jan Bakker)[http://www.meetup.com/010PHP/members/45692792/]
to elaborate on CQRS, especially the (Broadway)[http://labs.qandidate.com/blog/2014/08/26/broadway-our-cqrs-es-framework-open-sourced/]
framework they use at (Qandidate)[http://www.qandidate.com].

Frits-Jan's talk (there were no slides since he was filling in for another speaker, but that did not deter) consisted of 
a more in-depth explanation of CQRS, as well as a number of Best Practices on writing Event Sourced applications. Write 
applications that make sense, and make use of so-called "Domain Experts" to make sure that your event definitions are as
complete as possible, since your events are all that you are storing.

He did a great job on answering the audience's questions - why you would not just snapshot your event store all the time,
why they used MySQL to store their events, how the architecture scales, and more. Great talk!

Afterwards there was time for socialising but I had to duck out fairly early. It's a great group, would visit again!
