# 🚀 n8n Setup on EC2 (Ubuntu/Debian - APT-based Systems)

This guide walks you through installing and running [n8n](https://n8n.io) on an Ubuntu EC2 instance using APT and Node.js.

---

## 🧰 Prerequisites

- EC2 instance running Ubuntu 20.04+ or Debian
- SSH access with sudo privileges
- Open ports: `5678` for n8n web UI

---

## ⚙️ Step 1: Update System

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 📦 Step 2: Install Node.js (v18 LTS)

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
```

✅ Confirm installation:

```bash
node -v
npm -v
```

---

## 🧪 Step 3: Install n8n

```bash
sudo npm install n8n -g
```

---

## 🚀 Step 4: Run n8n

```bash
n8n
```

🔗 Access the UI at: `http://<your-ec2-public-ip>:5678`

---

## 🔐 (Optional) Step 5: Run as Background Service (PM2)

Install [PM2](https://pm2.keymetrics.io/) to keep `n8n` running even after logout:

```bash
sudo npm install pm2 -g
pm2 start n8n
pm2 startup
pm2 save
```

---

## 🔐 (Optional) Step 6: Secure with Basic Auth

Set environment variables (recommended via `.bashrc` or `.env`):

```bash
export N8N_BASIC_AUTH_ACTIVE=true
export N8N_BASIC_AUTH_USER=admin
export N8N_BASIC_AUTH_PASSWORD=your_secure_password
```

Then run `n8n` again:

```bash
n8n
```

---

## 🛠️ (Optional) Step 7: Setup Reverse Proxy with Nginx (for production)

To run on port 80 or 443 (with HTTPS), use Nginx:

```bash
sudo apt install nginx -y
```

Create a reverse proxy config for `n8n` (edit `/etc/nginx/sites-available/n8n`):

```
server {
    listen 80;

    server_name your-domain.com;

    location / {
        proxy_pass http://localhost:5678;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Enable and reload:

```bash
sudo ln -s /etc/nginx/sites-available/n8n /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## ✅ Done!

Your n8n instance is now live. Start building workflows 🚀

---

## 🧽 Troubleshooting

- Use `pm2 logs` to see background logs.
- Make sure port `5678` is open in AWS EC2 security group.
- If `n8n` command not found: ensure npm global bin is in `$PATH` (`/usr/local/bin` or `~/.npm-global/bin`)

---

## 🔄 Update n8n

```bash
sudo npm install n8n -g
pm2 restart n8n
```