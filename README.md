Post  :
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
     {{ page.date | date_to_string }}
    </li>
  {% endfor %}
</ul>
