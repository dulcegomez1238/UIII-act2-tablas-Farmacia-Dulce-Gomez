# UIII-act2-tablas-Farmacia-Dulce-Gomez
crear prompt con IA  para 3 tablas con django  para Farmacia 

##1. Crear carpeta del proyecto
mkdir UIII_Farmacia_0080
##2. Abrir VS Code en la carpeta
cd UIII_Farmacia_0080
code .
##3. Abrir terminal en VS Code
Ctrl + ù o View > Terminal
##4. Crear entorno virtual
bash
python -m venv .venv
##5. Activar entorno virtual
Windows:

bash
.venv\Scripts\activate
##6. Activar intérprete de Python
Ctrl + Shift + P

Buscar "Python: Select Interpreter"

Seleccionar el de .venv

##7. Instalar Django
    bash
    pip install django
    pip install pillow  # Para manejar imágenes
##8. Crear proyecto Django
bash
django-admin startproject backend_Farmacia .
##9. Crear aplicación
bash
python manage.py startapp app_Farmacia
##Estructura de archivos y código:
backend_Farmacia/settings.py - AGREGAR:

    python
    INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_Farmacia',  # Agregar esta línea
    ]

# Agregar al final
import os
STATIC_URL = '/static/'
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
app_Farmacia/models.py - COMPLETO:
python
from django.db import models

    class Proveedor(models.Model):
    nombre = models.CharField(max_length=100, unique=True)
    direccion = models.TextField(unique=True)
    telefono = models.BigIntegerField(unique=True)
    correo = models.EmailField(unique=True)
    tipoProducto = models.CharField(max_length=100)
    cantidadProducto = models.DecimalField(max_digits=10, decimal_places=2)
    
    def __str__(self):
        return self.nombre

    class Producto(models.Model):
    id_proveedor = models.ForeignKey(Proveedor, on_delete=models.CASCADE)
    tipoProducto = models.CharField(max_length=100)
    fechaNacimiento = models.DateField()
    precio = models.DecimalField(max_digits=10, decimal_places=2)
    nombre = models.CharField(max_length=100)
    cantidadProduc = models.DecimalField(max_digits=10, decimal_places=2)
    
    def __str__(self):
        return self.nombre

    class Inventario(models.Model):
    id_producto = models.ForeignKey(Producto, on_delete=models.CASCADE)
    id_proveedor = models.ForeignKey(Proveedor, on_delete=models.CASCADE)
    nombre = models.CharField(max_length=100)
    tipoProducto = models.CharField(max_length=100)
    foto = models.ImageField(upload_to='inventario/')
    fechaCaducidad = models.DateTimeField()
    cantidad = models.DecimalField(max_digits=10, decimal_places=2)
    
    def __str__(self):
        return self.nombre
#app_Farmacia/admin.py - REGISTRAR MODELOS:
python
from django.contrib import admin
from .models import Proveedor, Producto, Inventario

admin.site.register(Proveedor)
admin.site.register(Producto)
admin.site.register(Inventario)
app_Farmacia/views.py - VISTAS PROVEEDOR:
python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Proveedor

#def inicio_farmacia(request):
    return render(request, 'inicio.html')

# VISTAS PARA PROVEEDOR
    def agregar_proveedor(request):
    if request.method == 'POST':
        Proveedor.objects.create(
            nombre=request.POST['nombre'],
            direccion=request.POST['direccion'],
            telefono=request.POST['telefono'],
            correo=request.POST['correo'],
            tipoProducto=request.POST['tipoProducto'],
            cantidadProducto=request.POST['cantidadProducto']
        )
        return redirect('ver_proveedor')
    return render(request, 'proveedor/agregar_proveedor.html')

def ver_proveedor(request):
    proveedores = Proveedor.objects.all()
    return render(request, 'proveedor/ver_proveedor.html', {'proveedores': proveedores})

def actualizar_proveedor(request, id):
    proveedor = get_object_or_404(Proveedor, id=id)
    return render(request, 'proveedor/actualizar_proveedor.html', {'proveedor': proveedor})

def realizar_actualizacion_proveedor(request, id):
    if request.method == 'POST':
        proveedor = get_object_or_404(Proveedor, id=id)
        proveedor.nombre = request.POST['nombre']
        proveedor.direccion = request.POST['direccion']
        proveedor.telefono = request.POST['telefono']
        proveedor.correo = request.POST['correo']
        proveedor.tipoProducto = request.POST['tipoProducto']
        proveedor.cantidadProducto = request.POST['cantidadProducto']
        proveedor.save()
        return redirect('ver_proveedor')
    return redirect('ver_proveedor')

