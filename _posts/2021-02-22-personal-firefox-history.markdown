---
layout: post
title: "Personal Firefox history"
date: 2021-02-22
---

I find myself searching through my browser history to try to recall something I read a couple months ago. I found myself a little frustrated with Firefox's history UI. I also use several Firefox profiles, so my browsing history is saved across a number of different profiles.

Firefox stores your browsing history in a sqlite database. So I had the bright idea of extracting what I wanted from the various sqlite databases and consolidating it together so I could search across all of it using SQL. 

I got this working but it was a bit of a fragile process. I still need to automate it. Below are the queries for grabbing what I wanted out of each Firefox profile.

Bysides actually finishing this project (by automating it), I'd like to extend it by also archiving each webpage and stuffing it into the database. Sometimes pages get lost, and then there goes the content I was trying to recall. I know I could just use the Wayback Machine, but this sounded more fun.

{% highlight sql %}
formhistory.sqlite:
	SELECT 
	  fieldname, 
	  value, 
	  datetime(firstUsed / 1000000, 'unixepoch') AS firstUsed, 
	  datetime(lastUsed / 1000000, 'unixepoch') AS lastUsed 
	FROM moz_formhistory

places.sqlite
	SELECT 
	  visit_type, 
	  url, 
	  title, 
	  description, 
	  datetime(visit_date/1000000, 'unixepoch') AS visit_date 
	FROM moz_historyvisits 
	INNER JOIN moz_places 
	  ON moz_historyvisits.place_id = moz_places.id;
{% endhighlight %}

