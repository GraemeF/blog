---
layout: post
title: "Don't jump the red light!"
date: 2010-02-15 20:29
comments: true
categories: tdd
---
There’s a good reason why the [test-driven development cycle](http://en.wikipedia.org/wiki/Test-driven_development) says you
should always watch a test fail before you write the production code
that makes it pass. I was taught a lesson in this today, “school of hard
knocks” style…

### How did it happen?

I have a simple class which implements `INotifyPropertyChanged` and has
property:

    public class MyClass : INotifyPropertyChanged
    {
        private string _name;

        public string Name
        {
            get { return _name; }
            set
            {
                _name = value;
                PropertyChanged(this, new PropertyChangedEventArgs("Name"));
            }
        }

        public event PropertyChangedEventHandler PropertyChanged = delegate { };
    }

To test that the PropertyChanged event is raised at the appropriate time
I use [Caliburn](http://www.codeplex.com/caliburn)’s handy (and very
readable) `PropertyHasChangedAssertion`:

    [TestFixture]
    public class MyClassTestFixture
    {
        [Test]
        public void Name_WhenSet_RaisesPropertyChanged()
        {
            var test = new MyClass();

            test.AssertThatChangeNotificationIsRaisedBy(x => x.Name);
        }
    }

At the time I obviously thought this was too simple to worry about, saw
the test passed as expected and moved on. All good… Or so it seemed!
Fast forward a week or two. I started to get some strange errors – not
test failures - in my MSBuild output:

> error : Internal error: An unhandled exception occurred.
> error : System.Exception: No context was provided to test the notification, use When(Action affectProperty) to provide a context.
> error :    at Caliburn.Testability.Assertions.PropertyHasChangedAssertion`2.Finalize()

Not only is the message a bit cryptic without any context (e.g. a test)
but the error was intermittent. Oh joy! After a bit of detective work
(more than you might think!) I realised that this is because the
Caliburn’s `PropertyHasChangedAssertion` checks that you called its
`When(Action affectProperty)` method in its finalizer:

    ~PropertyHasChangedAssertion()
    {
        if(!_isValidAssertion)
            throw new Exception(
                "No context was provided to test the notification, use When(Action affectProperty) to provide a context.");
    }

While this makes the test extremely readable (which is, of course,
extremely important), if you forget to call `When()`, if and when you
get an error is up to the non-deterministic finalization gods. An easy
one to fix:

    [Test]
    public void Name_WhenSet_RaisesPropertyChanged()
    {
        var test = new MyClass();

        test.AssertThatChangeNotificationIsRaisedBy(x => x.Name)
            .When(() => test.Name = "New name");
    }

But I didn’t get the feedback that I should have done from doing TDD
properly, and wasted time as a result.

### What went wrong?

Here is how TDD is *supposed* to be performed:

![TDD cycle](http://bitbybitblog.com/wp-content/uploads/2011/12/tdd_cycle.jpg)

Because the code was trivial, I skipped the second step and didn’t check
that the test failed before I carried on and implemented the property.
If I hadn’t skipped this step, there’s a good chance that the following
sequence of events would have occurred:

1.  I write the test, and add an empty property definition to make it
    compile
2.  I run the test and *see that it unexpectedly passes*
3.  I scratch my head for a bit, but I’m already looking at the
    offending line of code so it’s much easier to spot the problem
4.  The penny drops, I spot the mistake and fix it
5.  I run the test again, and this time it fails. Happy days.
6.  I implement the property changed notification and carry on

Maybe I would have got the error when I ran the test, and that would
have given me another clue about the cause of the problem.

### It could have been worse!

In the week or two since I made the mistake I could have carried on to
use `AssertThatChangeNotificationIsRaisedBy` in tens or hundreds of
other tests, which would have made it much harder to find the one with
the missing `When()` call. I was lucky that there were only a few uses
in my tests.

### A lesson learned?

I hope so! When time is short it can be hard to make yourself go through
these steps over and over again, but they are all there for a reason –
*to stop us writing code that doesn’t do what we think it does*. I will
be trying especially hard to stick to the steps, but we’ll have to wait
and see how it goes.
