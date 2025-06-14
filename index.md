---
layout: default
title: EarlySignal
---

<div class="welcome-section">
  <h1>Welcome to EarlySignal</h1>
  <p class="welcome-text">EarlySignal is an innovative project focused on early detection of incorrect system and service behaviors using Machine Learning.</p>
</div>

<div class="two-column-layout">
  <div class="main-content">
    <h2>Blog Posts</h2>
    {% for post in site.posts %}
      {% unless post.categories contains 'announcement' %}
        <article>
          <h3><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h3>
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
          <p>{{ post.content | strip_html | truncatewords: 50 }}</p>
        </article>
      {% endif %}
    {% endfor %}
  </div>
</div>

<style>
body {
  background-color: #1a1a1a;
  color: white;
}

.welcome-section {
  text-align: center;
  max-width: 800px;
  margin: 3em auto;
  padding: 0 2em;
}

.welcome-section h1 {
  font-size: 2.5em;
  color: white;
  margin-bottom: 0.5em;
  font-weight: 600;
}

.welcome-text {
  font-size: 1.2em;
  line-height: 1.6;
  color: #e0e0e0;
  margin: 0;
}

.two-column-layout {
  display: flex;
  gap: 4em;
  margin: 2em 0;
  max-width: 1600px;
  padding: 0 2em 0 0;
  align-items: flex-start;
  background-color: #1a1a1a;
}

.main-content, .sidebar {
  padding-top: 1em;
}

.main-content {
  flex: 1;
}

.sidebar {
  flex: 1;
}

h2 {
  margin: -1em 0 1.5em 0;
  color: white;
  border-bottom: 2px solid #333;
  padding-bottom: 0.5em;
  font-size: 1.8em;
}

h3 {
  margin: 0 0 0.5em 0;
  color: white;
  font-size: 1.4em;
  padding-left: 2em;
}

article {
  margin-bottom: 2.5em;
  padding-bottom: 2.5em;
  border-bottom: 1px solid #333;
}

article:last-child {
  border-bottom: none;
  margin-bottom: 0;
  padding-bottom: 0;
}

article p {
  margin: 0.5em 0;
  line-height: 1.6;
  color: #e0e0e0;
  padding-left: 2em;
}

.post-meta {
  color: #b0b0b0;
  font-size: 0.9em;
}

a {
  color: #66b3ff;
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}

@media (max-width: 1400px) {
  .two-column-layout {
    max-width: 100%;
    gap: 3em;
  }
}

@media (max-width: 768px) {
  .welcome-section {
    margin: 2em auto;
    padding: 0 1em;
  }

  .welcome-section h1 {
    font-size: 2em;
  }

  .welcome-text {
    font-size: 1.1em;
  }

  .two-column-layout {
    flex-direction: column;
    gap: 2em;
    padding: 0 1em 0 0;
  }
}
</style> 