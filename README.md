
# 🔌 Understanding Proxies

<img src="https://github.com/bhuvan-raj/Logical-Volume-Management/blob/main/linux-proxy-server.png" alt="Banner" />
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

## 📆 What This Guide Covers

* Creating a small Flask app in Python on one EC2 instance (App Server)
* Setting up a second EC2 instance as a reverse proxy with NGINX (Proxy Server)
* Connecting the reverse proxy to the backend app server

---

## ✅ Prerequisites

* Two Amazon EC2 instances (Amazon Linux 2)

  * **App Server** to host the Python Flask application
  * **Proxy Server** to act as the reverse proxy
* Inbound rule in security group:

  * **App Server:** Port 3000 open for internal access (from Proxy Server)
  * **Proxy Server:** Port 80 open to public
* SSH access to both EC2 instances

---

## 🔧 Step-by-Step Instructions

### 1⃣ Set Up Flask on App Server (Backend)

#### 1. SSH into the App Server

```bash
ssh -i your-key.pem ec2-user@<APP-SERVER-PUBLIC-IP>
```

#### 2. Update and install Python

```bash
sudo su
yum update -y
yum install -y python3
yum install python3-pip -y
pip3 install flask

```

#### 3. Create and run a Flask app

```bash
mkdir flask-app && cd flask-app
nano app.py
```

Paste this code:

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "<h1>Hello from Flask App Server!</h1>"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=3000)
```

Run the app:

```bash
python3 app.py
```

Keep this running (or run in background using `nohup` or `tmux`).

---

### 2⃣ Set Up NGINX Reverse Proxy on Proxy Server

#### 1. SSH into the Proxy Server

```bash
ssh -i your-key.pem ec2-user@<PROXY-SERVER-PUBLIC-IP>
```

#### 2. Install NGINX

```bash
sudo yum install -y nginx
```

#### 3. Start and enable NGINX

```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

#### 4. Configure NGINX as reverse proxy

Edit config:

```bash
sudo nano /etc/nginx/nginx.conf
```

In the `http` block, modify or add this server block:

```nginx
server {
    listen 80;

    location / {
        proxy_pass http://<APP-SERVER-PRIVATE-IP>:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

> Replace `<APP-SERVER-PRIVATE-IP>` with the private IP of the App Server.

#### 5. Restart NGINX

```bash
sudo systemctl restart nginx
```

---

## 🔎 Test the Setup

* Open browser and visit: `http://<PROXY-SERVER-PUBLIC-IP>`
* You should see: `Hello from Flask App Server!`

---

## 📁 Folder Structure (on App Server)

```
/home/ec2-user/flask-app/
└── app.py
```

---

## ✅ Summary

* Flask app runs on **App Server** (port 3000)
* NGINX on **Proxy Server** forwards traffic to App Server
* Useful for scaling, security, and access control



## 📌 Summary

* A **proxy** routes traffic between clients and servers.
* **Forward proxy** is for outbound (client-side); **reverse proxy** is for inbound (server-side).
* Reverse proxies improve performance, security, and scalability.
* Apache can be used as a reverse proxy easily on Amazon Linux EC2.

---
