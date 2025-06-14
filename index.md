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
.welcome-section {
  text-align: center;
  max-width: 800px;
  margin: 3em auto;
  padding: 0 2em;
}

.welcome-section h1 {
  font-size: 2.5em;
  color: #24292e;
  margin-bottom: 0.5em;
  font-weight: 600;
}

.welcome-text {
  font-size: 1.2em;
  line-height: 1.6;
  color: #444;
  margin: 0;
}

.two-column-layout {
  display: flex;
  gap: 4em;
  margin: 2em auto;
  max-width: 1600px;
  padding: 0 2em;
  align-items: flex-start;
}

.main-content {
  flex: 1;
  min-width: 500px;
  max-width: 700px;
}

.sidebar {
  flex: 1;
  min-width: 500px;
  max-width: 700px;
}

h2 {
  margin: 0 0 1.5em 0;
  color: #333;
  border-bottom: 2px solid #eee;
  padding-bottom: 0.5em;
  font-size: 1.8em;
}

h3 {
  margin: 0 0 0.5em 0;
  color: #333;
  font-size: 1.4em;
}

article {
  margin-bottom: 2.5em;
  padding-bottom: 2.5em;
  border-bottom: 1px solid #eee;
}

article:last-child {
  border-bottom: none;
  margin-bottom: 0;
  padding-bottom: 0;
}

article p {
  margin: 0.5em 0;
  line-height: 1.6;
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

@media (max-width: 1400px) {
  .two-column-layout {
    max-width: 100%;
    gap: 3em;
  }
  
  .main-content, .sidebar {
    min-width: 450px;
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
    padding: 0 1em;
  }
  
  .main-content {
    min-width: 100%;
    max-width: none;
  }
  
  .sidebar {
    min-width: 100%;
    max-width: none;
  }
}
</style> 