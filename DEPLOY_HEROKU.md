# Heroku Deployment Guide

Panduan lengkap untuk deploy **Vulner-Validator** ke Heroku.

---

## 🎯 Prerequisites

1. **Akun Heroku** - Daftar gratis di [heroku.com](https://signup.heroku.com/)
2. **Git** - Sudah terinstall di komputer
3. **Heroku CLI** - Download dari [devcenter.heroku.com/articles/heroku-cli](https://devcenter.heroku.com/articles/heroku-cli)

---

## 📦 Step 1: Install Heroku CLI

### Windows
```bash
# Download installer dari website resmi
# Atau pakai winget:
winget install Heroku.HerokuCLI
```

### macOS
```bash
brew tap heroku/brew && brew install heroku
```

### Linux
```bash
curl https://cli-assets.heroku.com/install.sh | sh
```

**Verify installation:**
```bash
heroku --version
```

---

## 🔐 Step 2: Login ke Heroku

```bash
heroku login
```

Perintah ini akan membuka browser untuk login. Setelah login, kembali ke terminal.

---

## 🚀 Step 3: Deploy Aplikasi

### 3.1 Navigate ke project directory
```bash
cd d:\Vulner-Validator
```

### 3.2 Create Heroku app
```bash
# Buat app dengan nama random
heroku create

# ATAU buat dengan nama spesifik (harus unique)
heroku create vulner-validator-yourname
```

**Output:**
- App URL: `https://vulner-validator-yourname.herokuapp.com`
- Git remote: `https://git.heroku.com/vulner-validator-yourname.git`

### 3.3 Verify Git remote
```bash
git remote -v
```

Harus ada remote `heroku` yang ditambahkan otomatis.

### 3.4 Deploy ke Heroku
```bash
# Deploy dari branch main
git push heroku main

# ATAU jika masih pakai branch master
git push heroku master
```

**Deploy process akan:**
1. ✅ Upload code ke Heroku
2. ✅ Detect Node.js app
3. ✅ Install dependencies dari `package.json`
4. ✅ Run `npm start` (dari Procfile)
5. ✅ Assign URL dan start dynos

### 3.5 Open aplikasi
```bash
heroku open
```

Atau buka manual di browser: `https://your-app-name.herokuapp.com`

---

## 🔧 Step 4: Configuration (Optional)

### Set environment variables (jika diperlukan)
```bash
# Example
heroku config:set NODE_ENV=production
heroku config:set API_KEY=your-api-key

# View all config
heroku config
```

### View current configuration
```bash
heroku config
```

---

## ✅ Step 5: Verify Deployment

### 5.1 Check app status
```bash
heroku ps
```

### 5.2 View logs
```bash
# Streaming logs (real-time)
heroku logs --tail

# Last 100 lines
heroku logs -n 100
```

### 5.3 Test API endpoints

**Health check:**
```bash
curl https://your-app-name.herokuapp.com/api/health
```

**SSL check:**
```bash
curl https://your-app-name.herokuapp.com/api/check-ssl/google.com
```

**Web interface:**
- Buka `https://your-app-name.herokuapp.com` di browser
- Input domain dan test semua validators

---

## 🔄 Update Aplikasi

Setiap kali ada perubahan code:

```bash
# 1. Commit changes
git add .
git commit -m "Your commit message"

# 2. Push to GitHub (optional)
git push origin main

# 3. Deploy to Heroku
git push heroku main

# 4. View logs
heroku logs --tail
```

---

## 📊 Monitoring & Management

### View app info
```bash
heroku info
```

### Restart app
```bash
heroku restart
```

### Scale dynos
```bash
# Free tier (default)
heroku ps:scale web=1

# Upgrade to hobby (paid)
heroku ps:type hobby
```

### Open dashboard
```bash
heroku dashboard
```

---

## 🆓 Free Tier Limitations

> [!IMPORTANT]
> Heroku free tier memiliki batasan:
> - **550-1000 dyno hours/month** (cukup untuk 1 app jalan 24/7)
> - **Sleep setelah 30 menit tidak ada traffic** (first request akan lambat ~10s)
> - **Max 512MB RAM**
> - **Tidak ada custom domain HTTPS** (harus upgrade)

**Solusi sleep issue:**
- Upgrade ke Hobby dyno ($7/month) - no sleep
- Pakai ping service (tidak direkomendasikan untuk free tier)

---

## 🐛 Troubleshooting

### App crashed setelah deploy

**Check logs:**
```bash
heroku logs --tail
```

**Common issues:**
- ❌ `package.json` tidak ada `start` script → Sudah ada ✅
- ❌ Port not dynamic → Sudah pakai `process.env.PORT` ✅
- ❌ Missing dependencies → Run `npm install` locally dulu

### Port already in use (local)
```powershell
# Windows: Find process using port 3000
netstat -ano | findstr :3000

# Kill process
taskkill /PID <PID> /F
```

### Deploy gagal - not a git repository
```bash
# Initialize Git jika belum
git init
git add .
git commit -m "Initial commit"

# Add heroku remote
heroku git:remote -a your-app-name
```

### Changes tidak muncul setelah deploy
```bash
# Clear cache dan rebuild
heroku repo:purge_cache -a your-app-name
git commit --allow-empty -m "Rebuild"
git push heroku main
```

### Check buildpack
```bash
# Should detect Node.js automatically
heroku buildpacks

# If not, add manually
heroku buildpacks:set heroku/nodejs
```

---

## 🔗 Custom Domain (Paid Tier)

```bash
# Add domain (requires verified account + paid dyno)
heroku domains:add www.yourdomain.com

# Get DNS configuration
heroku domains

# Configure DNS di provider Anda sesuai instruksi
# SSL otomatis provided oleh Heroku
```

---

## 📱 Useful Commands Cheatsheet

| Command | Description |
|---------|-------------|
| `heroku apps` | List all your apps |
| `heroku logs --tail` | Real-time logs |
| `heroku restart` | Restart app |
| `heroku ps` | Check dyno status |
| `heroku open` | Open app in browser |
| `heroku run bash` | SSH into dyno |
| `heroku config` | View env vars |
| `heroku releases` | View deployment history |
| `heroku rollback` | Rollback ke versi sebelumnya |

---

## 🎯 Summary

**Your app URLs:**
- **Production**: `https://your-app-name.herokuapp.com`
- **API Health**: `https://your-app-name.herokuapp.com/api/health`
- **Dashboard**: `https://dashboard.heroku.com/apps/your-app-name`

**Deploy workflow:**
```bash
git add .
git commit -m "Update feature"
git push heroku main
heroku logs --tail
```

**Cost:**
- Free tier: $0 (dengan batasan)
- Hobby tier: $7/month (no sleep, better performance)
- Professional tier: $25-50/month (autoscaling, advanced features)

---

## 📚 Additional Resources

- [Heroku Node.js Guide](https://devcenter.heroku.com/articles/getting-started-with-nodejs)
- [Heroku CLI Commands](https://devcenter.heroku.com/articles/heroku-cli-commands)
- [Deploying with Git](https://devcenter.heroku.com/articles/git)
- [Node.js Best Practices](https://devcenter.heroku.com/articles/node-best-practices)

---

## 🆚 Comparison: Heroku vs VPS

Sudah punya panduan VPS di [`DEPLOY_VPS.md`](file:///d:/Vulner-Validator/DEPLOY_VPS.md). Pilih sesuai kebutuhan:

| Feature | Heroku | VPS |
|---------|--------|-----|
| **Setup time** | 5-10 menit | 30-60 menit |
| **Cost** | Free - $7/month | $5-10/month |
| **SSL** | Otomatis | Manual setup |
| **Scaling** | Sangat mudah | Manual/kompleks |
| **Control** | Limited | Full control |
| **Best for** | Quick deploy, demo, MVP | Production, full control |

**Recommendation:**
- 🎯 **Heroku**: Untuk demo, testing, atau production kecil-menengah
- 🎯 **VPS**: Untuk production besar, butuh custom config, atau cost optimization

---

## ✨ Next Steps

1. ✅ Deploy ke Heroku
2. 🧪 Test semua validators di production
3. 📊 Monitor logs dan performance
4. 🎨 (Optional) Setup custom domain jika butuh
5. 📈 (Optional) Upgrade ke Hobby dyno jika traffic tinggi

**Happy deploying! 🚀**
