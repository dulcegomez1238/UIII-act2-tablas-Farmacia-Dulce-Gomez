# UIII-act2-tablas-Farmacia-Dulce-Gomez
crear prompt con IA  para 3 tablas con django  para Farmacia 

Primero, crearemos la estructura completa de carpetas y archivos.
Crea la carpeta del proyecto:
code
Bash
mkdir UIII_Farmacia_0080
cd UIII_Farmacia_0080
Abre VS Code sobre la carpeta:
Asegúrate de estar en el directorio UIII_Farmacia_0080 en tu terminal y luego ejecuta:
code
Bash
code .
Esto abrirá VS Code con la carpeta del proyecto cargada.
Abre la terminal en VS Code:
Dentro de VS Code, ve a Terminal > New Terminal o usa el atajo Ctrl+ ` (la tilde invertida).
Ahora, sigamos con los pasos en la terminal de VS Code.
Crea la carpeta del entorno virtual .venv:
code
Bash
python -m venv .venv
Activa el entorno virtual:
En Windows:
code
Bash
.venv\Scripts\activate
En macOS/Linux:
code
Bash
source .venv/bin/activate
Verás (.venv) al inicio de tu prompt, indicando que el entorno virtual está activo.
Activa el intérprete de Python (VS Code):
Dentro de VS Code, presiona Ctrl+Shift+P (o Cmd+Shift+P en Mac), busca "Python: Select Interpreter" y selecciona el intérprete que se encuentra dentro de tu carpeta .venv.
Instala Django:
code
Bash
pip install Django
Crea el proyecto backend_Farmacia sin duplicar carpeta:
Asegúrate de estar en el directorio UIII_Farmacia_0080 y ejecuta:
code
Bash
django-admin startproject backend_Farmacia .
El . al final indica que cree el proyecto en el directorio actual.
Crea la aplicación app_Farmacia:
code
Bash
python manage.py startapp app_Farmacia
¡Ya tienes la estructura básica!
Ahora, vamos a completar los archivos dentro de VS Code.
12. Aquí el modelo models.py (en app_Farmacia/models.py):
Abre el archivo app_Farmacia/models.py y pega el siguiente código:
code
Python
from django.db import models

# ==========================================
# MODELO: PROVEEDOR
# ==========================================
class Proveedor(models.Model):
    nombre = models.CharField(max_length=100, unique=True)
    direccion = models.TextField(unique=True)
    telefono = models.BigIntegerField(unique=True)
    correo = models.EmailField(unique=True)
    tipoProducto = models.CharField(max_length=100, unique=True) # Cambiado de DecimalField a CharField
    cantidadProducto = models.DecimalField(max_digits=10, decimal_places=2) # Eliminado unique=True
    
    def __str__(self):
        return self.nombre

# ==========================================

# MODELO: PRODUCTO
# ==========================================
class Producto(models.Model):
    id_proveedor = models.ForeignKey(Proveedor, on_delete=models.CASCADE)
    tipoProducto = models.CharField(max_length=100, unique=True)
    fechaNacimiento = models.DateField(unique=True)
    precio = models.DecimalField(max_digits=10, decimal_places=2) # Eliminado unique=True
    nombre = models.CharField(max_length=100, unique=True)
    cantidadProduc = models.DecimalField(max_digits=10, decimal_places=2) # Eliminado unique=True
    
    def __str__(self):
        return self.nombre

# ==========================================
# MODELO: INVENTARIO
# ==========================================
class Inventario(models.Model):
    id_producto = models.ForeignKey(Producto, on_delete=models.CASCADE)
    id_proveedor = models.ForeignKey(Proveedor, on_delete=models.CASCADE)
    nombre = models.CharField(max_length=100, unique=True)
    tipoProducto = models.CharField(max_length=100, unique=True)
    foto = models.ImageField(upload_to='inventario/', unique=True)
    fechaCaducidad = models.DateTimeField(unique=True)
    cantidad = models.DecimalField(max_digits=10, decimal_places=2) # Eliminado unique=True
    
    def __str__(self):
        return self.nombre
Nota Importante: He realizado algunos cambios en los modelos tipoProducto de Proveedor (de DecimalField a CharField) y he eliminado unique=True de algunos campos DecimalField (cantidadProducto, precio, cantidadProduc, cantidad) y id_proveedor en Producto y Inventario, ya que estos campos rara vez son únicos para cada registro. Si necesitas que un campo sea único, puedes agregarlo de nuevo, pero es importante entender qué implica unique=True para la base de datos.
12.5 Procedimiento para realizar las migraciones:
Asegúrate de que tu entorno virtual esté activo.
code
Bash
python manage.py makemigrations
python manage.py migrate
13. Primero trabajamos con el MODELO: PROVEEDOR
14. En views.py de app_Farmacia crear las funciones:
Abre el archivo app_Farmacia/views.py y pega el siguiente código:
code
Python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Proveedor
from django.contrib import messages

# Función para la página de inicio de la farmacia
def inicio_farmacia(request):
    return render(request, 'app_Farmacia/inicio.html')

# Función para agregar un nuevo proveedor
def agregar_proveedor(request):
    if request.method == 'POST':
        nombre = request.POST['nombre']
        direccion = request.POST['direccion']
        telefono = request.POST['telefono']
        correo = request.POST['correo']
        tipoProducto = request.POST['tipoProducto']
        cantidadProducto = request.POST['cantidadProducto']

        try:
            Proveedor.objects.create(
                nombre=nombre,
                direccion=direccion,
                telefono=telefono,
                correo=correo,
                tipoProducto=tipoProducto,
                cantidadProducto=cantidadProducto
            )
            messages.success(request, 'Proveedor agregado exitosamente.')
            return redirect('ver_proveedor')
        except Exception as e:
            messages.error(request, f'Error al agregar proveedor: {e}')
            # Redirige a la misma página para mostrar el error y permitir corregir
            return render(request, 'app_Farmacia/proveedor/agregar_Proveedor.html')
    return render(request, 'app_Farmacia/proveedor/agregar_Proveedor.html')

# Función para ver todos los proveedores
def ver_proveedor(request):
    proveedores = Proveedor.objects.all()
    return render(request, 'app_Farmacia/proveedor/ver_proveedor.html', {'proveedores': proveedores})

# Función para actualizar un proveedor (muestra el formulario de edición)
def actualizar_proveedor(request, pk):
    proveedor = get_object_or_404(Proveedor, pk=pk)
    return render(request, 'app_Farmacia/proveedor/actualizar_proveedor.html', {'proveedor': proveedor})

# Función para realizar la actualización del proveedor (procesa el formulario)
def realizar_actualizacion_proveedor(request, pk):
    if request.method == 'POST':
        proveedor = get_object_or_404(Proveedor, pk=pk)
        proveedor.nombre = request.POST['nombre']
        proveedor.direccion = request.POST['direccion']
        proveedor.telefono = request.POST['telefono']
        proveedor.correo = request.POST['correo']
        proveedor.tipoProducto = request.POST['tipoProducto']
        proveedor.cantidadProducto = request.POST['cantidadProducto']
        
        try:
            proveedor.save()
            messages.success(request, 'Proveedor actualizado exitosamente.')
            return redirect('ver_proveedor')
        except Exception as e:
            messages.error(request, f'Error al actualizar proveedor: {e}')
            return render(request, 'app_Farmacia/proveedor/actualizar_proveedor.html', {'proveedor': proveedor})
    return redirect('ver_proveedor') # En caso de acceso directo GET

# Función para borrar un proveedor
def borrar_proveedor(request, pk):
    proveedor = get_object_or_404(Proveedor, pk=pk)
    if request.method == 'POST':
        try:
            proveedor.delete()
            messages.success(request, 'Proveedor eliminado exitosamente.')
            return redirect('ver_proveedor')
        except Exception as e:
            messages.error(request, f'Error al eliminar proveedor: {e}')
            return render(request, 'app_Farmacia/proveedor/borrar_Proveedor.html', {'proveedor': proveedor})
    return render(request, 'app_Farmacia/proveedor/borrar_Proveedor.html', {'proveedor': proveedor})
15. Crear la carpeta templates dentro de app_Farmacia.
21. Crear la subcarpeta Proveedor dentro de app_Farmacia/templates.
La estructura debe quedar así:
code
Code
UIII_Farmacia_0080/
├── .venv/
├── backend_Farmacia/
├── app_Farmacia/
│   ├── migrations/
│   ├── templates/
│   │   ├── app_Farmacia/
│   │   │   ├── proveedor/
│   │   │   │   ├── agregar_Proveedor.html
│   │   │   │   ├── actualizar_proveedor.html
│   │   │   │   ├── borrar_Proveedor.html
│   │   │   │   └── ver_proveedor.html
│   │   │   ├── base.html
│   │   │   ├── header.html
│   │   │   ├── navbar.html
│   │   │   ├── footer.html
│   │   │   └── inicio.html
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── tests.py
│   ├── views.py
│   └── urls.py  <-- Lo crearemos en un momento
├── manage.py
└── db.sqlite3
16, 17, 18, 19, 20. Contenido de los archivos HTML:
app_Farmacia/templates/app_Farmacia/base.html
code
Html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Sistema de Administración Farmacia{% endblock %}</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
    <!-- Font Awesome para iconos -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        body {
            display: flex;
            flex-direction: column;
            min-height: 100vh;
            background-color: #f8f9fa; /* Color de fondo suave */
        }
        .content {
            flex: 1;
            padding-top: 20px; /* Espacio para la barra de navegación fija */
            padding-bottom: 20px;
        }
        .footer {
            background-color: #e9ecef; /* Color de fondo del footer */
            color: #6c757d;
            padding: 15px 0;
            text-align: center;
            margin-top: auto; /* Mantiene el footer al final */
        }
        .navbar {
            box-shadow: 0 2px 4px rgba(0,0,0,.1); /* Sombra para la navbar */
        }
    </style>
</head>
<body>
    {% include 'app_Farmacia/navbar.html' %}

    <div class="container content">
        {% if messages %}
            {% for message in messages %}
                <div class="alert alert-{{ message.tags }} alert-dismissible fade show" role="alert">
                    {{ message }}
                    <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
                </div>
            {% endfor %}
        {% endif %}
        {% block content %}
        {% endblock %}
    </div>

    {% include 'app_Farmacia/footer.html' %}

    <!-- Bootstrap JS Bundle with Popper -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
</body>
</html>
app_Farmacia/templates/app_Farmacia/navbar.html
code
Html
<nav class="navbar navbar-expand-lg navbar-dark bg-primary sticky-top">
    <div class="container-fluid">
        <a class="navbar-brand" href="{% url 'inicio_farmacia' %}">
            <i class="fas fa-prescription-bottle-alt me-2"></i>Sistema de Administración Farmacia
        </a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNavDropdown">
            <ul class="navbar-nav ms-auto">
                <li class="nav-item">
                    <a class="nav-link active" aria-current="page" href="{% url 'inicio_farmacia' %}">
                        <i class="fas fa-home me-1"></i>Inicio
                    </a>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="navbarProveedorDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="fas fa-truck me-1"></i>Proveedor
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="navbarProveedorDropdown">
                        <li><a class="dropdown-item" href="{% url 'agregar_proveedor' %}">Agregar Proveedor</a></li>
                        <li><a class="dropdown-item" href="{% url 'ver_proveedor' %}">Ver Proveedores</a></li>
                        <!-- Los botones de actualizar y borrar se manejarán en la vista 'ver_proveedor' -->
                    </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="navbarProductoDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="fas fa-pills me-1"></i>Producto
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="navbarProductoDropdown">
                        <li><a class="dropdown-item" href="#">Agregar Producto</a></li>
                        <li><a class="dropdown-item" href="#">Ver Productos</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Producto</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Producto</a></li>
                    </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="navbarInventarioDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="fas fa-boxes me-1"></i>Inventario
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="navbarInventarioDropdown">
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
app_Farmacia/templates/app_Farmacia/footer.html
code
Html
<footer class="footer mt-auto py-3 bg-light">
    <div class="container text-center">
        <span class="text-muted">&copy; {% now "Y" %} Derechos de Autor. Creado por Alumna Dulce Gomez, Cbtis 128.</span>
        <br>
        <small class="text-muted">Fecha del Sistema: {% now "d/m/Y H:i" %}</small>
    </div>
</footer>
app_Farmacia/templates/app_Farmacia/inicio.html
code
Html
{% extends 'app_Farmacia/base.html' %}

{% block title %}Inicio - Sistema de Administración Farmacia{% endblock %}

{% block content %}
<div class="row">
    <div class="col-md-8 offset-md-2 text-center">
        <h1 class="display-4 mb-4 text-primary">¡Bienvenido al Sistema de Administración Farmacia!</h1>
        <p class="lead mb-4">
            Esta plataforma te permite gestionar de manera eficiente proveedores, productos e inventario de tu farmacia.
            Simplifica tus operaciones diarias y mantén un control preciso de tus recursos.
        </p>
        
        <div class="card shadow-sm mb-5">
            <div class="card-body">
                <h5 class="card-title text-success">Nuestra Misión</h5>
                <p class="card-text">
                    Proveer una herramienta robusta y fácil de usar para optimizar la gestión de farmacias,
                    asegurando la disponibilidad de medicamentos y una administración efectiva.
                </p>
            </div>
        </div>
        
        <img src="https://images.unsplash.com/photo-1628373302796-f94d30e3f1e9?q=80&w=2070&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D" 
             class="img-fluid rounded shadow-lg mt-4" 
             alt="Imagen de farmacia"
             style="max-width: 80%; height: auto;">
             
        <p class="mt-4 text-muted">
            Para comenzar, utiliza el menú superior para navegar entre las secciones de Proveedores, Productos e Inventario.
        </p>
    </div>
</div>
{% endblock %}
22. Archivos HTML para Proveedor (dentro de app_Farmacia/templates/app_Farmacia/proveedor/):
app_Farmacia/templates/app_Farmacia/proveedor/agregar_Proveedor.html
code
Html
{% extends 'app_Farmacia/base.html' %}

{% block title %}Agregar Proveedor{% endblock %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-8">
        <div class="card shadow-lg p-4 bg-white rounded">
            <h2 class="card-title text-center mb-4 text-primary">Agregar Nuevo Proveedor</h2>
            <form method="post">
                {% csrf_token %}
                <div class="mb-3">
                    <label for="nombre" class="form-label">Nombre del Proveedor</label>
                    <input type="text" class="form-control" id="nombre" name="nombre" required>
                </div>
                <div class="mb-3">
                    <label for="direccion" class="form-label">Dirección</label>
                    <textarea class="form-control" id="direccion" name="direccion" rows="3" required></textarea>
                </div>
                <div class="mb-3">
                    <label for="telefono" class="form-label">Teléfono</label>
                    <input type="number" class="form-control" id="telefono" name="telefono" required>
                </div>
                <div class="mb-3">
                    <label for="correo" class="form-label">Correo Electrónico</label>
                    <input type="email" class="form-control" id="correo" name="correo" required>
                </div>
                <div class="mb-3">
                    <label for="tipoProducto" class="form-label">Tipo de Producto Principal</label>
                    <input type="text" class="form-control" id="tipoProducto" name="tipoProducto" required>
                </div>
                <div class="mb-3">
                    <label for="cantidadProducto" class="form-label">Cantidad de Producto Suministrado (Ej: Kg, Unidades)</label>
                    <input type="number" step="0.01" class="form-control" id="cantidadProducto" name="cantidadProducto" required>
                </div>
                <div class="d-grid gap-2">
                    <button type="submit" class="btn btn-success btn-lg">Agregar Proveedor</button>
                    <a href="{% url 'ver_proveedor' %}" class="btn btn-secondary btn-lg">Cancelar</a>
                </div>
            </form>
        </div>
    </div>
</div>
{% endblock %}
app_Farmacia/templates/app_Farmacia/proveedor/ver_proveedor.html
code
Html
{% extends 'app_Farmacia/base.html' %}

{% block title %}Ver Proveedores{% endblock %}

{% block content %}
<div class="row">
    <div class="col-12">
        <h2 class="text-center mb-4 text-primary">Listado de Proveedores</h2>
        <div class="table-responsive">
            <table class="table table-hover table-striped table-bordered align-middle">
                <thead class="table-dark">
                    <tr>
                        <th scope="col">ID</th>
                        <th scope="col">Nombre</th>
                        <th scope="col">Dirección</th>
                        <th scope="col">Teléfono</th>
                        <th scope="col">Correo</th>
                        <th scope="col">Tipo Producto</th>
                        <th scope="col">Cantidad Producto</th>
                        <th scope="col">Acciones</th>
                    </tr>
                </thead>
                <tbody>
                    {% if proveedores %}
                        {% for proveedor in proveedores %}
                        <tr>
                            <th scope="row">{{ proveedor.id }}</th>
                            <td>{{ proveedor.nombre }}</td>
                            <td>{{ proveedor.direccion }}</td>
                            <td>{{ proveedor.telefono }}</td>
                            <td>{{ proveedor.correo }}</td>
                            <td>{{ proveedor.tipoProducto }}</td>
                            <td>{{ proveedor.cantidadProducto }}</td>
                            <td>
                                <a href="{% url 'actualizar_proveedor' proveedor.id %}" class="btn btn-sm btn-info me-2" title="Editar">
                                    <i class="fas fa-edit"></i>
                                </a>
                                <a href="{% url 'borrar_proveedor' proveedor.id %}" class="btn btn-sm btn-danger" title="Borrar">
                                    <i class="fas fa-trash-alt"></i>
                                </a>
                            </td>
                        </tr>
                        {% endfor %}
                    {% else %}
                        <tr>
                            <td colspan="8" class="text-center p-4">No hay proveedores registrados aún. <a href="{% url 'agregar_proveedor' %}">¡Agrega uno!</a></td>
                        </tr>
                    {% endif %}
                </tbody>
            </table>
        </div>
        <div class="text-center mt-4">
            <a href="{% url 'agregar_proveedor' %}" class="btn btn-primary btn-lg">
                <i class="fas fa-plus-circle me-2"></i>Agregar Nuevo Proveedor
            </a>
        </div>
    </div>
</div>
{% endblock %}
app_Farmacia/templates/app_Farmacia/proveedor/actualizar_proveedor.html
code
Html
{% extends 'app_Farmacia/base.html' %}

{% block title %}Actualizar Proveedor{% endblock %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-8">
        <div class="card shadow-lg p-4 bg-white rounded">
            <h2 class="card-title text-center mb-4 text-primary">Actualizar Proveedor: {{ proveedor.nombre }}</h2>
            <form method="post" action="{% url 'realizar_actualizacion_proveedor' proveedor.id %}">
                {% csrf_token %}
                <div class="mb-3">
                    <label for="nombre" class="form-label">Nombre del Proveedor</label>
                    <input type="text" class="form-control" id="nombre" name="nombre" value="{{ proveedor.nombre }}" required>
                </div>
                <div class="mb-3">
                    <label for="direccion" class="form-label">Dirección</label>
                    <textarea class="form-control" id="direccion" name="direccion" rows="3" required>{{ proveedor.direccion }}</textarea>
                </div>
                <div class="mb-3">
                    <label for="telefono" class="form-label">Teléfono</label>
                    <input type="number" class="form-control" id="telefono" name="telefono" value="{{ proveedor.telefono }}" required>
                </div>
                <div class="mb-3">
                    <label for="correo" class="form-label">Correo Electrónico</label>
                    <input type="email" class="form-control" id="correo" name="correo" value="{{ proveedor.correo }}" required>
                </div>
                <div class="mb-3">
                    <label for="tipoProducto" class="form-label">Tipo de Producto Principal</label>
                    <input type="text" class="form-control" id="tipoProducto" name="tipoProducto" value="{{ proveedor.tipoProducto }}" required>
                </div>
                <div class="mb-3">
                    <label for="cantidadProducto" class="form-label">Cantidad de Producto Suministrado</label>
                    <input type="number" step="0.01" class="form-control" id="cantidadProducto" name="cantidadProducto" value="{{ proveedor.cantidadProducto }}" required>
                </div>
                <div class="d-grid gap-2">
                    <button type="submit" class="btn btn-warning btn-lg">Actualizar Proveedor</button>
                    <a href="{% url 'ver_proveedor' %}" class="btn btn-secondary btn-lg">Cancelar</a>
                </div>
            </form>
        </div>
    </div>
</div>
{% endblock %}
app_Farmacia/templates/app_Farmacia/proveedor/borrar_Proveedor.html
code
Html
{% extends 'app_Farmacia/base.html' %}

{% block title %}Borrar Proveedor{% endblock %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-6">
        <div class="card shadow-lg p-4 bg-white rounded text-center">
            <h2 class="card-title mb-4 text-danger">Confirmar Eliminación de Proveedor</h2>
            <p class="lead">¿Estás seguro de que deseas eliminar al proveedor <strong>{{ proveedor.nombre }}</strong>?</p>
            <p class="text-muted">Esta acción es irreversible.</p>
            
            <form method="post">
                {% csrf_token %}
                <div class="d-grid gap-2 mt-4">
                    <button type="submit" class="btn btn-danger btn-lg"><i class="fas fa-trash-alt me-2"></i>Sí, Eliminar Proveedor</button>
                    <a href="{% url 'ver_proveedor' %}" class="btn btn-secondary btn-lg">Cancelar</a>
                </div>
            </form>
        </div>
    </div>
</div>
{% endblock %}
23. No utilizar forms.py. (Cumplido, estamos usando formularios HTML directos).
24. Procedimiento para crear el archivo urls.py en app_Farmacia:
Crea un archivo llamado urls.py dentro de la carpeta app_Farmacia y pega el siguiente código:
app_Farmacia/urls.py
code
Python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_farmacia, name='inicio_farmacia'),
    path('proveedores/', views.ver_proveedor, name='ver_proveedor'),
    path('proveedores/agregar/', views.agregar_proveedor, name='agregar_proveedor'),
    path('proveedores/actualizar/<int:pk>/', views.actualizar_proveedor, name='actualizar_proveedor'),
    path('proveedores/realizar_actualizacion/<int:pk>/', views.realizar_actualizacion_proveedor, name='realizar_actualizacion_proveedor'),
    path('proveedores/borrar/<int:pk>/', views.borrar_proveedor, name='borrar_proveedor'),
]
25. Procedimiento para agregar app_Farmacia en settings.py de backend_Farmacia:
Abre backend_Farmacia/settings.py y busca la lista INSTALLED_APPS. Agrega 'app_Farmacia' a esta lista.
code
Python
# backend_Farmacia/settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_Farmacia', # <--- AGREGAR ESTA LÍNEA
]
También, en backend_Farmacia/settings.py, para manejar las imágenes (Inventario foto), debes añadir lo siguiente al final del archivo:
code
Python
# backend_Farmacia/settings.py

import os

# ... (resto del archivo)

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
26. Realizar las configuraciones correspondiente a urls.py de backend_Farmacia para enlazar con app_Farmacia:
Abre backend_Farmacia/urls.py y modifica el archivo para incluir las URLs de app_Farmacia.
backend_Farmacia/urls.py
code
Python
from django.contrib import admin
from django.urls import path, include
from django.conf import settings # Importa settings
from django.conf.urls.static import static # Importa static

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Farmacia.urls')), # <--- Incluye las URLs de tu app
]

# Solo para desarrollo: servir archivos media
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
27. Procedimiento para registrar los modelos en admin.py y volver a realizar las migraciones.
Abre app_Farmacia/admin.py y registra el modelo Proveedor:
app_Farmacia/admin.py
code
Python
from django.contrib import admin
from .
