Tentu, mari kita integrasikan Bootstrap ke dalam proyek Django Anda, terutama untuk tampilan *dashboard*. Saya akan memberikan langkah-langkah untuk menginstal Bootstrap, memperbarui template dasar, dan menyesuaikan tampilan *dashboard*.

### 1. Instalasi Bootstrap

1.  **Unduh Bootstrap:**

    Anda dapat mengunduh Bootstrap dari [situs resminya](https://getbootstrap.com/) atau menggunakan CDN (Content Delivery Network). Untuk kemudahan, kita akan menggunakan CDN.

2.  **Tambahkan Bootstrap ke Template Dasar:**

    Buka `templates/base.html` (atau buat jika belum ada) dan tambahkan *link* ke CSS dan JavaScript Bootstrap.

```html 
    project="BugHunterPlatform" file="base.html" version=1
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>{% block title %}Bug Hunter Platform{% endblock %}</title>
        <!-- Bootstrap CSS -->
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
        <!-- Custom CSS -->
        <link rel="stylesheet" href="{% static 'css/style.css' %}">
    </head>
    <body>
        <div class="container">
            {% block content %}{% endblock %}
        </div>

        <!-- Bootstrap JS, Popper.js, and jQuery -->
        <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.5.3/dist/umd/popper.min.js"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js"></script>
    </body>
    </html>
```

    Pastikan Anda memiliki direktori `static` di dalam aplikasi `bughunter` dan file `css/style.css` di dalamnya. Anda dapat membuat file `css/style.css` untuk menambahkan *style* khusus Anda.

### 2. Memperbarui Template *Dashboard* dengan Bootstrap

1.  **Modifikasi `templates/dashboard.html`:**

    Gunakan kelas-kelas Bootstrap untuk membuat tata letak *dashboard* yang responsif dan menarik.

```html 
    project="BugHunterPlatform" file="dashboard.html" version=2
    {% extends 'base.html' %}

    {% block title %}Dashboard{% endblock %}

    {% block content %}
        <div class="container-fluid">
            <div class="row">
                <!-- Sidebar -->
                <nav id="sidebar" class="col-md-3 col-lg-2 d-md-block bg-light sidebar">
                    <div class="sidebar-sticky">
                        <ul class="nav flex-column">
                            <li class="nav-item">
                                <a class="nav-link active" href="{% url 'scanner:dashboard' %}">
                                    Dashboard <span class="sr-only">(current)</span>
                                </a>
                            </li>
                            <li class="nav-item">
                                <a class="nav-link" href="{% url 'scanner:scan_dashboard' %}">
                                    Scan Dashboard
                                </a>
                            </li>
                            <li class="nav-item">
                                <a class="nav-link" href="{% url 'scanner:create_scan_task' %}">
                                    Create Scan Task
                                </a>
                            </li>
                            <li class="nav-item">
                                <a class="nav-link" href="{% url 'logout' %}">
                                    Logout
                                </a>
                            </li>
                        </ul>
                    </div>
                </nav>

                <!-- Main Content -->
                <main role="main" class="col-md-9 ml-sm-auto col-lg-10 px-md-4">
                    <div class="d-flex justify-content-between flex-wrap flex-md-nowrap align-items-center pt-3 pb-2 mb-3 border-bottom">
                        <h1 class="h2">Dashboard</h1>
                    </div>
                    {% block dashboard_content %}
                        <p>Selamat Datang di Dashboard</p>
                    {% endblock %}
                </main>
            </div>
        </div>
    {% endblock %}
```

2.  **Memperbarui Template Lainnya:**

    Pastikan template `scan_dashboard.html` dan `create_scan_task.html` juga memperluas `dashboard.html` dan mengisi blok `dashboard_content`.

    *   `scanner/templates/scanner/scan_dashboard.html`:

```html 
        project="BugHunterPlatform" file="scan_dashboard.html" version=3
        {% extends 'dashboard.html' %}

        {% block dashboard_content %}
            <h2>Scan Dashboard</h2>
            <a href="{% url 'scanner:create_scan_task' %}" class="btn btn-primary">Create New Scan Task</a>
            <table class="table">
                <thead>
                    <tr>
                        <th>Target URL</th>
                        <th>Scan Type</th>
                        <th>Status</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody>
                    {% for task in scan_tasks %}
                        <tr>
                            <td>{{ task.target_url }}</td>
                            <td>{{ task.scan_type }}</td>
                            <td>{{ task.status }}</td>
                            <td>
                                <a href="{% url 'scanner:scan_results' task.id %}" class="btn btn-sm btn-info">View Results</a>
                            </td>
                        </tr>
                    {% endfor %}
                </tbody>
            </table>
        {% endblock %}
```

    *   `scanner/templates/scanner/create_scan_task.html`:

```html 
project="BugHunterPlatform" file="create_scan_task.html" version=3
        {% extends 'dashboard.html' %}

        {% block dashboard_content %}
            <h2>Create Scan Task</h2>
            <form method="post">
                {% csrf_token %}
                <div class="form-group">
                    <label for="{{ form.target_url.id_for_label }}">Target URL:</label>
                    {{ form.target_url }}
                </div>
                <div class="form-group">
                    <label for="{{ form.scan_type.id_for_label }}">Scan Type:</label>
                    {{ form.scan_type }}
                </div>
                <button type="submit" class="btn btn-primary">Create</button>
            </form>
        {% endblock %}
```

### 3. Menggunakan *Static Files*

1.  **Konfigurasi `settings.py`:**

    Pastikan Anda telah mengkonfigurasi *static files* dengan benar di `settings.py`.

```python 
    project="BugHunterPlatform" file="settings.py" version=2
    import os

    STATIC_URL = '/static/'
    STATICFILES_DIRS = [
        os.path.join(BASE_DIR, 'static'),
    ]
```

2.  **Memuat *Static Files* di Template:**

    Tambahkan `{% load static %}` di bagian atas template Anda untuk memuat *static files*.

```html 
    project="BugHunterPlatform" file="dashboard.html" version=3
    {% extends 'base.html' %}
    {% load static %}

    {% block title %}Dashboard{% endblock %}

    {% block content %}
        ...
    {% endblock %}
```

### 4. Menyesuaikan Tampilan

1.  **CSS Khusus:**

    Anda dapat menambahkan CSS khusus di `static/css/style.css` untuk menyesuaikan tampilan sesuai kebutuhan Anda.

```css 
    project="BugHunterPlatform" file="style.css" version=1
    /* static/css/style.css */
    body {
        font-family: 'Arial', sans-serif;
    }

    .sidebar {
        background-color: #f8f9fa;
    }

    .sidebar .nav-link {
        color: #495057;
    }

    .sidebar .nav-link.active {
        color: #007bff;
    }
```

### 5. Penjelasan Kode

*   **`base.html`:** Template dasar yang mencakup *link* ke CSS dan JavaScript Bootstrap.
*   **`dashboard.html`:** Template *dashboard* yang menggunakan kelas-kelas Bootstrap untuk tata letak dan *sidebar*.
*   **`scan_dashboard.html` dan `create_scan_task.html`:** Template yang memperluas `dashboard.html` dan mengisi blok `dashboard_content` dengan konten spesifik.
*   **`static/css/style.css`:** File CSS khusus untuk menyesuaikan tampilan.

Dengan mengikuti langkah-langkah ini, Anda dapat mengintegrasikan Bootstrap ke dalam proyek Django Anda dan membuat tampilan *dashboard* yang responsif dan menarik.