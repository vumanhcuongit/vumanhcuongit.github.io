---
layout: post
title: "RSpec: allow vs expect"
description: ""
category: Ruby, Ruby on Rails
tags: []
---

You know **allow** and **expect** are used in rspec-mocks, but you are confused with them ?. Don't worry, I'll explain them briefly.


### `allow`:
{% highlight ruby %}
book = double("book")
allow(book).to receive(:title) { "The RSpec Book" }
{% endhighlight %}
You ASSUME that **book object** has a method **title** and it'll return "The RSpec Book" when this method's called.

### `expect`:
{% highlight ruby %}
book = double("book")
allow(book).to receive(:title) { "The RSpec Book" }
expect(book).to receive(:title) { "The RSpec Book" }
{% endhighlight %}
This example's a bit different. You EXPECT that when **book object**  calls **title method**, it'll return that string "The RSpec Book".

## Sum up:
A big difference between them is that **ASSUME** and **EXPECT**

## Deep magic:
**why do we use 'allow' ?**
To answer this question, we should refer to the concept of Test Doubles:
> A test double is an object that stands in for another object in your system during a code example

This object isn't existed in your real system, it doesn't own methods and attributes. So we need to use **allow** to assume that this object has some methods and returns predefined values.


That's all, and thanks for your concern. If you have any questions, please don't hesitate to ask me :).


{% include JB/setup %}
