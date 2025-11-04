Perfecto — te dejo **un procedimiento completo, paso a paso** (en español) con todo el código que necesitas para crear el proyecto **UIII_Autozone_0544** en Django + VS Code, configurar el entorno, crear la app `app_Autozone`, el CRUD de **Proveedor** (sin `forms.py`) y las plantillas. También incluyo los archivos `urls.py`, `views.py`, `admin.py`, y ejemplos de HTML (con Bootstrap y colores suaves). Sigue los pasos en orden y copia/pega el código en los ficheros indicados.

---

# 1 — Crear la carpeta del proyecto `UIII_Autozone_0544`

En tu explorador de archivos o por terminal:

```bash
# Desde la carpeta donde quieres crear el proyecto:
mkdir UIII_Autozone_0544
cd UIII_Autozone_0544
```

# 2 — Abrir VS Code sobre la carpeta `UIII_Autozone_0544`

* Abre VS Code.
* Archivo → Abrir carpeta → selecciona `UIII_Autozone_0544`.
* O desde terminal:

```bash
code .
```

# 3 — Abrir terminal en VS Code

* Menú `Terminal > New Terminal` o usa atajo: `Ctrl + ` (backtick) en Windows/Linux o `⌃`+` (macOS).
* Verás el terminal con la ruta a `UIII_Autozone_0544`.

# 4 — Crear carpeta entorno virtual `.venv` desde terminal de VS Code

Desde la terminal, usando el Python del sistema:

```bash
# Windows (PowerShell o CMD)
python -m venv .venv

# macOS / Linux
python3 -m venv .venv
```

Se creará la carpeta oculta `.venv` dentro del proyecto.

# 5 — Activar el entorno virtual

Dependiendo de tu OS y shell:

* **Windows (PowerShell)**:

```powershell
.venv\Scripts\Activate.ps1
```

(si política restringe, usa `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass` temporalmente)

* **Windows (CMD)**:

```cmd
.venv\Scripts\activate.bat
```

* **macOS / Linux (bash, zsh)**:

```bash
source .venv/bin/activate
```

Al activarse verás `(.venv)` al inicio de la línea de tu terminal.

# 6 — Activar intérprete de Python en VS Code

* Abre la paleta `Ctrl+Shift+P` → `Python: Select Interpreter`.
* Elige el intérprete que apunte a `UIII_Autozone_0544/.venv/...` (verás `.venv` en la ruta).
  Esto hace que VS Code use ese Python para linting, ejecución, debugging y el terminal integrado.

# 7 — Instalar Django

Con el entorno activo:

```bash
pip install django
```

(Verifica versión con `python -m django --version`)

# 8 — Crear proyecto `backend_Autozone` sin duplicar carpeta

Importante: crea el *proyecto Django dentro de la carpeta actual* sin crear otra carpeta contenedora extra. En la raíz (`UIII_Autozone_0544`) ejecuta:

```bash
# Esto crea la estructura del proyecto en la carpeta actual (observe el punto final)
django-admin startproject backend_Autozone .
```

> Si omites el `.` Django creará una carpeta `backend_Autozone` dentro de la actual, lo que produce una carpeta extra. Usando `.` lo crea en la carpeta actual.

# 9 — Ejecutar servidor en el puerto 8544

Primero aplica migraciones básicas (más abajo hay pasos de migraciones con tus modelos). Para ahora:

```bash
python manage.py migrate
python manage.py runserver 0.0.0.0:8544
```

ó si quieres solo localhost:

```bash
python manage.py runserver 127.0.0.1:8544
```

# 10 — Copiar y pegar el link en el navegador

Cuando `runserver` arranque verás en la terminal:

```
Starting development server at http://127.0.0.1:8544/
```

Copia esa URL y pégala en tu navegador: `http://127.0.0.1:8544/`

# 11 — Crear la aplicación `app_Autozone`

En la raíz del proyecto (misma carpeta donde está `manage.py`):

```bash
python manage.py startapp app_Autozone
```

Se creará la carpeta `app_Autozone` con sus ficheros.

# 12 — Aquí está tu `models.py` (lo que pegaste)

Crea/edita `app_Autozone/models.py` con exactamente el código que nos diste (copiar y pegar). Para comodidad lo incluyo otra vez (puedes sobrescribir):

