# 📘 Dokumentasi Hosting Bot Telegram FastAPI di VPS (Tanpa Nginx)

## 🧾 Prasyarat

* Domain aktif, contoh: `bot.micin.id`
* VPS aktif, contoh IP: `203.123.45.67`
* Bot Telegram sudah menggunakan webhook
* Folder project sudah berisi struktur Python + virtualenv

---

## 🔐 1. Arahkan Domain ke IP VPS

Masuk ke DNS panel provider domain kamu (misalnya Niagahoster, Cloudflare, dll), lalu:

* Tambahkan **A Record**

```
  Name: bot
  Type: A
  Value: 203.123.45.67
  TTL: 3600
```

---

## 🖥️ 2. Login ke VPS via SSH

Buka terminal lokal dan ketik:

```bash
ssh root@203.123.45.67
```

Jika kamu pakai user non-root:

```bash
ssh youruser@203.123.45.67
```

---

## 🔧 3. Update VPS & Install Dependensi

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install python3-pip python3-venv curl git ufw -y
```

---

## 🔥 4. Konfigurasi Firewall UFW

```bash
sudo ufw allow OpenSSH
sudo ufw allow 80
sudo ufw allow 443
sudo ufw enable
```

Cek status:

```bash
sudo ufw status
```

---

## 📂 5. Clone / Upload Project ke VPS

Jika dari GitHub:

```bash
git clone https://github.com/kamu/repo-bot.git
cd repo-bot
```

Jika kamu upload manual, bisa pakai `scp`:

```bash
scp -r /lokal/foldermu/ root@203.123.45.67:/home/botuser/
```

---

## 🐍 6. Aktifkan Virtualenv

Misal struktur project seperti ini:

```
/home/botuser/project_bot/
├── env_bot/
├── main.py
├── requirements.txt
```

Aktifkan venv:

```bash
cd /home/botuser/project_bot/
source env_bot/bin/activate
```

Install dependensi:

```bash
pip install -r requirements.txt
```

---

## 🔑 7. Install Certbot (TLS Tanpa Nginx)

```bash
sudo apt install certbot -y
```

Kita akan buat cert untuk `bot.micin.id` (tanpa nginx):

```bash
sudo certbot certonly --standalone -d bot.micin.id
```

Setelah berhasil, sertifikat disimpan di:

* **Fullchain**: `/etc/letsencrypt/live/bot.micin.id/fullchain.pem`
* **Privkey**: `/etc/letsencrypt/live/bot.micin.id/privkey.pem`

---

## 🔁 8. Buat File Systemd Service

Misalnya file ini:

```bash
sudo nano /etc/systemd/system/botwebhook.service
```

Isi:

```ini
[Unit]
Description=Bot Telegram FastAPI - Gunicorn
After=network.target

[Service]
User=botuser
WorkingDirectory=/home/botuser/project_bot
ExecStart=/home/botuser/project_bot/env_bot/bin/gunicorn main:app \
  -k uvicorn.workers.UvicornWorker \
  --bind 0.0.0.0:443 \
  --certfile=/etc/letsencrypt/live/bot.micin.id/fullchain.pem \
  --keyfile=/etc/letsencrypt/live/bot.micin.id/privkey.pem

Restart=always

[Install]
WantedBy=multi-user.target
```

Simpan dan aktifkan:

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable botwebhook
sudo systemctl start botwebhook
sudo systemctl status botwebhook
```

---

## 🔗 9. Set Webhook Bot Telegram

Contoh endpoint webhook:

```
https://bot.micin.id/webhook
```

Set webhook (bisa via script atau curl):

```bash
curl -X POST https://api.telegram.org/bot<YOUR_BOT_TOKEN>/setWebhook \
-d "url=https://bot.micin.id/webhook"
```

---

## 🔁 10. Perpanjang Otomatis Sertifikat TLS

Tambahkan ke cron:

```bash
sudo crontab -e
```

Tambahkan:

```
0 3 * * * certbot renew --quiet --standalone && systemctl restart botwebhook
```

---

## ✅ Selesai

### Untuk cek:

* Buka: [https://bot.micin.id](https://bot.micin.id)
* Cek: `systemctl status botwebhook`
* Log: `journalctl -u botwebhook -f`

---

Kalau kamu perlu versi markdown atau PDF tinggal bilang aja. Mau saya lanjut generate `README.md` dari dokumentasi ini?
