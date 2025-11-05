# UIII-act2-tablas-Farmacia-Dulce-Gomez
crear prompt con IA  para 3 tablas con django  para Farmacia 

Proyecto Farmacia

1. Crear carpeta del proyecto
2. bash
mkdir UIII_Farmacia_0080

2. Abrir VS Code en la carpeta
3. bash
cd UIII_Farmacia_0080
code .

3. Abrir terminal en VS Code
4. Ctrl + Ñ (Windows/Linux) o Cmd + Ñ (Mac)

O ir a Terminal > New Terminal

4. Crear entorno virtual
bash
python -m venv .venv
5. Activar entorno virtual
Windows:

bash
.venv\Scripts\activate
Mac/Linux:

bash
source .venv/bin/activate
6. Activar intérprete de Python
Ctrl + Shift + P

Buscar "Python: Select Interpreter"

Seleccionar el de .venv

7. Instalar Django
bash
pip install django
8. Crear proyecto Django
bash
django-admin startproject backend_Farmacia .
9. Ejecutar servidor en puerto 0080
bash
python manage.py runserver 0080
10. Ver en navegador
Abrir: http://localhost:0080

11. Crear aplicación
bash
python manage.py startapp app_Farmacia
12. Configurar models.py
En app_Farmacia/models.py:

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
#12.5 Realizar migraciones
bash
python manage.py makemigrations
python manage.py migrate
13-14. Configurar views.py
En app_Farmacia/views.py:

#python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Proveedor, Producto, Inventario

    def inicio_farmacia(request):
    return render(request, 'inicio.html')

    # VISTAS PARA PROVEEDOR
    def agregar_proveedor(request):
    if request.method == 'POST':
        nombre = request.POST['nombre']
        direccion = request.POST['direccion']
        telefono = request.POST['telefono']
        correo = request.POST['correo']
        tipoProducto = request.POST['tipoProducto']
        cantidadProducto = request.POST['cantidadProducto']
        
        Proveedor.objects.create(
            nombre=nombre,
            direccion=direccion,
            telefono=telefono,
            correo=correo,
            tipoProducto=tipoProducto,
            cantidadProducto=cantidadProducto
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

    def borrar_proveedor(request, id):
    proveedor = get_object_or_404(Proveedor, id=id)
    proveedor.delete()
    return redirect('ver_proveedor')
#15-23. Crear estructura de templates

Estructura de carpetas:
text
app_Farmacia/
├── templates/
│   ├── base.html
│   ├── header.html
│   ├── navbar.html
│   ├── footer.html
│   ├── inicio.html
│   └── proveedor/
│       ├── agregar_proveedor.html
│       ├── ver_proveedor.html
│       ├── actualizar_proveedor.html
│       └── borrar_proveedor.html
Archivos templates:
#base.html:
html
<!DOCTYPE html>
    <html lang="es">
    <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema Farmacia</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
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
        .main-content {
            background-color: #ecf0f1;
        }
        .card {
            border: none;
            border-radius: 15px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }
        .btn-primary {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            border: none;
        }
    </style>
    </head>
    <body>
    {% include 'navbar.html' %}
    
    <div class="container-fluid">
        <div class="row">
            <main class="col-md-12 ms-sm-auto px-md-4">
                {% block content %}
                {% endblock %}
            </main>
        </div>
    </div>
    
    {% include 'footer.html' %}
    
    <!-- Bootstrap JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
    </body>
    </html>
#navbar.html:

    html
    <nav class="navbar navbar-expand-lg navbar-dark">
    <div class="container-fluid">
        <a class="navbar-brand" href="{% url 'inicio_farmacia' %}">
            <i class="fas fa-clinic-medical"></i> Sistema de Administración Farmacia
        </a>
        
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
            <span class="navbar-toggler-icon"></span>
        </button>
        
        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav me-auto">
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'inicio_farmacia' %}">
                        <i class="fas fa-home"></i> Inicio
                    </a>
                </li>
                
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">
                        <i class="fas fa-truck"></i> Proveedor
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="{% url 'agregar_proveedor' %}">Agregar Proveedor</a></li>
                        <li><a class="dropdown-item" href="{% url 'ver_proveedor' %}">Ver Proveedor</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Proveedor</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Proveedor</a></li>
                    </ul>
                </li>
                
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">
                        <i class="fas fa-pills"></i> Producto
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="#">Agregar Producto</a></li>
                        <li><a class="dropdown-item" href="#">Ver Producto</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Producto</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Producto</a></li>
                    </ul>
                </li>
                
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">
                        <i class="fas fa-boxes"></i> Inventario
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="#">Agregar Inventario</a></li>
                        <li><a class="dropdown-item" href="#">Ver Inventario</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Inventario</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Inventario</a></li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
    </nav>

