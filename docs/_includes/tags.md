{% assign sorted_tags = site.tags | sort %}
<p>
  {% for tag in sorted_tags %}
  <a href="/tags#{{tag[0]}}">
    {{ tag[0] }}
  </a> /
  {% endfor %}
</p>
