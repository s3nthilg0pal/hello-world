---
layout: default
title: "Senthil's blogs"
---

<div class = "max-w-2xl mx-auto py-10 px-4">
    {% include navigation.html %}
    <main>
        <div class="prose dark:prose-invert">
            {% for post in site.posts %}
                <article><a href="{{ post.url }}"><h2>{{ post.title }}</h2></a><p>{{ post.subtitle }}</p></article>
            {% endfor %}
        </div>
    </main>
</div>

