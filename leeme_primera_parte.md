
El desarrollo del proyecto Django "Victorysports" se realizará siguiendo los pasos solicitados:

### 1\. Preparación del Entorno y Proyecto

1.  **Procedimiento para crear carpeta del Proyecto: UIII\_Victorysports\_0362**
    ```bash
    mkdir UIII_Victorysports_0362
    ```
2.  **Procedimiento para abrir VS Code sobre la carpeta UIII\_Victorysports\_0362**
    ```bash
    cd UIII_Victorysports_0362
    code .
    ```
3.  **Procedimiento para abrir terminal en VS Code**
      * En VS Code: `Terminal` -\> `New Terminal` o usar el atajo ` Ctrl + Shift + \`` (Windows/Linux) o  `Cmd + \`\` (Mac).
4.  **Procedimiento para crear carpeta entorno virtual ".venv" desde terminal de VS Code**
    ```bash
    python -m venv .venv
    ```
5.  **Procedimiento para activar el entorno virtual**
      * **Windows (Command Prompt):**
        ```bash
        .venv\Scripts\activate
        ```
      * **Windows (PowerShell):**
        ```bash
        .venv\Scripts\Activate.ps1
        ```
      * **macOS/Linux:**
        ```bash
        source .venv/bin/activate
        ```
    *(Aparecerá `(.venv)` al inicio de la línea de comandos indicando que está activo.)*
6.  **Procedimiento para activar intérprete de python**
      * En VS Code:
          * Presionar `Ctrl + Shift + P` o `Cmd + Shift + P` para abrir la **Paleta de Comandos**.
          * Escribir y seleccionar `Python: Select Interpreter`.
          * Seleccionar el intérprete que se encuentra dentro de la carpeta `.venv` (Ej: `./.venv/Scripts/python.exe` o `./.venv/bin/python`).
7.  **Procedimiento para instalar Django**
    ```bash
    pip install django
    ```
8.  **Procedimiento para crear proyecto backend\_Victorysports sin duplicar carpeta**
    ```bash
    django-admin startproject backend_Victorysports .
    ```
9.  **Procedimiento para crear aplicacion app\_victorysports**
    ```bash
    python manage.py startapp app_victorysports
    ```

### 2\. Configuración de Modelos (models.py)

10. **Aquí el modelo models.py**
      * Crear o modificar el archivo `app_victorysports/models.py` con el siguiente contenido (basado en el archivo adjunto):
    <!-- end list -->
    ```python
    from django.db import models

    # --- Modelo para Proveedor ---
    class Proveedor(models.Model):
        id_proveedor = models.AutoField(primary_key=True)
        nombre_empresa = models.CharField(max_length=150, unique=True)
        telefono_empresa = models.CharField(max_length=15)
        email_empresa = models.EmailField(max_length=100)
        pais_origen = models.CharField(max_length=50)
        contacto_principal = models.CharField(max_length=100)
        fecha_registro = models.DateField(auto_now_add=True)
        direccion = models.TextField()

        class Meta:
            verbose_name = "Proveedor"
            verbose_name_plural = "Proveedores"

        def __str__(self):
            return self.nombre_empresa

    # --- Modelo para Producto ---
    class Producto(models.Model):
        id_producto = models.AutoField(primary_key=True)
        nombre = models.CharField(max_length=255)
        precio_unitario = models.DecimalField(max_digits=10, decimal_places=2)
        stock = models.IntegerField(default=0)
        marca = models.CharField(max_length=100)
        img_url = models.URLField(max_length=255, blank=True, null=True)
        categoria = models.CharField(max_length=50)
        color = models.CharField(max_length=50)

        class Meta:
            verbose_name = "Producto"
            verbose_name_plural = "Productos"

        def __str__(self):
            return f"{self.nombre} ({self.marca})"

    # --- Modelo para Producto Proveedor (Tabla Intermedia/Many-to-Many con Datos Adicionales) ---
    class ProductoProveedor(models.Model):
        producto = models.ForeignKey(Producto, on_delete=models.CASCADE,
                                     related_name='relaciones_proveedor')
        proveedor = models.ForeignKey(Proveedor, on_delete=models.CASCADE,
                                      related_name='relaciones_producto')
        precio_compra = models.DecimalField(max_digits=10, decimal_places=2)
        fecha_ultima_compra = models.DateField(null=True, blank=True)
        cantidad_comprada = models.IntegerField(default=1)
        referencia_pedido = models.CharField(max_length=50, blank=True, null=True)
        es_proveedor_activo = models.BooleanField(default=True)

        class Meta:
            unique_together = (('producto', 'proveedor'),)
            verbose_name = "Producto por Proveedor"
            verbose_name_plural = "Productos por Proveedores"

        def __str__(self):
            return f"Relación: {self.producto.nombre} - {self.proveedor.nombre_empresa}"
    ```

### 3\. Configuraciones y Migraciones Iniciales

11. **Procedimiento para agregar app\_victorysports en settings.py de backend\_victorysports**
      * Abrir el archivo `backend_Victorysports/settings.py` y agregar `'app_victorysports'` a la lista `INSTALLED_APPS`:
    <!-- end list -->
    ```python
    # backend_Victorysports/settings.py
    # ...
    INSTALLED_APPS = [
        # ... otras apps
        'app_victorysports',  # <--- AGREGAR ESTA LÍNEA
    ]
    # ...
    ```
12. **Procedimiento para realizar las migraciones (makemigrations y migrate)**
    ```bash
    python manage.py makemigrations app_victorysports
    python manage.py migrate
    ```

### 4\. Vistas (Views)

13. **En view de app\_victorysports crear las funciones con sus códigos correspondientes...**
      * Modificar el archivo `app_victorysports/views.py` para incluir las funciones (se asume un flujo básico de CRUD sin uso de `forms.py` y solo como esqueleto):
    <!-- end list -->
    ```python
    # app_victorysports/views.py
    from django.shortcuts import render, redirect, get_object_or_404
    from django.http import HttpResponse
    from .models import Proveedor
    from django.db import IntegrityError # Para manejar unique=True
    from django.views.decorators.http import require_POST

    def inicio_victorysports(request):
        # Función para la página de inicio
        return render(request, 'inicio.html') # Se asumirá 'inicio.html' en templates

    def agregar_proveedor(request):
        if request.method == 'POST':
            # Obtener datos de POST, sin validación según la instrucción
            nombre_empresa = request.POST.get('nombre_empresa')
            telefono_empresa = request.POST.get('telefono_empresa')
            email_empresa = request.POST.get('email_empresa')
            pais_origen = request.POST.get('pais_origen')
            contacto_principal = request.POST.get('contacto_principal')
            direccion = request.POST.get('direccion')

            try:
                Proveedor.objects.create(
                    nombre_empresa=nombre_empresa,
                    telefono_empresa=telefono_empresa,
                    email_empresa=email_empresa,
                    pais_origen=pais_origen,
                    contacto_principal=contacto_principal,
                    direccion=direccion
                )
                return redirect('ver_proveedor') # Redirigir a la lista de proveedores
            except IntegrityError:
                # Manejar caso de nombre_empresa duplicado
                return render(request, 'proveedor/agregar_proveedor.html', {
                    'error_message': 'Ya existe un proveedor con ese nombre de empresa.'
                })

        return render(request, 'proveedor/agregar_proveedor.html')

    def ver_proveedor(request):
        proveedores = Proveedor.objects.all()
        return render(request, 'proveedor/ver_proveedor.html', {'proveedores': proveedores})

    def actualizar_proveedor(request, pk):
        proveedor = get_object_or_404(Proveedor, pk=pk)
        return render(request, 'proveedor/actualizar_proveedor.html', {'proveedor': proveedor})

    def realizar_actualizacion_proveedor(request, pk):
        if request.method == 'POST':
            proveedor = get_object_or_404(Proveedor, pk=pk)
            # Obtener datos de POST, sin validación
            proveedor.nombre_empresa = request.POST.get('nombre_empresa')
            proveedor.telefono_empresa = request.POST.get('telefono_empresa')
            proveedor.email_empresa = request.POST.get('email_empresa')
            proveedor.pais_origen = request.POST.get('pais_origen')
            proveedor.contacto_principal = request.POST.get('contacto_principal')
            proveedor.direccion = request.POST.get('direccion')

            try:
                proveedor.save()
                return redirect('ver_proveedor')
            except IntegrityError:
                return render(request, 'proveedor/actualizar_proveedor.html', {
                    'proveedor': proveedor,
                    'error_message': 'Ya existe otro proveedor con ese nombre de empresa.'
                })

        return redirect('ver_proveedor')

    @require_POST
    def borrar_proveedor(request, pk):
        proveedor = get_object_or_404(Proveedor, pk=pk)
        proveedor.delete()
        return redirect('ver_proveedor')
    ```

### 5\. Configuración de URLs

14. **Procedimiento para crear el archivo urls.py en app\_victorysports con el código correspondiente...**

      * Crear el archivo `app_victorysports/urls.py`:

    <!-- end list -->

    ```python
    # app_victorysports/urls.py
    from django.urls import path
    from . import views

    urlpatterns = [
        path('', views.inicio_victorysports, name='inicio_victorysports'),
        # CRUD Proveedor
        path('proveedor/agregar/', views.agregar_proveedor, name='agregar_proveedor'),
        path('proveedor/ver/', views.ver_proveedor, name='ver_proveedor'),
        path('proveedor/actualizar/<int:pk>/', views.actualizar_proveedor, name='actualizar_proveedor'),
        path('proveedor/realizar_actualizacion/<int:pk>/', views.realizar_actualizacion_proveedor, name='realizar_actualizacion_proveedor'),
        path('proveedor/borrar/<int:pk>/', views.borrar_proveedor, name='borrar_proveedor'),
    ]
    ```

15. **Realizar las configuraciones correspondiente a urls.py de backend\_victorysports...**

      * Abrir el archivo `backend_Victorysports/urls.py` y agregar la ruta de `app_victorysports`:

    <!-- end list -->

    ```python
    # backend_Victorysports/urls.py
    from django.contrib import admin
    from django.urls import path, include # Importar include

    urlpatterns = [
        path('admin/', admin.site.urls),
        path('', include('app_victorysports.urls')), # <--- AGREGAR ESTA LÍNEA
    ]
    ```

### 6\. Archivos HTML (Templates)

16. **Crear la carpeta "templates" dentro de "app\_victorysports"**

    ```bash
    mkdir app_victorysports/templates
    ```

17. **En la carpeta templates crear los archivos html (base.html, header.html, navbar.html, footer.html, inicio.html)**

      * Crear la subcarpeta `proveedor` dentro de `app_victorysports/templates` para el CRUD de proveedor.

    <!-- end list -->

    ```bash
    mkdir app_victorysports/templates/proveedor
    ```

      * Crear los archivos HTML:
          * `app_victorysports/templates/base.html`
          * `app_victorysports/templates/header.html`
          * `app_victorysports/templates/navbar.html`
          * `app_victorysports/templates/footer.html`
          * `app_victorysports/templates/inicio.html`
          * `app_victorysports/templates/proveedor/agregar_proveedor.html`
          * `app_victorysports/templates/proveedor/ver_proveedor.html`
          * `app_victorysports/templates/proveedor/actualizar_proveedor.html`
          * `app_victorysports/templates/proveedor/borrar_proveedor.html` (Este archivo no es necesario si se usa un formulario de confirmación, pero se crea por instrucción)

18. **Contenido de `base.html` (Incluyendo Bootstrap)**

    ```html
    {% load static %}
    <!DOCTYPE html>
    <html lang="es">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>{% block title %}Victorysports{% endblock %}</title>
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
        {% block extra_head %}{% endblock %}
    </head>
    <body class="d-flex flex-column min-vh-100" style="background-color: #f8f9fa;">
        {% include 'header.html' %}
        {% include 'navbar.html' %}
        <main class="container my-4 flex-grow-1">
            {% block content %}
            {% endblock %}
        </main>
        {% include 'footer.html' %}
        <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
        {% block extra_js %}{% endblock %}
    </body>
    </html>
    ```

19. **Contenido de `header.html`**

    ```html
    <header class="py-3 mb-4 border-bottom" style="background-color: #4CAF50;">
        <div class="container d-flex flex-wrap justify-content-center">
            <a href="{% url 'inicio_victorysports' %}" class="d-flex align-items-center mb-3 mb-lg-0 me-lg-auto text-decoration-none">
                <span class="fs-4 text-white">Sistema de Administración Victorysports</span>
            </a>
        </div>
    </header>
    ```

20. **Contenido de `navbar.html`** (Adaptando las opciones del archivo adjunto)

    ```html
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <div class="container">
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav me-auto mb-2 mb-lg-0">
                    <li class="nav-item">
                        <a class="nav-link active" aria-current="page" href="{% url 'inicio_victorysports' %}"><i class="fas fa-home me-1"></i> Inicio</a>
                    </li>
                    <li class="nav-item dropdown">
                        <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownProveedor" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                            <i class="fas fa-truck me-1"></i> Proveedores
                        </a>
                        <ul class="dropdown-menu" aria-labelledby="navbarDropdownProveedor">
                            <li><a class="dropdown-item" href="{% url 'agregar_proveedor' %}">Agregar Proveedor</a></li>
                            <li><a class="dropdown-item" href="{% url 'ver_proveedor' %}">Ver Proveedores</a></li>
                        </ul>
                    </li>
                    <li class="nav-item dropdown">
                        <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownCategoria" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                            <i class="fas fa-tags me-1"></i> Categorías
                        </a>
                        <ul class="dropdown-menu" aria-labelledby="navbarDropdownCategoria">
                            <li><a class="dropdown-item" href="#">Agregar Categoría</a></li>
                            <li><a class="dropdown-item" href="#">Ver Categorías</a></li>
                            <li><a class="dropdown-item" href="#">Actualizar Categoría</a></li>
                            <li><a class="dropdown-item" href="#">Borrar Categoría</a></li>
                        </ul>
                    </li>
                    <li class="nav-item dropdown">
                        <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownSalas" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                            <i class="fas fa-chair me-1"></i> Salas
                        </a>
                        <ul class="dropdown-menu" aria-labelledby="navbarDropdownSalas">
                            <li><a class="dropdown-item" href="#">Agregar Salas</a></li>
                            <li><a class="dropdown-item" href="#">Ver Salas</a></li>
                            <li><a class="dropdown-item" href="#">Actualizar Salas</a></li>
                            <li><a class="dropdown-item" href="#">Borrar Salas</a></li>
                        </ul>
                    </li>
                    <li class="nav-item dropdown">
                        <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownPeliculas" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                            <i class="fas fa-film me-1"></i> Películas
                        </a>
                        <ul class="dropdown-menu" aria-labelledby="navbarDropdownPeliculas">
                            <li><a class="dropdown-item" href="#">Agregar Películas</a></li>
                            <li><a class="dropdown-item" href="#">Ver Películas</a></li>
                            <li><a class="dropdown-item" href="#">Actualizar Películas</a></li>
                            <li><a class="dropdown-item" href="#">Borrar Películas</a></li>
                        </ul>
                    </li>
                </ul>
            </div>
        </div>
        <script src="https://kit.fontawesome.com/tu-token-de-font-awesome.js" crossorigin="anonymous"></script>
    </nav>
    ```

21. **Contenido de `footer.html`**

    ```html
    <footer class="footer mt-auto py-3 bg-dark fixed-bottom">
        <div class="container text-center">
            <span class="text-white small">
                &copy; {{ "now"|date:"Y" }} Derechos de Autor Victorysports. | Creado por Tec. Ricardo Santiago, Cbtis 128
            </span>
        </div>
    </footer>
    ```

22. **Contenido de `inicio.html`**

    ```html
    {% extends 'base.html' %}
    {% block title %}Inicio - Victorysports{% endblock %}
    {% block content %}
    <div class="row">
        <div class="col-lg-12 text-center">
            <h1 class="display-4" style="color: #4CAF50;">Bienvenido al Sistema de Administración Victorysports</h1>
            <p class="lead">Gestión eficiente de proveedores, productos y más.</p>
        </div>
    </div>
    <div class="row mt-4">
        <div class="col-lg-8 offset-lg-2">
            <div class="card shadow">
                <img src="https://via.placeholder.com/800x400?text=Victorysports+Imagen" class="card-img-top" alt="Imagen de Victorysports">
                <div class="card-body">
                    <h5 class="card-title">Información del Sistema</h5>
                    <p class="card-text">
                        Este sistema permite la administración completa de los recursos de Victorysports, incluyendo el manejo de Proveedores, Productos y la relación entre ellos. Desarrollado con Django y Python.
                    </p>
                    <ul>
                        <li>**Lenguaje:** Python</li>
                        <li>**Framework:** Django</li>
                        <li>**Editor:** VS code</li>
                    </ul>
                </div>
            </div>
        </div>
    </div>
    {% endblock %}
    ```

23. **Contenido de `proveedor/agregar_proveedor.html`**

    ```html
    {% extends 'base.html' %}
    {% block title %}Agregar Proveedor{% endblock %}
    {% block content %}
    <h2 class="mb-4" style="color: #4CAF50;">Agregar Nuevo Proveedor</h2>

    {% if error_message %}
    <div class="alert alert-danger">{{ error_message }}</div>
    {% endif %}

    <div class="card p-4">
        <form method="post" action="{% url 'agregar_proveedor' %}">
            {% csrf_token %}
            <div class="mb-3">
                <label for="nombre_empresa" class="form-label">Nombre de la Empresa</label>
                <input type="text" class="form-control" id="nombre_empresa" name="nombre_empresa" required>
            </div>
            <div class="mb-3">
                <label for="telefono_empresa" class="form-label">Teléfono</label>
                <input type="text" class="form-control" id="telefono_empresa" name="telefono_empresa">
            </div>
            <div class="mb-3">
                <label for="email_empresa" class="form-label">Email</label>
                <input type="email" class="form-control" id="email_empresa" name="email_empresa">
            </div>
            <div class="mb-3">
                <label for="pais_origen" class="form-label">País de Origen</label>
                <input type="text" class="form-control" id="pais_origen" name="pais_origen">
            </div>
            <div class="mb-3">
                <label for="contacto_principal" class="form-label">Contacto Principal</label>
                <input type="text" class="form-control" id="contacto_principal" name="contacto_principal">
            </div>
            <div class="mb-3">
                <label for="direccion" class="form-label">Dirección</label>
                <textarea class="form-control" id="direccion" name="direccion"></textarea>
            </div>

            <button type="submit" class="btn btn-success">Guardar Proveedor</button>
            <a href="{% url 'ver_proveedor' %}" class="btn btn-secondary">Cancelar</a>
        </form>
    </div>
    {% endblock %}
    ```

24. **Contenido de `proveedor/ver_proveedor.html`**

    ```html
    {% extends 'base.html' %}
    {% block title %}Ver Proveedores{% endblock %}
    {% block content %}
    <h2 class="mb-4" style="color: #4CAF50;">Listado de Proveedores</h2>
    <a href="{% url 'agregar_proveedor' %}" class="btn btn-success mb-3">Agregar Nuevo Proveedor</a>

    {% if proveedores %}
    <div class="table-responsive">
        <table class="table table-striped table-hover shadow-sm">
            <thead class="bg-success text-white">
                <tr>
                    <th>ID</th>
                    <th>Nombre de Empresa</th>
                    <th>Teléfono</th>
                    <th>Email</th>
                    <th>País</th>
                    <th>Registro</th>
                    <th>Acciones</th>
                </tr>
            </thead>
            <tbody>
                {% for proveedor in proveedores %}
                <tr>
                    <td>{{ proveedor.id_proveedor }}</td>
                    <td>{{ proveedor.nombre_empresa }}</td>
                    <td>{{ proveedor.telefono_empresa }}</td>
                    <td>{{ proveedor.email_empresa }}</td>
                    <td>{{ proveedor.pais_origen }}</td>
                    <td>{{ proveedor.fecha_registro|date:"Y-m-d" }}</td>
                    <td>
                        <a href="{% url 'actualizar_proveedor' proveedor.id_proveedor %}" class="btn btn-info btn-sm me-1" title="Ver"><i class="fas fa-eye"></i></a>
                        <a href="{% url 'actualizar_proveedor' proveedor.id_proveedor %}" class="btn btn-warning btn-sm me-1" title="Editar"><i class="fas fa-edit"></i></a>
                        <form action="{% url 'borrar_proveedor' proveedor.id_proveedor %}" method="post" class="d-inline" onsubmit="return confirm('¿Está seguro de borrar a {{ proveedor.nombre_empresa }}?');">
                            {% csrf_token %}
                            <button type="submit" class="btn btn-danger btn-sm" title="Borrar"><i class="fas fa-trash-alt"></i></button>
                        </form>
                    </td>
                </tr>
                {% endfor %}
            </tbody>
        </table>
    </div>
    {% else %}
    <div class="alert alert-warning">No hay proveedores registrados.</div>
    {% endif %}

    {% endblock %}
    {% block extra_head %}
        <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    {% endblock %}
    ```

25. **Contenido de `proveedor/actualizar_proveedor.html`**

    ```html
    {% extends 'base.html' %}
    {% block title %}Actualizar Proveedor{% endblock %}
    {% block content %}
    <h2 class="mb-4" style="color: #4CAF50;">Actualizar Proveedor: {{ proveedor.nombre_empresa }}</h2>

    {% if error_message %}
    <div class="alert alert-danger">{{ error_message }}</div>
    {% endif %}

    <div class="card p-4">
        <form method="post" action="{% url 'realizar_actualizacion_proveedor' proveedor.id_proveedor %}">
            {% csrf_token %}
            <div class="mb-3">
                <label for="nombre_empresa" class="form-label">Nombre de la Empresa</label>
                <input type="text" class="form-control" id="nombre_empresa" name="nombre_empresa" value="{{ proveedor.nombre_empresa }}" required>
            </div>
            <div class="mb-3">
                <label for="telefono_empresa" class="form-label">Teléfono</label>
                <input type="text" class="form-control" id="telefono_empresa" name="telefono_empresa" value="{{ proveedor.telefono_empresa }}">
            </div>
            <div class="mb-3">
                <label for="email_empresa" class="form-label">Email</label>
                <input type="email" class="form-control" id="email_empresa" name="email_empresa" value="{{ proveedor.email_empresa }}">
            </div>
            <div class="mb-3">
                <label for="pais_origen" class="form-label">País de Origen</label>
                <input type="text" class="form-control" id="pais_origen" name="pais_origen" value="{{ proveedor.pais_origen }}">
            </div>
            <div class="mb-3">
                <label for="contacto_principal" class="form-label">Contacto Principal</label>
                <input type="text" class="form-control" id="contacto_principal" name="contacto_principal" value="{{ proveedor.contacto_principal }}">
            </div>
            <div class="mb-3">
                <label for="direccion" class="form-label">Dirección</label>
                <textarea class="form-control" id="direccion" name="direccion">{{ proveedor.direccion }}</textarea>
            </div>

            <button type="submit" class="btn btn-success">Actualizar Proveedor</button>
            <a href="{% url 'ver_proveedor' %}" class="btn btn-secondary">Cancelar</a>
        </form>
    </div>
    {% endblock %}
    ```

26. **Contenido de `proveedor/borrar_proveedor.html`** (Formulario de confirmación opcional, la lógica se manejó en la vista)

    ```html
    {% extends 'base.html' %}
    {% block title %}Borrar Proveedor{% endblock %}
    {% block content %}
    <h2 class="mb-4 text-danger">Confirmar Borrado</h2>
    <div class="alert alert-warning">
        <p>¿Está seguro que desea eliminar al proveedor **{{ proveedor.nombre_empresa }}**?</p>
        <p>Esta acción no se puede deshacer.</p>
    </div>
    <form action="{% url 'borrar_proveedor' proveedor.id_proveedor %}" method="post">
        {% csrf_token %}
        <button type="submit" class="btn btn-danger">Confirmar Borrado</button>
        <a href="{% url 'ver_proveedor' %}" class="btn btn-secondary">Cancelar</a>
    </form>
    {% endblock %}
    ```

### 7\. Administración de Django (Admin)

27. **Procedimiento para registrar los modelos en admin.py y volver a realizar las migraciones**
      * Modificar el archivo `app_victorysports/admin.py`:
    <!-- end list -->
    ```python
    # app_victorysports/admin.py
    from django.contrib import admin
    from .models import Proveedor, Producto, ProductoProveedor

    # Opciones para Proveedor
    class ProveedorAdmin(admin.ModelAdmin):
        list_display = ('id_proveedor', 'nombre_empresa', 'pais_origen', 'fecha_registro', 'contacto_principal')
        search_fields = ('nombre_empresa', 'email_empresa')

    # Opciones para Producto
    class ProductoAdmin(admin.ModelAdmin):
        list_display = ('id_producto', 'nombre', 'precio_unitario', 'stock', 'marca', 'categoria')
        search_fields = ('nombre', 'marca', 'categoria')
        list_filter = ('categoria', 'marca')

    # Opciones para ProductoProveedor
    class ProductoProveedorAdmin(admin.ModelAdmin):
        list_display = ('producto', 'proveedor', 'precio_compra', 'cantidad_comprada', 'es_proveedor_activo')
        list_filter = ('es_proveedor_activo',)
        raw_id_fields = ('producto', 'proveedor') # Útil para Many-to-Many

    # Registro de modelos
    admin.site.register(Proveedor, ProveedorAdmin)
    admin.site.register(Producto, ProductoAdmin)
    admin.site.register(ProductoProveedor, ProductoProveedorAdmin)
    ```
      * **Volver a realizar las migraciones** (solo `makemigrations` es necesario si se agregó o modificó el modelo en `admin.py`, pero la instrucción pide ambos):
    <!-- end list -->
    ```bash
    python manage.py makemigrations
    python manage.py migrate
    ```

### 8\. Ejecución

28. **Finalmente ejecutar servidor en el puerto puerto 8036**

      * Asegúrate de que el entorno virtual esté activo (`(.venv)` al inicio de la línea de comandos).

    <!-- end list -->

    ```bash
    python manage.py runserver 8036
    ```

29. **Procedimiento para copiar y pegar el link en el navegador**

      * Una vez que el servidor esté corriendo, abrir el navegador y navegar a:

    <!-- end list -->

    ```
    http://127.0.0.1:8036/
    ```

-----

Para continuar, puedo ayudarte a crear el superusuario de Django necesario para acceder al panel de administración. ¿Te gustaría que te proporcione el comando para **crear el superusuario**?