```python
from django.db import models

# ==========================================
# MODELO: PROVEEDOR
# ==========================================
class Proveedor(models.Model):
    nombre = models.CharField(max_length=100)
    direccion = models.CharField(max_length=200)
    telefono = models.CharField(max_length=20)
    email = models.EmailField(max_length=50)
    fecha_registro = models.DateField()
    dias_entrega = models.CharField(max_length=150)
    hora_entrega = models.CharField(max_length=50)
    
    class Meta:
        verbose_name = 'Proveedor'
        verbose_name_plural = 'Proveedores'
    
    def __str__(self):
        return self.nombre

# ==========================================
# MODELO: SUCURSAL
# ==========================================
class Sucursal(models.Model):
    nombre = models.CharField(max_length=100)
    direccion = models.CharField(max_length=200)
    telefono = models.CharField(max_length=20)
    ciudad = models.CharField(max_length=50)
    fecha_apertura = models.DateField()
    horario = models.CharField(max_length=100)
    id_proveedor = models.ForeignKey(
        Proveedor, 
        on_delete=models.CASCADE,
        related_name='sucursales',
        verbose_name='Proveedor'
    )
    
    class Meta:
        verbose_name = 'Sucursal'
        verbose_name_plural = 'Sucursales'
    
    def __str__(self):
        return f"{self.nombre} - {self.ciudad}"

# ==========================================
# MODELO: ALMACÉN
# ==========================================
class Almacen(models.Model):
    id_sucursal = models.ForeignKey(
        Sucursal, 
        on_delete=models.CASCADE,
        related_name='almacenes',
        verbose_name='Sucursal'
    )
    nombre = models.CharField(max_length=100)
    direccion = models.CharField(max_length=200)
    capacidad_max = models.DecimalField(max_digits=10, decimal_places=2)
    capacidad_disp = models.DecimalField(max_digits=10, decimal_places=2)
    telefono = models.CharField(max_length=20)
    horario = models.CharField(max_length=100)
    
    class Meta:
        verbose_name = 'Almacén'
        verbose_name_plural = 'Almacenes'
    
    def __str__(self):
        return f"{self.nombre} - {self.id_sucursal.nombre}"
```

# 12.5 — Procedimiento para realizar las migraciones (`makemigrations` y `migrate`)

Después de agregar la app a `settings.py` (punto 25), ejecuta:

```bash
python manage.py makemigrations app_Autozone
python manage.py migrate
```

Si ya corriste `migrate` antes, simplemente vuelve a ejecutar `makemigrations` y `migrate` cuando cambies modelos.

# 13 — Primero trabajamos con el MODELO: PROVEEDOR

Vamos a construir CRUD solo para `Proveedor` por ahora.

# 14 — `views.py` de `app_Autozone` con las funciones solicitadas

Crea/edita `app_Autozone/views.py` con este contenido (usa las funciones que pediste). No usamos `forms.py`, manejamos `request.POST` directamente:

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Proveedor
from django.urls import reverse
from django.utils import timezone

def inicio_autozone(request):
    # Página de inicio; puedes añadir más contexto si quieres
    return render(request, 'inicio.html')

def agregar_proveedor(request):
    if request.method == 'POST':
        # No validamos según tu instrucción
        nombre = request.POST.get('nombre')
        direccion = request.POST.get('direccion')
        telefono = request.POST.get('telefono')
        email = request.POST.get('email')
        fecha_registro = request.POST.get('fecha_registro')  # formato YYYY-MM-DD
        dias_entrega = request.POST.get('dias_entrega')
        hora_entrega = request.POST.get('hora_entrega')

        Proveedor.objects.create(
            nombre=nombre,
            direccion=direccion,
            telefono=telefono,
            email=email,
            fecha_registro=fecha_registro,
            dias_entrega=dias_entrega,
            hora_entrega=hora_entrega
        )
        return redirect('ver_proveedores')

    return render(request, 'proveedor/agregar_proveedor.html')

def ver_proveedores(request):
    proveedores = Proveedor.objects.all()
    return render(request, 'proveedor/ver_proveedores.html', {'proveedores': proveedores})

def actualizar_proveedor(request, proveedor_id):
    proveedor = get_object_or_404(Proveedor, pk=proveedor_id)
    return render(request, 'proveedor/actualizar_proveedor.html', {'proveedor': proveedor})