def borrar_proveedor(request, id):
    proveedor = get_object_or_404(Proveedor, id=id)
    proveedor.delete()
    return redirect('ver_proveedor')
app_Farmacia/urls.py - CREAR ARCHIVO:
python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_farmacia, name='inicio_farmacia'),
    
    # URLs para Proveedor
    path('proveedor/agregar/', views.agregar_proveedor, name='agregar_proveedor'),
    path('proveedor/ver/', views.ver_proveedor, name='ver_proveedor'),
    path('proveedor/actualizar/<int:id>/', views.actualizar_proveedor, name='actualizar_proveedor'),
    path('proveedor/realizar_actualizacion/<int:id>/', views.realizar_actualizacion_proveedor, name='realizar_actualizacion_proveedor'),
    path('proveedor/borrar/<int:id>/', views.borrar_proveedor, name='borrar_proveedor'),
]
backend_Farmacia/urls.py - MODIFICAR:
python
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Farmacia.urls')),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
CREAR TEMPLATES:
app_Farmacia/templates/base.html
html
<!DOCTYPE html>
    <html lang="es">
    <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema Farmacia</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        body {
            background-color: #f8f9fa;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        .navbar {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        }
        .sidebar {
            background-color: #2c3e50;
            min-height: 100vh;
        }
        .sidebar .nav-link {
            color: #ecf0f1;
        }
        .sidebar .nav-link:hover {
            background-color: #34495e;
        }
        .main-content {
            padding: 20px;
        }
        .footer {
            background-color: #2c3e50;
            color: white;
            padding: 15px 0;
            margin-top: auto;
        }
        .card {
            border: none;
            border-radius: 15px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            transition: transform 0.3s;
        }
        .card:hover {
            transform: translateY(-5px);
        }
    </style>
    </head>
    <body>
    {% include 'header.html' %}
    {% include 'navbar.html' %}
    
    <div class="container-fluid">
        <div class="row">
            <div class="col-md-2 sidebar d-none d-md-block">
                {% include 'sidebar.html' %}
            </div>
            <div class="col-md-10 main-content">
                {% block content %}
                {% endblock %}
            </div>
        </div>
    </div>
    
    {% include 'footer.html' %}

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
    </body>
    </html>
    app_Farmacia/templates/header.html
    html
    <nav class="navbar navbar-expand-lg navbar-dark">
    <div class="container">
        <a class="navbar-brand" href="{% url 'inicio_farmacia' %}">
            <i class="fas fa-clinic-medical"></i>
            Sistema de Administración Farmacia
        </a>
    </div>
    </nav>
     app_Farmacia/templates/navbar.html
    html
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
    <div class="container">
        <div class="collapse navbar-collapse">
            <ul class="navbar-nav me-auto">
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'inicio_farmacia' %}">
                        <i class="fas fa-home"></i> Inicio
                    </a>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="proveedorDropdown" role="button" data-bs-toggle="dropdown">
                        <i class="fas fa-truck"></i> Proveedor
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="{% url 'agregar_proveedor' %}">Agregar Proveedor</a></li>
                        <li><a class="dropdown-item" href="{% url 'ver_proveedor' %}">Ver Proveedor</a></li>
                    </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="productoDropdown" role="button" data-bs-toggle="dropdown">
                        <i class="fas fa-pills"></i> Producto
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="#">Agregar Producto</a></li>
                        <li><a class="dropdown-item" href="#">Ver Producto</a></li>
                    </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="inventarioDropdown" role="button" data-bs-toggle="dropdown">
                        <i class="fas fa-boxes"></i> Inventario
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="#">Agregar Inventario</a></li>
                        <li><a class="dropdown-item" href="#">Ver Inventario</a></li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
    </nav>
    app_Farmacia/templates/sidebar.html
    html
    <ul class="nav flex-column">
    <li class="nav-item">
        <a class="nav-link" href="{% url 'inicio_farmacia' %}">
            <i class="fas fa-home"></i> Inicio
        </a>
    </li>
    <li class="nav-item">
        <a class="nav-link" href="{% url 'agregar_proveedor' %}">
            <i class="fas fa-plus"></i> Agregar Proveedor
        </a>
    </li>
    <li class="nav-item">
        <a class="nav-link" href="{% url 'ver_proveedor' %}">
            <i class="fas fa-list"></i> Ver Proveedores
        </a>
    </li>
    </ul>
    app_Farmacia/templates/footer.html
    html
    <footer class="footer mt-5">
    <div class="container text-center">
        <p>&copy; {% now "Y" %} Sistema Farmacia - Todos los derechos reservados</p>
        <p>Fecha del sistema: {% now "d/m/Y H:i" %}</p>
        <p><strong>Creado por Alumna Dulce Gomez, Cbtis 128</strong></p>
    </div>
    </footer>

