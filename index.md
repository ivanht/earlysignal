---
layout: home
title: EarlySignal
---

# Welcome to EarlySignal

EarlySignal is an innovative project focused on early detection of incorrect system and service behaviors using Machine Learning.

## Latest Posts

{% for post in site.posts %}
  <article>
    <h2><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
    <p class="post-meta">{{ post.date | date: "%B %-d, %Y" }}</p>
    {{ post.excerpt }}
    <a href="{{ post.url | relative_url }}">Read more...</a>
  </article>
{% endfor %} 