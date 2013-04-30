---
layout: post
title: "Reconciling ReST, CQRS and Event Sourcing"
description: ""
category: software
tags: [CQRS, ReST, architecture]
---

I decided to write this post because it was getting really hard to discuss 140 characters at a time on Twitter, and I just want to get some thoughts down before I go looking for other people's solution to the problem.

Over the last couple of years I've learned a lot about ReSTful web API's (the kind that use HATEOAS, not the namby-pamby "it's HTTP therefore it's ReST" variety) and have also been intrigued by the simplicity of CQRS+ES to address scalability. I've played with both in personal projects, and done a bit of each at work too (although not as much as I'd like), and have wondered how to bring the best of both together. At DDD10 yesterday I attended [Neil Barnwell](https://twitter.com/neilbarnwell)'s [CQRS and Event Sourcing... how do I actually DO it?](http://developerdeveloperdeveloper.com/ddd10/ViewSession.aspx?SessionID=927) and [Jacob Reimers](https://twitter.com/jjrdk)' [Taking REST beyond the pretty URL](http://developerdeveloperdeveloper.com/ddd10/ViewSession.aspx?SessionID=950). In the latter Neil asked this very question, which got me thinking about it again.

After a bit of discussion on Twitter, Neil identified the problem as this:

{% tweet https://twitter.com/neilbarnwell/status/242192811892539393 %}

CQRS with event sourcing only really comes into its own with a task-based UI; instead of simply updating (in the CRUD sense) a customer's address you would send a command saying the customer is moving to a different address, or perhaps a different command if merely correcting a typo in their current address. This captures the intent as well as the change, which allows for far more interesting things to happen later on as that intent is captured in the event raised as a result of processing the command.

HATEOS in a ReSTful web API decouples the client from the server. The client doesn't need to know what the application rules allow it to do - instead the server guides the client along a path by telling it which possible next steps that it might like to take, just as a website guides a user through it by providing links to click in the browser. If the server business logic changes then the client doesn't necessarily need to be updated; the server will just change the links it provides to the client to reflect the new valid next steps that the client could take.

In my mind CQRS+ES doesn't allow for that loose coupling between client and server because the client needs to know about the commands it can send, and they go far beyond the HTTP verbs GET, PUT, DELETE and - arguably - POST. This is the problem that Neil pointed out.

Commands are simply messages with all of the information needed for the command to be executed. So in order to distinguish between a MoveToNewAddress command and a FixTypoInAddress command (badly worded examples, but hey-ho) the client needs to know about each of them and what parameters they require. If these change then either the client needs to change to match or the server needs to maintain support for old versions of the commands. If we stick to the ReSTful style then only the HTTP verbs are allowed and, as we shouldn't represent verbs as resources, the client can't discover new commands by being given a new link to follow.

On the read side of CQRS+ES things aren't so bad because the server can represent entities as resources to support a ReSTful API, but it's not obvious how PUT and DELETE could work on those resources while still capturing the intent.

## Representin' ##

My initial thought was that you could represent the commands themselves as resources and POST them to a collection:

    POST /commands

The response could be a command resource allowing the client to poll to see if it has completed yet, but that could be hard to do depending on how the command pipeline is implemented. I seem to remember one of Greg's articles on CQRS+ES suggested a UI with a list of outstanding commands, but I don't think this is common practice because it would often be hard to get this information without adding a lot of complexity.

Alternatively it could redirect you to the related resource, but the fact that it could be hours before the command is processed (if at all, which is why commands should be idempotent) means that some of the business logic would have to be duplicated in the HTTP facade.
So I'm not a big fan of that idea.

Jacob has another:

{% tweet https://twitter.com/jjrdk/status/242216243678040064 %}

It could be that this is the answer, but as it's solving the problem one command at a time I'm not sure yet. For example, instead of having a single address resource for a customer that we PUT a new address to (therefore failing to capture the intent), we could have a collection of addresses that we POST a new address resource to if the customer moves, or we could amend a mistake in an existing address with a PUT on the resource for that address. It's debatable whether or not that is adequate to capture the intent, but maybe with some more tweaks it could.

I'd be interested to hear how other people have tackled this...
