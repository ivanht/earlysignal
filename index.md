---
layout: default
title: EarlySignal
---

# Welcome to EarlySignal

EarlySignal is an innovative project focused on early detection of incorrect system and service behaviors using Machine Learning.

## Latest Posts

<div class="posts">
  {% for post in site.posts %}
    <div class="post">
      <h2><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
      <p class="post-meta">{{ post.date | date: "%B %-d, %Y" }}</p>
      <div class="post-excerpt">
        {{ post.excerpt }}
      </div>
      <a href="{{ post.url | relative_url }}" class="read-more">Read more...</a>
    </div>
  {% endfor %}
</div>

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