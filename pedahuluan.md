# Memulai project

### install yang di perlukan

```
pip install django
```


Setelah Django terinstal, Anda bisa membuat proyek baru dengan
perintah berikut:

```
django-admin startproject bughunter_platform
```
Perintah ini akan membuat folder bernama bughuntet_platform
yang berisi file dan folder dasar untuk proyek Django kita.

## Persiapan project
Kita akan membuat struktur nya terlebih dahulu

### membuat folder dan file yang di perlukan

Struktur nya akan seperti ini

```

bug_hunter_platform/
├── bug_hunter_platform/
├── core/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── views.py
│   └── urls.py
├── landing/
├── users/
├── templates/
	├── landing/
	│   └── base.html
│       └── home.html
	├── account/
	├── dashboard/
	├── users/

├── static/
│   └── assets/
│  		└── main.css
│  		└── css/
│       	└── style.css
│  		└── img/
│  		└── js/
├── tailwind.config.js
├── postcss.config.js`
├── manage.py
├── db.sqlite3
└── ...
```

```
touch tailwind.config.js
touch postcss.config.js
mkdir templates
mkdir templates/landing
mkdir static
mkdir static/assets
mkdir static/assets/css
mkdir static/assets/js
mkdir static/assets/img
touch static/assets/main.css
```

### Konfigurasi Tailwind CSS
Pertama kita lakukan init

```
npm init -y
```

Selanjutnya, kita akan mengintegrasikan Tailwind CSS ke dalam proyek Django ini.

#### Instalasi Tailwind CSS

Pastikan Node.js sudah terinstal. Kemudian, instal Tailwind CSS dan buat file konfigurasi.

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

#### Konfigurasi File `tailwind.config.js`

Edit file `tailwind.config.js` untuk menyertakan direktori template Django.

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    './templates/**/*.html',
    './**/templates/**/*.html',
    './static/assets/js/**/*.js',
  ],
  theme: {
    extend: {},
  },
  plugins: [],
};

```

#### Konfigurasi File `postcss.config.js`

Edit file `post.config.js` untuk menyertakan direktori template Django.

```
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
};
```


#### Buat File CSS Utama

Buat direktori `static/assets``, lalu edit file `static/assets/main.css` dengan isi berikut:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```


Build ke css biasa dengan perintah berikut

```
npx tailwindcss-i ./static/assets/main.css -o ./static/assets/css/style.css --watch
```

#### Tambahkan Script Build

Tambahkan script untuk build Tailwind CSS di `package.json`.

```json
{
  "name": "bughunter_platform",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:css": "tailwindcss build static/assets/main.css -o static/assets/css/style.css",
    "watch:css": "tailwindcss build static/assets/main.css -o static/assets/css/style.css --watch"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "description": "",
  "devDependencies": {
    "autoprefixer": "^10.4.21",
    "postcss": "^8.5.4",
    "tailwindcss": "^4.1.8"
  }
}

```



### Membuat app
app yang akan di buat :

- users  ( auth user, profile,dsb)
- landing (home,about,kontak)
- core 
- integrations ( API shodan,antivirus,dll)
- scanner ( port scan, subfinder, dll )
- automation ( otomatisasi multi scan)
- suggestions ( rekonendasi bug )
- exploits ( basic )

buat app dengan perintah

```
django-admin startapp core
django-admin startapp landing
django-admin startapp users
django-admin startapp exploits
django-admin startapp integrations
django-admin startapp scanner
django-admin startapp automation
django-admin startapp suggestions
django-admin startapp exploits
```

## Membuat File Context Processor di app

```
# File: core/context_processors.py
from django.conf import settings
def cfg_assets_root(request):
	return {'ASSETS_ROOT': settings.ASSETS_ROOT}
```

## settings

### setting app
tambahkan ini di setting di installed apps

```
INSTALLED_APPS = [
...
'apps.authentication',
autentikasi
'users'
'landing'
'core',
'integrations',
'scanner',
'automation',
'suggestions',
'exploits',
```
## setting static
tambahkan ini

```

STATIC_ROOT =  BASE_DIR / 'staticfiles' 
STATICFILES_DIRS = [BASE_DIR / 'static',]

```