def realizar_actualizacion_proveedor(request, proveedor_id):
    proveedor = get_object_or_404(Proveedor, pk=proveedor_id)
    if request.method == 'POST':
        proveedor.nombre = request.POST.get('nombre')
        proveedor.direccion = request.POST.get('direccion')
        proveedor.telefono = request.POST.get('telefono')
        proveedor.email = request.POST.get('email')
        proveedor.fecha_registro = request.POST.get('fecha_registro')
        proveedor.dias_entrega = request.POST.get('dias_entrega')
        proveedor.hora_entrega = request.POST.get('hora_entrega')
        proveedor.save()
        return redirect('ver_proveedores')
    # si no es POST, redirige a la página de edición
    return redirect('actualizar_proveedor', proveedor_id=proveedor.id)

def borrar_proveedor(request, proveedor_id):
    proveedor = get_object_or_404(Proveedor, pk=proveedor_id)
    if request.method == 'POST':
        proveedor.delete()
        return redirect('ver_proveedores')
    return render(request, 'proveedor/borrar_proveedor.html', {'proveedor': proveedor})
```

# 15 — Crear la carpeta `templates` dentro de `app_Autozone`

Estructura recomendada:

```
app_Autozone/
    templates/
        base.html
        header.html
        navbar.html
        footer.html
        inicio.html
        proveedor/
            agregar_proveedor.html
            ver_proveedores.html
            actualizar_proveedor.html
            borrar_proveedor.html
```

# 16 — Archivos HTML: `base.html`, `header.html`, `navbar.html`, `footer.html`, `inicio.html`

## `app_Autozone/templates/base.html`

```html
<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Autozone - {% block title %}{% endblock %}</title>

  <!-- Bootstrap CSS -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">

  <!-- Bootstrap Icons -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.3/font/bootstrap-icons.css" rel="stylesheet">

  <style>
    :root{
      --primary:#7FB3D5; /* suave azul */
      --accent:#F7D6B5;  /* suave coral claro */
      --muted:#F4F7FA;
      --card:#FFFFFF;
    }
    body{
      background: var(--muted);
      color: #243240;
    }
    .navbar-brand { font-weight: 700; }
    .main-container { padding: 24px; }
    footer { background:#f8f9fa; padding:12px 0; position: fixed; bottom: 0; width: 100%; }
  </style>
  {% block extra_head %}{% endblock %}
</head>
<body>

  {% include 'navbar.html' %}

  <div class="container main-container">
    {% block content %}{% endblock %}
  </div>

  {% include 'footer.html' %}

  <!-- Bootstrap JS -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
  {% block extra_js %}{% endblock %}
</body>
</html>
```

## `app_Autozone/templates/navbar.html`

```html
<nav class="navbar navbar-expand-lg" style="background: var(--primary);">
  <div class="container-fluid">
    <a class="navbar-brand text-white" href="{% url 'inicio_autozone' %}">
      <i class="bi bi-gear-wide-connected"></i> Sistema de Administración Autozone
    </a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#mainNav">
      <span class="navbar-toggler-icon"></span>
    </button>

    <div class="collapse navbar-collapse" id="mainNav">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item"><a class="nav-link text-white" href="{% url 'inicio_autozone' %}"><i class="bi bi-house"></i> Inicio</a></li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle text-white" href="#" role="button" data-bs-toggle="dropdown">
            <i class="bi bi-box-seam"></i> Proveedores
          </a>
          <ul class="dropdown-menu dropdown-menu-end">
            <li><a class="dropdown-item" href="{% url 'agregar_proveedor' %}">Agregar Proveedor</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_proveedores' %}">Ver Proveedores</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_proveedores' %}">Actualizar Proveedor</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_proveedores' %}">Borrar Proveedor</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle text-white" href="#" role="button" data-bs-toggle="dropdown">
            <i class="bi bi-building"></i> Sucursales
          </a>
          <ul class="dropdown-menu dropdown-menu-end">
            <li><a class="dropdown-item" href="#">Agregar sucursales</a></li>
            <li><a class="dropdown-item" href="#">Ver sucursales</a></li>
            <li><a class="dropdown-item" href="#">Actualizar sucursales</a></li>
            <li><a class="dropdown-item" href="#">Borrar sucursales</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle text-white" href="#" role="button" data-bs-toggle="dropdown">
            <i class="bi bi-archive"></i> Almacenes
          </a>
          <ul class="dropdown-menu dropdown-menu-end">
            <li><a class="dropdown-item" href="#">Agregar almacenes</a></li>
            <li><a class="dropdown-item" href="#">Ver almacenes</a></li>
            <li><a class="dropdown-item" href="#">Actualizar almacenes</a></li>
            <li><a class="dropdown-item" href="#">Borrar almacenes</a></li>
          </ul>
        </li>

      </ul>
    </div>
  </div>
