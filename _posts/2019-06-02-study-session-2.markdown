---
layout: post
title:  "EmployeeInfo Exercise: Pattern Matching in the wild"
date:   2019-06-13 09:00:00 -0600
read-minutes: 10
categories: elixir study learning evangelize pattern-matching
photo: 2019.may/session2/SpeedLimit_45_s.jpg
excerpt_separator: <!--more-->
---

<div class="paragraph"><p> </p></div>
A custom exercise that emphasizes Pattern Matching with Elixir.
<!--more-->

## Context

I am presenting Elixir to the software developers at my company during lunch hours.
This is the exercise for session 2.  For session 1, please refer to [this post]({% post_url 2019-05-19-study-session-1 %}).

Session 2 was focused on Pattern Matching principles, as covered by chapter 3 of ["Learn Functional Programming with Elixir"](https://www.amazon.ca/Learn-Functional-Programming-Elixir-Foundations/dp/168050245X/ref=sr_1_fkmrnull_1?keywords=learn+functional+programming+with+elixir&qid=1558307840&s=gateway&sr=8-1-fkmrnull).

## Exercise: Employee Info

My goal was show a realistic program with extensive use of pattern matching.  I decided to create a custom implementation.

The program maintains a list of employee records.  Originally the employee records are read from a CSV file, but then there is an interactive menu that allows records to be added, deleted, or changed.  

* [Exercise description & source code](https://bitbucket.org/siberianTiger/elixir-sessions/src/master/exercises/employee-info/)  
The assignment is to understand the existing program, and then extend it by filling out the contents of these two functions:
{% highlight elixir  %}
def change_salary(employee_data, employee, new_salary) when is_integer(new_salary) do
  ## ************************************************************************
  ## TODO: insert code here, return an updated list of employee_data structs.
  ## The employee's ID should not change.
  ## ************************************************************************
end

def change_first_name(employee_data, employee, new_name) when is_binary(new_name) do
  ## ************************************************************************
  ## TODO: insert code here, return an updated list of employee_data structs.
  ## The employee's ID should not change.
  ## ************************************************************************
end
{% endhighlight %}
* [A solution](https://bitbucket.org/siberianTiger/elixir-sessions/src/master/solutions/employee-info/)

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

Second, pattern matching is used to determine which `get_unique_id/1` function is called:

{% highlight elixir  %}
def get_unique_id([]), do: 1
def get_unique_id(employee_data) do
  %Employee{id: max_id} = Enum.max_by(employee_data, fn %Employee{id: id} -> id end)
  max_id + 1
end
{% endhighlight %}

Third, before saving the new employee data to disk, the employee structs are sorted by ID.

Guards are used to determine which `sort_employee_data/1` function is called.  
If the list only contains one record, then the `length(employee_data) >= 2` check
will fail, and the second implementation will execute and return the list unchanged.

What makes this example interesting from a pattern matching perspective
is that the `compare_ids/2` function only needs to extract the `id` from each
structure.  The entire structure gets sorted, but the rest of the fields
are not needed in the `compare_ids/2` function logic.

{% highlight elixir  %}
def sort_employee_data(employee_data) when is_list(employee_data)
                                       and length(employee_data) >= 2 do
  employee_data
  |> Enum.sort(fn x, y -> compare_ids(x, y) end)
end

def sort_employee_data(employee_data) when is_list(employee_data) do
  employee_data
end

def compare_ids(%Employee{id: id1}, %Employee{id: id2}) do
  id1 < id2
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

### Spotlight on the Pipeline Operator

The unit tests include the below snippets.  When writing this code
I was pleased with how clear the logic was, thanks to the
expressiveness of the pipeline operator.

To setup an initial list of employee structs:
{% highlight elixir  %}
  initial_data =
    []
    |> EmployeeInfo.add_employee(jane.first_name, jane.last_name, jane.salary)
    |> EmployeeInfo.add_employee(john.first_name, john.last_name, john.salary)
{% endhighlight %}

Later, when we change the data as part of a test:
{% highlight elixir  %}
  changed_data =
      initial_data
      |> EmployeeInfo.change_salary(jane.id, 200000)
      |> EmployeeInfo.change_salary(john.id, 300000)
{% endhighlight %}

## Next session

The next session will focus on Recursion.
