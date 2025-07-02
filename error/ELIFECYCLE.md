# Mengatasi error ELIFECYCLE` (Script error saat install/build)

## 🚫 2. `ELIFECYCLE` (Script error saat install/build)

### 💥 Contoh error:

```bash
npm ERR! code ELIFECYCLE
npm ERR! Failed at the <nama-package> script 'postinstall'.
```

### 🧠 Penyebab:

* Package mencoba menjalankan script saat `npm install`, dan script itu gagal.
* Biasanya karena:

  * Dependensi native tidak cocok (misalnya Node versi terlalu baru/lama)
  * Tidak cocok di ARM seperti Raspberry Pi
  * Ada module compiler seperti `node-gyp` yang gagal build

---

### ✅ Solusi:

#### 🔧 A. Pastikan semua dependensi build tersedia

Untuk Linux/RPi:

```bash
sudo apt install -y build-essential python3 g++ make
```

#### 🔧 B. Pastikan Node.js tidak terlalu baru atau terlalu lama

Gunakan `n` untuk mengelola versi Node:

```bash
sudo npm install -g n
sudo n lts
```

#### 🔧 C. Lewati script (kalau benar-benar darurat)

> Tidak disarankan, tapi bisa dicoba kalau install gagal hanya karena `postinstall`.

```bash
npm install --ignore-scripts
```

---

### 🔄 Bonus: Cek log untuk detail

`npm` selalu simpan log error di file seperti:

```
~/.npm/_logs/2025-06-11T12_34_56_789Z-debug.log
```

---


