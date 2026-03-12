# engce301-final-lab1--66543206012-7---66543206023-4-
### 👨‍🎓 Students
1.นายนฤชิต  ไชยมงคล 66543206012-7 <br>
2.นายจิรภัทร จันทร์เทพ 66543206023-4

---

# 🏗️ System Architecture

```
Browser / Postman
       │
       │ HTTPS :443
       ▼
┌────────────────────────────────────────────┐
│        🛡️ Nginx (API Gateway)             │
│                                            │
│ /api/auth/*  → auth-service:3001           │
│ /api/tasks/* → task-service:3002           │
│ /api/logs/*  → log-service:3003            │
│ /            → frontend                    │
└───────┬───────────────┬───────────────┬────┘
        │               │               │
        ▼               ▼               ▼

┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│ Auth Service │ │ Task Service │ │ Log Service  │
│    :3001     │ │    :3002     │ │    :3003     │
│              │ │              │ │              │
│ Login        │ │ CRUD Tasks   │ │ Save logs    │
│ JWT Token    │ │ JWT Guard    │ │ Query logs   │
└──────┬───────┘ └──────┬───────┘ └──────────────┘
       │                │
       └────────┬───────┘
                ▼
        ┌────────────────┐
        │  PostgreSQL DB │
        │                │
        │ users table    │
        │ tasks table    │
        │ logs table     │
        └────────────────┘
```

---

# 🔐 HTTPS Architecture

ระบบนี้ใช้ **Nginx เป็น API Gateway และ TLS Termination**

ขั้นตอนการทำงานของ HTTPS

1. ผู้ใช้เปิดเว็บไซต์

```
https://localhost
```

2. Browser เชื่อมต่อกับ **Nginx ผ่าน HTTPS (Port 443)**

3. Nginx ใช้ **Self-Signed Certificate**

```
nginx/certs/cert.pem
nginx/certs/key.pem
```

4. Nginx ถอดรหัส HTTPS และ forward request ไปยัง backend services

```
auth-service
task-service
log-service
```

5. Backend services ทำงานผ่าน **HTTP ภายใน Docker network**

---

# 📂 Project Structure

```
final-lab-set1/
│
├── docker-compose.yml
├── README.md
├── .env.example
│
├── nginx/
│   ├── nginx.conf
│   ├── Dockerfile
│   └── certs/
│
├── frontend/
│   ├── Dockerfile
│   ├── index.html
│   └── logs.html
│
├── auth-service/
│   ├── Dockerfile
│   └── src/
│
├── task-service/
│   ├── Dockerfile
│   └── src/
│
├── log-service/
│   ├── Dockerfile
│   └── src/
│
├── db/
│   └── init.sql
│
├── scripts/
│   └── gen-certs.sh
│
└── screenshots/
```

---

# ⚙️ How to Run the Project

### 1️⃣ Generate HTTPS Certificate

```
chmod +x scripts/gen-certs.sh
./scripts/gen-certs.sh
```

จะสร้าง

```
nginx/certs/cert.pem
nginx/certs/key.pem
```

---

### 2️⃣ Create Environment File

```
cp .env.example .env
```

---

### 3️⃣ Start All Services

```
docker compose up --build
```

---

### 4️⃣ Open Browser

```
https://localhost
```

Browser จะเตือน certificate เพราะเป็น **self-signed**

ให้กด

```
Advanced → Continue
```

---

# 🔑 Seed Users (Login Accounts)

| Username | Email                                     | Password  | Role   |
| -------- | ----------------------------------------- | --------- | ------ |
| alice    | [alice@lab.local](mailto:alice@lab.local) | alice123  | member |
| bob      | [bob@lab.local](mailto:bob@lab.local)     | bob456    | member |
| admin    | [admin@lab.local](mailto:admin@lab.local) | adminpass | admin  |

---

# 📋 API Endpoints

### Auth Service

```
POST /api/auth/login
GET  /api/auth/verify
GET  /api/auth/me
GET  /api/auth/health
```

---

### Task Service

```
GET    /api/tasks/
POST   /api/tasks/
PUT    /api/tasks/:id
DELETE /api/tasks/:id
```

ต้องใช้

```
Authorization: Bearer <JWT>
```

---

### Log Service

```
POST /api/logs/internal
GET  /api/logs/
GET  /api/logs/stats
```

---

# 🧪 Example API Test

### Login

```
curl -k -X POST https://localhost/api/auth/login \
-H "Content-Type: application/json" \
-d '{"email":"alice@lab.local","password":"alice123"}'
```

---

### Create Task

```
curl -k -X POST https://localhost/api/tasks/ \
-H "Authorization: Bearer TOKEN" \
-H "Content-Type: application/json" \
-d '{"title":"Test task"}'
```

---

### Get Tasks

```
curl -k https://localhost/api/tasks/ \
-H "Authorization: Bearer TOKEN"
```

---

# 📝 Logging System

ระบบใช้ **Lightweight Logging Service**

services ต่าง ๆ จะส่ง log ผ่าน API

```
POST /api/logs/internal
```

# 📸 Screenshots

1. [01 - Docker Running](screenshots/T1.jpg)
2. [02 - HTTPS in Browser](screenshots/T2.jpg)
3. [03 - Login Success](screenshots/T3.jpg)
4. [04 - Login Fail](screenshots/T4.jpg)
5. [05 - Create Task](screenshots/T5.jpg)
6. [06 - Get Tasks](screenshots/T6.jpg)
7. [07 - Update Task](screenshots/T7.png)
8. [08 - Delete Task](screenshots/T8.png)
9. [09 - No JWT 401 Unauthorized](screenshots/T9.jpg)
10. [10 - API Logs](screenshots/T10.jpg)
11. [11 - Rate Limiting](screenshots/T11.jpg)
12. [12 - Frontend Screenshot](screenshots/T12.jpg)

---

