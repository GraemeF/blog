---
layout: post
title: "A quick comparison of some .NET mocking frameworks"
date: 2011-02-10 07:01
comments: true
categories: .net mocking tools tdd
---
What's the collective noun for mocking frameworks? If there is one, then
.NET has it!

I've used Moq for years but I'm always keen to make my tests more
readable, so I thought it was time to compare some of the modern
alternatives and see how they perform in a test fixture plucked almost
at random from a project I recently worked on. So, here are the same
tests using fakes from [Moq](http://moq.me),
[NSubstitute](http://nsubstitute.github.com/) and
[FakeItEasy](http://code.google.com/p/fakeiteasy/), all of which are
available from the [NuGet](http://nuget.org/) gallery. If there's
another hot framework you think compares well then let me know and I'll
try it out too.

**Disclaimer**: I'm quite familar with Moq but this is my first time
with NSubstitute and FakeItEasy, so I'm not necesarily using the best
option for these frameworks. If you spot something that would be better
done in another way, fork the Gist on GitHub and let me know!

## Setup

I use [xUnit.net](http://xunit.codeplex.com/) which works slightly
differently to most of the other test frameworks: it creates a new
instance of your test fixture class for each test, which allows you to
use field initializers and a constructor to set up your fakes. I take
advantage of this in the following snippets.

### Moq

Most of the time we can use a LINQ query to set up a fake with Moq. This
leads to nice and clean test set up as it can be done from a field
initializer if you have setup that applies to all the tests in the
fixture:

{% gist 820677 %}

Quite succinct, no constructor needed, but a little bit ugly.

### NSubstitute

The instantiation of the fakes is very similar to Moq, but there is no
equivalent to the LINQ setup so we need a constructor:

{% gist 820679 %}

I find this very readable, and it's probably easier to understand than
the LINQ setup.

### FakeItEasy

Slightly different take on instantiation, and again the setup needs to
be done in a constructor:

{% gist 820697 %}

The setup reads well, but is verbose when compared to NSubstitute.

## Verification

In the following snippets we have a field referencing the fake object,
and we want to verify that a method was called on it.

### Moq

We get the `Mock` for the fake object and verify:

{% gist 820728 %}

Getting the `Mock` degrades readability a bit, but not bad.

### NSubstitute

{% gist 820753 %}

Wow. Couldn't really be any shorter, could it? My only criticism is that
it doesn't scream "ASSERTION!!!" to me.

### FakeItEasy

{% gist 820737 %}

I like the way the fake is incorporated into the the call, it's much
less intrusive than Moq. Ending with `MustHaveHappened` makes a pretty
clear statement that verification is happening here.

## Raising an event

In this test I want to check that the presenter raises its
`PropertyChanged` event when the model's `PropertyChanged` event is
raised. I'm making use of a [handy extension method from
Caliburn.Testability](https://caliburn.svn.codeplex.com/svn/trunk/src/Caliburn.Testability/Extensions/PropertyAssertionExtensions.cs)
that lets me write
`AssertThatChangeNotificationIsRaisedBy([property]).When([something happens])`.
In this case *`[something happens]`* is going to be the
`PropertyChanged` event being raised on the model, and we're going to
see how that is done with the different mocking frameworks.

In the interests of staying DRY, I usually make an extension method of
my own to raise `PropertyChanged`, but I won't here so we can see how
the frameworks work!

### Moq

Again we have to get the `Mock`, then we call `Raise` on it:

{% gist 820766 %}

Still not keen on getting the `Mock`, and it's a shame we have to write
`+= null` just to make a valid expression. Bit long and nasty.

### NSubstitute

{% gist 820791 %}

So close to being very nice, but spoiled by having to supply the generic
argument to `Raise`. This [isn't always the case](http://nsubstitute.github.com/help/raising-events/), but as the
`PropertyChanged` event is declared with a delegate it's necessary here.
Still, it reads fairly well, certainly better than having `+= null` in
the middle.

### FakeItEasy

{% gist 820810 %}

A lot shorter than NSubstitute and more readable than Moq, I think
that's quite good. Although at first glance the `Now` on the end seems a
bit odd.

## Conclusion

Each of these frameworks has a lot more to offer than I've touched on
here, covering just about anything you could do to an object (and
probably a few things you wouldn't want to!). I wanted to see what the
basic, everyday scenarios look like as those are the ones that really
matter to me, and after this I will definitely try NSubstitute out on a
real project.

It's amazing how far mocking frameworks have come in the last couple of
years!
