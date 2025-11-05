¡Absolutamente\! Aquí tienes los pasos detallados con el código y comandos, siguiendo el orden y los requisitos de tu plan para el proyecto **VictorySports** con **Django** y **Python**.

-----

## 💻 Configuración Inicial del Proyecto

Estos pasos configuran el entorno de trabajo y la estructura base.

### 1\. Crear carpeta del Proyecto: `UIII_Victorysports_0362`

Abre tu terminal o símbolo del sistema y ejecuta:

```bash
mkdir UIII_Victorysports_0362
cd UIII_Victorysports_0362
```

### 2\. Abrir VS Code sobre la carpeta

Desde la terminal dentro de la carpeta:

```bash
code .
```

*(Asume que tienes configurado VS Code para abrir desde la línea de comandos)*

### 3\. Abrir terminal en VS Code

En VS Code, usa el atajo **`Ctrl + \`** o ve a **Terminal** \> **New Terminal**.

### 4\. Crear carpeta entorno virtual `.venv`

Utiliza el módulo `venv` de Python:

```bash
python -m venv .venv
```

### 5\. Activar el entorno virtual

El comando varía según el sistema operativo:

  * **Windows (PowerShell):**
    ```bash
    .venv\Scripts\Activate.ps1
    ```
  * **Windows (CMD):**
    ```bash
    .venv\Scripts\activate.bat
    ```
  * **macOS/Linux:**
    ```bash
    source .venv/bin/activate
    ```

Verás `(.venv)` al inicio de la línea de comandos indicando que está activo.

### 6\. Activar intérprete de python.

Al activar el entorno virtual, automáticamente estás usando el intérprete de Python asociado a ese entorno.

### 7\. Instalar Django

Con el entorno virtual activo:

```bash
pip install django
```

### 8\. Crear proyecto `backend_Victorysports` sin duplicar carpeta

El punto `.` al final evita la carpeta duplicada:

```bash
django-admin startproject backend_Victorysports .
```

### 9\. Crear aplicación `app_victorysports`

```bash
python manage.py startapp app_victorysports
```

### 10\. Estructura de Carpetas y Archivos (Paso 29)

Tu estructura inicial debe verse así:

```
UIII_Victorysports_0362/
├── .venv/
├── backend_Victorysports/
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py  <-- URLS principal del proyecto
│   └── wsgi.py
├── app_victorysports/
│   ├── migrations/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py  <-- Aquí va el código de los modelos
│   ├── tests.py
│   └── views.py   <-- Aquí va el código de las vistas
├── manage.py
└── (crear) app_victorysports/templates/
    ├── base.html
    ├── header.html
    ├── navbar.html
    ├── footer.html
    ├── inicio.html
    └── proveedor/
        ├── agregar_proveedor.html
        ├── ver_proveedor.html
        ├── actualizar_proveedor.html
        └── borrar_proveedor.html
```

Crea manualmente la carpeta `app_victorysports/templates` y la subcarpeta `proveedor` dentro de `templates`. Luego crea los archivos HTML dentro de ellas.

-----

## 🛠️ Configuración de Django

### 11\. Agregar `app_victorysports` en `settings.py`

Abre `backend_Victorysports/settings.py` y agrega la aplicación a `INSTALLED_APPS`:

```python
# backend_Victorysports/settings.py

INSTALLED_APPS = [
    # ... otras apps de Django
    'app_victorysports',  # <--- Agregar esta línea
]

# (Opcional, si no lo tienes)
LANGUAGE_CODE = 'es-mx'
TIME_ZONE = 'America/Mexico_City' # O tu zona horaria
```

### 12\. Configurar `urls.py` de `backend_Victorysports`

Abre `backend_Victorysports/urls.py` para enlazar con las URLs de la aplicación:

```python
# backend_Victorysports/urls.py

from django.contrib import admin
from django.urls import path, include  # Importar include

