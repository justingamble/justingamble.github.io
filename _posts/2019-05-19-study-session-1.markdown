---
layout: post
title:  "Studying Elixir at Work: session 1"
date:   2019-05-19 22:00:00 -0600
read-minutes: 12
categories: elixir study learning evangelize
photo: 2019.may/session1/dilbert.strategy.make.sense.gif
excerpt_separator: <!--more-->
---

<div class="paragraph"><p> </p></div>
I started an Elixir study group at my work.  For Elixir evangelists
out there, I'm describing the approach we are using.
<!--more-->

## The Study Group: Building a foundation

The curriculum our study group is following (for now) is the 1st part of
[the study plan that Plataformatec released](http://blog.plataformatec.com.br/2018/11/starting-with-elixir-the-study-guide/).  In short, to prepare for each session we read one chapter of the book
["Learn Functional Programming with Elixir"](https://www.amazon.ca/Learn-Functional-Programming-Elixir-Foundations/dp/168050245X/ref=sr_1_fkmrnull_1?keywords=learn+functional+programming+with+elixir&qid=1558307840&s=gateway&sr=8-1-fkmrnull).  

We plan to meet over lunch hour once every 2 weeks, for a duration of 3 months.  That
translates into 6 study sessions, which is exactly enough to complete the entire
book (the first session covers 2 chapters).

I previously read through the book ["Make it stick"](https://www.amazon.ca/Make-Stick-Science-Successful-Learning/dp/0674729013/ref=sr_1_1?keywords=make+it+stick&qid=1558309112&s=gateway&sr=8-1), where it
was clearly explained that the fastest way to learn something new is to be tested on it.
With programming, this translates into solving problems with the technology.
Therefore I have decided to include exercises as part of the sessions.

The format is for myself to present material for 30 minutes, and then for the
group to work on an exercise for 30 minutes.  During the exercise I'm available
to answer questions or review solutions.  I publish a solution to each exercise
prior to the subsequent session.

I should point out the Elixir is completely new to my company.  My goal for
these sessions is to develop a solid foundation in Elixir.  In particular,
I am saving discussion of OTP for a subsequent session series.

## Session 1: Immutable data, Named Functions

In my view the first two chapters are primarily focused on data immutability
and named functions.  Anonymous functions are also covered, but I did not
discuss them in the presentation.

### Motivational Video

Before diving into Elixir, I wanted to add some inspiration.  I played
a 5 minute clip (23:40-28:29) from [Boyd Multerer's 2018 Introduction to Scenic](https://www.youtube.com/watch?v=1QNxLNMq3Uw).  I love this clip because it is a visual display of supervisors, and also because Boyd's appreciation for Elixir shines through.

<div class="center-me">
{% include youtubePlayer.html id="1QNxLNMq3Uw" %}
</div>

### Putting Elixir in Perspective

Following the video clip I presented a handful of slides.  Much of the content from
these slides was derived from the ["Elixir In Action"](https://www.amazon.ca/Elixir-Action-Sa%C5%A1a-Juri-cacute/dp/1617295027/ref=sr_1_1?keywords=elixir+in+action&qid=1558322196&s=gateway&sr=8-1) book, written by Saša Jurić.  I figure
that if the group is reading one book, then it is more useful to present
complementary material from a different source - for which I relied on Saša's book.

* <a href="/assets/powerpoint/Elixir.session.1.pptx">Session 1 Powerpoint slides</a>

### Example 1: Function with a case statement

{% highlight elixir  %}
defmodule Example do

  def colour(fruit) do
    case fruit do
      :pineapple -> "Yellowy"
      :apple -> "Red"
      _ -> "Purple?"
    end
  end

  def show_colour() do
    pineapple_col = colour(:pineapple)
    apple_col = colour(:apple)
    grapefruit_col = colour(:grapefruit)

    IO.puts("Pineapple colour: #{pineapple_col}")
    IO.puts("Apple colour: #{apple_col}")
    IO.puts("Grapefruit colour: #{grapefruit_col}")
  end

  # ...  
end

iex(1)> c "examples.exs"
[Example]
iex(2)> Example.show_colour()
Pineapple colour: Yellowy
Apple colour: Red
Grapefruit colour: Purple?
:ok
{% endhighlight %}

The above example illustrates:
* code in an Elixir function executes from top-to-bottom
* all statements in Elixir return a value, including ```if``` and ```case```.  The last line of a function returns the value of the function.
* atoms
* the ```case``` statement, with a default clause
* how to print text to stdout
* invoking functions from the ```iex``` interpreter

### Example 2: IO.inspect/2 and immutability

{% highlight elixir  %}
defmodule Example do

  # ...

  def inspect_list() do
    list = [1, 2, 3, 4]
    IO.puts("The list is #{list}")
    IO.puts("The inspected list is #{inspect list}")
    IO.puts("Alternate: inspected list is... ")
    IO.inspect(list)
    IO.puts("Ok?")
  end

  def inspect_map(map) do
#    IO.puts("The map is: #{map}")
    IO.puts("The inspected map: #{inspect map}")
    new_map = Map.put(map, :d, 99)
    IO.puts("The inspected new_map: #{inspect new_map}")
    map    # <------ return value from function
  end
end

{% endhighlight %}

Example 2 illustrates:
* ```IO.inspect/2``` is a built-in pretty-print tool.  In particular, it is useful for examining lists and maps.
* maps are immutable

<img src="/assets/images/2019.may/session1/dilbert.replace.code.jpg" class="dilbert-image">

### Exercise: Space Age

I found this awesome exercise on the first-rate [Exercism website](https://exercism.io/tracks/elixir). I love the exercise because it comes complete with unit tests, and even touches on @specs.

* [Exercise description & source code](https://bitbucket.org/siberianTiger/elixir-sessions/src/master/exercises/space-age/)
* [A solution](https://bitbucket.org/siberianTiger/elixir-sessions/src/master/solutions/space-age/)

## Learnings

After Session 1 with the group, the part that really connected with the audience
was the examples.  For the next session I plan to put more focus on examples.  

I've also gained a strong appreciation for the [Exercism website](https://exercism.io/tracks/elixir) - it
is not easy coming up with example problems.  I also love that after solving a puzzle you can view
alternate solutions.
