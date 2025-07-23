# 🚀 n8n EC2 Deployment Guide

This repository contains step-by-step guides to help you deploy [n8n](https://n8n.io) on an Amazon EC2 instance. You can choose between two setup methods depending on your preference and experience.

---

## 📦 Setup Options

### 1. [Deploy with Node.js and npm](./setup-n8n-ec2-npm.md)
Use this method if you prefer installing n8n globally with npm and managing it manually via `systemd`.

Includes:
- Node.js 18.x installation
- Global n8n setup
- Background service via `systemd`
- HTTPS & Basic Auth configuration

### 2. [Deploy with Yum Package Manager](./setup-n8n-ec2-yum.md)
This method is optimized for Amazon Linux 2 using yum and system-level packages.

Includes:
- Node.js installation via yum (Nodesource)
- Global n8n setup
- `systemd` service creation
- SSL setup using OpenSSL
- Basic Auth with environment variables

---

## 🔐 Security Note
Ensure port `5678` is open in your EC2 Security Group, and always secure your deployment using **SSL certificates** and **Basic Auth**.

---

## 📁 File Structure

```
n8n-aws-deployment/
├── setup-n8n-ec2-npm.md
├── setup-n8n-ec2-yum.md
└── README.md
```

---

## ✍️ Author
Created with ❤️ by Bitpixel Coders