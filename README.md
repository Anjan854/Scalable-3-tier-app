# 🚀 Scalable 3-Tier Web Application on AWS (Free Tier)

A production-ready 3-tier web application deployed on an AWS EC2 instance using open-source tools — optimized for zero-cost deployment using the AWS Free Tier. Includes Node.js backend, React frontend, MongoDB database, and NGINX reverse proxy. Managed and automated using PM2.

---

## 📦 Tech Stack

| Layer     | Technology         |
|-----------|--------------------|
| Frontend  | React (Vite)       |
| Backend   | Node.js + Express  |
| Database  | MongoDB (local)    |
| Hosting   | AWS EC2 (Ubuntu)   |
| Reverse Proxy | NGINX          |
| Process Manager | PM2          |

---

## 🧱 Architecture

```
Browser
   │
   ▼
NGINX (port 80)
 ├── /           → Frontend (React on port 5173)
 └── /api/       → Backend (Node.js on port 5000)
                     │
                     ▼
               MongoDB (localhost:27017)
```

---

## 🚀 Features

- 🔁 3-tier architecture (frontend, backend, DB)
- 🔐 NGINX reverse proxy with basic security
- ⚙️ PM2 for automated service management
- 💾 MongoDB installed and managed locally
- 🌍 Zero-cost hosting on AWS EC2 Free Tier

---

## 🛠️ Setup Instructions

### 1. Clone the repository
```bash
git clone https://github.com/your-username/3-tier-app.git
cd 3-tier-app
```

### 2. Install MongoDB (Ubuntu)
```bash
# Add MongoDB repo
curl -fsSL https://pgp.mongodb.com/server-8.0.asc | \
sudo gpg -o /usr/share/keyrings/mongodb-server-8.0.gpg --dearmor

echo "deb [ signed-by=/usr/share/keyrings/mongodb-server-8.0.gpg ] \
https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/8.0 multiverse" | \
sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list

sudo apt update && sudo apt install -y mongodb-org
sudo systemctl start mongod
```

### 3. Install Node.js (via NVM)
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.nvm/nvm.sh
nvm install --lts
```

---

## 🔧 Backend Setup

```bash
cd backend
npm ci
pm2 start server.js --name backend
```

Make sure `.env` has:
```env
PORT=5000
MONGO_URI=mongodb://localhost:27017/your-db-name
```

---

## 🎨 Frontend Setup

```bash
cd ../frontend
npm ci

# Set VITE_BASE_URL to use reverse-proxy path
echo "VITE_BASE_URL=http://<your-ec2-ip>/api" > .env

pm2 start 'npm run dev -- --host' --name frontend
```

---

## 🌐 NGINX Configuration

Update `/etc/nginx/sites-available/default`:
```nginx
server {
    listen 80;
    server_name <your-ec2-ip>;

    location / {
        proxy_pass http://localhost:5173;
        proxy_set_header Host $host;
    }

    location /api/ {
        proxy_pass http://localhost:5000/;
        proxy_set_header Host $host;
    }
}
```

Then reload:
```bash
sudo nginx -t
sudo systemctl restart nginx
```

---

## 🛡 Security

```bash
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx Full'
sudo ufw enable
```

---

## 🔄 PM2 Automation

```bash
pm2 save
pm2 startup   # Follow printed instructions to enable on reboot
```

To view logs or monitor:
```bash
pm2 logs
pm2 list
```

---

## 📂 Project Structure

```
3-tier-app/
├── backend/
│   └── server.js, .env
├── frontend/
│   └── Vite + React app
└── nginx/
    └── default (NGINX config)
```

---

## 🧑‍💼 DevOps Responsibilities

- Provisioned and configured AWS EC2 (Ubuntu) instance
- Installed MongoDB, Node.js (via NVM), and NGINX
- Deployed backend and frontend apps using PM2
- Configured NGINX reverse proxy and firewall
- Secured ports and automated app lifecycle

---

## 📚 Future Improvements

- 🔒 HTTPS with Let's Encrypt
- 📦 Dockerize backend/frontend
- 🔁 GitHub Actions for CI/CD
- 🗃 Use MongoDB Atlas for hosted DB
- ☁️ Replace EC2 with container services (e.g., ECS)

---

## 📜 License

This project is open source under the [MIT License](LICENSE).
