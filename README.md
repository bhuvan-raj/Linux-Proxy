# 🔌 Understanding Proxies

## 📘 What is a Proxy?

A **proxy** is an intermediary server that sits between a client and a destination server. It forwards requests from the client to another server and then sends the response back to the client.

---

## 🔄 Types of Proxies

### 1. **Forward Proxy**

* Sits **in front of clients**.
* Used to access the internet on behalf of clients.
* Common in corporate networks.

**Example Use Cases:**

* Content filtering
* Internet access control
* Anonymous browsing

### 2. **Reverse Proxy**

* Sits **in front of servers**.
* Accepts requests from clients and forwards them to the appropriate backend server.

**Example Use Cases:**

* Load balancing
* SSL termination
* Caching
* Application firewall

---

## ✅ Why Proxies Are Important

* Security: Hide backend server details
* Load balancing: Distribute load across servers
* Caching: Improve performance
* SSL offloading: Manage HTTPS connections
* Centralized logging and access control

---

# 🚀 Setup: Reverse Proxy using NGINX on AWS EC2 (Amazon Linux)

## 📦 What This Guide Covers

* Installing and configuring NGINX as a reverse proxy
* Running everything on an EC2 instance (Amazon Linux 2)

---

## ✅ Prerequisites

* Amazon EC2 instance with Amazon Linux 2
* Inbound rule in security group allowing **port 80**
* SSH access to EC2 instance

---

## 🔧 Step-by-Step Instructions

### 1️⃣ SSH into your EC2 instance

```bash
ssh -i your-key.pem ec2-user@<EC2-PUBLIC-IP>
```

### 2️⃣ Update the system

```bash
sudo yum update -y
```

### 3️⃣ Install Python 3 and pip

```bash
sudo yum install -y python3
sudo pip3 install flask
```

### 4️⃣ Create a simple Flask app

```bash
mkdir flask-app && cd flask-app
nano app.py
```

Paste the following Flask code:

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "<h1>Hello from Flask behind NGINX!</h1>"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=3000)
```

### 5️⃣ Run the Flask app

```bash
python3 app.py
```

Flask will now run on port 3000.

---

## 🌐 Set Up NGINX as a Reverse Proxy

### 6️⃣ Install NGINX

```bash
sudo amazon-linux-extras enable nginx1
sudo yum install -y nginx
```

### 7️⃣ Start and enable NGINX

```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

### 8️⃣ Configure reverse proxy

Edit the config:

```bash
sudo nano /etc/nginx/nginx.conf
```

Inside the `http` block, add or modify this `server` block:

```nginx
server {
    listen 80;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

### 9️⃣ Restart NGINX

```bash
sudo systemctl restart nginx
```

---

## 🔎 Test the Setup

* Open your browser and go to: `http://<EC2-PUBLIC-IP>`
* You should see: `Hello from Flask behind NGINX!`

---

## 📁 Folder Structure

```
/home/ec2-user/flask-app/
└── app.py
```

---

## ✅ Summary

* Flask runs your backend on port 3000
* NGINX forwards requests from port 80 to Flask
* EC2 security group must allow HTTP traffic

Let me know if you'd like to dockerize this setup or deploy with Terraform!

## 📌 Summary

* A **proxy** routes traffic between clients and servers.
* **Forward proxy** is for outbound (client-side); **reverse proxy** is for inbound (server-side).
* Reverse proxies improve performance, security, and scalability.
* Apache can be used as a reverse proxy easily on Amazon Linux EC2.

---
