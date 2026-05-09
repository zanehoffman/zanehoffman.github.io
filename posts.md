---
title: Posts
permalink: /posts/
---

<section class="archive">
  <h1>All Posts</h1>
  <div class="post-list">
    {% for post in site.posts %}
      <article class="post-card">
        <a href="{{ post.url | relative_url }}">
          <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>
          <h2>{{ post.title }}</h2>
          <p>{{ post.excerpt | strip_html | truncate: 180 }}</p>
        </a>
        {% if post.tags %}
          <div class="tag-row">
            {% for tag in post.tags %}
              <span>{{ tag }}</span>
            {% endfor %}
          </div>
        {% endif %}
      </article>
    {% endfor %}
  </div>
</section>
