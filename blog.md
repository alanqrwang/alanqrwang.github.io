---
layout: default
---
<a href="{{ site.baseurl }}/coffee">Coffee</a>

<a href="{{ site.baseurl }}/about">About</a>

<a href="{{ site.baseurl }}/readinglist">Reading List</a>

<a href="{{ site.baseurl }}/blog">Blog</a>
            
<div class="posts">
  {% for post in site.posts %}
    <article class="post">

      <h1><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h1>

      <div class="entry">
        {{ post.excerpt }}
      </div>

      <a href="{{ site.baseurl }}{{ post.url }}" class="read-more">Read More</a>
    </article>
  {% endfor %}
</div>
