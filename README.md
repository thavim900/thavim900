from gettext import install


pip install django
django-admin startproject fogaca_autor
cd fogaca_autor
python manage.py startapp blog
INSTALLED_APPS = [
    ...
    'blog',
]
from django.db import models
from django.utils import timezone

class Post(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    created_at = models.DateTimeField(default=timezone.now)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.title
python manage.py makemigrations
python manage.py migrate
from django.contrib import admin
from .models import Post

admin.site.register(Post)
from django.shortcuts import render, get_object_or_404
from .models import Post

def post_list(request):
    posts = Post.objects.all().order_by('-created_at')
    return render(request, 'blog/post_list.html', {'posts': posts})

def post_detail(request, pk):
    post = get_object_or_404(Post, pk=pk)
    return render(request, 'blog/post_detail.html', {'post': post})
from django.urls import path
from . import views

urlpatterns = [
    path('', views.post_list, name='post_list'),
    path('post/<int:pk>/', views.post_detail, name='post_detail'),
]
from django.contrib import admin
from django.urls import path, include # type: ignore

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('blog.urls')),
]
<!DOCTYPE html>
<html>
<head>
    <title>Fogaça Autor - Blog</title>
</head>
<body>
    <h1>Fogaça Autor - Blog</h1>
    <ul>
        {% for post in posts %}
        <li>
            <a href="{% url 'post_detail' post.pk %}">{{ post.title }}</a>
            <p>{{ post.created_at }}</p>
        </li>
        {% endfor %}
    </ul>
</body>
</html>
<!DOCTYPE html>
<html>
<head>
    <title>{{ post.title }}</title>
</head>
<body>
    <h1>{{ post.title }}</h1>
    <p>{{ post.created_at }}</p>
    <p>{{ post.content }}</p>
    <a href="{% url 'post_list' %}">Voltar para a lista de posts</a>
</body>
</html>
python manage.py runserver
