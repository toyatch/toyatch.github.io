---
layout: page
title: Tags
permalink: /tags/
---
{% include tags.md %}

{% for tag in sorted_tags %}
  <div class="post-list-heading" id="{{tag[0]}}">
    {{ tag[0] }}
  </div>
  <ul class="post-list">
    {% for post in tag[1] %}
      <li>
        <span class='post-meta'>
          {{ post.date | date_to_string: "ordinal", "US" }}
          -
          {{ post.tags | join: '/' }}
        </span>
        <a class='post-link' href="{{post.url}}">
         {{ post.title }}
        </a>
      </li>
    {% endfor %}
  </ul>
{% endfor %}