jalakan
```
python manage.py collectstatic
```

## setting templates
tambahkan ini di setting

```
'DIRS': [ BASE_DIR / 'templates']
```
## setting context processor

```
ASSETS_ROOT = '/static/assets'

'core.context_processors.cfg_assets_root',
```

## setting Media files

```
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR, 'media'
```

## Tambahkan URL Media di urls.py
Tambahkan konfigurasi untuk melayani file media selama pengembangan:

```
# bughunter_platform/urls.py
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static
urlpatterns = [
	path('admin/', admin.site.urls),
	path('',include('landing.urls')), 
]
if settings.DEBUG:
	urlpatterns += static(settings.MEDIA_URL,document_root=settings.MEDIA_ROOT)
```

## Membuat landing page

```
# File: landing/views.py
from django.shortcuts import render
# View untuk halaman home
def home(request):
	context = {
	'ASSETS_ROOT': '/static/assets', 
	}
	return render(request, 'landing/home.html',context)
```


## buat templates halaman home


### base



```html 
 <!-- File: templates/landing/base.html -->
{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Bug Hunter Platform{% endblock %}</title>
    <link href="{{ ASSETS_ROOT }}/css/style.css"rel="stylesheet"/>
</head>
<body class="bg-gray-100">
    <div class="container mx-auto py-8">
        {% block content %}
        {% endblock %}
    </div>
</body>
</html>

```


### home 

```
<!-- File: templates/landing/home.html -->
{% extends 'landing/base.html' %}
{% block title %}Home - Platform Bot{% endblock %}



{% block content %}
    <h1 class="text-2xl font-bold mb-4">Selamat Datang di Bug Hunter Platform</h1>
    <p class="mb-4">Platform ini akan membantu Anda dalam melakukan scanning, enumerasi, eksploitasi, pengumpulan payload, dan automasi scan.</p>

    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
        <div class="bg-white shadow-md rounded-md p-4">
            <h2 class="text-xl font-semibold mb-2">Scanning</h2>
            <p>Lakukan scanning terhadap target untuk mencari celah keamanan.</p>
            <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Mulai Scanning</button>
        </div>

        <div class="bg-white shadow-md rounded-md p-4">
            <h2 class="text-xl font-semibold mb-2">Enumerasi</h2>
            <p>Kumpulkan informasi sebanyak mungkin tentang target.</p>
            <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Mulai Enumerasi</button>
        </div>

        <div class="bg-white shadow-md rounded-md p-4">
            <h2 class="text-xl font-semibold mb-2">Eksploitasi</h2>
            <p>Eksploitasi celah keamanan yang ditemukan.</p>
            <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Mulai Eksploitasi</button>
        </div>

        <div class="bg-white shadow-md rounded-md p-4">
            <h2 class="text-xl font-semibold mb-2">Pengumpulan Payload</h2>
            <p>Kumpulkan payload yang berguna untuk eksploitasi.</p>
            <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Kumpulkan Payload</button>
        </div>

        <div class="bg-white shadow-md rounded-md p-4">
            <h2 class="text-xl font-semibold mb-2">Automasi Scan</h2>
            <p>Automasi proses scanning untuk efisiensi.</p>
            <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Mulai Automasi Scan</button>
        </div>

        <div class="bg-white shadow-md rounded-md p-4">
            <h2 class="text-xl font-semibold mb-2">Ringkasan Hasil Scan</h2>
            <p>Lihat ringkasan dari hasil scan yang telah dilakukan.</p>
            <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Lihat Ringkasan</button>
        </div>
    </div>
{% endblock %}

```


### menambahkan urls

```python
# File: landing/urls.py

from django.urls import path
from .views import home
urlpatterns = [
	path('', home, name='home'),
]
```

## Membuat user authetication
### 5. Aplikasi `users`

1.  **Model (users/forms.py):**

```
from django import forms
from django.contrib.auth.forms import UserCreationForm

class RegistrationForm(UserCreationForm):
    email = forms.EmailField(required=True)

    class Meta:
        model = UserCreationForm.Meta.model
        fields = UserCreationForm.Meta.fields + ('email',)
```

1.  **Model (users/models.py):**

