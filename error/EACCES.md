# Mengatasi error EACCES` (Permission denied)
Tentu, berikut **dua jenis error paling sering dialami saat pakai `npm`**, khususnya oleh developer yang bekerja di environment terbatas (seperti Raspberry Pi, Linux low-resource, dll), dan **cara memperbaikinya**:

---

## 🔥 1. `EACCES` (Permission denied)

### 💥 Contoh error:

```bash
npm ERR! Error: EACCES: permission denied, access '/usr/lib/node_modules'
```

### 🧠 Penyebab:

Kamu mencoba install package **secara global** tanpa hak akses (`sudo`), padahal folder tujuan dimiliki root.

---

### ✅ Solusi:

#### 🔧 A. Pakai `sudo` untuk install global:

```bash
sudo npm install -g <nama-package>
```

#### 🔧 B. ATAU (lebih direkomendasikan): ubah lokasi global install ke folder home kamu

```bash
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
```

Edit `~/.profile` atau `~/.bashrc` dan tambahkan:

```bash
export PATH=~/.npm-global/bin:$PATH
```

Lalu:

```bash
source ~/.profile
```

---

