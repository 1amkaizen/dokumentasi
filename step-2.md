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