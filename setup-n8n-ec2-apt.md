## 🧾 Table of Contents
1. Overview  
2. Prerequisites  
3. System Update  
4. Node.js and npm Installation  
5. Installing n8n  
6. Running n8n  
7. Creating a systemd Service for n8n  
8. Securing n8n with SSL (HTTPS)  
9. Adding Basic Auth  
10. Final Steps  
11. Troubleshooting  

# ✅ Setup n8n on EC2 Ubuntu with Subdomain and PM2

Follow these steps to install and configure `n8n` on an **Ubuntu-based EC2 instance**, bind it to a **subdomain** like `https://n8n.example.com`, and run it using **PM2** for background process management.


---

## 🔧  Prerequisites

- Amazon EC2 instance (Amazon Linux 2 or similar)
- SSH access
- Security Group with port `5678` open (for n8n access)
- Public IP or domain
- A domain/subdomain (e.g., `n8n.example.com`)
- `A` record pointing `n8n.example.com` → your EC2 IP
- `nginx` installed
- Free SSL setup for subdomain


---

## 1️⃣ Update & Install Essentials

```bash
sudo apt update -y
sudo apt upgrade -y
sudo apt install curl nginx unzip -y
```

---

## 2️⃣ Install Node.js (v18)

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
node -v
npm -v
```

---

## 3️⃣ Install PM2 Globally

```bash
sudo npm install pm2 -g
```

---

## 4️⃣ Install n8n

```bash
sudo npm install -g n8n
```

---

## 5️⃣ Start n8n with PM2

```bash
pm2 start n8n
pm2 startup
pm2 save
```

> Optional: Set environment variables:
```bash
export N8N_PORT=5678
export N8N_HOST=localhost
```

---

## 6️⃣ Setup Nginx Reverse Proxy

### 🔧 Create Config File

```bash
sudo nano /etc/nginx/sites-available/n8n
```

Paste:

```nginx
server {
    server_name n8n.example.com;

    location / {
        proxy_pass http://localhost:5678/;
         # WebSocket support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }   

}
server {
    if ($host = n8n.example.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot

    listen 80;
    server_name n8n.example.com;
    return 404; # managed by Certbot
}

```

> Replace `n8n.example.com` with your actual domain.

### 🔗 Enable the Config

```bash
sudo ln -s /etc/nginx/sites-available/n8n /etc/nginx/sites-enabled/
```

> If already exists:
```bash
sudo rm /etc/nginx/sites-enabled/n8n
sudo ln -s /etc/nginx/sites-available/n8n /etc/nginx/sites-enabled/
```

---

## 7️⃣ Restart nginx

```bash
sudo nginx -t
sudo systemctl restart nginx
```

Now visit `http://n8n.example.com` — it should load the n8n interface.

---

## 8️⃣ (Optional) Setup HTTPS with Let's Encrypt

```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d n8n.example.com
```

Choose `redirect` to enable HTTPS.

---

## ✅ Verify Everything

```bash
pm2 list
sudo systemctl status nginx
curl -I http://localhost:5678
```

Visit: `https://n8n.example.com`

---

## 🧠 Useful PM2 Commands

```bash
pm2 logs n8n
pm2 restart n8n
pm2 stop n8n
```

---

🎉 You’re done! n8n is now live on your subdomain with HTTPS and auto-restarting via PM2.
