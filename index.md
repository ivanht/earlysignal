---
layout: default
title: EarlySignal
---

# Welcome to EarlySignal

EarlySignal is an innovative project focused on early detection of incorrect system and service behaviors using Machine Learning.

<div class="two-column-layout">
  <div class="main-content">
    <h2>Blog Posts</h2>
    {% for post in site.posts %}
      {% unless post.categories contains 'announcement' %}
        <article>
          <h3><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h3>
          <p>Date: {{ post.date }}</p>
          <p>{{ post.content | strip_html | truncatewords: 50 }}</p>
        </article>
      {% endunless %}
    {% endfor %}
  </div>

  <div class="sidebar">
    <h2>Announcements</h2>
    {% for post in site.posts %}
      {% if post.categories contains 'announcement' %}
        <article>
          <h3><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h3>
          <p>Date: {{ post.date }}</p>
          <p>{{ post.content | strip_html | truncatewords: 50 }}</p>
        </article>
      {% endif %}
    {% endfor %}
  </div>
</div>

<style>
.two-column-layout {
  display: flex;
  gap: 3em;
  margin-top: 2em;
  align-items: flex-start;
}

.main-content {
  flex: 2;
  min-width: 0; /* Prevents flex item from overflowing */
}

.sidebar {
  flex: 1;
  min-width: 300px;
  max-width: 400px;
}

h2 {
  margin-top: 0;
  margin-bottom: 1em;
  color: #333;
  border-bottom: 2px solid #eee;
  padding-bottom: 0.5em;
}

h3 {
  margin-top: 0;
  margin-bottom: 0.5em;
  color: #333;
}

article {
  margin-bottom: 2em;
  padding-bottom: 2em;
  border-bottom: 1px solid #eee;
}

article:last-child {
  border-bottom: none;
  margin-bottom: 0;
  padding-bottom: 0;
}

article p {
  margin: 0.5em 0;
}

.post-meta {
  color: #666;
  font-size: 0.9em;
}

a {
  color: #0366d6;
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}

@media (max-width: 768px) {
  .two-column-layout {
    flex-direction: column;
    gap: 2em;
  }
  
  .sidebar {
    min-width: 100%;
    max-width: none;
  }
}
</style> 