urlpatterns = [
    path('admin/', admin.site.urls),
    # Enlaza todas las rutas que empiecen con 'victorysports/' a la app
    path('', include('app_victorysports.urls')), # <--- Agrega esta línea
]
```

> **Nota:** Se usa `path('', ...)` para que las URL de la aplicación sean la raíz del proyecto.

### 13\. Crear `urls.py` en `app_victorysports`

Crea el archivo `app_victorysports/urls.py` y añade el siguiente código para el CRUD de `Proveedor` (las URLs se definirán después de crear las vistas):

```python
# app_victorysports/urls.py

from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_victorysports, name='inicio_victorysports'),
    path('proveedores/', views.ver_proveedor, name='ver_proveedor'),
    path('proveedores/agregar/', views.agregar_proveedor, name='agregar_proveedor'),
    path('proveedores/actualizar/<int:pk>/', views.actualizar_proveedor, name='actualizar_proveedor'),
    path('proveedores/realizar_actualizacion/', views.realizar_actualizacion_proveedor, name='realizar_actualizacion_proveedor'),
    path('proveedores/borrar/<int:pk>/', views.borrar_proveedor, name='borrar_proveedor'),
]
```

-----

## 🧱 Definición de Modelos (Paso 12)

### 14\. `models.py` de `app_victorysports`

Abre `app_victorysports/models.py` y define los modelos **Proveedor**, **Producto** y **ProductoProveedor**.

```python
# app_victorysports/models.py

from django.db import models

# --- Modelo para Proveedor ---
class Proveedor(models.Model):
    id_proveedor = models.AutoField(primary_key=True)  # Clave primaria explícita
    nombre_empresa = models.CharField(max_length=150, unique=True)
    telefono_empresa = models.CharField(max_length=15)
    email_empresa = models.EmailField(max_length=100)  # Uso de EmailField
    pais_origen = models.CharField(max_length=50)
    contacto_principal = models.CharField(max_length=100)
    fecha_registro = models.DateField(auto_now_add=True)  # Fecha de registro automática
    direccion = models.TextField()

    class Meta:
        verbose_name = "Proveedor"
        verbose_name_plural = "Proveedores"

    def __str__(self):
        return self.nombre_empresa

# --- Modelo para Producto ---
class Producto(models.Model):
    id_producto = models.AutoField(primary_key=True)  # Clave primaria explícita
    nombre = models.CharField(max_length=255)
    precio_unitario = models.DecimalField(max_digits=10, decimal_places=2)
    stock = models.IntegerField(default=0)
    marca = models.CharField(max_length=100)
    img_url = models.URLField(max_length=255, blank=True, null=True)  # URLField
    categoria = models.CharField(max_length=50)
    color = models.CharField(max_length=50)

    class Meta:
        verbose_name = "Producto"
        verbose_name_plural = "Productos"

    def __str__(self):
        return f"{self.nombre} ({self.marca})"

# Modelo para Producto Proveedor (Tabla Intermedia/Many-to-Many con Datos Adicionales)
class ProductoProveedor(models.Model):
    # ForeignKey a Producto
    producto = models.ForeignKey(Producto, on_delete=models.CASCADE,
                                 related_name='relaciones_proveedor')
    # ForeignKey a Proveedor
    proveedor = models.ForeignKey(Proveedor, on_delete=models.CASCADE,
                                  related_name='relaciones_producto')
    precio_compra = models.DecimalField(max_digits=10, decimal_places=2)
    fecha_ultima_compra = models.DateField(null=True, blank=True)
    cantidad_comprada = models.IntegerField(default=1)
    referencia_pedido = models.CharField(max_length=50, blank=True, null=True)
    es_proveedor_activo = models.BooleanField(default=True)

    class Meta:
        # Define la clave primaria compuesta (producto, proveedor)
        unique_together = (('producto', 'proveedor'),)
        verbose_name = "Producto por Proveedor"
        verbose_name_plural = "Productos por Proveedores"

    def __str__(self):
        return f"Relación: {self.producto.nombre} - {self.proveedor.nombre_empresa}"
