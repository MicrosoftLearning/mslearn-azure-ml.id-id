---
title: Petunjuk Host Online
permalink: index.html
layout: home
---

# Microsoft Learn - Latihan Langsung

Latihan langsung berikut dirancang untuk mendukung pelatihan [Microsoft Learn](https://docs.microsoft.com/training/) .

{% menetapkan lab = site.pages | where_exp:"page", "page.url contains '/Instructions'" %}
| |
| --- | --- | 
{% for activity in labs  %}| [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}
