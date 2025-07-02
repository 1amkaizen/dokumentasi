Tentu, mari kita bangun proyek platform SaaS *bug hunting* ini dengan Django. Saya akan memberikan struktur proyek, contoh kode, dan penjelasan langkah demi langkah.

### 1. Struktur Proyek Django

Berikut adalah struktur proyek Django yang disarankan:

```
bughunter/
    manage.py
    bughunter/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
    scanner/
        __init__.py
        models.py
        views.py
        urls.py
        admin.py
    analyzer/
        __init__.py
        utils.py
    users/
        __init__.py
        models.py
        views.py
        urls.py
        admin.py
```

*   `bughunter/`: Direktori utama proyek Django.
*   `manage.py`: Utilitas baris perintah Django.
*   `bughunter/bughunter/`: Direktori konfigurasi proyek.
*   `scanner/`: Aplikasi Django untuk manajemen pemindaian.
*   `analyzer/`: Modul Python untuk analisis hasil pemindaian.
*   `users/`: Aplikasi Django untuk manajemen pengguna.

### 2. Membuat Proyek dan Aplikasi Django

1.  **Membuat Proyek Django:**

```bash
    django-admin startproject bughunter
    cd bughunter
```
2.  **Membuat Aplikasi Django:**

```bash
    python manage.py startapp scanner
    python manage.py startapp users
```

### 3. Konfigurasi Proyek (settings.py)

Tambahkan aplikasi yang baru dibuat ke `INSTALLED_APPS` di `bughunter/settings.py`:

```python project="BugHunterPlatform" file="settings.py" version=1
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'scanner',
    'users',
]
```

### 4. Aplikasi `scanner`

1.  **Model (scanner/models.py):**

```python 
    project="BugHunterPlatform" file="models.py" version=1
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
2.  **Views (scanner/views.py):**

```python 
    project="BugHunterPlatform" file="views.py" version=1
    from django.shortcuts import render, redirect
    from django.contrib.auth.decorators import login_required
    from .models import ScanTask, ScanResult
    from .forms import ScanTaskForm
    from django.http import JsonResponse
    import subprocess
    import json

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

        # Contoh: Menjalankan Nmap
        if scan_task.scan_type == "nmap":
            try:
                command = ['nmap', '-oJ', '-', scan_task.target_url]
                result = subprocess.run(command, capture_output=True, text=True, timeout=60)
                scan_task.status = "Completed"
                scan_task.save()
                parsed_result = json.loads(result.stdout)
                # Simpan hasil
                scan_result = ScanResult(scan_task=scan_task, tool_name="Nmap", raw_result=result.stdout, parsed_result=parsed_result)
                scan_result.save()

            except subprocess.TimeoutExpired:
                scan_task.status = "Failed"
                scan_task.save()
                print(f"Pemindaian {scan_task.target_url} melampaui batas waktu.")
            except Exception as e:
                scan_task.status = "Failed"
                scan_task.save()
                print(f"Terjadi kesalahan selama pemindaian: {e}")
        else:
            scan_task.status = "Failed"
            scan_task.save()
            print(f"Tipe pemindaian tidak didukung: {scan_task.scan_type}")

    def scan_results(request, scan_task_id):
        scan_task = ScanTask.objects.get(pk=scan_task_id)
        scan_results = ScanResult.objects.filter(scan_task=scan_task)
        return render(request, 'scanner/scan_results.html', {'scan_task': scan_task, 'scan_results': scan_results})
```

3.  **Forms (scanner/forms.py):**

```python 
    project="BugHunterPlatform" file="forms.py" version=1
    from django import forms
    from .models import ScanTask

    class ScanTaskForm(forms.ModelForm):
        class Meta:
            model = ScanTask
            fields = ['target_url', 'scan_type']
```

4.  **URLs (scanner/urls.py):**

```python 
    project="BugHunterPlatform" file="urls.py" version=1
    from django.urls import path
    from . import views

    app_name = 'scanner'

    urlpatterns = [
        path('dashboard/', views.scan_dashboard, name='scan_dashboard'),
        path('create/', views.create_scan_task, name='create_scan_task'),
        path('results/<int:scan_task_id>/', views.scan_results, name='scan_results'),
    ]
