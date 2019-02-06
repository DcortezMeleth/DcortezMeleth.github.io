---
layout: post
title: TestDoc - Elixir self testing documentation
---

While playing with the Elixir for the last few months I have discovered plenty of interesting features. All of them was amazing for my, the OOP guy who never had an opportunity to work with functional language (except of Lisp during studies but that's another story). But the winner can be only one and for me it is definitely testdoc.

### Documentation as first-class citizen

As official documentation of Elixir states, documentation is a first-class citizen in the language. What it mean is that it is treated on the same level as the code. For me, who coded all my life in Java, this is a great news. In my life I have seen a load of crappy documentation and only a few well written docs. It is quite fun because Java, actually has a quite good standard for code documentation called JavaDoc. It has also support in tools which help you to format it or convert it to html pages. 

Sample JavaDoc:
```java
/**
 * Retrieves parameter from application context. You can also use
 * {@link #getParamFromContext(String, Class)} to retrieve parameters 
 * other than strings.
 *
 * @param paramName Name of the parameter in upper case.
 * @return Return parameter as String when found, null otherwise.
 */
public String getParamFromContext(String paramName) {
    ...
}
```

The problem here is that there is now way to enforce keeping documentation in sync with code. That's why people often states that docs are not necessary, because in the future I will almost certainly describe logic not longer present in the method/class. There is popular opinion that code should be self-documenting and the specially written docs are evil cause they suggest that method cannot be understand without them.

Where they are wrong, in my opinion is that they don't realize why we are writing documentation. It's not for telling friend in a team that method `add(int, int)` adds 2 integers. **Methods should be self-documenting**, I'm not disagreeing with that. Unfortunately not every method is so simple and not everybody can see sources to understand our code. People will often see only apis of our libraries. To them some thing obvious to us maybe somewhat mysterious. Additionally some methods have side effects which may not be clear to the user. In this case docs are the only way to tell us what to expect when invoking this kind of methods.

But there is no enough people who think as I do as well as there no good support is no support for keeping docs in sync with the code. This way people still think of docs as some kind of a ballast which requires work, but provides not value, just problems and misunderstandings.

### Testdoc for the rescue

And here comes aforementioned [testdoc](https://elixir-lang.org/getting-started/mix-otp/docs-tests-and-with.html). It's a special kind of a comment in which we can write sample method calls with expected results. Later on we can run this examples as a part of our test suite.

Testdoc in practice:
```elixir
defmodule Fib do

  @doc ~S"""
    Returns n-th element of Fibonacci sequence. Count elements starting at 0.

  ## Examples

    iex> Fib.value(0)
    1

    iex> Fib.value(9)
    55

    iex> Fib.value(20)
    10946
  """
  @spec value(integer) :: integer
  def value(0), do: 1
  def value(1), do: 1
  def value(n), do: value(n-1) + value(n-2)
end

defmodule FibTest do
  use ExUnit.Case
  doctest Fib
end
```

This way if we change api of our library but forget to change docs, our tests will fail. What's more our documentation is much easier to read and to understand when supported by live examples. 

Some may think that this kind of tests cannot exist in object oriented languages because our object way to often has state and use it to change the result of the methods. And of course you cannot use them as easily as in functional languages where you cannot have state, and functions by definition should be pure and side-effect free. However, no matter of which language you are using, I'm sure you have a lot of utility classes or stateless services which could easily be tested this way. Nowadays there is a trend to write functional code even in languages traditionally perceived as object as they can get. Even in Java a lot of tools promoting this paradigm emerged in the last years. We have RxJava, we have stream and lambdas, we even have functional interfaces. Maybe someday we will eventually see function as first-class citizen? Who knows what the future may bring to us. Anyway there is a lot of code that could be tested this way.

I strongly believe that this is a direction where all popular testing frameworks should be heading. Maybe someday I will even try to implement this kind of solution myself? But I think that even today all of us can learn from this approach. We don't have to run our docs from the start. We can simply start with providing simple examples to our methods to enhance understating of our library. There will still be problem with sync, but at least some of us my find it more useful than explaining how method works with words. And every bit of understating will lead to less errors in usage.

### Other languages

It's quite fun that till I started to write this article, I thought that this approach to documentation is unique to Elixir. I now, stupid of me D: Such tools exist in other languages. You can see them in pure functional languages as [Haskell](http://hackage.haskell.org/package/doctest) or [Lisp](https://github.com/johanlindberg/doctest) and in more mainstream ones like [Python](https://docs.python.org/3/library/doctest.html). There are even tries to make it work for compiled languages like Java ([doctestj](https://code.google.com/archive/p/doctestj/) and [JDoctest](https://github.com/cscott/JDoctest)).