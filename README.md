Post  :
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}{{ page.date | date_to_string }}</a>
    </li>
  {% endfor %}
</ul>