```python 
    project="BugHunterPlatform" file="models.py" version=1
    from django.contrib.auth.models import AbstractUser

    class CustomUser(AbstractUser):
        # Tambahkan field tambahan jika diperlukan
        pass
```

2.  **Views (users/views.py):**

```python 
from django.shortcuts import render, redirect
from django.contrib.auth import login, logout, authenticate
from django.contrib.auth.forms import AuthenticationForm
from .forms import RegistrationForm

def register(request):
    if request.method == 'POST':
        form = RegistrationForm(request.POST)
        if form.is_valid():
            user = form.save()
            login(request, user)
            return redirect('index')  # Redirect ke halaman utama setelah registrasi
    else:
        form = RegistrationForm()
    return render(request, 'accounts/register.html', {'form': form})

def login_view(request):
    if request.method == 'POST':
        form = AuthenticationForm(request, data=request.POST)
        if form.is_valid():
            username = form.cleaned_data.get('username')
            password = form.cleaned_data.get('password')
            user = authenticate(username=username, password=password)
            if user is not None:
                login(request, user)
                return redirect('index')  # Redirect ke halaman utama setelah login
            else:
                return render(request, 'accounts/login.html', {'form': form, 'error': 'Invalid credentials'})
    else:
        form = AuthenticationForm()
    return render(request, 'accounts/login.html', {'form': form})

def logout_view(request):
    logout(request)
    return redirect('index')  # Redirect ke halaman utama setelah logout
```

3.  **URLs (users/urls.py):**

```python 
from django.urls import path
from . import views

urlpatterns = [
    path('register/', views.register, name='register'),
    path('login/', views.login_view, name='login'),
    path('logout/', views.logout_view, name='logout'),
]
```


4.  **Templates (users/templates/users/):**
5.  

    *   `register.html`:

```html 
{% extends 'base.html' %}

{% block content %}
    <h1 class="text-2xl font-bold mb-4">Register</h1>
    <form method="post" class="max-w-md mx-auto">
        {% csrf_token %}
        {% for field in form %}
            <div class="mb-4">
                <label for="{{ field.id_for_label }}" class="block text-gray-700 text-sm font-bold mb-2">{{ field.label_tag }}</label>
                {{ field }}
                {% if field.errors %}
                    <p class="text-red-500 text-xs italic">{{ field.errors|striptags }}</p>
                {% endif %}
            </div>
        {% endfor %}
        <button type="submit" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline">Register</button>
    </form>
{% endblock %}
```

    *   `login.html`:

```

{% extends 'base.html' %}

{% block content %}
    <h1 class="text-2xl font-bold mb-4">Login</h1>
    <form method="post" class="max-w-md mx-auto">
        {% csrf_token %}
        {% for field in form %}
            <div class="mb-4">
                <label for="{{ field.id_for_label }}" class="block text-gray-700 text-sm font-bold mb-2">{{ field.label_tag }}</label>
                {{ field }}
                {% if field.errors %}
                    <p class="text-red-500 text-xs italic">{{ field.errors|striptags }}</p>
                {% endif %}
            </div>
        {% endfor %}
        <button type="submit" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline">Login</button>
        {% if error %}
            <p class="text-red-500 text-xs italic">{{ error }}</p>
        {% endif %}
    </form>
{% endblock %}
```

### tambah cta ke home

```
{% extends 'base.html' %}

{% block content %}
    <div class="flex justify-end mb-4">
        {% if user.is_authenticated %}
            <a href="{% url 'logout' %}" class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline mr-2">Logout</a>
        {% else %}
            <a href="{% url 'login' %}" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline mr-2">Login</a>
            <a href="{% url 'register' %}" class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline">Register</a>
        {% endif %}
    </div>

    <h1 class="text-2xl font-bold mb-4">Selamat Datang di Bug Hunter Platform</h1>
    <p class="mb-4">Platform ini akan membantu Anda dalam melakukan scanning, enumerasi, eksploitasi, pengumpulan payload, dan automasi scan.</p>

    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
        <div class="bg-white shadow-md rounded-md p-4">
            <h2 class="text-xl font-semibold mb-2">Scanning</h2>
            <p>Lakukan scanning terhadap target untuk mencari celah keamanan.</p>
            <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Mulai Scanning</button>
        </div>

        <div class="bg-white shadow-md rounded-md p-4">
            <h2 class="text-xl font-semibold mb-2">Enumerasi</h2>
            <p>Kumpulkan informasi sebanyak mungkin tentang target.</p>
            <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Mulai Enumerasi</button>
        </div>

        <!-- Tambahkan fitur-fitur lainnya di sini -->
    </div>
{% endblock %}
```

