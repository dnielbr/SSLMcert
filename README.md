# SSLMkcert

🔒 SSL com Mkcert e Nginx

Guia completo para instalar o Mkcert e configurar certificados SSL locais para o Nginx.
Ideal para ambientes de desenvolvimento, permitindo acesso via HTTPS sem avisos de certificado inválido no navegador.
---

## 1. instalar mkcert
```bash
sudo apt update
sudo apt install libnss3-tools -y
sudo apt install mkcert -y
```
---
## 2. Criar a autoridade certificadora local (CA)
```bash
mkcert -install

Created a new local CA 💥
The local CA is now installed in the system trust store! ⚡️
The local CA is now installed in the Firefox trust store (requires browser restart)! 🦊
```
---
## 3. Gerar certificados SSL
```bash
mkcert gcsi local localhost 127.0.0.1 ::1

Created a new local CA 💥
The local CA is now installed in the system trust store.
The local CA is now installed in the Java trust store.

Created a new certificate valid for the following names 📜
 - "gcsi"
 - "local"
 - "localhost"
 - "127.0.0.1"
 - "::1"

The certificate is at "./gcsi+4.pem" and the key at "./gcsi+4-key.pem" ✅
```
---
## 4. Organizar os certificados
```bash
cd /etc/nginx
sudo mkdir ssl
sudo mv ~/gcsi+4.pem ~/gcsi+4-key.pem /etc/nginx/ssl/
sudo chmod 600 /etc/nginx/ssl/gcsi+4-key.pem
```
---
## 5. Configurar o Nginx
```bash
sudo nano /etc/nginx/sites-available/default

# --- HTTPS ---
server {
    listen 443 ssl;
    
    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;
    
    server_name localhost;

    ssl_certificate /etc/nginx/ssl/gcsi+4.pem
    ssl_certificate_key /etc/nginx/ssl/gcsi+4-key.pem

    location / {
        try_files $uri $uri/ =404;
    }
}

# --- HTTP (redireciona para HTTPS) ---
server {
    listen 80;
    listen [::]:80;
    server_name localhost;

    return 301 https://$host$request_uri;
}
```
---
## 6. Ative a configuração
```bash
sudo ln -s /etc/nginx/sites-available/gcsi.conf /etc/nginx/sites-enabled/
```

## 7. Teste e reinicie o Nginx
```bash
sudo nginx -t
sudo systemctl restart nginx
```
---
## 8. Adicionar o domínio ao /etc/hosts
```bash
sudo nano /etc/hosts
Adicione: 127.0.0.1 localhost
```
---
## 9. Testar no navegador
```bash
https://localhost
```
---
# 📜 Licença
Este guia é livre para uso e modificação.
Criado para simplificar o uso de SSL local com mkcert + Nginx 💚