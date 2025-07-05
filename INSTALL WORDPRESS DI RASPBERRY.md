## ✅ INSTALL WORDPRESS DI RASPBERRY PI (LOCAL DEV)

### 1. **Update & Install Paket Dasar**

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install apache2 mariadb-server php php-mysql libapache2-mod-php php-curl php-json php-cgi php-gd php-mbstring php-xml php-xmlrpc unzip curl wget -y
```

### 2. **Setup Database**

```bash
sudo mysql -u root -p
```

Lalu jalankan di prompt SQL:

```sql
CREATE DATABASE wordpress;
CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'passwordku';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### 3. **Download & Pasang WordPress**

```bash
cd /var/www/html
sudo rm index.html
sudo wget https://wordpress.org/latest.zip
sudo unzip latest.zip
sudo mv wordpress/* .
sudo rm -rf wordpress latest.zip
sudo chown -R www-data:www-data /var/www/html
```

### 4. **Konfigurasi WordPress**

```bash
sudo cp wp-config-sample.php wp-config.php
sudo vim wp-config.php
```

Edit bagian ini:

```php
define( 'DB_NAME', 'wordpress' );
define( 'DB_USER', 'wpuser' );
define( 'DB_PASSWORD', 'passwordku' );
define( 'DB_HOST', 'localhost' );
```

### 5. **Restart Service**

```bash
sudo systemctl restart apache2
```

Sekarang buka browser:

```text
http://localhost/
```

Lanjutkan instalasi WordPress seperti biasa.

---

## 🌐 EKSPOS VIA NGROK

### 6. **Install ngrok & Jalankan**

```bash
wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-arm.zip
unzip ngrok-stable-linux-arm.zip
chmod +x ngrok
sudo mv ngrok /usr/local/bin/
ngrok config add-authtoken <TOKEN_KAMU>
ngrok http 80
```

Nanti URL `https://xxxxx.ngrok.io` bisa kamu share ke klien.

---

## ⚠️ CATATAN

* Untuk Telegram login, karena ngrok URL berubah, domainnya juga berubah → pastikan update **redirect URL** di konfigurasi bot Telegram.
* Bisa pakai [ngrok reserved domain](https://dashboard.ngrok.com/cloud-edge/domains) (berbayar) kalau mau domain tetap.