### 6. Konfigurasi URL Utama (bughunter/urls.py)

```python 
# urls.py

from django.urls import include, path
from django.contrib import admin

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('landing.urls')),
    path('users/', include('users.urls')),  # Tambahkan URL aplikasi 'users'
]
```

### 7. Membuat dan Menerapkan Migrasi

```bash
python manage.py makemigrations scanner users
python manage.py migrate
```

### 8. Membuat Superuser

```bash
python manage.py createsuperuser
```

### 9. Menjalankan Proyek

```bash
python manage.py runserver
```

Buka `http://localhost:8000/` di browser Anda.




### 1. Membuat Tampilan Dashboard

Buat tampilan dashboard di `hunter/views.py`:

```python 
from django.shortcuts import render
from django.contrib.auth.decorators import login_required

@login_required
def dashboard(request):
    return render(request, 'hunter/dashboard.html')

def index(request):
    return render(request, 'hunter/index.html')
```

### 2. Membuat Template Dashboard

Buat template `hunter/dashboard.html`:

```html 
{% extends 'base.html' %}

{% block content %}
    <h1 class="text-2xl font-bold mb-4">Dashboard</h1>
    <p class="mb-4">Selamat datang di dashboard Anda. Di sini Anda dapat mengakses semua tools dan fitur yang tersedia.</p>

    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
        <div class="bg-white shadow-md rounded-md p-4">
            <h2 class="text-xl font-semibold mb-2">Scanning</h2>
            <p>Lakukan scanning terhadap target untuk mencari celah keamanan.</p>
            <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Mulai Scanning</button>
        </div>

        <div class="bg-white shadow-md rounded-md p-4">
            <h2 class="text-xl font-semibold mb-2">Enumerasi</h2>
            <p>Kumpulkan informasi sebanyak mungkin tentang target.</p>
            <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Mulai Enumerasi</button>
        </div>

        <div class="bg-white shadow-md rounded-md p-4">
            <h2 class="text-xl font-semibold mb-2">Eksploitasi</h2>
            <p>Eksploitasi celah keamanan yang ditemukan.</p>
            <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Mulai Eksploitasi</button>
        </div>

        <div class="bg-white shadow-md rounded-md p-4">
            <h2 class="text-xl font-semibold mb-2">Pengumpulan Payload</h2>
            <p>Kumpulkan payload yang berguna untuk eksploitasi.</p>
            <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Kumpulkan Payload</button>
        </div>

        <div class="bg-white shadow-md rounded-md p-4">
            <h2 class="text-xl font-semibold mb-2">Automasi Scan</h2>
            <p>Automasi proses scanning untuk efisiensi.</p>
            <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Mulai Automasi Scan</button>
        </div>

        <div class="bg-white shadow-md rounded-md p-4">
            <h2 class="text-xl font-semibold mb-2">Ringkasan Hasil Scan</h2>
            <p>Lihat ringkasan dari hasil scan yang telah dilakukan.</p>
            <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Lihat Ringkasan</button>
        </div>
    </div>
{% endblock %}
```

### 3. Konfigurasi URL

Tambahkan URL untuk dashboard di `hunter/urls.py`:

```python 
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
    path('dashboard/', views.dashboard, name='dashboard'),  # Tambahkan URL untuk dashboard
]
```

### 4. Mengarahkan Setelah Login

Ubah tampilan `accounts/views.py` untuk mengarahkan pengguna ke dashboard setelah login:

