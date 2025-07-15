# mydraft
optional

<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <title>Қишлоқ Хўжалиги Бозори</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
    <link rel="stylesheet" href="/static/css/style.css">
</head>
<body>
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <div class="container-fluid">
    <a class="navbar-brand" href="/">AgroMarket</a>
    <form class="d-flex" method="get">
        <input class="form-control me-2" type="search" placeholder="Mahsulot qidiring" name="q">
        <select name="region" class="form-select">
            <option value="">Barcha hududlar</option>
            <option value="Toshkent">Toshkent</option>
            <option value="Andijon">Andijon</option>
        </select>
        <button class="btn btn-outline-success ms-2" type="submit">Qidirish</button>
    </form>
  </div>
</nav>

<div class="container mt-4">
    <div class="row">
        {% for p in products %}
        <div class="col-md-4">
            <div class="card mb-4">
                <img src="{{ p.image.url }}" class="card-img-top" alt="{{ p.name }}">
                <div class="card-body">
                    <h5 class="card-title">{{ p.name }}</h5>
                    <p class="card-text">Narxi: {{ p.price }} so'm</p>
                    <p>{{ p.region }}</p>
                    <button class="btn btn-success">Sotib olish</button>
                </div>
            </div>
        </div>
        {% endfor %}
    </div>
</div>
</body>
</html>

#urls.py

from django.urls import path
from django.contrib.auth import views as auth_views
from . import views

urlpatterns = [
    path('', views.index, name='index'),
    path('login/', auth_views.LoginView.as_view(template_name='login.html'), name='login'),
    path('logout/', auth_views.LogoutView.as_view(), name='logout'),
    path('add/', views.add_product, name='add_product'),
]

#forms.py

from django import forms
from .models import Product

class ProductForm(forms.ModelForm):
    class Meta:
        model = Product
        fields = ['name', 'image', 'price', 'description', 'region']

#models.py

from django.db import models
from django.contrib.auth.models import User

class Product(models.Model):
    REGION_CHOICES = [
        ('Toshkent', 'Toshkent'),
        ('Andijon', 'Andijon'),
        ('Samarqand', 'Samarqand'),
        # boshqa hududlar...
    ]
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    name = models.CharField(max_length=100)
    image = models.ImageField(upload_to='products/')
    price = models.DecimalField(max_digits=10, decimal_places=2)
    description = models.TextField()
    region = models.CharField(max_length=50, choices=REGION_CHOICES)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.name

#settings.py

INSTALLED_APPS = [
    ...,
    'market',
    'rest_framework',
    'django.contrib.staticfiles',
    'django.contrib.humanize',
]

MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'

STATIC_URL = '/static/'
STATICFILES_DIRS = [BASE_DIR / 'market/static']

LOGIN_REDIRECT_URL = '/'
LOGOUT_REDIRECT_URL = '/'

#urls.py

from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('market.urls')),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)


#views.py

from django.shortcuts import render, redirect
from .models import Product
from .forms import ProductForm
from django.contrib.auth.decorators import login_required
from django.db.models import Q

def index(request):
    query = request.GET.get('q')
    region = request.GET.get('region')
    products = Product.objects.all()
    if query:
        products = products.filter(name__icontains=query)
    if region:
        products = products.filter(region=region)
    return render(request, 'index.html', {'products': products})

@login_required
def add_product(request):
    if request.method == 'POST':
        form = ProductForm(request.POST, request.FILES)
        if form.is_valid():
            product = form.save(commit=False)
            product.user = request.user
            product.save()
            return redirect('/')
    else:
        form = ProductForm()
    return render(request, 'add_product.html', {'form': form})
