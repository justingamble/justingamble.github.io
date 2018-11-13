---
layout: post
title:  "Dealing with BOMs with Elixir"
date:   2018-11-12 09:00:00 -0600
read-minutes: 10
categories: text BOM Elixir
#photo: BookPile.jpg
---

Introduction

UTF text files may contain a Byte Order Mark (BOM) at the start of the file.

At my work we came across a BOM in our input file.  That put the idea in my head to explore
this topic more, and play around with Elixir code for handling BOM.  This blog is the output 
of that exploration.

Outline

Let me provide a brief story to give context for how BOM entered my world, then we'll get some 
background on BOMs, and finally present some simple ELixir code for having complete control over the
BOMs that (might) exist in your text files.

How I came to learn about BOM

At my work my team was developing an application that reads a YAML text file.  The YAML text file is 
a configuration file for the application.  We used a 3rd party library to read and parse the YAML file.
One day, seemimgly out of the blue, the YAML parser aborted with a cryptic error message.  We reverted 
the YAML text file to an earlier known version and all was fine.  Over time, the same issue occurred again.
We decided we needed to understand it.  After some googling we determined that the issue was 
a BOM sequence of characters that was being added to the YAML file.  Our 3rd party library didn't expect 
the BOM character sequence.  We never bothered to narrow down which of our editors was inadvertently adding
the BOM sequence.  We simply decided to remove the BOM if it existed, that way our 3rd party library would
work correctly.

Our YAML file is stored in a git repository.  The first solution we found was to correct the file
using a VIM editor and then recommit the YAML file to git.  That worked.  These were the VIM commands
that we used to detect and remove the BOM:

:setlocal bomb?
This returns "bomb" is there exists a BOM, and "no bomb" otherwise.

If you want to add a BOM (i.e. for test purposes), you can use:
:setlocal bomb
:wq

To remove a BOM:
:setlocal nobomb
:wq


Pretty straight-forward; VIM is awesome.  (I also appreciate the sense of humour, by using 'bomb' instead of 'bom')

But the question is, can we do better?  Well, of course.  A better solution is to have the
program examine the YAML configuration file before loading it, and remove any BOM characters 
automatically before attempting to load it.  (Stay tuned, as that is exactly what we will do below.)  
This is a better solution because the YAML file is fixed automatically by the program instead of 
required a human to open the file with VIM.

Some background on BOM

Before we dive into solutioning, let's talk a bit more about why the BOM exists.

What is a BOM?  

According to www.unicode.org/faq :
"A byte order mark (BOM) consists of the character code U+FEFF at the beginning of a data stream, where it can be used as a signature defining the byte order and encoding form, primarily of unmarked plaintext files."

According to Wikipedia  https://en.wikipedia.org/wiki/Byte_order_mark :
"Unicode can be encoded in units of 8-bit, 16-bit, or 32-bit integers. For the 16- and 32-bit representations, a computer receiving text from arbitrary sources needs to know which byte order the integers are encoded in. The BOM is encoded in the same scheme as the rest of the document and becomes an invalid Unicode code point if its bytes are swapped. Hence, the process accessing the text can examine these first few bytes to determine the endianess, without requiring some contract or metadata outside of the text stream itself."

It is worth mentioning that BOM characters are only useful at the very start of a file.
They should not be embedded within the file contents.

I can't resist saying that while reading the Wikipedia page, I found the following quote that 
neatly summarizes the problem that my team ran into with the 3rd party YAML parser:

"BOM use is optional.  Its prescence interferes with the use of UTF-8 by software that does
not expect non-ASCII bytes at the start of a file but that could otherwise handle the text stream."

*** Insert table here ***

One interesting use for the BOM character is to tell Microsoft Excel to interpret the CSV 
file with a unicode encoding.  This was revealed to me by this excellent post:
https://underthehood.meltwater.com/blog/2018/08/08/excel-friendly-csv-exports-with-elixir/

The above descriptions are sufficient for following along with this blog, but if you want to 
dive more deeply into the world of BOM and Unicode then check out:
  * https://en.wikipedia.org/wiki/Byte_order_mark
  * http://www.unicode.org/faq/utf_bom.html


Commentary

An argument could be made that my unit tests are testing the Elixir File library, more than anything else.
I agree.  This software and blog was an exercise for myself, both for writing the unit tests and the code.

Elixir solution: BOM Manipulator

I decided to create a package to make it easy to detect BOMs, remove BOMs, and add BOMs.  As I looked 
into how to implement this I discovered that the heavy lifting had already been done by Erlang's unicode library
and Elixir's File library.  This does not really surprise me; my experience is that the Elixir/Erlang's
ecosystem has an expansive collection of tools already available at our disposal.  Even so, I decided to neatly 
package up the library calls into a custom package that is super simple to understand and use.

Unexpected Learnings

It seems like each time I write a program I learn something new, that I was not expecting to learn.  
Today I learned about the the Briefly library, as a means to create temporary files.  I ended up using this library extensively in the unit tests.  Sweet!



{% highlight elixir  %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}



I wanted to experiment with the process of creating a package on Hex.pm.  So I decided
to make a package for this "BOM Manipulator".  I followed the steps in this excellent 
post:  (reference to steps to create a package on Hex.pm).  Is it necessary to create this
package?  No, I don't think so.  Once you understand what the program is doing, it is fairly
simple to write custom code to resolve the issue (in whatever language you like).
But for me it was fun to create this package, and I learned things in the process.

References

While creating this package, I found it educational to review a previous solution that 
already exists in Hex.pm:
https://github.com/shufo/bom/blob/master/lib/bom.ex

No doubt the above package influence the code in my package.