</nav>
```

## `app_Autozone/templates/footer.html`

```html
<footer class="text-center">
  <div class="container">
    <small>&copy; {{ now.year }} - Derechos reservados. Creado por Rolando Contreras, Cbtis 128</small>
  </div>
</footer>

{% comment %}
To make {{ now.year }} available, add context processor or include in each view context:
from datetime import datetime
context = {'now': datetime.now()}
{% endcomment %}
```

> Nota: para que `{{ now.year }}` funcione, puedes pasar `{'now': timezone.now()}` desde las vistas o crear un context processor. O simplemente pone la fecha fija si prefieres.

## `app_Autozone/templates/inicio.html`

```html
{% extends 'base.html' %}
{% block title %}Inicio{% endblock %}
{% block content %}
<div class="row">
  <div class="col-md-8">
    <div class="card p-4">
      <h3>Bienvenido al Sistema de Administración Autozone</h3>
      <p>Este sistema permite gestionar proveedores, sucursales y almacenes.</p>
    </div>
  </div>
  <div class="col-md-4">
    <div class="card p-2">
      <img src="https://upload.wikimedia.org/wikipedia/commons/6/6b/Car_PNG51319.png" class="img-fluid" alt="Autozone">
    </div>
  </div>
</div>
{% endblock %}
```

(La imagen es de internet; funciona mientras tengas conexión.)

# 21 — Crear subcarpeta `proveedor` dentro de `app_Autozone/templates`

Crea la carpeta `app_Autozone/templates/proveedor/`

# 22 — Archivos HTML de proveedor (sin forms.py)

## `agregar_proveedor.html`

```html
{% extends 'base.html' %}
{% block title %}Agregar Proveedor{% endblock %}
{% block content %}
<div class="card p-4">
  <h4>Agregar Proveedor</h4>
  <form method="post">
    {% csrf_token %}
    <div class="mb-3">
      <label class="form-label">Nombre</label>
      <input class="form-control" name="nombre" required>
    </div>
    <div class="mb-3">
      <label class="form-label">Dirección</label>
      <input class="form-control" name="direccion">
    </div>
    <div class="mb-3">
      <label class="form-label">Teléfono</label>
      <input class="form-control" name="telefono">
    </div>
    <div class="mb-3">
      <label class="form-label">Email</label>
      <input class="form-control" name="email" type="email">
    </div>
    <div class="mb-3">
      <label class="form-label">Fecha de registro</label>
      <input class="form-control" name="fecha_registro" type="date">
    </div>
    <div class="mb-3">
      <label class="form-label">Días de entrega</label>
      <input class="form-control" name="dias_entrega">
    </div>
    <div class="mb-3">
      <label class="form-label">Hora de entrega</label>
      <input class="form-control" name="hora_entrega">
    </div>
    <button class="btn btn-primary">Guardar</button>
    <a href="{% url 'ver_proveedores' %}" class="btn btn-secondary">Cancelar</a>
  </form>
</div>
{% endblock %}
```

## `ver_proveedores.html`

```html
{% extends 'base.html' %}
{% block title %}Ver Proveedores{% endblock %}
{% block content %}
<div class="card p-3">
  <h4>Lista de Proveedores</h4>
  <table class="table table-striped">
    <thead>
      <tr>
        <th>Nombre</th><th>Email</th><th>Teléfono</th><th>Fecha registro</th><th>Acciones</th>
      </tr>
    </thead>
    <tbody>
      {% for p in proveedores %}
      <tr>
        <td>{{ p.nombre }}</td>
        <td>{{ p.email }}</td>
        <td>{{ p.telefono }}</td>
        <td>{{ p.fecha_registro }}</td>
        <td>
          <a class="btn btn-sm btn-info" href="{% url 'actualizar_proveedor' p.id %}">Editar</a>
          <a class="btn btn-sm btn-danger" href="{% url 'borrar_proveedor' p.id %}">Borrar</a>
        </td>
      </tr>
      {% empty %}
      <tr><td colspan="5">No hay proveedores registrados.</td></tr>
      {% endfor %}
    </tbody>
  </table>
  <a class="btn btn-success" href="{% url 'agregar_proveedor' %}">Agregar Proveedor</a>
