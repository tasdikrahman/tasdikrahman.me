---
layout: page
title: archives
---

{% capture tags %}
  {% for tag in site.tags %}
    {{ tag[1].size | plus: -10000 }}###{{ tag[0] | replace: ' ', '##' }}###{{ tag[1].size }}
  {% endfor %}
{% endcapture %}
{% assign sorted_tags = tags | split: ' ' | sort %}
{% for sorted_tag in sorted_tags %}
    {% assign items = sorted_tag | split: '###' %}
    {% assign tag = items[1] | replace: '##', ' ' %}
    {% assign count = items[2] | plus: 0 %}
    {% if count > 5 %}
        {% assign size = 5 %}
    {% else %}
        {% assign size = count %}
    {% endif %}
    <span class="tag-size-{{ size }}">
        <a class="tag-link" href="/blog/tag/{{ tag | slugify }}" rel="tag">{{ tag }}</a> ({{ count }})
    </span>
{% endfor %}
