---
layout: post
title:  "Picking the Low-Hanging Fruits"
subtitle:  "UI Dimension Naming"
description: ""
date: 2020-12-5
categories: styles, themes, theming, dimensions
---

Naming UI dimensions can be a challenging task especially when your codebase already uses three different naming conventions but none of them seem to fulfill to most recent requirements.
I was confronted with such a dilemma just recently and made some discoveries which I'm sharing here.

## T-shirt Sizes

First of all, I'm a fan of T-shirt sizes as they give a rough sense of dimension without the need for detail.
When defining and using dimensions in the UI programming context though, we want exactly that.
We want detail because more detailed and fine-grained than dimensions used in the UI context it doesn't really get.
A dimension defines a single concrete value.

Don't get me wrong, I still believe that they come in handy when consecutive but imprecise relations between dimensions are sufficient.
The definition of UI dimensions doesn't match these requirements unfortunately.

{% highlight yml linenos %}
xsmall = 2
small = 3
medium = 5
large = 7
xlarge = 11
{% endhighlight %}

## Enumeration

We've recently made the switch from scaling dimensions by the golden ratio to using multiples of four.
We used to have a base size that got multiplied with ~1.618.
Its result got multiplied with the same number and so on.
This technique seems interesting at first but after using it for some time we realized that it introduces unnatural steps for mobile scales.
The difference between two dimensions becomes big even for low numbers.

{% highlight yml linenos %}
dimension_1 = 2
dimension_2 = 3
dimension_3 = 5
dimension_4 = 7
dimension_5 = 11
{% endhighlight %}

## Literal - "it is what it is" 

Finally, let's take a look at an expressive style of naming dimensions.

{% highlight yml linenos %}
dimension_2 = 2
dimension_3 = 3
dimension_5 = 5
dimension_7 = 7
dimension_11 = 11
{% endhighlight %}

It's time to remove semantic names from the dimension layer and only use it on the style level.
Whenever text is styled that will be used as a title, for instance, we simply refer to a dimension like `dimension_11` but refer to the style as `title`.
Whenever I'm building UI that is made of reusable components, I simply check the style name in the designs and apply the matching style to the text view.
On this level of abstraction, I don't care about the used dimension at all.

{% highlight yml linenos %}
title
  text_size = dimension_11  

text_view
  style = title
{% endhighlight %}