</div>
{% endblock %}
```

## `actualizar_proveedor.html`

```html
{% extends 'base.html' %}
{% block title %}Actualizar Proveedor{% endblock %}
{% block content %}
<div class="card p-3">
  <h4>Actualizar Proveedor</h4>
  <form method="post" action="{% url 'realizar_actualizacion_proveedor' proveedor.id %}">
    {% csrf_token %}
    <div class="mb-3">
      <label class="form-label">Nombre</label>
      <input class="form-control" name="nombre" value="{{ proveedor.nombre }}">
    </div>
    <div class="mb-3">
      <label class="form-label">Dirección</label>
      <input class="form-control" name="direccion" value="{{ proveedor.direccion }}">
    </div>
    <div class="mb-3">
      <label class="form-label">Teléfono</label>
      <input class="form-control" name="telefono" value="{{ proveedor.telefono }}">
    </div>
    <div class="mb-3">
      <label class="form-label">Email</label>
      <input class="form-control" name="email" value="{{ proveedor.email }}">
    </div>
    <div class="mb-3">
      <label class="form-label">Fecha de registro</label>
      <input class="form-control" name="fecha_registro" type="date" value="{{ proveedor.fecha_registro }}">
    </div>
    <div class="mb-3">
      <label class="form-label">Días de entrega</label>
      <input class="form-control" name="dias_entrega" value="{{ proveedor.dias_entrega }}">
    </div>
    <div class="mb-3">
      <label class="form-label">Hora de entrega</label>
      <input class="form-control" name="hora_entrega" value="{{ proveedor.hora_entrega }}">
    </div>
    <button class="btn btn-primary">Actualizar</button>
    <a class="btn btn-secondary" href="{% url 'ver_proveedores' %}">Cancelar</a>
  </form>
</div>
{% endblock %}
```

## `borrar_proveedor.html`

```html
{% extends 'base.html' %}
{% block title %}Borrar Proveedor{% endblock %}
{% block content %}
<div class="card p-3">
  <h4>Confirmar eliminación</h4>
  <p>¿Deseas eliminar al proveedor <strong>{{ proveedor.nombre }}</strong>?</p>
  <form method="post">
    {% csrf_token %}
    <button class="btn btn-danger" type="submit">Sí, borrar</button>
    <a class="btn btn-secondary" href="{% url 'ver_proveedores' %}">Cancelar</a>
  </form>
</div>
{% endblock %}
```

# 23 — No utilizar `forms.py`

Hecho: todas las plantillas y vistas usan `request.POST` y `csrf_token`. No hay `forms.py`.

# 24 — Crear `urls.py` en `app_Autozone`

Crea `app_Autozone/urls.py`:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_autozone, name='inicio_autozone'),
    path('proveedor/agregar/', views.agregar_proveedor, name='agregar_proveedor'),
    path('proveedor/ver/', views.ver_proveedores, name='ver_proveedores'),
    path('proveedor/editar/<int:proveedor_id>/', views.actualizar_proveedor, name='actualizar_proveedor'),
    path('proveedor/editar/guardar/<int:proveedor_id>/', views.realizar_actualizacion_proveedor, name='realizar_actualizacion_proveedor'),
    path('proveedor/borrar/<int:proveedor_id>/', views.borrar_proveedor, name='borrar_proveedor'),
]
```

# 25 — Agregar `app_Autozone` en `settings.py` de `backend_Autozone`

Abre `backend_Autozone/settings.py` y en `INSTALLED_APPS` añade:

```python
INSTALLED_APPS = [
    # apps default...
    'django.contrib.staticfiles', 
    # ...
    'app_Autozone',
]
```

Asegúrate también de tener configuración de `TEMPLATES` (por defecto Django la trae) y `STATIC_URL = '/static/'`.

# 26 — Configurar `urls.py` de `backend_Autozone` para enlazar con `app_Autozone`

