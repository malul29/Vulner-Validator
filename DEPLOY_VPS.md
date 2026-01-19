# VPS Deployment Guide

## Prerequisites

- Ubuntu 20.04+ or similar Linux VPS
- SSH access to your VPS
- Domain name (optional, bisa pakai IP address)

---

## Step 1: Persiapan VPS

### 1.1 Update sistem
```bash
sudo apt update && sudo apt upgrade -y
```

### 1.2 Install Node.js (v18+)
```bash
# Install NodeSource repository
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -

# Install Node.js
sudo apt install -y nodejs

# Verify installation
node -v
npm -v
```

### 1.3 Install PM2 (Process Manager)
```bash
sudo npm install -g pm2
```

### 1.4 Install Nginx (Reverse Proxy)
```bash
sudo apt install -y nginx
```

### 1.5 Install Git
```bash
sudo apt install -y git
```

---

## Step 2: Clone & Setup Aplikasi

### 2.1 Clone repository
```bash
cd /var/www
sudo git clone https://github.com/malul29/Vulner-Validator.git
cd Vulner-Validator
```

### 2.2 Install dependencies
```bash
npm install --production
```

### 2.3 Setup environment (optional)
```bash
cp .env.example .env
nano .env  # Edit jika perlu
```

### 2.4 Create logs directory
```bash
mkdir -p logs
```

---

## Step 3: Start Aplikasi dengan PM2

### 3.1 Start aplikasi
```bash
pm2 start ecosystem.config.js
```

### 3.2 Setup PM2 startup (auto-start saat reboot)
```bash
pm2 startup
# Copy & paste command yang muncul, lalu jalankan

# Save PM2 process list
pm2 save
```

### 3.3 Check status
```bash
pm2 status
pm2 logs vulner-validator
```

**Aplikasi sekarang jalan di `http://localhost:3000`**

---

## Step 4: Setup Nginx Reverse Proxy

### 4.1 Create Nginx config
```bash
sudo nano /etc/nginx/sites-available/vulner-validator
```

### 4.2 Copy config dari `nginx.conf.example`
- Ganti `your-domain.com` dengan domain Anda
- Atau pakai IP address jika tidak punya domain

### 4.3 Enable site
```bash
sudo ln -s /etc/nginx/sites-available/vulner-validator /etc/nginx/sites-enabled/
```

### 4.4 Test & reload Nginx
```bash
sudo nginx -t
sudo systemctl reload nginx
```

**Aplikasi sekarang bisa diakses dari `http://your-domain.com`**

---

## Step 5: Setup SSL (Optional tapi Recommended)

### 5.1 Install Certbot
```bash
sudo apt install -y certbot python3-certbot-nginx
```

### 5.2 Get SSL certificate
```bash
sudo certbot --nginx -d your-domain.com -d www.your-domain.com
```

### 5.3 Test SSL renewal
```bash
sudo certbot renew --dry-run
```

**Aplikasi sekarang aman dengan HTTPS: `https://your-domain.com`**

---

## Step 6: Firewall Setup (Optional)

```bash
# Allow SSH, HTTP, HTTPS
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx Full'
sudo ufw enable
sudo ufw status
```

---

## Maintenance Commands

### View logs
```bash
pm2 logs vulner-validator
pm2 logs vulner-validator --lines 100
```

### Restart aplikasi
```bash
pm2 restart vulner-validator
```

### Update aplikasi
```bash
cd /var/www/Vulner-Validator
sudo git pull
npm install --production
pm2 restart vulner-validator
```

### Monitor
```bash
pm2 monit
```

---

## Troubleshooting

### Aplikasi tidak jalan
```bash
# Check PM2 status
pm2 status

# Check logs
pm2 logs vulner-validator --err

# Restart
pm2 restart vulner-validator
```

### Nginx error
```bash
# Check nginx error log
sudo tail -f /var/log/nginx/error.log

# Test config
sudo nginx -t

# Restart nginx
sudo systemctl restart nginx
```

### Port sudah dipakai
```bash
# Check what's using port 3000
sudo lsof -i :3000

# Kill process if needed
sudo kill -9 <PID>
```

---

## Summary

**URLs:**
- Development: `http://localhost:3000`
- Production (Nginx): `http://your-domain.com`
- Production (SSL): `https://your-domain.com`

**Important Files:**
- App: `/var/www/Vulner-Validator`
- Nginx config: `/etc/nginx/sites-available/vulner-validator`
- PM2 logs: `/var/www/Vulner-Validator/logs/`
- Nginx logs: `/var/log/nginx/`

**Commands to remember:**
- `pm2 status` - Check app status
- `pm2 logs vulner-validator` - View logs
- `pm2 restart vulner-validator` - Restart app
- `sudo systemctl status nginx` - Check Nginx status