```

5.  **Templates (scanner/templates/scanner/):**

    *   `scan_dashboard.html`:

```html 
project="BugHunterPlatform" file="scan_dashboard.html" version=1
        {% extends 'base.html' %}

        {% block content %}
            <h1>Scan Dashboard</h1>
            <a href="{% url 'scanner:create_scan_task' %}">Create New Scan Task</a>
            <ul>
                {% for task in scan_tasks %}
                    <li>
                        {{ task.target_url }} - {{ task.scan_type }} - {{ task.status }}
                        <a href="{% url 'scanner:scan_results' task.id %}">View Results</a>
                    </li>
                {% endfor %}
            </ul>
        {% endblock %}
```

    *   `create_scan_task.html`:

```html 
project="BugHunterPlatform" file="create_scan_task.html" version=1
        {% extends 'base.html' %}

        {% block content %}
            <h1>Create Scan Task</h1>
            <form method="post">
                {% csrf_token %}
                {{ form.as_p }}
                <button type="submit">Create</button>
            </form>
        {% endblock %}
```

    *   `scan_results.html`:

```html 
project="BugHunterPlatform" file="scan_results.html" version=1
        {% extends 'base.html' %}

        {% block content %}
            <h1>Scan Results for {{ scan_task.target_url }}</h1>
            {% for result in scan_results %}
                <h2>{{ result.tool_name }}</h2>
                <pre>{{ result.raw_result }}</pre>
            {% endfor %}
        {% endblock %}
```

### 5. Aplikasi `users`

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
    project="BugHunterPlatform" file="views.py" version=1
    from django.shortcuts import render, redirect
    from django.contrib.auth import login, logout
    from django.contrib.auth.forms import UserCreationForm

    def register(request):
        if request.method == 'POST':
            form = UserCreationForm(request.POST)
            if form.is_valid():
                user = form.save()
                login(request, user)
                return redirect('scanner:scan_dashboard')
        else:
            form = UserCreationForm()
        return render(request, 'users/register.html', {'form': form})

    def logout_view(request):
        logout(request)
        return redirect('login')
```

3.  **URLs (users/urls.py):**

```python 
    project="BugHunterPlatform" file="urls.py" version=1
    from django.urls import path
    from . import views
    from django.contrib.auth.views import LoginView

    urlpatterns = [
        path('register/', views.register, name='register'),
        path('login/', LoginView.as_view(template_name='users/login.html'), name='login'),
        path('logout/', views.logout_view, name='logout'),
    ]
```

4.  **Templates (users/templates/users/):**

    *   `register.html`:

```html 
        project="BugHunterPlatform" file="register.html" version=1
        {% extends 'base.html' %}

        {% block content %}
            <h1>Register</h1>
            <form method="post">
                {% csrf_token %}
                {{ form.as_p }}
                <button type="submit">Register</button>
            </form>
        {% endblock %}
        ```

    *   `login.html`:

        ```html project="BugHunterPlatform" file="login.html" version=1
        {% extends 'base.html' %}

        {% block content %}
            <h1>Login</h1>
            <form method="post">
                {% csrf_token %}
                {{ form.as_p }}
                <button type="submit">Login</button>
            </form>
        {% endblock %}
```


### 6. Konfigurasi URL Utama (bughunter/urls.py)

```python 
project="BugHunterPlatform" file="urls.py" version=1
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('scanner/', include('scanner.urls')),
    path('users/', include('users.urls')),
    path('accounts/', include('django.contrib.auth.urls')),  # Untuk login, logout, dll.
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

### 10. Penjelasan Kode

*   **Model:** Mendefinisikan struktur data untuk tugas pemindaian dan hasil pemindaian.
*   **Views:** Menangani logika aplikasi, seperti membuat tugas pemindaian, menjalankan pemindaian, dan menampilkan hasil.
*   **Forms:** Memudahkan pembuatan formulir HTML untuk input pengguna.
*   **URLs:** Memetakan URL ke views yang sesuai.
*   **Templates:** Menentukan tampilan antarmuka pengguna.

### 11. Langkah Selanjutnya

*   **Implementasi Analisis Hasil:** Buat modul `analyzer` untuk mengurai dan menganalisis hasil pemindaian.
*   **Integrasi Alat Pemindaian:** Tambahkan dukungan untuk alat pemindaian lainnya seperti Nikto, OWASP ZAP, dll.
*   **Penjadwalan Tugas:** Gunakan Celery untuk menjalankan tugas pemindaian di latar belakang.
*   **Keamanan:** Terapkan praktik keamanan terbaik, seperti validasi input, otentikasi yang kuat, dan otorisasi yang tepat.

Dengan panduan ini, Anda dapat memulai pengembangan platform SaaS *bug hunting* Anda dengan Django.