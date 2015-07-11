---
layout: post
title: Default Params In Elixir
categories: [elixir]
fullview: true
---

In elixir you can provide default params like so:
{% highlight elixir linenos %}
defmodule Example do
  def say_hello(subject \\ "world") do
    IO.puts "Hello #{subject}"
  end
end
{% endhighlight %}

And everything works just fine and dandy:
{% highlight elixir %}
iex(1)> Example.say_hello("mars")
Hello mars
:ok
iex(2)> Example.say_hello
Hello world
:ok
{% endhighlight %}

Ok cool! So lets do some recursion...because elixir!
{% highlight elixir linenos %}
defmodule Example do
  def say_hellos([subject|subjects]) do
    IO.puts "Hello #{subject}"
    say_hellos(subjects)
  end

  def say_hellos([]) do
    IO.puts "Done saying hello!"
  end
end
{% endhighlight %}

{% highlight elixir %}
iex(1)> Example.say_hellos(['world', 'earth'])
Hello world
Hello earth
Done saying hello!
:ok
{% endhighlight %}

Now lets try to add some defaults:
{% highlight elixir linenos %}
defmodule Example do
  def say_hellos([subject|subjects] \\ ["World", "Moon", "Sun"]) do
    IO.puts "Hello #{subject}"
    say_hellos(subjects)
  end

  def say_hellos([]) do
    IO.puts "Done saying hello!"
  end
end
{% endhighlight %}

And we hit a compile error:

~~~
** (CompileError) test.exs:7: def say_hellos/1 has default values and multiple clauses, define a function head with the defaults
    (elixir) src/elixir_def.erl:328: :elixir_def.store_each/7
    (elixir) src/elixir_def.erl:91: :elixir_def.store_definition/9
    (stdlib) erl_eval.erl:657: :erl_eval.do_apply/6
~~~

All we need to do to fix this is define a bodyless function with the defaults provided
there:
{% highlight elixir linenos %}
defmodule Example do
  def say_hellos(subjects \\ ["World", "Moon", "Sun"])
  def say_hellos([subject|subjects]) do
    IO.puts "Hello #{subject}"
    say_hellos(subjects)
  end

  def say_hellos([]) do
    IO.puts "Done saying hello!"
  end
end
{% endhighlight %}

{% highlight elixir linenos %}
iex(1)> Example.say_hellos
Hello World
Hello Moon
Hello Sun
Done saying hello!
:ok
iex(2)> Example.say_hellos(["Moon"])
Hello Moon
Done saying hello!
:ok
{% endhighlight %}

And all is right with the world again.