```python 
from django.shortcuts import render, redirect
from django.contrib.auth import login, logout, authenticate
from django.contrib.auth.forms import AuthenticationForm
from .forms import RegistrationForm

def register(request):
    if request.method == 'POST':
        form = RegistrationForm(request.POST)
        if form.is_valid():
            user = form.save()
            login(request, user)
            return redirect('dashboard')  # Redirect ke dashboard setelah registrasi
    else:
        form = RegistrationForm()
    return render(request, 'accounts/register.html', {'form': form})

def login_view(request):
    if request.method == 'POST':
        form = AuthenticationForm(request, data=request.POST)
        if form.is_valid():
            username = form.cleaned_data.get('username')
            password = form.cleaned_data.get('password')
            user = authenticate(username=username, password=password)
            if user is not None:
                login(request, user)
                return redirect('dashboard')  # Redirect ke dashboard setelah login
            else:
                return render(request, 'accounts/login.html', {'form': form, 'error': 'Invalid credentials'})
    else:
        form = AuthenticationForm()
    return render(request, 'accounts/login.html', {'form': form})

def logout_view(request):
    logout(request)
    return redirect('index')  # Redirect ke halaman utama setelah logout
```

### 5. Update Tautan Logout

Update tautan logout di `hunter/templates/hunter/index.html` dan `hunter/templates/hunter/dashboard.html`:

```html
<a href="{% url 'logout' %}" class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline mr-2">Logout</a>
```

### 6. Update Tautan Login dan Register

Pastikan tautan login dan register di `hunter/templates/hunter/index.html` sudah benar:

```html
<a href="{% url 'login' %}" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline mr-2">Login</a>
<a href="{% url 'register' %}" class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline">Register</a>
```

### 7. Menambahkan Dekorator `@login_required`

Tambahkan dekorator `@login_required` ke tampilan dashboard di `hunter/views.py` untuk memastikan hanya pengguna yang sudah login yang dapat mengakses dashboard:

```python
from django.shortcuts import render
from django.contrib.auth.decorators import login_required

@login_required
def dashboard(request):
    return render(request, 'hunter/dashboard.html')

def index(request):
    return render(request, 'hunter/index.html')
```

### 8. Jalankan Proyek

Jalankan server pengembangan:

```bash
python manage.py runserver
```

Sekarang, setelah login atau registrasi, pengguna akan diarahkan ke halaman dashboard yang berisi tautan ke semua tools dan fitur yang tersedia.






### 1. Instalasi dan Konfigurasi Alat Pemindaian

1.  **Instalasi Alat:**

    Pastikan alat-alat yang ingin Anda gunakan (Nmap, Nikto, OWASP ZAP, dll.) sudah terinstal di server Anda. Contoh:

```bash
    sudo apt update
    sudo apt install nmap nikto
```

2.  **Akses ke Alat:**

    Pastikan pengguna yang menjalankan server Django memiliki izin untuk menjalankan alat-alat tersebut. Anda mungkin perlu menambahkan pengguna ke grup yang sesuai atau menggunakan `sudo` (dengan hati-hati).

### 2. Integrasi Alat Pemindaian dalam Django

1.  **Modifikasi `scanner/views.py`:**

