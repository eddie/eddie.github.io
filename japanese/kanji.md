---
layout: page
title: Kanji
permalink: /kanji/
---

Whilst studying Japanese, I have come across many Kanji, I find the etymology interesting so even though I learn
Kanji through context, I wanted to know a bit more about how they were constructed .

I recently started reading through [Heisig's RTK](http://amzn.to/1vXiwqD) to find out more.. I have studied Kanji in a similar
way before, but this time it wasn't with the intention of learning their meanings specifically.

Here is a list of current Kanji I have studied in RTK, not a list of the overall kanji I have studied.

{% assign kanji_list = site.data.kanji | where: "studied","1" | group_by: "lesson" %}
{% for level in kanji_list %}
<br>
Level {{ level.name }}
    {% for kanji in level.items %}
<div class='kanji-item'>
{{ kanji.kanji }}
</div>
    {% endfor %}
{% endfor %}

