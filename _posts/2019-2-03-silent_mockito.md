---
layout: post
title: Unnecessary stubbing in new Mockito
---

### New Feature

With version 2.x _Mockito_ introduced a new feature. Since than all mocks which were unused are during test causes test to fail with [UnnecessaryStubbingException](https://github.com/mockito/mockito/blob/release/2.x/src/main/java/org/mockito/exceptions/misusing/UnnecessaryStubbingException.java). I have tried to dig for a issue with reasoning for this but all I managed to achieve is finding [commit](https://github.com/mockito/mockito/commit/0214dbfec662f920f9180539b774840249398aa6#diff-6d2ff66e977b4dedb5417e0a2778057c) which created the aforementioned exception. It's quite interesting that there is no evidence of this feature even on long [release notes](https://github.com/mockito/mockito/blob/release/2.x/doc/release-notes/official.md) list. Nevertheless it's here from early **2.0.0** beta version of Mockito library.

### Why is it here?

As there is no official explanation standing behind that we can only guess. I, personally, came up with to reasons. 

The first one, is that it was a way, _Mockito_ maintainers, tried to enforce code quality in out test. Now we can't simply mock all the stuff just because it may come handy someday. I know that not many of us do that, but believe me, there always will be someone who will mock everything because it's faster than checking what's in fact is necessary for a tests to run and pass.

The second, and in my opinion more important one, is that it forces us to modify tests along with the code. We can no longer just delete calls on some apis without removing, now unused, stubs from tests. Why is it important you may ask? It is because tests, are widely known, as a good starting point for reading code. When you try to understand what is going on in some piece of an application you don't know, you may often find yourself analyzing tests. Seeing entry points of your application with required arguments and result of calls can give you good insight into how functionality is working. But beside that you will also look at what we are mocking for our tests to run. This stubs implies at which classes or apis our functionality depends. With unnecessary stubs you may come to conclusion that this functionality needs way more external this in order to work than it actually does. 

### Why am I getting it?

At first you should check your test logic. Usually there are 3 cases. 

The first one is situation in which you are mocking wrong method. Probably you made a typo in method name or called method with more/less arguments than the one used by your code. Eventually someone changed tested code so that mocked method is no longer used. 

Your tests may also be failing before stubbed method is called. For example there may be exceptions thrown or some edge cases which makes your code return earlier than you expect.

The last reason is that your logic falls in wrong if/switch branch somewhere in the code so that mocked method is no called.

### How to disable (not recommended!)

First you have to remember that if this is the first case (no longer used method) you always want to change mocked method for the one used in the code or remove mock entirely. 

With second and the third option it depends. Usually you should just delete this mock if it has no use. But sometimes there are certain cases, for example in parameterized tests, which should take this different path or fail earlier. Then there are two ways. You can split this test into two or more separate ones but that's not always as readable as we would like it to be. 3 test methods with possibly 3 arguments providers test one method that make people wonder why it is tested like that. Eventually you can silent this exception in _JUnit 4_ with either 

```java
@RunWith(MockitoJUnitRunner.Silent.class) 
```

annotation or if you are using rule approach

```java
@Rule
public MockitoRule rule = 
            MockitoJUnit.rule().strictness(Strictness.LENIENT); 
```

rule, eventually (the same behavior):

```java
@Rule
public MockitoRule rule = MockitoJUnit.rule().silent(); 
```

For _JUnit 5_ tests you can silent this exception using annotation provided in mockito-junit-jupiter package.

```java
@ExtendWith(MockitoExtension.class)
@MockitoSettings(strictness = Strictness.LENIENT)
class JUnit5MockitoTest {
} 
```

Personally I'm currently using only _JUnit_ for testing my code but if you know how to silent that exception in other testing frameworks, tell me. I will update my article with that.

### Motivation

My motivation for writing this article is my currently most upvotes [answer](https://stackoverflow.com/questions/42947613/how-to-resolve-unneccessary-stubbing-exception?answertab=votes#tab-top) on StackOverflow. Yup, I know, there is not a lot of them, but still, you have to start with something. 


If you have different opinion on this subject please let me know. I would love to disuse it! :)

Thanks for reading and see you next time!