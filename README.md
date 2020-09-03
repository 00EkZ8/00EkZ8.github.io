Post  :
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}{{ page.date }}</a>
    </li>
  {% endfor %}
</ul>