<!-- Font Awesome -->
    <script src="https://kit.fontawesome.com/your-fontawesome-kit.js" crossorigin="anonymous"></script>
    footer.html:

    html
    <footer class="bg-dark text-white text-center py-3 mt-5 fixed-bottom">
    <div class="container">
        <p class="mb-0">
            &copy; {% now "Y" %} Todos los derechos reservados | 
            <span id="fecha-sistema">{% now "d/m/Y H:i" %}</span> |
            Creado por Alumna Dulce Gomez, Cbtis 128
        </p>
    </div>
    </footer>
#inicio.html:

    html
    { % extends 'base.html' %}

    {% block content %}
    <div class="container mt-4">
    <div class="row">
        <div class="col-md-8 mx-auto">
            <div class="card">
                <div class="card-header bg-primary text-white">
                    <h3 class="text-center">Bienvenido al Sistema de Gestión Farmacéutica</h3>
                </div>
                <div class="card-body">
                    <div class="text-center mb-4">
                        <img src="https://images.unsplash.com/photo-1559757148-5c350d0d3c56?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1000&q=80" 
                             alt="Farmacia" class="img-fluid rounded" style="max-height: 300px;">
                    </div>
                    
                    <h4 class="text-center text-primary">Sistema Integral de Gestión</h4>
                    <p class="lead text-center">
                        Este sistema permite gestionar proveedores, productos e inventario 
                        de manera eficiente y organizada.
                    </p>
                    
                    <div class="row mt-4">
                        <div class="col-md-4">
                            <div class="card text-center">
                                <div class="card-body">
                                    <i class="fas fa-truck fa-3x text-primary mb-3"></i>
                                    <h5>Gestión de Proveedores</h5>
                                    <p>Administra toda la información de tus proveedores</p>
                                </div>
                            </div>
                        </div>
                        <div class="col-md-4">
                            <div class="card text-center">
                                <div class="card-body">
                                    <i class="fas fa-pills fa-3x text-success mb-3"></i>
                                    <h5>Control de Productos</h5>
                                    <p>Gestiona el catálogo completo de productos</p>
                                </div>
                            </div>
                        </div>
                        <div class="col-md-4">
                            <div class="card text-center">
                                <div class="card-body">
                                    <i class="fas fa-boxes fa-3x text-warning mb-3"></i>
                                    <h5>Inventario</h5>
                                    <p>Controla el stock y caducidad de productos</p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    </div>
    {% endblock %}
#proveedor/agregar_proveedor.html:

    html
    {% extends 'base.html' %}

    {% block content %}
    <div class="container mt-4">
    <div class="row">
        <div class="col-md-8 mx-auto">
            <div class="card">
                <div class="card-header bg-success text-white">
                    <h3 class="text-center">Agregar Nuevo Proveedor</h3>
                </div>
                <div class="card-body">
                    <form method="POST">
                        {% csrf_token %}
                        <div class="row">
                            <div class="col-md-6 mb-3">
                                <label class="form-label">Nombre:</label>
                                <input type="text" class="form-control" name="nombre" required>
                            </div>
                            <div class="col-md-6 mb-3">
                                <label class="form-label">Teléfono:</label>
                                <input type="number" class="form-control" name="telefono" required>
                            </div>
                        </div>
                        
                        <div class="mb-3">
                            <label class="form-label">Dirección:</label>
                            <textarea class="form-control" name="direccion" rows="3" required></textarea>
                        </div>
                        
                        <div class="row">
                            <div class="col-md-6 mb-3">
                                <label class="form-label">Correo Electrónico:</label>
                                <input type="email" class="form-control" name="correo" required>
                            </div>
                            <div class="col-md-6 mb-3">
                                <label class="form-label">Tipo de Producto:</label>
                                <input type="text" class="form-control" name="tipoProducto" required>
                            </div>
                        </div>
                        
                        <div class="mb-3">
                            <label class="form-label">Cantidad de Producto:</label>
                            <input type="number" step="0.01" class="form-control" name="cantidadProducto" required>
                        </div>
                        
                        <div class="text-center">
                            <button type="submit" class="btn btn-success btn-lg">
                                <i class="fas fa-save"></i> Guardar Proveedor
                            </button>
                            <a href="{% url 'ver_proveedor' %}" class="btn btn-secondary btn-lg">
                                <i class="fas fa-list"></i> Ver Proveedores
                            </a>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
    </div>
    {% endblock %}
#proveedor/ver_proveedor.html:

    html
    {% extends 'base.html' %}

    {% block content %}
    <div class="container mt-4">
    <div class="card">
        <div class="card-header bg-primary text-white">
            <h3 class="text-center">Lista de Proveedores</h3>
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
            
            <div class="text-center mt-3">
                <a href="{% url 'agregar_proveedor' %}" class="btn btn-success btn-lg">
                    <i class="fas fa-plus"></i> Agregar Nuevo Proveedor
                </a>
                <a href="{% url 'inicio_farmacia' %}" class="btn btn-secondary btn-lg">
                    <i class="fas fa-home"></i> Ir al Inicio
                </a>
            </div>
        </div>
    </div>
    </div>
    {% endblock %}