Edita `backend_Autozone/urls.py`:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Autozone.urls')),  # rutas principales
]
```

# 27 — Registrar modelos en `admin.py` y volver a realizar migraciones

En `app_Autozone/admin.py`:

```python
from django.contrib import admin
from .models import Proveedor, Sucursal, Almacen

@admin.register(Proveedor)
class ProveedorAdmin(admin.ModelAdmin):
    list_display = ('nombre', 'email', 'telefono', 'fecha_registro')
    search_fields = ('nombre', 'email')

# Registramos Sucursal y Almacen (aunque por ahora no trabajaremos con ellos)
@admin.register(Sucursal)
class SucursalAdmin(admin.ModelAdmin):
    list_display = ('nombre', 'ciudad', 'id_proveedor')
    search_fields = ('nombre', 'ciudad')

@admin.register(Almacen)
class AlmacenAdmin(admin.ModelAdmin):
    list_display = ('nombre', 'id_sucursal', 'capacidad_disp')
```

Luego:

```bash
python manage.py makemigrations app_Autozone
python manage.py migrate
```

# 27 (nota) — Por el momento solo trabajar con "Proveedor"

Hecho: las vistas y plantillas creadas gestionan únicamente `Proveedor`. `Sucursal` y `Almacen` quedan definidas en modelos y registradas en admin para cuando las implementes.

# 28 — Estilo: colores suaves, atractivo y moderno

* Ya se usaron variables CSS suaves en `base.html`. Cambia colores en `:root` si deseas otros tonos.
* Páginas sencillas, Bootstrap para responsividad.

# 29 — Al inicio crear la estructura completa de carpetas y archivos

Resumen de estructura recomendada (crea todos los archivos vacíos y luego pega el código):

```
UIII_Autozone_0544/
├─ .venv/
├─ manage.py
├─ backend_Autozone/
│  ├─ settings.py
│  ├─ urls.py
│  └─ ...
├─ app_Autozone/
│  ├─ migrations/
│  ├─ templates/
│  │  ├─ base.html
│  │  ├─ navbar.html
│  │  ├─ footer.html
│  │  ├─ inicio.html
│  │  └─ proveedor/
│  │     ├─ agregar_proveedor.html
│  │     ├─ ver_proveedores.html
│  │     ├─ actualizar_proveedor.html
│  │     └─ borrar_proveedor.html
│  ├─ models.py
│  ├─ views.py
│  ├─ urls.py
│  └─ admin.py
```

# 30 — Proyecto totalmente funcional (comprobaciones)

Pasos para comprobar que todo funcione:

1. Entorno activo `(.venv)`.
2. `python manage.py makemigrations`
3. `python manage.py migrate`
4. `python manage.py createsuperuser` (opcional para admin)
5. `python manage.py runserver 127.0.0.1:8544`
6. Abrir en navegador `http://127.0.0.1:8544/` → verás la página de inicio.
7. Navegar a `Agregar Proveedor`, crear uno y luego `Ver Proveedores`.

# 31 — Ejecutar servidor en el puerto 8544 (otra vez)

```bash
python manage.py runserver 127.0.0.1:8544
```

ó `0.0.0.0:8544` si quieres acceso desde otra máquina en la misma red.

---

## Notas, consejos y recordatorios prácticos

* **No validar entrada**: el código no realiza validaciones complejas (tal como pediste). Ten en cuenta que inputs erróneos pueden producir errores en DB (ej.: fecha mal formada).
* **Fecha en footer**: pasé el placeholder `{{ now.year }}`, pero en cada vista puedes enviar `{'now': timezone.now()}` si quieres que aparezca correctamente.
* **Recursos estáticos**: si necesitas agregar CSS/JS local, configura `STATICFILES_DIRS` en `settings.py` y usa `{% load static %}`. Ahora usamos CDN para Bootstrap.
* **Evitar duplicar carpeta al crear proyecto**: recuerda usar `django-admin startproject backend_Autozone .` (con el punto).
* **Admin**: registro de modelos listo. Accede en `http://127.0.0.1:8544/admin/`.

---

Si quieres, hago **alguna de estas tareas automáticamente ahora** (por ejemplo: generar los archivos `views.py`, `urls.py`, y los `templates` con el contenido exacto y te doy un archivo comprimido), o te doy **copias de los archivos listos para pegar**. ¿Qué prefieres?

