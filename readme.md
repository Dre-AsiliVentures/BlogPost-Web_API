# Django REST API 🚀

![Django REST Framework](img/Screenshot%20BlogPosttListCreate.png)

## 🔥 Setup & Installation

### 1️⃣ Create a Virtual Environment
```bash
python -m venv API_VE
source API_VE/Scripts/activate
pip install -r requirements.txt
```

### 2️⃣ Set Up Django Project
```bash
django-admin startproject mysite
cd mysite
```

### 3️⃣ Create Django App
```bash
python manage.py startapp api
```

### 4️⃣ Connect App & REST Framework
- Add `api` to `INSTALLED_APPS` in `mysite/settings.py`
- Add `rest_framework` to `INSTALLED_APPS` in `mysite/settings.py`

---

![Django REST Framework](img/Screenshot%20BlogPosttListCreate2.png)

## 📌 Define Models
Edit `api/models.py` and define your models:
```python
from django.db import models

class BlogPost(models.Model):
    title = models.CharField(max_length=255)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
```

Run migrations:
```bash
python manage.py makemigrations
python manage.py migrate
```

---

## 🔄 Create Serializers
In `api/serializers.py`, create a serializer to convert models into JSON format:
```python
from rest_framework import serializers
from .models import BlogPost

class BlogPostSerializer(serializers.ModelSerializer):
    class Meta:
        model = BlogPost
        fields = '__all__'
```

---

## 🌐 Update Views
Modify `api/views.py` to implement API endpoints:
```python
from .models import BlogPost
from .serializers import BlogPostSerializer
from rest_framework import generics, status
from rest_framework.response import Response
from rest_framework.views import APIView

class BlogPostList(APIView):
    def get(self, request, format=None):
        title = request.query_params.get("title", "")
        blog_posts = BlogPost.objects.filter(title__icontains=title) if title else BlogPost.objects.all()
        serializer = BlogPostSerializer(blog_posts, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK)

class BlogPostListCreate(generics.ListCreateAPIView):
    queryset = BlogPost.objects.all()
    serializer_class = BlogPostSerializer

    def delete(self, request, *args, **kwargs):
        BlogPost.objects.all().delete()
        return Response(status=status.HTTP_204_NO_CONTENT)

class BlogPostRetrieveUpdateDestroy(generics.RetrieveUpdateDestroyAPIView):
    queryset = BlogPost.objects.all()
    serializer_class = BlogPostSerializer
    lookup_field = "pk"
```

---

## 🚏 Define URLs
Create `api/urls.py` and add routes:
```python
from django.urls import path 
from . import views 

urlpatterns = [
    path("blogposts/", views.BlogPostListCreate.as_view(), name="blogpost_view_create"),
    path("blogposts/<int:pk>/", views.BlogPostRetrieveUpdateDestroy.as_view(), name="update")
]
```

Include it in `mysite/urls.py`:
```python
from django.urls import path, include

urlpatterns = [
    path('', include("api.urls")), 
    path('api/', include("api.urls")),
]
```

---

## 🚀 Run the Server
Start the Django development server:
```bash
python manage.py runserver
```

Your API is now live at `http://127.0.0.1:8000/blogposts/`  and `http://127.0.0.1:8000/api/blogposts/`🎉

---

## 🛠 API Endpoints
| Endpoint                 | Method | Description                    |
|-------------------------|--------|--------------------------------|
| `/blogposts/`           | GET    | Fetch all blog posts          |
| `/blogposts/`           | POST   | Create a new blog post        |
| `/blogposts/<pk>/`      | GET    | Retrieve a single blog post   |
| `/blogposts/<pk>/`      | PUT    | Update a blog post            |
| `/blogposts/<pk>/`      | DELETE | Delete a blog post            |

---

## 🎯 Next Steps
✅ Implement authentication & permissions 🔐  
✅ Add pagination & filtering 📊  
✅ Deploy to a cloud platform 🌍  

Happy Coding! 🚀

