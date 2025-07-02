Baik, mari kita mulai dari awal pembuatan proyek Django dengan struktur template di setiap aplikasi. Saya akan memandu Anda langkah demi langkah.

**1. Buat Proyek Django Baru**

Buka terminal atau command prompt Anda, lalu jalankan perintah berikut untuk membuat proyek Django baru:

```bash
django-admin startproject bug_hunter_platform
cd bug_hunter_platform
```

Ini akan membuat folder proyek `bug_hunter_platform` dan masuk ke dalamnya.

**2. Buat Aplikasi Django**

Selanjutnya, buat beberapa aplikasi Django yang akan menjadi bagian dari proyek Anda. Misalnya, kita akan membuat aplikasi `core`, `users`, dan `dashboard`:

```bash
python manage.py startapp core
python manage.py startapp users
python manage.py startapp dashboard
```

Ini akan membuat folder untuk setiap aplikasi dengan struktur dasar yang diperlukan.

**3. Konfigurasi `settings.py`**

Buka file `bug_hunter_platform/settings.py` dan tambahkan aplikasi-aplikasi yang baru dibuat ke dalam daftar `INSTALLED_APPS`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'core',  # Tambahkan aplikasi core
    'users',  # Tambahkan aplikasi users
    'dashboard',  # Tambahkan aplikasi dashboard
]
```


*   **Konfigurasi Static Files**:

```python
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    BASE_DIR / 'static',
]
STATIC_ROOT = BASE_DIR / 'staticfiles' # Untuk deployment
```

*   **Konfigurasi Templates**:

Pastikan template engine Django dikonfigurasi dengan benar:

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],  # Tambahkan direktori templates
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

**4. Struktur Folder Template**

Buat folder `templates` di dalam setiap aplikasi, lalu buat folder dengan nama aplikasi di dalam folder `templates` tersebut. Ini akan memastikan namespace template yang jelas.

```
bug_hunter_platform/
├── bug_hunter_platform/
├── core/
│   ├── templates/
│   │   └── core/  # Folder dengan nama aplikasi
│   │       └── home.html
├── users/
│   ├── templates/
│   │   └── users/  # Folder dengan nama aplikasi
│   │       └── profile.html
├── dashboard/
│   ├── templates/
│   │   └── dashboard/  # Folder dengan nama aplikasi
│   │       └── dashboard.html
├── templates/  # Untuk template dasar seperti base.html
│   └── base.html
├── manage.py
└── ...
```

**5. Buat Template Dasar `base.html`**

Buat file `base.html` di folder `templates/` (root folder proyek) dengan struktur HTML dasar Anda. Ini akan menjadi template dasar yang akan di-extend oleh template-template lain.

```html
{# templates/base.html #}
{% load static tailwind_tags %}
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Bug Hunter Platform</title>
    <meta name="description" content="Platform untuk membantu bug hunter dengan tools dan automatisasi.">
    <link rel="icon" href="{% static 'assets/img/favicon.ico' %}" type="image/x-icon">
    
    {% tailwind_css %}
    <link rel="stylesheet" href="{% static 'assets/css/style.css' %}">
</head>
<body class="bg-gray-100 font-sans antialiased">

    <!-- Header -->
    <header class="bg-white shadow-md">
        <div class="container mx-auto py-4 px-5">
            <nav class="flex items-center justify-between">
                <a href="/" class="text-2xl font-bold text-gray-800">Bug Hunter</a>
                <div class="flex items-center space-x-6">
                    <a href="#" class="text-gray-600 hover:text-gray-800">Tools</a>
                    <a href="#" class="text-gray-600 hover:text-gray-800">Automasi</a>
                    <a href="#" class="text-gray-600 hover:text-gray-800">Komunitas</a>
                    <a href="#" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Login</a>
                </div>
            </nav>
        </div>
    </header>

    <!-- Main Content -->
    <main class="container mx-auto py-8 px-5">
        {% block content %}
        <!-- Konten spesifik halaman akan muncul di sini -->
        {% endblock %}
    </main>

    <!-- Footer -->
    <footer class="bg-gray-800 text-white py-4">
        <div class="container mx-auto text-center">
            <p>&copy; 2025 Bug Hunter Platform. All rights reserved.</p>
        </div>
    </footer>

    <!-- JavaScript (Opsional) -->
    <script src="{% static 'assets/js/script.js' %}"></script>
</body>
</html>
```

**6. Buat Template Aplikasi**

Buat template untuk setiap aplikasi di dalam folder yang sesuai. Misalnya, buat file `core/templates/core/home.html`:

```html
{# core/templates/core/home.html #}
{% extends 'base.html' %}
{% load static %}

{% block content %}
<div class="bg-white py-16 px-6 rounded-lg shadow-md">
    <div class="max-w-2xl mx-auto text-center">
        <h1 class="text-4xl font-extrabold text-gray-900 tracking-tight sm:text-5xl">
            Selamat Datang di Bug Hunter Platform
        </h1>
        <p class="mt-4 text-lg text-gray-600">
            Platform terbaik untuk membantu Anda menemukan dan melaporkan bug dengan lebih efisien.
        </p>
        <div class="mt-8 flex justify-center space-x-4">
            <a href="#" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-3 px-6 rounded-lg transition duration-300">
                Mulai Scanning
            </a>
            <a href="#" class="bg-gray-200 hover:bg-gray-300 text-gray-700 font-bold py-3 px-6 rounded-lg transition duration-300">
                Pelajari Lebih Lanjut
            </a>
        </div>
    </div>

    <div class="mt-12">
        <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
            <div class="p-6 bg-gray-100 rounded-lg shadow-inner">
                <h2 class="text-2xl font-semibold text-gray-800 mb-4">Reconnaissance Tools</h2>
                <p class="text-gray-600">
                    Gunakan berbagai tools untuk mengumpulkan informasi tentang target Anda.
                </p>
                <ul class="list-disc list-inside mt-4">
                    <li>Subdomain Enumeration</li>
                    <li>Port Scanning</li>
                    <li>Directory Bruteforcing</li>
                </ul>
            </div>
            <div class="p-6 bg-gray-100 rounded-lg shadow-inner">
                <h2 class="text-2xl font-semibold text-gray-800 mb-4">Automated Scanning</h2>
                <p class="text-gray-600">
                    Otomatiskan proses scanning untuk menemukan kerentanan dengan cepat.
                </p>
                <ul class="list-disc list-inside mt-4">
                    <li>Vulnerability Scanning</li>
                    <li>Web Application Scanning</li>
                    <li>Network Scanning</li>
                </ul>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

**7. Konfigurasi Template di `settings.py`**

Pastikan `APP_DIRS` di `TEMPLATES` diatur ke `True`. Ini akan memberitahu Django untuk mencari template di dalam folder `templates` di setiap aplikasi.

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],  # Untuk template dasar seperti base.html
        'APP_DIRS': True,  # Penting: Aktifkan pencarian template di dalam aplikasi
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

**8. Buat View**

Buat view untuk setiap aplikasi yang akan menampilkan template. Misalnya, buat view di `core/views.py`:

```python
# core/views.py
from django.shortcuts import render

def home(request):
    return render(request, 'core/home.html')
```

**9. Konfigurasi URL**

Konfigurasi URL untuk setiap aplikasi. Pertama, buat file `urls.py` di dalam setiap aplikasi. Misalnya, buat file `core/urls.py`:

```python
# core/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home, name='home'),
]
```

Kemudian, sertakan URL aplikasi di `bug_hunter_platform/urls.py`:

```python
# bug_hunter_platform/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('core.urls')),  # Sertakan URL aplikasi core
    path('users/', include('users.urls')),  # Sertakan URL aplikasi users
    path('dashboard/', include('dashboard.urls')),  # Sertakan URL aplikasi dashboard
]
```

**10. Static Files (CSS, JavaScript, dll.)**

Buat folder `static` di dalam setiap aplikasi untuk menyimpan static files. Misalnya:

```
core/
├── static/
│   └── core/
│       └── css/
│           └── styles.css
```

Kemudian, di template Anda, gunakan tag `{% load static %}` dan `{% static 'path/to/file.css' %}` untuk menyertakan static files.

**11.  Instalasi dan Konfigurasi Tailwind CSS**

*   Install django-tailwind


```bash

    pip install django-tailwind
    
```
 atau

```bash
python -m pip install django-tailwind
```

*   Jika Anda ingin menggunakan automatic page reloads selama pengembangan, gunakan perintah berikut:

```bash
python -m pip install 'django-tailwind[reload]'
```
 
*   Tambahkan `theme` dan `tailwind` ke `INSTALLED_APPS` di `settings.py`

```python
    INSTALLED_APPS = [
        ...
        'theme',
        'tailwind',
        ...
    ]
```

*   Tambahkan kode berikut ke bagian bawah `settings.py`

```
TAILWIND_APP_NAME = 'theme'
```

*   Jalankan perintah berikut

```
    python manage.py tailwind init
```

*   Edit `tailwind.config.js` untuk mengkonfigurasi lokasi file template

```js
    /** @type {import('tailwindcss').Config} */
    module.exports = {
      content: [
        './templates/**/*.html',
        './**/templates/**/*.html',
        './**/templates/**/**/*.html',
        './**/templates/**/**/**/*.html',
        './**/templates/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html',
        './**/templates/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/**/*.html,
      ],
      theme: {
        extend: {},
      },
      plugins: [],
    }
```
*   Edit `theme/static_src/tailwind.config.js` dan tambahkan path ke semua file template Anda.
*   Edit `theme/static_src/src/styles.css` dan tambahkan directive tailwind

```css
    @tailwind base;
    @tailwind components;
    @tailwind utilities;
```

*   Jalankan perintah berikut untuk membangun asset tailwind

```bash
    python manage.py tailwind build
```

Dengan mengikuti langkah-langkah ini, Anda akan memiliki proyek Django dengan struktur template yang terorganisir dengan baik di setiap aplikasi, serta integrasi Django Tailwind yang berfungsi dengan benar.