```

### 15\. Realizar las migraciones (Paso 12.5)

Ejecuta los siguientes comandos en la terminal con el entorno virtual activo:

```bash
python manage.py makemigrations app_victorysports
python manage.py migrate
```

-----

## 👁️ Vistas (Views) para Proveedor (Paso 14)

### 16\. `views.py` para CRUD de Proveedor

Abre `app_victorysports/views.py` y crea las funciones para el CRUD de `Proveedor`, sin usar `forms.py`.

```python
# app_victorysports/views.py

from django.shortcuts import render, redirect, get_object_or_404
from django.urls import reverse
from .models import Proveedor
from django.db import IntegrityError # Necesario para manejar el unique=True en nombre_empresa

# Función de inicio
def inicio_victorysports(request):
    """Muestra la página de inicio."""
    return render(request, 'inicio.html') # Asume que inicio.html extiende de base.html

# CREATE: Función para agregar proveedor
def agregar_proveedor(request):
    if request.method == 'POST':
        # No se usa forms.py, se accede directamente a request.POST
        try:
            Proveedor.objects.create(
                nombre_empresa=request.POST.get('nombre_empresa'),
                telefono_empresa=request.POST.get('telefono_empresa'),
                email_empresa=request.POST.get('email_empresa'),
                pais_origen=request.POST.get('pais_origen'),
                contacto_principal=request.POST.get('contacto_principal'),
                direccion=request.POST.get('direccion')
                # fecha_registro se añade automáticamente por auto_now_add
            )
            # Redirigir a la vista de lista después de crear
            return redirect(reverse('ver_proveedor'))
        except IntegrityError:
            # Manejar el error de nombre de empresa duplicado (unique=True)
            # Podrías pasar un mensaje de error a la plantilla si fuera necesario
            context = {'error_message': 'Ya existe un proveedor con ese nombre de empresa.'}
            return render(request, 'proveedor/agregar_proveedor.html', context)
        except Exception as e:
            # Manejar otros errores
            context = {'error_message': f'Ocurrió un error: {e}'}
            return render(request, 'proveedor/agregar_proveedor.html', context)

    # Si es GET, muestra el formulario
    return render(request, 'proveedor/agregar_proveedor.html')

# READ: Función para ver proveedores
def ver_proveedor(request):
    """Muestra todos los proveedores en una tabla."""
    proveedores = Proveedor.objects.all().order_by('nombre_empresa')
    context = {'proveedores': proveedores}
    # Muestra en tabla con botones ver, editar y borrar
    return render(request, 'proveedor/ver_proveedor.html', context)

# UPDATE (Formulario): Función para obtener el proveedor a actualizar
def actualizar_proveedor(request, pk):
    """Muestra el formulario precargado con los datos del proveedor para editar."""
    proveedor = get_object_or_404(Proveedor, pk=pk)
    context = {'proveedor': proveedor}
    return render(request, 'proveedor/actualizar_proveedor.html', context)

# UPDATE (Procesamiento): Función para realizar la actualización
def realizar_actualizacion_proveedor(request):
    if request.method == 'POST':
        # Se asume que el id_proveedor viene en un campo oculto en el POST
        proveedor_id = request.POST.get('id_proveedor')
        proveedor = get_object_or_404(Proveedor, pk=proveedor_id)

        try:
            # Actualiza los campos
            proveedor.nombre_empresa = request.POST.get('nombre_empresa')
            proveedor.telefono_empresa = request.POST.get('telefono_empresa')
            proveedor.email_empresa = request.POST.get('email_empresa')
            proveedor.pais_origen = request.POST.get('pais_origen')
            proveedor.contacto_principal = request.POST.get('contacto_principal')
            proveedor.direccion = request.POST.get('direccion')
            
            # Guarda los cambios en la base de datos
            proveedor.save()

            # Redirigir a la vista de lista después de actualizar
            return redirect(reverse('ver_proveedor'))
        except IntegrityError:
            # Manejar el error de nombre de empresa duplicado
            context = {'proveedor': proveedor, 'error_message': 'Ya existe un proveedor con ese nombre de empresa.'}
            return render(request, 'proveedor/actualizar_proveedor.html', context)
        except Exception as e:
            context = {'proveedor': proveedor, 'error_message': f'Ocurrió un error: {e}'}
            return render(request, 'proveedor/actualizar_proveedor.html', context)

    # Si se accede por GET directamente, redirigir a la lista
    return redirect(reverse('ver_proveedor'))