##app_Farmacia/templates/inicio.html
    html
    {% extends 'base.html' %}

    {% block content %}
    <div class="row">
    <div class="col-12">
        <div class="card">
            <div class="card-body">
                <h2 class="card-title text-center mb-4">Bienvenido al Sistema de Administración Farmacia</h2>
                <div class="row">
                    <div class="col-md-6">
                        <h4>Gestión Completa de Farmacia</h4>
                        <p>Este sistema permite administrar:</p>
                        <ul>
                            <li>Proveedores</li>
                            <li>Productos</li>
                            <li>Inventario</li>
                            <li>Caducidades</li>
                        </ul>
                    </div>
                    <div class="col-md-6">
                        <img src="https://images.unsplash.com/photo-1559757148-5c350d0d3c56?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1000&q=80" 
                             alt="Farmacia" class="img-fluid rounded">
                    </div>
                </div>
            </div>
        </div>
    </div>
    </div>
{% endblock %}
##TEMPLATES PARA PROVEEDOR:
    app_Farmacia/templates/proveedor/agregar_proveedor.html
    html
    {% extends 'base.html' %}

    {% block content %}
            <div class="row">
     <div class="col-md-8 mx-auto">
        <div class="card">
            <div class="card-header bg-primary text-white">
                <h4 class="mb-0"><i class="fas fa-plus"></i> Agregar Nuevo Proveedor</h4>
            </div>
            <div class="card-body">
                <form method="POST">
                    {% csrf_token %}
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Nombre</label>
                            <input type="text" class="form-control" name="nombre" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Teléfono</label>
                            <input type="number" class="form-control" name="telefono" required>
                        </div>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Dirección</label>
                        <textarea class="form-control" name="direccion" rows="3" required></textarea>
                    </div>
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Correo Electrónico</label>
                            <input type="email" class="form-control" name="correo" required>
                        </div>
                        <div class="col-md-3 mb-3">
                            <label class="form-label">Tipo Producto</label>
                            <input type="text" class="form-control" name="tipoProducto" required>
                        </div>
                        <div class="col-md-3 mb-3">
                            <label class="form-label">Cantidad</label>
                            <input type="number" step="0.01" class="form-control" name="cantidadProducto" required>
                        </div>
                    </div>
                    <div class="d-grid gap-2 d-md-flex justify-content-md-end">
                        <a href="{% url 'ver_proveedor' %}" class="btn btn-secondary me-md-2">Cancelar</a>
                        <button type="submit" class="btn btn-primary">Guardar Proveedor</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
    </div>
{% endblock %}
##app_Farmacia/templates/proveedor/ver_proveedor.html
    html
    {% extends 'base.html' %}

    {% block content %}
    <div class="row">
    <div class="col-12">
        <div class="card">
            <div class="card-header bg-success text-white d-flex justify-content-between align-items-center">
                <h4 class="mb-0"><i class="fas fa-list"></i> Lista de Proveedores</h4>
                <a href="{% url 'agregar_proveedor' %}" class="btn btn-light">
                    <i class="fas fa-plus"></i> Nuevo Proveedor
                </a>
            </div>
            <div class="card-body">
                <div class="table-responsive">
                    <table class="table table-striped table-hover">
                        <thead class="table-dark">
                            <tr>
                                <th>ID</th>
                                <th>Nombre</th>
                                <th>Teléfono</th>
                                <th>Correo</th>
                                <th>Tipo Producto</th>
                                <th>Cantidad</th>
                                <th>Acciones</th>
                            </tr>
                        </thead>
                        <tbody>
                            {% for proveedor in proveedores %}
                            <tr>
                                <td>{{ proveedor.id }}</td>
                                <td>{{ proveedor.nombre }}</td>
                                <td>{{ proveedor.telefono }}</td>
                                <td>{{ proveedor.correo }}</td>
                                <td>{{ proveedor.tipoProducto }}</td>
                                <td>{{ proveedor.cantidadProducto }}</td>
                                <td>
                                    <a href="{% url 'actualizar_proveedor' proveedor.id %}" class="btn btn-warning btn-sm">
                                        <i class="fas fa-edit"></i> Editar
                                    </a>
                                    <a href="{% url 'borrar_proveedor' proveedor.id %}" class="btn btn-danger btn-sm" 
                                       onclick="return confirm('¿Estás seguro de eliminar este proveedor?')">
                                        <i class="fas fa-trash"></i> Eliminar
                                    </a>
                                </td>
                            </tr>
                            {% empty %}
                            <tr>
                                <td colspan="7" class="text-center">No hay proveedores registrados</td>
                            </tr>
                            {% endfor %}
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
    </div>
    {% endblock %}
