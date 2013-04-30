---
layout: post
title: "How I'm Growing Object-Oriented Software, Guided By Tests"
date: 2010-02-08 20:22
comments: true
categories: tdd goos
---
[Growing Object-Oriented Software, Guided by Tests](http://www.growing-object-oriented-software.com/)
by [Steve Freeman](http://www.m3p.co.uk/) and [Nat Pryce](http://www.natpryce.com/) is a book about test-driven
development. Here are a few notes on my experiences of following its
methods.

### The Story So Far

I was fortunate enough to start work on a new desktop application in the
middle of last year, around the time I read through the freely-available
online version of the book before it was finally published in November.
This was an ideal opportunity to put TDD into practice so I started by
building a “walking skeleton” using
[Prism](http://www.codeplex.com/CompositeWPF),
[CruiseControl.NET](http://confluence.public.thoughtworks.org/display/CCNET/Welcome+to+CruiseControl.NET),
[WiX](http://wix.sourceforge.net/), [Gallio](http://www.gallio.org/),
[MbUnit](http://www.mbunit.com/), [NCover](http://www.ncover.com/) and
[White](http://white.codeplex.com/) as a wrapper around [UI
Automation](http://msdn.microsoft.com/en-us/library/ms747327.aspx) for
the acceptance tests, and took it from there. I’ll admit that there was
a slow start (WPF/Prism and White/UI Automation were new to me too) but
development speed has been steadily increasing ever since, and now I’m
able to get what feels like a lot done each day. And that’s pretty much
*every* day; it’s been a long time since I’ve had to halt progress for a
significant amount of time in order to squash a bug or redo a chunk of
work.

### Where am I now?

I’m still learning. It’s easy to slip back into changing code then
updating the tests to match, and I do find myself doing that sometimes.
I’m also finding it hard to perform only one refactoring step at a time
(oh, let me just rename that class while I’m here…), and the acceptance
tests can be brittle and sometimes feel like a burden to write. But what
doesn’t kill you makes you stronger, right? It’s getting noticeably
easier as I learn and improve, and every bit of pain along the way has
been worth it.

### Does it work?

For me, **yes**. Test-driven development feels *so right* that I don’t
think I could ever go back to hacking stuff together without building
the safety net of tests to fall back on as I go. I am sure that my
design is much better than anything I have produced before, and that I
have far fewer bugs than usual, too :) So this experience has been
nothing short of (professional) life-changing. I have read similar stuff
before, but *GOOS* was the one that finally made me “get it.”
