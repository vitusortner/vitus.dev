---
layout: post
title:  "Taking Another Perspective"
subtitle:  "Utilizing Database Views"
date:   2020-6-16 13:07:56 -0500
categories: database
---
There is that one part in the codebase that is always bothering you when you touch or even just see it. “Let’s fix it later” is what you might be telling yourself and your colleagues.

I finally got to work on such a part of our app. We’re dealing with some sort of sensitive data that shouldn’t be accessible to everyone. Instead, only premium users are eligible to access it. In order to realize this, our app makes two API calls. One to retrieve “open” data. The second, to fetch the secrets such as URLs and checksums.

We’ve been using the same model to do that since forever. We even went one step further and stored this data in the same database table. Because accessing one database table instead of two, when selecting data, is more convenient. This meant that we stored the open data in the database and we persisted the secrets in the same table. Both actions were happening asynchronously and independently of each other. This meant that we filled the database table with a lot of nulls depending on which API request responded first. This behavior also forced us to look for persistent data sets before inserting new rows to not mistakenly override valid data.

Fast forward to today. We’re in the middle of developing one of the biggest and most exciting new features, which allows us to redo certain parts of the app. The mentioned database table is part of the changes but we’re still stuck with two API requests that happen independently of each other. Luckily, SQLite can help. Before we go into how. Let’s mention the requirements.
- we still have two independent API requests that deliver data in different formats
- we want easy access to both open and secret data just as before

Now to the solution. The urge to store both data structures in separate tables became bigger and thus, I gave it a try. Both open and secret data tables now house four fields whereas the previously unified table was described with seven. Perfect — we have simpler tables.

But how do we access the data through a single interface in order to maintain simple access? SQLite’s database views come to the rescue. I did set up a view which queries both tables and joins the results. The resulting sets have the same structure as the initial table but without the nullability. We were able to get rid of it because we don’t need null as a placeholder anymore. The two new tables only define columns that are actually needed.

The following code snippets are using annotations to mark classes as database relevant. In this case, we’re using the Room database library where @Entity results in a database table and @DatabaseView in a view.

### Previous database model

{% highlight kotlin linenos %}
@Entity
data class Meditation(
  val id: String,
  val title: String?,
  val description: String?,
  val duration: Long?,
  val url: String?,
  val checksum: String?,
  val isUnlocked: Boolean?
)
{% endhighlight %}

### New database models

{% highlight kotlin linenos %}
@Entity
data class OpenData(
  val id: String,
  val title: String,
  val description: String,
  val duration: Long
)

@Entity
data class SecretData(
  val id: String,
  val url: String,
  val checksum: String,
  val isUnlocked: Boolean
)

@DatabaseView(“””
  SELECT 
    open_data.id,
    open_data.title,
    open_data.description,
    open_data.duration,
    scret_data.url,
    scret_data.checksum,
    scret_data.is_unlocked
  FROM open_data
  JOIN secret_data ON secret_data.id = open_data.id 
“””)
data class Meditation(
  val id: String,
  val title: String,
  val description: String,
  val duration: Long,
  val url: String,
  val checksum: String,
  val isUnlocked: Boolean
)
{% endhighlight %}

## Result

- we still have two independent API requests that deliver data in different formats ✔️
- we want easy access to both open and secret data just as before — it even got simpler because we got rid of unnecessary nullability ✔️