# DELETE: Función para borrar proveedor
def borrar_proveedor(request, pk):
    """Procesa la eliminación de un proveedor."""
    proveedor = get_object_or_404(Proveedor, pk=pk)
    
    if request.method == 'POST':
        # Solo permite POST para la acción de borrado
        proveedor.delete()
        # Redirigir a la vista de lista
        return redirect(reverse('ver_proveedor'))
    
    # Si es GET, muestra una página de confirmación de borrado
    context = {'proveedor': proveedor}
    return render(request, 'proveedor/borrar_proveedor.html', context)
```

-----

## 🖼️ Archivos HTML (Templates)

> **Nota de Estilo:** Se requiere usar colores suaves, atractivos y modernos, y páginas web sencillas, utilizando Bootstrap. Por brevedad, el código de Bootstrap se representa de forma simplificada.

### 17\. `base.html` (Incluir Bootstrap)

Crea `app_victorysports/templates/base.html`.

```html
{% load static %}
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}VictorySports Admin{% endblock %}</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    {% block extra_css %}{% endblock %}
    <style>
        /* Colores suaves/modernos, ejemplo: */
        body { background-color: #f8f9fa; } /* Gris muy claro */
        .navbar { background-color: #4a6fa5 !important; } /* Azul suave */
        .footer { background-color: #343a40; color: white; }
    </style>
</head>
<body>
    {% include "header.html" %} {% include "navbar.html" %} <div class="container mt-4 mb-5">
        {% block content %}
        {% endblock %}
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
    {% include "footer.html" %} </body>
</html>
```

### 18\. `header.html` (Opcional, incluido en `base.html`)

Crea `app_victorysports/templates/header.html`. (Se incluye como bloque separado para modularidad, aunque puede ir directo en `base.html`).

```html
```

### 19\. `navbar.html` (Opciones de navegación)

Crea `app_victorysports/templates/navbar.html`.

```html
<nav class="navbar navbar-expand-lg navbar-dark bg-primary sticky-top">
    <div class="container-fluid">
        <a class="navbar-brand" href="{% url 'inicio_victorysports' %}">Sistema de Administración Victorysports</a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNavDropdown">
            <ul class="navbar-nav">
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'inicio_victorysports' %}">Inicio</a>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="proveedorDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        Proveedor
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="proveedorDropdown">
                        <li><a class="dropdown-item" href="{% url 'agregar_proveedor' %}">Agregar Proveedor</a></li>
                        <li><a class="dropdown-item" href="{% url 'ver_proveedor' %}">Ver Proveedores</a></li>
                        </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="categoriasDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        Categorías
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="categoriasDropdown">
                        <li><a class="dropdown-item" href="#">Agregar Categoría</a></li>
                        <li><a class="dropdown-item" href="#">Ver Categoría</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Categoría</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Categoría</a></li>
                    </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="productosDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        Productos
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="productosDropdown">
                        <li><a class="dropdown-item" href="#">Agregar Producto</a></li>
                        <li><a class="dropdown-item" href="#">Ver Producto</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Producto</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Producto</a></li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
</nav>
```

### 20\. `footer.html` (Incluir derechos, fecha, y fijo)

Crea `app_victorysports/templates/footer.html`.

```html
{% load static %}
<footer class="footer mt-auto py-3 bg-dark fixed-bottom"> <div class="container text-center">
        <span class="text-light">
            &copy; Derechos de Autor Victorysports {{ "now"|date:"Y" }} |
            Fecha del Sistema: {{ "now"|date:"d/m/Y H:i:s" }} |
            Creado por Tec. Ricardo Santiago, Cbtis 128
        </span>
    </div>
</footer>
```

### 21\. `inicio.html` (Información e imagen)

Crea `app_victorysports/templates/inicio.html`.

```html
{% extends "base.html" %}
{% load static %}

{% block title %}Inicio - VictorySports{% endblock %}

{% block content %}
<div class="p-5 mb-4 bg-light rounded-3 text-center">
    <div class="container-fluid py-5">
        <h1 class="display-5 fw-bold text-primary">Bienvenido al Sistema de Administración VictorySports</h1>
        <p class="col-md-8 fs-4 mx-auto">
            Este sistema permite gestionar de manera eficiente los **Proveedores** y **Productos**
            de la empresa VictorySports, incluyendo operaciones de Creación, Lectura, Actualización y Eliminación (CRUD).
        </p>
        <hr class="my-4">
        <p class="fs-6 text-muted">Desarrollado en Python con el framework Django.</p>
    </div>
</div>

<div class="row">
    <div class="col-lg-12 text-center">
        <img src="https://via.placeholder.com/800x300?text=Imagen+VictorySports" class="img-fluid rounded shadow" alt="Imagen representativa de VictorySports">
    </div>
</div>
{% endblock content %}
```

### 22\. `agregar_proveedor.html`

Crea `app_victorysports/templates/proveedor/agregar_proveedor.html`.

```html
{% extends "base.html" %}

{% block title %}Agregar Proveedor{% endblock %}

{% block content %}
<h2 class="mb-4 text-primary">Agregar Nuevo Proveedor</h2>

{% if error_message %}
    <div class="alert alert-danger" role="alert">{{ error_message }}</div>
{% endif %}

<form method="POST" action="{% url 'agregar_proveedor' %}">
    {% csrf_token %}
    <div class="card shadow-sm">
        <div class="card-body">
            <div class="mb-3">
                <label for="nombre_empresa" class="form-label">Nombre de la Empresa</label>
                <input type="text" class="form-control" id="nombre_empresa" name="nombre_empresa" required>
            </div>
            <div class="mb-3">
                <label for="contacto_principal" class="form-label">Contacto Principal</label>
                <input type="text" class="form-control" id="contacto_principal" name="contacto_principal" required>
            </div>
            <div class="mb-3">
                <label for="telefono_empresa" class="form-label">Teléfono</label>
                <input type="tel" class="form-control" id="telefono_empresa" name="telefono_empresa" required>
            </div>
            <div class="mb-3">
                <label for="email_empresa" class="form-label">Email</label>
                <input type="email" class="form-control" id="email_empresa" name="email_empresa" required>
            </div>
            <div class="mb-3">
                <label for="pais_origen" class="form-label">País de Origen</label>
                <input type="text" class="form-control" id="pais_origen" name="pais_origen" required>
            </div>
            <div class="mb-3">
                <label for="direccion" class="form-label">Dirección</label>
                <textarea class="form-control" id="direccion" name="direccion" rows="3" required></textarea>
            </div>
        </div>
        <div class="card-footer text-end">
            <button type="submit" class="btn btn-success">Guardar Proveedor</button>
            <a href="{% url 'ver_proveedor' %}" class="btn btn-secondary">Cancelar</a>
        </div>
    </div>
</form>
{% endblock content %}
```

### 23\. `ver_proveedor.html` (Mostrar en tabla con botones ver, editar y borrar)

Crea `app_victorysports/templates/proveedor/ver_proveedor.html`.

```html
{% extends "base.html" %}

{% block title %}Ver Proveedores{% endblock %}

{% block content %}
<h2 class="mb-4 text-primary">Lista de Proveedores</h2>

<div class="table-responsive">
    <table class="table table-striped table-hover align-middle">
        <thead class="table-dark">
            <tr>
                <th>ID</th>
                <th>Nombre Empresa</th>
                <th>Contacto</th>
                <th>Teléfono</th>
                <th>Email</th>
                <th>País</th>
                <th>Registro</th>
                <th class="text-center">Acciones</th>
            </tr>
        </thead>
        <tbody>
            {% for proveedor in proveedores %}
            <tr>
                <td>{{ proveedor.id_proveedor }}</td>
                <td>{{ proveedor.nombre_empresa }}</td>
                <td>{{ proveedor.contacto_principal }}</td>
                <td>{{ proveedor.telefono_empresa }}</td>
                <td>{{ proveedor.email_empresa }}</td>
                <td>{{ proveedor.pais_origen }}</td>
                <td>{{ proveedor.fecha_registro|date:"d/m/Y" }}</td>
                <td class="text-center">
                    <a href="#" class="btn btn-sm btn-info" title="Ver Detalle">Ver</a> 
                    <a href="{% url 'actualizar_proveedor' pk=proveedor.id_proveedor %}" class="btn btn-sm btn-warning" title="Editar">Editar</a>
                    <a href="{% url 'borrar_proveedor' pk=proveedor.id_proveedor %}" class="btn btn-sm btn-danger" title="Borrar">Borrar</a>
                </td>
            </tr>
            {% empty %}
            <tr>
                <td colspan="8" class="text-center">No hay proveedores registrados.</td>
            </tr>
            {% endfor %}
        </tbody>
    </table>
</div>

<div class="text-end mt-3">
    <a href="{% url 'agregar_proveedor' %}" class="btn btn-primary">Agregar Nuevo Proveedor</a>
</div>

{% endblock content %}
```

### 24\. `actualizar_proveedor.html`

Crea `app_victorysports/templates/proveedor/actualizar_proveedor.html`.

```html
{% extends "base.html" %}

{% block title %}Actualizar Proveedor: {{ proveedor.nombre_empresa }}{% endblock %}

{% block content %}
<h2 class="mb-4 text-warning">Actualizar Proveedor</h2>

{% if error_message %}
    <div class="alert alert-danger" role="alert">{{ error_message }}</div>
{% endif %}

<form method="POST" action="{% url 'realizar_actualizacion_proveedor' %}">
    {% csrf_token %}
    <input type="hidden" name="id_proveedor" value="{{ proveedor.id_proveedor }}">
    
    <div class="card shadow-sm">
        <div class="card-body">
            <div class="mb-3">
                <label for="nombre_empresa" class="form-label">Nombre de la Empresa</label>
                <input type="text" class="form-control" id="nombre_empresa" name="nombre_empresa" value="{{ proveedor.nombre_empresa }}" required>
            </div>
            <div class="mb-3">
                <label for="contacto_principal" class="form-label">Contacto Principal</label>
                <input type="text" class="form-control" id="contacto_principal" name="contacto_principal" value="{{ proveedor.contacto_principal }}" required>
            </div>
            <div class="mb-3">
                <label for="telefono_empresa" class="form-label">Teléfono</label>
                <input type="tel" class="form-control" id="telefono_empresa" name="telefono_empresa" value="{{ proveedor.telefono_empresa }}" required>
            </div>
            <div class="mb-3">
                <label for="email_empresa" class="form-label">Email</label>
                <input type="email" class="form-control" id="email_empresa" name="email_empresa" value="{{ proveedor.email_empresa }}" required>
            </div>
            <div class="mb-3">
                <label for="pais_origen" class="form-label">País de Origen</label>
                <input type="text" class="form-control" id="pais_origen" name="pais_origen" value="{{ proveedor.pais_origen }}" required>
            </div>
            <div class="mb-3">
                <label for="direccion" class="form-label">Dirección</label>
                <textarea class="form-control" id="direccion" name="direccion" rows="3" required>{{ proveedor.direccion }}</textarea>
            </div>
            <div class="mb-3">
                <label class="form-label">Fecha de Registro</label>
                <p class="form-control-static">{{ proveedor.fecha_registro|date:"d/m/Y" }}</p>
                <small class="text-muted">La fecha de registro no se puede modificar.</small>
            </div>
        </div>
        <div class="card-footer text-end">
            <button type="submit" class="btn btn-warning">Actualizar Proveedor</button>
            <a href="{% url 'ver_proveedor' %}" class="btn btn-secondary">Cancelar</a>
        </div>
    </div>
</form>
{% endblock content %}
```

### 25\. `borrar_proveedor.html`

Crea `app_victorysports/templates/proveedor/borrar_proveedor.html`.

```html
{% extends "base.html" %}

{% block title %}Borrar Proveedor{% endblock %}

{% block content %}
<div class="alert alert-danger" role="alert">
    <h4 class="alert-heading">¡Advertencia de Eliminación!</h4>
    <p>Estás a punto de eliminar al proveedor **{{ proveedor.nombre_empresa }}** (ID: {{ proveedor.id_proveedor }}). Esta acción es irreversible.</p>
    <hr>
    <p class="mb-0">¿Estás absolutamente seguro que deseas continuar?</p>
</div>

<div class="card shadow-sm">
    <div class="card-body">
        <p><strong>Detalles del Proveedor:</strong></p>
        <ul>
            <li>**Empresa:** {{ proveedor.nombre_empresa }}</li>
            <li>**Contacto:** {{ proveedor.contacto_principal }}</li>
            <li>**Teléfono:** {{ proveedor.telefono_empresa }}</li>
        </ul>

        <form method="POST" action="{% url 'borrar_proveedor' pk=proveedor.id_proveedor %}">
            {% csrf_token %}
            <button type="submit" class="btn btn-danger btn-lg">Sí, Eliminar Permanentemente</button>
            <a href="{% url 'ver_proveedor' %}" class="btn btn-secondary btn-lg">No, Volver a la Lista</a>
        </form>
    </div>
</div>
{% endblock content %}
```

-----

## 🔒 Administración y Migraciones (Paso 27)

### 26\. Registrar los modelos en `admin.py`

Abre `app_victorysports/admin.py` y registra los tres modelos.

```python
# app_victorysports/admin.py

from django.contrib import admin
from .models import Proveedor, Producto, ProductoProveedor

# Clase para personalizar la vista en el Admin
class ProveedorAdmin(admin.ModelAdmin):
    list_display = ('id_proveedor', 'nombre_empresa', 'contacto_principal', 'pais_origen', 'fecha_registro')
    search_fields = ('nombre_empresa', 'contacto_principal', 'email_empresa')
    list_filter = ('pais_origen', 'fecha_registro')
    ordering = ('nombre_empresa',)

class ProductoAdmin(admin.ModelAdmin):
    list_display = ('id_producto', 'nombre', 'marca', 'categoria', 'stock', 'precio_unitario')
    search_fields = ('nombre', 'marca', 'categoria')
    list_filter = ('categoria', 'marca', 'stock')
    ordering = ('nombre',)

class ProductoProveedorAdmin(admin.ModelAdmin):
    list_display = ('producto', 'proveedor', 'precio_compra', 'cantidad_comprada', 'es_proveedor_activo')
    list_filter = ('es_proveedor_activo', 'fecha_ultima_compra')
    search_fields = ('producto__nombre', 'proveedor__nombre_empresa')

# Registrar los modelos
admin.site.register(Proveedor, ProveedorAdmin)
admin.site.register(Producto, ProductoAdmin)
admin.site.register(ProductoProveedor, ProductoProveedorAdmin)
```

### 27\. Crear Superusuario

Si no lo has hecho, es necesario para acceder al panel de administración de Django:

```bash
python manage.py createsuperuser
```

*(Sigue las instrucciones en la terminal para crear el usuario.)*

### 28\. Volver a realizar las migraciones

Aunque no se hicieron cambios en la estructura de la base de datos (solo en `admin.py`), este paso es el proceso estándar.

```bash
python manage.py makemigrations
python manage.py migrate
```

*(Si no hay cambios en `models.py`, el primer comando indicará que no hay migraciones por hacer.)*

-----

## 🚀 Ejecución Final (Paso 30, 31)

### 29\. Ejecutar servidor en el puerto 8036

Asegúrate de que el entorno virtual esté activo y ejecuta:

```bash
python manage.py runserver 8036
```

*(El proyecto estará totalmente funcional.)*

### 30\. Copiar y pegar el link en el navegador

Copia el link que aparece en la terminal (normalmente `http://127.0.0.1:8036/`) y pégalo en tu navegador para ver la página de inicio.

-----
