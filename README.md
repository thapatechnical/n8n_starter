# 🚀 Setting Up n8n on a VPS

This guide walks you through setting up `n8n` using Docker on a Linux VPS, with firewall protection and domain setup.

---

## 📦 Update and Upgrade Your VPS

Update the package list:

```bash
sudo apt update
```

Upgrade the packages:

```bash
sudo apt upgrade
```

Check if a reboot is required:

```bash
cat /var/run/reboot-required
```

If required, reboot:

```bash
sudo reboot
```

---

## 🔥 Set Up a Firewall with UFW (Uncomplicated Firewall)

A firewall helps protect your server by controlling network traffic.

### 👉 Install UFW (If not pre-installed)

> Hostinger already includes UFW by default.

```bash
sudo apt install ufw
```

### 🔍 Check UFW Status

```bash
sudo ufw status
```

### 🔒 Default Firewall Rules

Deny all incoming requests:

```bash
sudo ufw default deny incoming
```

Allow all outgoing requests:

```bash
sudo ufw default allow outgoing
```

### ✅ Allow SSH Connection (IMPORTANT)

```bash
sudo ufw allow OpenSSH
```

> If you changed your SSH port, allow it explicitly:
```bash
sudo ufw allow <port-number>
```

### 📋 View Configured Rules

```bash
sudo ufw show added
```

### 🔛 Enable Firewall

```bash
sudo ufw enable
```

### 🌐 Allow HTTP and HTTPS Traffic

```bash
sudo ufw allow http        # or sudo ufw allow 80/tcp
sudo ufw allow https       # or sudo ufw allow 443/tcp
```

---

## 🐳 Install Docker

Official guide: [Docker Engine Install for Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

### Add Docker’s Official GPG Key and Repo

```bash
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

### Install Docker Engine

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Verify installation:

```bash
sudo docker run hello-world
```

Check version:

```bash
docker --version
```

---

## 🌐 Set Up Domain

1. Buy a domain from any registrar.
2. Add A Records in your DNS settings:

### Example for `n8n.thapatechnical.in`

#### A Record (Main)

- **Name**: `n8n`
- **Value**: `Your VPS IP`
- **TTL**: `60`

#### A Record (www subdomain)

- **Name**: `www.n8n`
- **Value**: `Your VPS IP`
- **TTL**: `60`

### Verify DNS:

```bash
nslookup n8n.thapatechnical.in
```

---

## ⬇️ Clone n8n Starter

Install git:

```bash
sudo apt install git
```

Clone repo:

```bash
git clone https://github.com/thapatechnical/n8n_starter.git
cd n8n_starter
```

Edit `.env` file with your domain and settings.

---

## 🐳 Run n8n with Docker Compose

```bash
docker compose up -d
```

This sets up `n8n` on `localhost:5678`.

---

## 🔁 Reverse Proxy with Caddy

Visit: [Caddy Install Docs](https://caddyserver.com/docs/install)

### For Ubuntu:

```bash
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https curl
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy
```

Start and enable Caddy:

```bash
sudo systemctl enable caddy
sudo systemctl start caddy
```

---

## 🛠️ Configure Caddy

Edit the `Caddyfile`:

```bash
cd /etc/caddy
sudo nano Caddyfile
```

Paste and modify the following:

```
n8n.thapatechnical.in {
    reverse_proxy localhost:5678
}

www.n8n.thapatechnical.in {
    redir https://n8n.thapatechnical.in{uri}
}
```

Save and exit (`Ctrl + O`, `Enter`, `Ctrl + X`)

Restart Caddy:

```bash
sudo systemctl restart caddy
```

---

## ✅ Access Your n8n Instance

Visit: `https://n8n.thapatechnical.in` in your browser — your instance is now live! 🎉