```python 
    project="BugHunterPlatform" file="views.py" version=2
    from django.shortcuts import render, redirect
    from django.contrib.auth.decorators import login_required
    from .models import ScanTask, ScanResult
    from .forms import ScanTaskForm
    import subprocess
    import json
    import xmltodict  # Untuk parsing XML

    @login_required
    def scan_dashboard(request):
        scan_tasks = ScanTask.objects.filter(user=request.user).order_by('-start_time')
        return render(request, 'scanner/scan_dashboard.html', {'scan_tasks': scan_tasks})

    @login_required
    def create_scan_task(request):
        if request.method == 'POST':
            form = ScanTaskForm(request.POST)
            if form.is_valid():
                scan_task = form.save(commit=False)
                scan_task.user = request.user
                scan_task.save()
                # Memulai tugas pemindaian di latar belakang (contoh sederhana)
                start_scan(scan_task.id)
                return redirect('scanner:scan_dashboard')
        else:
            form = ScanTaskForm()
        return render(request, 'scanner/create_scan_task.html', {'form': form})

    def start_scan(scan_task_id):
        scan_task = ScanTask.objects.get(pk=scan_task_id)
        scan_task.status = "Running"
        scan_task.save()

        try:
            if scan_task.scan_type == "nmap":
                run_nmap_scan(scan_task)
            elif scan_task.scan_type == "nikto":
                run_nikto_scan(scan_task)
            else:
                scan_task.status = "Failed"
                scan_task.save()
                print(f"Tipe pemindaian tidak didukung: {scan_task.scan_type}")

        except Exception as e:
            scan_task.status = "Failed"
            scan_task.save()
            print(f"Terjadi kesalahan selama pemindaian: {e}")

    def run_nmap_scan(scan_task):
        try:
            command = ['nmap', '-oX', '-', scan_task.target_url]
            result = subprocess.run(command, capture_output=True, text=True, timeout=60)
            scan_task.status = "Completed"
            scan_task.save()
            # Konversi XML ke JSON
            xml_data = xmltodict.parse(result.stdout)
            # Simpan hasil
            scan_result = ScanResult(scan_task=scan_task, tool_name="Nmap", raw_result=result.stdout, parsed_result=xml_data)
            scan_result.save()

        except subprocess.TimeoutExpired:
            scan_task.status = "Failed"
            scan_task.save()
            print(f"Pemindaian Nmap {scan_task.target_url} melampaui batas waktu.")

    def run_nikto_scan(scan_task):
        try:
            command = ['nikto', '-o', 'json', '-host', scan_task.target_url]
            result = subprocess.run(command, capture_output=True, text=True, timeout=60)
            scan_task.status = "Completed"
            scan_task.save()
            # Parsing JSON
            json_data = json.loads(result.stdout)
            # Simpan hasil
            scan_result = ScanResult(scan_task=scan_task, tool_name="Nikto", raw_result=result.stdout, parsed_result=json_data)
            scan_result.save()

        except subprocess.TimeoutExpired:
            scan_task.status = "Failed"
            scan_task.save()
            print(f"Pemindaian Nikto {scan_task.target_url} melampaui batas waktu.")

    def scan_results(request, scan_task_id):
        scan_task = ScanTask.objects.get(pk=scan_task_id)
        scan_results = ScanResult.objects.filter(scan_task=scan_task)
        return render(request, 'scanner/scan_results.html', {'scan_task': scan_task, 'scan_results': scan_results})
    ```

2.  **Modifikasi `scanner/models.py`:**

    Pastikan `parsed_result` adalah `JSONField` untuk menyimpan hasil yang diurai.

    ```python 
    project="BugHunterPlatform" file="models.py" version=2
    from django.db import models
    from django.contrib.auth.models import User

    class ScanTask(models.Model):
        user = models.ForeignKey(User, on_delete=models.CASCADE)
        target_url = models.URLField()
        scan_type = models.CharField(max_length=50)  # Contoh: "nmap", "nikto"
        start_time = models.DateTimeField(auto_now_add=True)
        end_time = models.DateTimeField(null=True, blank=True)
        status = models.CharField(max_length=20, default="Pending")  # Contoh: "Pending", "Running", "Completed", "Failed"

        def __str__(self):
            return f"Scan Task for {self.target_url} by {self.user.username}"

    class ScanResult(models.Model):
        scan_task = models.ForeignKey(ScanTask, on_delete=models.CASCADE)
        tool_name = models.CharField(max_length=50)  # Contoh: "Nmap", "Nikto"
        raw_result = models.TextField()  # Hasil mentah dari alat pemindaian
        parsed_result = models.JSONField(null=True, blank=True)  # Hasil yang diurai
        vulnerability = models.CharField(max_length=255, null=True, blank=True)
        severity = models.CharField(max_length=20, null=True, blank=True)  # Contoh: "High", "Medium", "Low"

        def __str__(self):
            return f"Result from {self.tool_name} for {self.scan_task.target_url}"
```

### 3. Menampilkan Hasil yang Dirangkum

1.  **Modifikasi `scanner/views.py`:**

    Tambahkan logika untuk merangkum hasil pemindaian.

