---
layout: post
title:  "Study session 2: Pattern Matching"
date:   2019-06-02 18:00:00 -0600
read-minutes: 7
categories: elixir study learning evangelize pattern-matching
photo: 2019.may/session2/SpeedLimit_45_s.jpg
excerpt_separator: <!--more-->
---

<div class="paragraph"><p> </p></div>
A custom exercise that emphasizes Pattern Matching with Elixir.
<!--more-->

## Context

I am presenting Elixir to the software developers at my company during lunch hours.
This is the material from session 2.  For session 1, please refer to [this post]({% post_url 2019-05-19-study-session-1 %}).

## Presentation Material

For the presentation material, I collected and presented a number of examples from the
relevant pattern matching chapters of
* ["Learn Functional Programming with Elixir"](https://www.amazon.ca/Learn-Functional-Programming-Elixir-Foundations/dp/168050245X/ref=sr_1_fkmrnull_1?keywords=learn+functional+programming+with+elixir&qid=1558307840&s=gateway&sr=8-1-fkmrnull), and
* ["Elixir In Action (2nd ed)"](https://www.amazon.ca/Elixir-Action-Sa%C5%A1a-Juri-cacute/dp/1617295027/ref=sr_1_1?keywords=elixir+in+action&qid=1558322196&s=gateway&sr=8-1)

## Exercise: Employee Info

I reviewed a number of online exercises, but I found them to be too simple.  I wanted a realistic program with extensive use of pattern matching.  I decided to create my own.

The exercise program maintains a list of employee records.  Originally the employee records are read from a CSV file, but then there is an interactive menu that allows records to be added, deleted, or changed.  

* [Exercise description & source code](https://bitbucket.org/siberianTiger/elixir-sessions/src/master/exercises/employee-info/)
* A solution (forthcoming.. stay tuned)

### Pattern Matching examples

These examples have been extracted from the [above exercise](https://bitbucket.org/siberianTiger/elixir-sessions/src/master/exercises/employee-info/).

First, pattern matching is used to parse the input file into an Employee structure:

{% highlight elixir  %}
# convert csv_line into an Employee struct
def deconstruct_line(csv_line) do
  line_parts = String.split(csv_line, ",")
  [id, first_name, last_name, salary] = line_parts

  %Employee{
    id:            id |> String.trim |> String.to_integer,
    first_name:    String.trim(first_name),
    last_name:     String.trim(last_name),
    salary:        salary |> String.trim |> String.to_integer
  }
end
{% endhighlight %}

Second, pattern matching is used to determine which `get_unique_id` function is called:

{% highlight elixir  %}
def get_unique_id([]) do
  1
end

def get_unique_id(employee_data) do
  %Employee{id: max_id} = Enum.max_by(employee_data, fn %Employee{id: id} -> id end)
  max_id + 1
end
{% endhighlight %}

Finally, pattern matching is used to find an particular struct among the list
of employee records.  This example demonstrates the pin operator.

{% highlight elixir  %}
def get_employee_record(employee_data, id) when is_integer(id) do
  Enum.find(employee_data, fn(element) ->
    match?(%Employee{id: ^id}, element)
  end)
end
{% endhighlight %}

## Next session

The next session will focus on Recursion.
