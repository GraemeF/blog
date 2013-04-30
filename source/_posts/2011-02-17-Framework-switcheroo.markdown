---
layout: post
title: "Framework switcheroo"
date: 2011-02-17 04:39
comments: true
categories: .net tdd mocking
---

Following on from my [mocking framework comparison](http://graemef.com/42600017), I changed
[Twiddler](https://github.com/GraemeF/Twiddler) (my pet Twitter client
project) from [Moq](http://moq.me) to
[NSubstitute](http://nsubstitute.github.com/), and from
[xUnit.net](http://xunit.codeplex.com/)'s built-in Asserts to
[Should.Fluent](http://should.codeplex.com/), and I think both changes
greatly improved readability of my tests.

Full diffs are here for comparison:

-   [Moq → NSubstitute](https://github.com/GraemeF/Twiddler/commit/6527ef85f36bef51b3db96c94b648148b39fb697)
-   [xUnit.net Assert → Should.Fluent](https://github.com/GraemeF/Twiddler/commit/d34993a656c2655ff690eaacb57b81e490861b84)