```python 
    project="BugHunterPlatform" file="views.py" version=3
    from django.shortcuts import render
    from .models import ScanTask, ScanResult

    def scan_results(request, scan_task_id):
        scan_task = ScanTask.objects.get(pk=scan_task_id)
        scan_results = ScanResult.objects.filter(scan_task=scan_task)
        
        # Rangkuman hasil
        summary = {}
        for result in scan_results:
            if result.tool_name == "Nmap":
                summary['nmap'] = summarize_nmap_results(result.parsed_result)
            elif result.tool_name == "Nikto":
                summary['nikto'] = summarize_nikto_results(result.parsed_result)
        
        return render(request, 'scanner/scan_results.html', {'scan_task': scan_task, 'summary': summary})

    def summarize_nmap_results(parsed_result):
        # Logika untuk merangkum hasil Nmap
        # Contoh: Menemukan port terbuka
        open_ports = []
        for host in parsed_result['nmaprun']['host']:
            if 'ports' in host and host['ports']:
                for port in host['ports']['port']:
                    open_ports.append({
                        'portid': port['@portid'],
                        'protocol': port['@protocol'],
                        'state': port['state']['@state'],
                        'service': port['service']['@name']
                    })
        return {'open_ports': open_ports}

    def summarize_nikto_results(parsed_result):
        # Logika untuk merangkum hasil Nikto
        # Contoh: Menemukan potensi kerentanan
        vulnerabilities = []
        for item in parsed_result['results']:
            vulnerabilities.append({
                'id': item['id'],
                'description': item['msg'],
                'uri': item['uri']
            })
        return {'vulnerabilities': vulnerabilities}
```

2.  **Modifikasi `scanner/templates/scanner/scan_results.html`:**

    Tampilkan hasil yang dirangkum.

```html 
    project="BugHunterPlatform" file="scan_results.html" version=2
    {% extends 'base.html' %}

    {% block content %}
        <h1>Scan Results for {{ scan_task.target_url }}</h1>

        <h2>Nmap Summary</h2>
        {% if summary.nmap %}
            <h3>Open Ports:</h3>
            <ul>
                {% for port in summary.nmap.open_ports %}
                    <li>
                        Port {{ port.portid }} ({{ port.protocol }}): {{ port.state }} - {{ port.service }}
                    </li>
                {% endfor %}
            </ul>
        {% else %}
            <p>No Nmap results found.</p>
        {% endif %}

        <h2>Nikto Summary</h2>
        {% if summary.nikto %}
            <h3>Vulnerabilities:</h3>
            <ul>
                {% for vuln in summary.nikto.vulnerabilities %}
                    <li>
                        {{ vuln.id }}: {{ vuln.description }} - <a href="{{ vuln.uri }}">{{ vuln.uri }}</a>
                    </li>
                {% endfor %}
            </ul>
        {% else %}
            <p>No Nikto results found.</p>
        {% endif %}
    {% endblock %}
```

### 4. Penjelasan Kode

*   **`run_nmap_scan` dan `run_nikto_scan`:** Fungsi-fungsi ini menjalankan alat pemindaian yang sesuai dan menyimpan hasilnya ke dalam model `ScanResult`.
*   **`summarize_nmap_results` dan `summarize_nikto_results`:** Fungsi-fungsi ini mengolah hasil pemindaian dan merangkum informasi penting.
*   **`scan_results`:** View ini mengambil hasil pemindaian dan merangkumnya sebelum mengirimkannya ke template.
*   **Template:** Menampilkan hasil yang dirangkum dalam format yang mudah dibaca.

### 5. Pertimbangan Tambahan

*   **Keamanan:** Pastikan untuk membersihkan input pengguna dan menghindari menjalankan perintah shell secara langsung untuk mencegah injeksi perintah.
*   **Penanganan Kesalahan:** Tambahkan penanganan kesalahan yang lebih baik untuk menangani kasus di mana alat pemindaian gagal atau menghasilkan output yang tidak terduga.
*   **Asinkron:** Gunakan Celery atau Redis Queue untuk menjalankan tugas pemindaian secara asinkron agar tidak memblokir respons web.
*   **Konfigurasi:** Sediakan opsi konfigurasi untuk alat pemindaian (misalnya, opsi Nmap atau Nikto) melalui formulir Django.

Dengan mengikuti langkah-langkah ini, Anda dapat mengintegrasikan alat pemindaian ke dalam aplikasi Django Anda dan menyajikan hasil yang dirangkum kepada pengguna.