# Nexify Webgrip Services Pvt. Ltd.
### Web Development · Web Management · AI-Led Technology Solutions

**CIN:** U62099MH2024PTC436743  
**Website:** http://13.60.88.111

---

## 🚀 Quick Deploy

### Local
```bash
docker compose up -d --build
```
Visit: http://localhost

### EC2 (manual)
```bash
git clone https://github.com/shresth111/SHRESTH.git nexify
cd nexify
docker compose up -d --build
```

---

## ⚙️ Auto Deploy (GitHub Actions)

Every push to `main` branch automatically deploys to EC2.

### Setup GitHub Secrets:
Go to → **GitHub Repo → Settings → Secrets → Actions → New secret**

| Secret Name | Value |
|-------------|-------|
| `EC2_HOST` | `13.60.88.111` |
| `EC2_USER` | `ubuntu` |
| `EC2_KEY` | contents of `nexify.pem` file |

---

## 📁 Project Structure
```
├── index.html          # Main website
├── Dockerfile          # Docker build config
├── docker-compose.yml  # Container orchestration
└── .github/
    └── workflows/
        └── deploy.yml  # Auto-deploy on push
```

---

## 📍 Offices
- **Mumbai:** A26, 27, Floor 25–26, Ahuja Tower, Rajabhau Anant Desai Marg, 400025
- **Gurugram:** Plot 46, Phase IV, Udyog Vihar, Sector 18, Haryana 122015

📧 dev@nexifywebgripservicesprivatelimited.com  
📞 +91 78381 88283
