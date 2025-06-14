---
layout: default
title: EarlySignal
---

# Welcome to EarlySignal

EarlySignal is an innovative project focused on early detection of incorrect system and service behaviors using Machine Learning.

## Latest Posts

Number of posts: {{ site.posts.size }}

{% for post in site.posts %}
  <article>
    <h2><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h2>
    <p>Date: {{ post.date }}</p>
    <p>{{ post.content | strip_html | truncatewords: 20 }}</p>
  </article>
{% endfor %}

<style>
.posts {
  margin-top: 2em;
}
.post {
  margin-bottom: 2em;
  padding-bottom: 2em;
  border-bottom: 1px solid #eee;
}
.post-meta {
  color: #666;
  font-size: 0.9em;
}
.post-excerpt {
  margin: 1em 0;
}
.read-more {
  display: inline-block;
  margin-top: 0.5em;
}
</style> 