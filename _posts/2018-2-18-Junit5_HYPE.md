---
layout: post
title: JUnit 5 HYPE!
---

Recently I had an occasion to speak at our local Meet Up. Prior to this event i had to choose a topic of my presentation. As a group was though to be for both back-end and front-end developers, range of possibilities was wide. I discussed few options with me teammates and ended up deciding to go for unit testing in Java with a use of new version of JUnit testing library.

As I started preparing for this event version 5.0.0 was just released (like a week or two earlier) so everything than was still quite fresh and new. I know that you could have used earlier milestones, but introducing them in a company which wants every tool to be proven in a heat of a battle may no be easy. :D Luckily for me, I managed to sneak in one of the release candidates version to our project and experiment a little bit with it.

What I found out interesting in the first place is that new version was founded by a [crowd-funding campaign](https://junit.org/junit4/junit-lambda-campaign.html). Not often do you see such things in IT world. Luckily it managed to find quite a few founders which offered money and people to work on completely reworked version of nearly 10 year old project.

The major improvement in JUnit 5 release it's new structure. As earlier everything was packed in a single jar, now we have really modular structure. I know that at first glance it may seem more problematic for us, developers, as now we need to look for correct dependencies to use and just import single one. But believe my, it here for a reason.

Parts of this new version are separated into 3 main groups. First of them, called _Platform_,  collects artifacts used by IDE's and build tools to discover and execute tests. Second one, named _Jupiter_, contains API for writing tests. Last one, the _Vintage_, supports migration process thanks to possibility of running old tests (prior to version 5) on a new platform.

![modules]({{ site.baseurl }}/images/junit5_hype/modules.png)

To better understand how this modules work and where it place for them, we can look at diagram below.

![layers]({{ site.baseurl }}/images/junit5_hype/layers.png)

So how it works? First build tool like maven, calls _JUnit Platform_ to discover test which should be run. Then platform looks for provided engines end asks them to find tests. Each engine can look for test in other ways. This way we can have _Jupiter Engine_ looking for test written with a use of new API. We can also have _Vintage Engine_ looking for test using elements of older versions. What's more, we can implement our own engine and use it with _JUnit Platform_. This way tools like Intelij can support only one solution at the same time giving us possibility of using many different libraries. This enables other libs like Spock or TestNg to be ported to this ecosystem to make a life of tools developers a little bit easier.

I know what you are thinking now. **That's great. But what are we, regular developers, getting?**

Don't worry! There is even more for us.

We will begin with a feature, which goes along with a goal of JUnit to make tests more verbose and easier to understand. **@DisplayName** annotation gives us possibility of writing full name of a test class/method using normal language.

![test1]({{ site.baseurl }}/images/junit5_hype/test_1.png)

Thanks to this in a report produced by tools we will see TEST 1 rather then test1 which is the name of the method. Second new annotation brought by JUnit team is **@Tag**. This is a new form of old categories. It allows us to group tests by categories for example, unit/end-to-end/integration or internal/external. Later we can run just one group without a need to run all tests for project.

![test2]({{ site.baseurl }}/images/junit5_hype/test_2.png)

Those features seems nice but not life changing. So what is? This way we come to new concept of **Extensions**. They are some form of merge between old Rules and Runners. Extension can inject code in different phases of test execution make it a great choice as a tool for enabling support of libraries like Spring or Mockito. One extension can inject in multiple phases like beforeTest, aftersTests. It can also get access to method parameters and catch exceptions thrown in tests. The most important thing here is that now we can have more than one exception on a single test class. This way we can combine parametrized tests, Spring and Mockito in one class. It's huge because now we don't have to choose between those features. We can even write our own extensions bringing some common functionalities.

Going further, now we can build **custom annotation** composing existing ones into more compact version. For example we can define our own annotation simultaneously enabling Mockito and Spring functionalities. Thank's to this now we can annotate our classes with only one annotation. It also helps us in cases where we have to change configuration for all our test, for example when we need to add new configuration class for Spring. Here it's enough to add just once in our annotation.

![test3]({{ site.baseurl }}/images/junit5_hype/test_3.png)

Next, we have new **Suite API** allowing us to run multiple classes in one context, this way we can save time by starting containers like Spring just once for whole suite.

![test4]({{ site.baseurl }}/images/junit5_hype/test_4.png)

Last but definitively not least we have got a new **parametrized tests**. Anyone remembers ugly, obscure parametrized tests from JUnit 4? Thanks God, they are gone now. In their places we received set of new annotation allowing us to build simple and readable test.

![test5]({{ site.baseurl }}/images/junit5_hype/test_5.png)

As you can see, now we can write simple sets of parameters using annotations. We can even use external csv file to store our parameters. In case you have more complicated objects as parameters you can write your own converters producing more complicated entities. In case that is not enough you can still use old way of writing test, by providing method producing parameters but even then it's still much simpler than before.

![test6]({{ site.baseurl }}/images/junit5_hype/test_6.png)

Seams like a lot? There is even more than that but if I would like to share that all, you wouldn't have a pleasure of discovering that by yourself.

So go ahead and dive into new release. I'm sure you won't regret it!

**Resources**:
* [My repository](https://github.com/DcortezMeleth/junit5-presentation) with examples used during presentation
* [Slides from my presentation](https://www.slideshare.net/BartoszSdel/junit-5-testowanie-jednostkowe-nowej-generacji) - unfortunately in Polish
* [Official page of JUnit](https://junit.org/junit5/)
* [GitHub page of new release](https://github.com/junit-team/junit5/)