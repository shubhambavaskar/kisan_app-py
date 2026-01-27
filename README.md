# kisan_app-py

Here is your complete Maharashtra Kisan App in a single file format, including frontend, backend, MySQL setup, and deployment guide.

---

### ✅ 1. **Project Structure (Single File: `kisan_app.py`)**

This Python Flask app includes:
- Frontend (HTML + CSS)
- Backend logic
- MySQL connectivity
- Simple logo display

---

### ✅ 2. **Code (Save as `kisan_app.py`)**

```python
from flask import Flask, render_template_string, request, redirect
import mysql.connector

app = Flask(__name__)

# MySQL Connection
conn = mysql.connector.connect(
    host="localhost",
    user="root",
    password="yourpassword",  # Change this!
    database="kisan_app"
)
cursor = conn.cursor()

# HTML Templates
login_page = '''
<!DOCTYPE html>
<html>
<head>
    <title>Maharashtra Kisan App</title>
    <style>
        body {
            background-color: white;
            color: black;
            font-family: Arial;
            text-align: center;
            margin-top: 50px;
        }
        .container {
            width: 300px;
            margin: auto;
        }
        input[type="text"], input[type="email"] {
            width: 100%%;
            padding: 10px;
            margin: 8px 0;
            border: 1px solid #ccc;
            border-radius: 4px;
        }
        button {
            background-color: green;
            color: white;
            padding: 10px;
            width: 100%%;
            border: none;
            border-radius: 4px;
        }
        img {
            width: 100px;
        }
    </style>
</head>
<body>
    <img src="{{ url_for('static', filename='kisan_logo.png') }}" alt="Kisan Logo">
    <h2 style="color: green;">Maharashtra Kisan App</h2>
    <h3 style="color: green;">किसान लॉगिन</h3>
    <form action="/login" method="POST">
        <div class="container">
            <input type="text" name="mobile" placeholder="Mobile Number" required>
            <input type="email" name="email" placeholder="Email ID" required>
            <input type="text" name="name" placeholder="Name" required>
            <input type="text" name="surname" placeholder="Surname" required>
            <input type="text" name="pincode" placeholder="Pin Code" required>
            <button type="submit">लॉगिन</button>
        </div>
    </form>
</body>
</html>
'''

success_page = '''
<!DOCTYPE html>
<html>
<head>
    <title>Welcome</title>
    <style>
        body {
            background-color: white;
            color: black;
            text-align: center;
            margin-top: 100px;
            font-family: Arial;
        }
        img {
            width: 100px;
        }
    </style>
</head>
<body>
    <img src="{{ url_for('static', filename='kisan_logo.png') }}" alt="Kisan Logo">
    <h2 style="color: green;">Maharashtra Kisan App</h2>
    <h1>Welcome, {{ name }}!</h1>
    <p>You are logged in to Maharashtra Kisan App successfully.</p>
</body>
</html>
'''

@app.route('/')
def home():
    return render_template_string(login_page)

@app.route('/login', methods=['POST'])
def login():
    mobile = request.form['mobile']
    email = request.form['email']
    name = request.form['name']
    surname = request.form['surname']
    pincode = request.form['pincode']

    cursor.execute("INSERT INTO farmers (mobile, email, name, surname, pincode) VALUES (%s, %s, %s, %s, %s)",
                   (mobile, email, name, surname, pincode))
    conn.commit()

    return render_template_string(success_page, name=name)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=80)
```

---

### ✅ 3. **Database Setup (Run These Commands on MySQL CLI)**

```sql
CREATE DATABASE kisan_app;
USE kisan_app;

CREATE TABLE farmers (
    id INT AUTO_INCREMENT PRIMARY KEY,
    mobile VARCHAR(15),
    email VARCHAR(50),
    name VARCHAR(50),
    surname VARCHAR(50),
    pincode VARCHAR(10)
);
```

---

### ✅ 4. **Dependencies (Install with pip)**

```bash
sudo yum install python3 -y
sudo pip3 install flask mysql-connector-python
```

---

### ✅ 5. **Setup Instructions on EC2**

1. **Launch EC2 Amazon Linux**
2. **Install MySQL**
   ```bash
   sudo yum install mariadb105-server -y
   sudo systemctl start mariadb
   sudo systemctl enable mariadb
   sudo mysql_secure_installation
   ```
3. **Setup MySQL Table (as shown above)**

4. **Upload `kisan_app.py` and logo image**
   ```bash
   mkdir -p static
   mv kisan_logo.png static/
   ```

5. **Run App**
   ```bash
   sudo python3 kisan_app.py
   ```

---

### ✅ 6. **Final Output**

Open browser:
```
http://<EC2-IP>/
```

You will see the login page with your cartoon-style Kisan logo (also provided in the file uploaded).

------------------------------------------------------------------------------------------------------------------------------------------------------------
------------------------------------------------------------------------------------------------------------------------------------------------------------
 GitHub Actions Architecture

Below is production-grade Continuous Deployment (CD) design for
GitHub Actions → ECR → ECS Fargate → ALB → Auto Scaling

This is exactly how real SaaS companies deploy microservices.

High-level Architecture
------------------------------------------------------------------------------------------------------------------------------------------------------------
GitHub (Push to main)
        │
        ▼
GitHub Actions (CI/CD)
        │
        ▼
Build Docker Image
        │
        ▼
Push to Amazon ECR
        │
        ▼
Update ECS Task Definition
        │
        ▼
Deploy to ECS Fargate Service
        │
        ▼
Application Load Balancer
        │
        ▼
Auto Scaling based on CPU / Requests
------------------------------------------------------------------------------------------------------------------------------------------------------------
Deployment Flow

1) When you push to main branch:

2) GitHub Actions builds Docker image

3) Pushes image to Amazon ECR

4) ECS pulls latest image

5) New containers start

6) ALB routes traffic

7) Old containers are stopped

8) Auto Scaling adjusts based on traffic

9) This gives Zero Downtime Deployment.

📊 6. Production Benefits
Feature	Why it matters
Zero-downtime	ALB + rolling deployment
Auto scaling	Pay only for traffic
No servers	Fargate manages infra
Secure	IAM + private networking
Fast deploy	~2–3 minutes
