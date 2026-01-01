# Static Website Hosting Using Jenkins

This guide explains how to host a **static website on a live server using a Jenkins server** with GitHub integration.

---

## 1. Prepare Static Website (Local / Dev Machine)

```bash
mkdir static-website
cd static-website
```
Create HTML files:
- `index.html`
- `home.html`

Initialize Git and push to GitHub:
```bash
git init
git add .
git commit -m "Initial static website"
git branch -M master
git remote add origin <your-github-repo-url>
git push -u origin master
```

Install zip package (Ubuntu):
```bash
sudo apt update
sudo apt install zip -y
```

---

## 2. Configure GitHub Webhook

1. Go to **GitHub Repository → Settings → Webhooks**
2. Add webhook:
   - Payload URL: `http://<jenkins-ip>:8080/github-webhook/`
3. Save webhook

---

## 3. Launch Live Server

- Launch an **Ubuntu EC2**
- Open port **80** (HTTP) in security group / firewall

---

## 4. Jenkins Server Configuration

### Create Jenkins Job
- Item Name: `static-website-hosting`
- Type: **Freestyle Project**
- Description: Host static website using Jenkins

### Source Code Management
- Git Repository URL: `<your-github-repo-url>`
- Branch: `*/master`

### Build Triggers
- GitHub hook trigger for SCM polling

---

## 5. Jenkins Execute Shell Script

```bash
zip -r myfile.zip ./*.html static-website/
sudo chmod 600 /home/ubuntu/public.pem
sudo scp -i /home/ubuntu/public.pem -o StrictHostKeyChecking=no myfile.zip ubuntu@100.26.242.29:.
sudo ssh -i /home/ubuntu/public.pem  -o StrictHostKeyChecking=no ubuntu@100.26.242.29 << EOF
sudo apt update
sudo apt install nginx -y
sudo apt install zip -y
sudo cp myfile.zip /var/www/html
cd /var/www/html
sudo rm *.html
sudo unzip myfile.zip
curl http://localhost
EOF
EOF
```
### Save and Build item
---
 
## 6. Verification

- Open browser
- Visit:  
  ```
  http://<leve-server-ip>
  ```
- Static website should be live 🎉
<img src="" width="600">

---

## Architecture Flow

GitHub → Jenkins → SCP/SSH → Nginx Live Server

---