app_Farmacia/templates/proveedor/actualizar_proveedor.html
html
    {% extends 'base.html' %}

    {% block content %}
    <div class="row">
    <div class="col-md-8 mx-auto">
        <div class="card">
            <div class="card-header bg-warning text-dark">
                <h4 class="mb-0"><i class="fas fa-edit"></i> Actualizar Proveedor</h4>
            </div>
            <div class="card-body">
                <form method="POST" action="{% url 'realizar_actualizacion_proveedor' proveedor.id %}">
                    {% csrf_token %}
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Nombre</label>
                            <input type="text" class="form-control" name="nombre" value="{{ proveedor.nombre }}" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Teléfono</label>
                            <input type="number" class="form-control" name="telefono" value="{{ proveedor.telefono }}" required>
                        </div>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Dirección</label>
                        <textarea class="form-control" name="direccion" rows="3" required>{{ proveedor.direccion }}</textarea>
                    </div>
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Correo Electrónico</label>
                            <input type="email" class="form-control" name="correo" value="{{ proveedor.correo }}" required>
                        </div>
                        <div class="col-md-3 mb-3">
                            <label class="form-label">Tipo Producto</label>
                            <input type="text" class="form-control" name="tipoProducto" value="{{ proveedor.tipoProducto }}" required>
                        </div>
                        <div class="col-md-3 mb-3">
                            <label class="form-label">Cantidad</label>
                            <input type="number" step="0.01" class="form-control" name="cantidadProducto" value="{{ proveedor.cantidadProducto }}" required>
                        </div>
                    </div>
                    <div class="d-grid gap-2 d-md-flex justify-content-md-end">
                        <a href="{% url 'ver_proveedor' %}" class="btn btn-secondary me-md-2">Cancelar</a>
                        <button type="submit" class="btn btn-warning">Actualizar Proveedor</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
    </div>
    {% endblock %}
app_Farmacia/templates/proveedor/borrar_proveedor.html

    html
    {% extends 'base.html' %}

    {% block content %}
    <div class="row">
    <div class="col-md-6 mx-auto">
        <div class="card">
            <div class="card-header bg-danger text-white">
                <h4 class="mb-0"><i class="fas fa-trash"></i> Confirmar Eliminación</h4>
            </div>
            <div class="card-body text-center">
                <div class="alert alert-warning">
                    <h5>¿Estás seguro de que deseas eliminar este proveedor?</h5>
                    <p class="mb-0"><strong>Esta acción no se puede deshacer.</strong></p>
                </div>
                
                <div class="card mb-3">
                    <div class="card-body">
                        <h6>Información del Proveedor:</h6>
                        <p><strong>Nombre:</strong> {{ proveedor.nombre }}</p>
                        <p><strong>Teléfono:</strong> {{ proveedor.telefono }}</p>
                        <p><strong>Correo:</strong> {{ proveedor.correo }}</p>
                        <p><strong>Tipo Producto:</strong> {{ proveedor.tipoProducto }}</p>
                    </div>
                </div>

                <form method="POST" action="{% url 'borrar_proveedor' proveedor.id %}">
                    {% csrf_token %}
                    <div class="d-grid gap-2 d-md-flex justify-content-md-center">
                        <a href="{% url 'ver_proveedor' %}" class="btn btn-secondary me-md-2">
                            <i class="fas fa-times"></i> Cancelar
                        </a>
                        <button type="submit" class="btn btn-danger">
                            <i class="fas fa-trash"></i> Sí, Eliminar Proveedor
                        </button>
                    </div>
                </form>
            </div>
        </div>
    </div>
    </div>
    {% endblock %}

##12.5 Realizar migraciones:
bash
python manage.py makemigrations
python manage.py migrate

##27. Crear superusuario (opcional):
bash
python manage.py createsuperuser
##31. Ejecutar servidor en puerto 0080:
bash
python manage.py runserver 8080
##10. Acceder al sistema:
Abrir navegador e ir a: http://localhost:8080