#proveedor/actualizar_proveedor.html:

html
{% extends 'base.html' %}

    {% block content %}
    <div class="container mt-4">
    <div class="row">
        <div class="col-md-8 mx-auto">
            <div class="card">
                <div class="card-header bg-warning text-white">
                    <h3 class="text-center">Actualizar Proveedor</h3>
                </div>
                <div class="card-body">
                    <form method="POST" action="{% url 'realizar_actualizacion_proveedor' proveedor.id %}">
                        {% csrf_token %}
                        <div class="row">
                            <div class="col-md-6 mb-3">
                                <label class="form-label">Nombre:</label>
                                <input type="text" class="form-control" name="nombre" value="{{ proveedor.nombre }}" required>
                            </div>
                            <div class="col-md-6 mb-3">
                                <label class="form-label">Teléfono:</label>
                                <input type="number" class="form-control" name="telefono" value="{{ proveedor.telefono }}" required>
                            </div>
                        </div>
                        
                        <div class="mb-3">
                            <label class="form-label">Dirección:</label>
                            <textarea class="form-control" name="direccion" rows="3" required>{{ proveedor.direccion }}</textarea>
                        </div>
                        
                        <div class="row">
                            <div class="col-md-6 mb-3">
                                <label class="form-label">Correo Electrónico:</label>
                                <input type="email" class="form-control" name="correo" value="{{ proveedor.correo }}" required>
                            </div>
                            <div class="col-md-6 mb-3">
                                <label class="form-label">Tipo de Producto:</label>
                                <input type="text" class="form-control" name="tipoProducto" value="{{ proveedor.tipoProducto }}" required>
                            </div>
                        </div>
                        
                        <div class="mb-3">
                            <label class="form-label">Cantidad de Producto:</label>
                            <input type="number" step="0.01" class="form-control" name="cantidadProducto" value="{{ proveedor.cantidadProducto }}" required>
                        </div>
                        
                        <div class="text-center">
                            <button type="submit" class="btn btn-warning btn-lg">
                                <i class="fas fa-sync-alt"></i> Actualizar Proveedor
                            </button>
                            <a href="{% url 'ver_proveedor' %}" class="btn btn-secondary btn-lg">
                                <i class="fas fa-times"></i> Cancelar
                            </a>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
    </div>
    {% endblock %}
#24. Crear urls.py en app_Farmacia
En app_Farmacia/urls.py:

python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_farmacia, name='inicio_farmacia'),
    
    # URLs para Proveedor
    path('agregar_proveedor/', views.agregar_proveedor, name='agregar_proveedor'),
    path('ver_proveedor/', views.ver_proveedor, name='ver_proveedor'),
    path('actualizar_proveedor/<int:id>/', views.actualizar_proveedor, name='actualizar_proveedor'),
    path('realizar_actualizacion_proveedor/<int:id>/', views.realizar_actualizacion_proveedor, name='realizar_actualizacion_proveedor'),
    path('borrar_proveedor/<int:id>/', views.borrar_proveedor, name='borrar_proveedor'),
]
25. Agregar app en settings.py
En backend_Farmacia/settings.py:

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
26. Configurar urls.py principal
En backend_Farmacia/urls.py:

python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Farmacia.urls')),
]
27. Registrar modelos en admin.py
En app_Farmacia/admin.py:

python
from django.contrib import admin
from .models import Proveedor, Producto, Inventario

@admin.register(Proveedor)
class ProveedorAdmin(admin.ModelAdmin):
    list_display = ['nombre', 'telefono', 'correo', 'tipoProducto']
    search_fields = ['nombre', 'correo']

@admin.register(Producto)
class ProductoAdmin(admin.ModelAdmin):
    list_display = ['nombre', 'tipoProducto', 'precio', 'cantidadProduc']
    search_fields = ['nombre', 'tipoProducto']

@admin.register(Inventario)
class InventarioAdmin(admin.ModelAdmin):
    list_display = ['nombre', 'tipoProducto', 'fechaCaducidad', 'cantidad']
    search_fields = ['nombre', 'tipoProducto']
Migraciones finales
bash
python manage.py makemigrations
python manage.py migrate
Crear superusuario
bash
python manage.py createsuperuser
Ejecutar servidor
bash
python manage.py runserver 0080
Estructura final del proyecto:
text
UIII_Farmacia_0080/
├── .venv/
├── backend_Farmacia/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── app_Farmacia/
│   ├── migrations/
│   ├── templates/
│   │   ├── base.html
│   │   ├── header.html
│   │   ├── navbar.html
│   │   ├── footer.html
│   │   ├── inicio.html
│   │   └── proveedor/
│   │       ├── agregar_proveedor.html
│   │       ├── ver_proveedor.html
│   │       ├── actualizar_proveedor.html
│   │       └── borrar_proveedor.html
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── tests.py
│   ├── urls.py
│   └── views.py
├── db.sqlite3
└── manage.py


