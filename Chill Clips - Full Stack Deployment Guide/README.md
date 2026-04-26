# Chill Clips - Full Stack Deployment Guide

---

## How I Built and Deployed a Netflix-Style Streaming App from Scratch

**By Oluwasheyi Olayemi Ojelade | Virusinferno Digital Studio**

**Live App:** [https://chill-clips.virusinferno.xyz](https://chill-clips.virusinferno.xyz)
**Frontend Repo:** [https://github.com/virusinferno911/chill-clips.git](https://github.com/virusinferno911/chill-clips.git)
**Backend Repo:** [https://github.com/virusinferno911/chill-clips-backend.git](https://github.com/virusinferno911/chill-clips-backend.git)

---

## What I Built

Chill Clips is a fully custom Netflix-style streaming application deployed on a live production server. It has a React frontend, a Java Spring Boot backend API, a MongoDB database, and user authentication built entirely from scratch, no third-party auth services. Users can browse movies and series, watch trailers without signing in, and must create an account to access full movie playback.

The entire stack runs on a single Contabo VPS running Ubuntu 24.04, served securely over HTTPS through a Dockerized Caddy reverse proxy.

---

## Tech Stack

| Layer | Technology |
| --- | --- |
| Frontend | React 18 + Vite |
| Backend | Java 17 + Spring Boot 3 + Maven |
| Database | MongoDB 8.0 |
| Auth | Custom JWT + BCrypt (no third-party) |
| Web Server | Caddy (Dockerized) + Let's Encrypt SSL |
| Hosting | Contabo VPS — Ubuntu 24.04 LTS |
| Version Control | Git + GitHub |

---

## Overview of the Architecture

Before starting, it helps to understand how the three tiers connect:

```
User's Browser
      |
   Caddy (HTTPS reverse proxy — Docker)
      |
   React Build (static files at /var/www/chill-clips)
      |
   Spring Boot API (port 8080)
      |
   MongoDB (port 27017 — localhost only)
```

The deployment order is critical. MongoDB must be running before the backend starts. The backend must be running before the frontend is configured. This is the same pattern used in enterprise deployments.

---

## PHASE 1: Setting Up the Database (MongoDB)

### Why MongoDB first?

The backend Java application connects to MongoDB on startup. If MongoDB is not running, Spring Boot will crash immediately with a connection error. Always deploy the database first.

### Step 1: SSH into your VPS

```bash
ssh root@your-vps-ip
```

### Step 2: Install MongoDB 8.0

Ubuntu 24.04 requires MongoDB 8.0 specifically. Version 7.0 does not have packages for Ubuntu 24.04 and will throw a 404 error during installation.

```bash
sudo apt update -y
sudo apt install -y gnupg curl

curl -fsSL https://www.mongodb.org/static/pgp/server-8.0.asc | \
  sudo gpg -o /usr/share/keyrings/mongodb-server-8.0.gpg --dearmor

echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-8.0.gpg ] \
https://repo.mongodb.org/apt/ubuntu noble/mongodb-org/8.0 multiverse" | \
sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list

sudo apt update -y
sudo apt install -y mongodb-org
```

### Step 3: Start and enable MongoDB

```bash
sudo systemctl start mongod
sudo systemctl enable mongod
sudo systemctl status mongod
```

You should see `active (running)` in the output. The `enable` command ensures MongoDB restarts automatically if the server ever reboots.

### Step 4: Temporarily allow external connections for data seeding

By default MongoDB only accepts connections from localhost. To import data using MongoDB Compass from your laptop, you need to briefly open it to external connections.

```bash
sudo nano /etc/mongod.conf
```

Find the `bindIp` line and change it:

```yaml
# Before
bindIp: 127.0.0.1

# After
bindIp: 0.0.0.0
```

Save with `Ctrl+X`, then `Y`, then `Enter`. Restart MongoDB:

```bash
sudo systemctl restart mongod
sudo ufw allow 27017
sudo ufw allow OpenSSH
sudo ufw enable
```

### Step 5: Seed your movie data

**Important:** Never paste multi-line commands directly into the mongosh shell because it interprets them differently. Always write to a script file and run it.

```bash
nano /root/seed.js
```

Paste your complete movie data script into this file, then run it:

```bash
mongosh /root/seed.js
```

You should see output confirming how many documents were inserted. Verify it worked:

```bash
mongosh --eval "use('movieDB'); print(db.movies.countDocuments());"
```

Clean up the script file:

```bash
rm /root/seed.js
```

### Step 6: Lock the database back down immediately

This is a critical security step. MongoDB should never remain open to the internet. The backend connects to it locally, not from outside.

```bash
sudo nano /etc/mongod.conf
```

Change `bindIp: 0.0.0.0` back to `bindIp: 127.0.0.1`. Save and restart:

```bash
sudo systemctl restart mongod
sudo ufw delete allow 27017
sudo ufw status
```

Your firewall should now only show OpenSSH. Port 27017 should be gone. The database is now only accessible from within the VPS itself.

**Why this matters:** Within days of leaving MongoDB open, automated bots scan the internet for open MongoDB ports and wipe your data. This happened to my server during the project — a ransomware bot deleted all collections and left a ransom note in its place. Locking it down immediately after seeding prevents this.

---

## PHASE 2: Deploying the Backend (Java Spring Boot)

### Step 1: Install Java JDK 17 and Maven

Java is the runtime that executes the Spring Boot application. Maven is the build tool that compiles the Java source code into a single executable JAR file.

```bash
sudo apt install -y openjdk-17-jdk maven
java -version
mvn -version
```

Both commands should return version numbers without errors.

### Step 2: Clone the backend repository

```bash
git clone https://github.com/virusinferno911/chill-clips-backend.git
cd chill-clips-backend
```

### Step 3: Configure the application

Find the properties file:

```bash
find . -name "application.properties"
```

Open it:

```bash
nano src/main/resources/application.properties
```

Set the MongoDB connection string and port:

```
spring.data.mongodb.uri=mongodb://localhost:27017/movieDB
server.port=8080
jwt.secret=ChillClipsSecretKey2026VirusInfernoDigitalStudio
jwt.expiration=86400000
```

**Common mistakes to avoid here:**

- Never use `mongodb+srv://` - that format is only for MongoDB Atlas clusters. Use plain `mongodb://` for a self-hosted MongoDB.
- Make sure the database name in the URI exactly matches the database you created. A mismatch will cause the app to start but return empty results.
- Remove any `${}` placeholder variables that were left over from the original tutorial code. These cause a fatal crash on startup.

### Step 4: Build the application

Maven reads the `pom.xml` file, downloads all dependencies, compiles the Java code, and packages everything into a single `.jar` file in the `target/` folder.

```bash
mvn clean package -DskipTests
```

The `-DskipTests` flag skips unit tests to speed up the build. The output should end with `BUILD SUCCESS`.

### Step 5: Run the backend in the background

`nohup` keeps the process running after you close the terminal. Without it, the backend stops the moment you disconnect from SSH.

```bash
nohup java -jar target/*.jar > backend.log 2>&1 &
```

Check that it started correctly:

```bash
tail -f backend.log
```

You should see the Spring Boot logo and a line saying `Started ... on port 8080`. Press `Ctrl+C` to stop watching the log (this does not stop the backend).

### Step 6: Open the port and verify

```bash
sudo ufw allow 8080
```

Test it by visiting `http://your-vps-ip:8080/api/v1/movies` in your browser. You should see a JSON array of your movie data.

---

## PHASE 3: Building the Frontend (React + Vite)

### Step 1: Install Node.js 20

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
node -v
npm -v
```

### Step 2: Clone the frontend repository

```bash
git clone https://github.com/virusinferno911/chill-clips.git
cd chill-clips
```

### Step 3: Configure the environment

The React app needs to know where the backend API is. This is done through an environment variable, not hardcoded in the source code.

```bash
cp .env.example .env
nano .env
```

Set:

```
VITE_API_URL=http://your-vps-ip:8080
```

### Step 4: Install dependencies and build for production

```bash
npm install
npm run build
```

`npm install` reads `package.json` and downloads all required packages into a `node_modules` folder. `npm run build` compiles and minifies all the React components into plain HTML, CSS, and JavaScript files inside a `dist/` folder. These are the files that get served to users — not the raw source code.

---

## PHASE 4: Web Server Configuration (The Caddy Pivot)

### Why I did not use Nginx

The original plan was to install Nginx to serve the React build files. After installing it:

```bash
sudo apt install -y nginx
sudo systemctl status nginx
```

Nginx was crashing on startup. To diagnose the problem:

```bash
lsof -i :80
docker ps
```

This revealed that a Dockerized Caddy reverse proxy was already running on port 80 and 443, handling my existing n8n automation service and Evolution API WhatsApp integration. Nginx could not bind to a port already held by another process.

The correct DevOps decision was not to kill the existing production services. Instead, I removed Nginx and integrated the new React app directly into the existing Caddy infrastructure.

```bash
sudo apt remove nginx -y
sudo apt autoremove -y
```

### Step 1: Create a directory for the static files

```bash
sudo mkdir -p /var/www/chill-clips
sudo cp -r ~/chill-clips/dist/* /var/www/chill-clips/
```

### Step 2: Update docker-compose.yml to mount the directory

The Caddy container needs access to the files on the host machine. This is done by adding a volume mapping to the existing docker-compose.yml:

```yaml
volumes:
  - /var/www/chill-clips:/var/www/chill-clips
```

### Step 3: Add a server block to the Caddyfile

```
chill-clips.virusinferno.xyz {
    root * /var/www/chill-clips
    file_server
    try_files {path} /index.html
}
```

The `try_files {path} /index.html` line is critical for React apps. Without it, refreshing any page other than the homepage returns a 404 error because the server looks for a physical file that does not exist. This directive tells Caddy to always fall back to index.html, letting React Router handle the routing client-side.

### Step 4: Recreate the Caddy container

```bash
docker compose up -d --force-recreate caddy
```

Caddy automatically reads the new configuration, connects to Let's Encrypt, and provisions a free SSL certificate. The app becomes available at `https://chill-clips.virusinferno.xyz` within seconds.

---

## PHASE 5: DNS Configuration

In your domain registrar, add an A record:

| Type | Name | Value | TTL |
| --- | --- | --- | --- |
| A | chill-clips | your-vps-ip | Auto |

Wait 5 to 10 minutes for DNS propagation. Once Caddy detects the domain is resolving to your server, it automatically handles the SSL certificate from Let's Encrypt.

---

## PHASE 6: The Custom Authentication System

Rather than using a third-party service like Firebase or Supabase, I built the entire authentication system from scratch using Spring Boot Security and JWT tokens. This is a deliberate choice, it demonstrates actual understanding of how auth works, not just how to use someone else's SDK.

### How it works

1. A user submits their email and password to `POST /api/v1/auth/signup`
2. Spring Boot hashes the password using BCrypt before saving it to MongoDB. BCrypt is a one-way hashing algorithm, even if someone gains access to the database, they cannot recover the original passwords.
3. The server generates a JWT (JSON Web Token), a digitally signed string that contains the user's email and an expiry timestamp.
4. The frontend stores this token and includes it in the `Authorization: Bearer <token>` header on every request to a protected endpoint.
5. Spring Security intercepts every incoming request, extracts the JWT from the header, verifies the signature, and either grants or denies access.

### Protected vs public endpoints

```
GET  /api/v1/movies          → Public (anyone can fetch movie list)
GET  /api/v1/movies/trailers → Public (trailers accessible without login)
GET  /api/v1/movies/full     → Protected (requires valid JWT)
POST /api/v1/auth/signup     → Public
POST /api/v1/auth/signin     → Public
GET  /api/v1/auth/me         → Protected
```

### Frontend auth flow

On the React side, auth state is managed using React Context so the signed-in state is available across all components without prop drilling. When a user clicks Watch Trailer, it plays immediately. When they click Watch Full Movie, the app checks if a JWT exists in local storage. If not, a WatchGate modal appears prompting them to sign in or create an account. Once authenticated, the video player opens with the full movie.

---

## PHASE 7: Pushing to GitHub

```bash
cd chill-clips-backend
git remote remove origin
git remote add origin https://github.com/virusinferno911/chill-clips-backend.git
git branch -M main
git add .
git commit -m "Chill Clips backend — Virusinferno Digital Studio"
git push -u origin main
```

GitHub no longer accepts account passwords for terminal authentication. You need a Personal Access Token (PAT). Generate one at: `GitHub Settings > Developer Settings > Personal Access Tokens > Tokens (classic)`. Tick the `repo` scope and use the generated token as your password.

---

## Key Lessons and Troubleshooting Decisions

**MongoDB 7.0 not available on Ubuntu 24.04 -** The solution was to switch to MongoDB 8.0 which has native Noble (Ubuntu 24.04) package support. Always check the OS compatibility matrix before installing.

**Never paste multi-line code into mongosh directly** - The shell interprets the text character by character and breaks on any markdown symbols or special characters. Always write to a `.js` file and run with `mongosh /path/to/file.js`.

**Spring Boot crashes on startup** - Usually caused by a malformed `application.properties` file. Check for stray `${}` placeholders from tutorial code, wrong database names, or `+srv` in the connection string.

**Nginx port conflict** - Diagnosed using `lsof -i :80` which shows what process is holding a port. `docker ps` revealed the Caddy container. The solution was to integrate into Caddy rather than fight it.

**MongoDB ransomware** - Leaving port 27017 open for even a short period attracted automated bots. Always close the port and revert `bindIp` immediately after seeding. Back up your data regularly.

---

## How to Update the App After Changes

**Frontend update:**

```bash
# On your local machine
npm run build
git add .
git commit -m "your message"
git push origin main

# On the VPS
cd ~/chill-clips
git pull
npm run build
sudo cp -r dist/* /var/www/chill-clips/
```

**Backend update:**

```bash
# On your local machine
git add .
git commit -m "your message"
git push origin main

# On the VPS
cd ~/chill-clips-backend
git pull
mvn clean package -DskipTests
pkill -f "java -jar"
nohup java -jar target/*.jar > backend.log 2>&1 &
```

---

## Final Architecture Summary

```
https://chill-clips.virusinferno.xyz
              |
        Caddy (Docker) + Let's Encrypt SSL
              |
    React Build — /var/www/chill-clips/
              |
    Spring Boot API — localhost:8080
      - /api/v1/auth/signup  (public)
      - /api/v1/auth/signin  (public)
      - /api/v1/movies       (public)
      - /api/v1/movies/full  (JWT required)
              |
    MongoDB 8.0 — localhost:27017
      - movieDB.movies  (12 documents)
      - movieDB.users   (BCrypt hashed passwords)
```

---

This project demonstrates a complete production deployment covering Linux server administration, database management, backend API development, frontend build pipelines, reverse proxy configuration, SSL provisioning, custom JWT authentication, and Git-based deployment workflows.