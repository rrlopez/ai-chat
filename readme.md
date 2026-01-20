# Full-Stack Automation & Chat (Rocket.Chat + n8n + Nginx)

This repository contains a production-ready Docker Compose stack featuring **Rocket.Chat** for communication, **n8n** for workflow automation, and **NGINX** as a reverse proxy, all securely tunneled through **ngrok**.

---

## 📋 Prerequisites

* **Docker** and **Docker Compose** installed.
* **ngrok Account**: You must have a free or paid account to obtain an `AUTHTOKEN`.
* **Static Domain**: This setup is configured to use your specific ngrok domain: `marx-hydromechanical-counterproductively.ngrok-free.dev`.

---

## 🚀 Step 1: Initialize External Volumes

Your configuration uses **external volumes** to protect data. You must create them manually before running the stack, or Docker will throw an error:

```bash
docker volume create mongodb_data
docker volume create rocketchat_uploads
docker volume create n8n_db_data
docker volume create n8n_app_data

```

---

## 🚀 Step 2: Create Configuration Files

Create the following files in your project directory:

### 1. `.env.ngrok`

```env
NGROK_AUTHTOKEN=your_ngrok_token_here

```

### 2. `.env.rocketchat`

```env
PORT=3000
ROOT_URL=[https://marx-hydromechanical-counterproductively.ngrok-free.dev](https://marx-hydromechanical-counterproductively.ngrok-free.dev)
MONGO_URL=mongodb://mongodb:27017/rocketchat?replicaSet=rs0
MONGO_OPLOG_URL=mongodb://mongodb:27017/local?replicaSet=rs0

```

### 3. `.env.n8n`

```env
N8N_HOST=marx-hydromechanical-counterproductively.ngrok-free.dev
N8N_PORT=5678
N8N_PROTOCOL=https
NODE_ENV=production
WEBHOOK_URL=[https://marx-hydromechanical-counterproductively.ngrok-free.dev/n8n/](https://marx-hydromechanical-counterproductively.ngrok-free.dev/n8n/)
DB_TYPE=postgresdb
DB_POSTGRESDB_HOST=postgres
DB_POSTGRESDB_PORT=5432
DB_POSTGRESDB_DATABASE=n8n
DB_POSTGRESDB_USER=n8n_admin
DB_POSTGRESDB_PASSWORD=n8n_secure_password

```

### 4. `.env.postgres`

```env
POSTGRES_USER=n8n_admin
POSTGRES_PASSWORD=n8n_secure_password
POSTGRES_DB=n8n

```

---

## 🚀 Step 3: Deployment

1. **Start the containers:**
```bash
docker-compose up -d

```


2. **Wait for Initialization:**
* The `mongodb` container will start.
* The `mongo-init-replica` will run once to configure the Replica Set (required for Rocket.Chat).
* Once healthy, Rocket.Chat and n8n will become available.



---

## 🔗 Accessing the Services

| Service | Access URL |
| --- | --- |
| **Rocket.Chat** | [https://marx-hydromechanical-counterproductively.ngrok-free.dev/](https://www.google.com/search?q=https://marx-hydromechanical-counterproductively.ngrok-free.dev/) |
| **n8n** | [https://marx-hydromechanical-counterproductively.ngrok-free.dev/n8n/](https://www.google.com/url?sa=E&source=gmail&q=https://marx-hydromechanical-counterproductively.ngrok-free.dev/n8n/) |

---

## 🛠 Project Structure Detail

### NGINX Routing Logic

* **`/n8n/`**: Proxies to the n8n container. The trailing slash in `proxy_pass http://n8n:5678/;` is critical to strip the `/n8n/` prefix before sending it to the app.
* **`/`**: Proxies everything else to Rocket.Chat. It includes WebSocket support (`Upgrade` headers) required for real-time messaging.

### Database Persistence

* **MongoDB**: Uses a replica set (`rs0`) for Rocket.Chat's Oplog tailing, which enables high-performance updates.
* **PostgreSQL**: Used by n8n to store workflow data and execution history.

---

## 🧹 Maintenance

* **Check Logs:** `docker-compose logs -f`
* **Stop All:** `docker-compose down` (Note: Volumes will be preserved).
* **Reset Database:** To completely wipe data, you must delete the external volumes: `docker volume rm [volume_name]`.
