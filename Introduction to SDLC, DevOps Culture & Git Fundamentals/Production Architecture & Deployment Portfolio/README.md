# Production Architecture & Deployment Portfolio

> **Project:** Chill Clips Full-Stack Cloud Deployment
**Lead Engineer:** Oluwasheyi Ojelade | Virusinferno Digital Studio
**Date:** March 2026
**Status:** 🟢 Live & Secured
> 

> 🎥 **[VIDEO PLACEHOLDER: A 30-to-60-second screen recording clicking through the live `https://chill-clips.virusinferno.xyz` website, showing the movies loading, followed by a quick switch to the Contabo terminal showing the backend logs running smoothly.]**
> 

### 🏗️ Architecture Overview

> 📸 **[IMAGE PLACEHOLDER: A flowchart or diagram showing the traffic flow. (e.g., User -> Caddy Reverse Proxy -> React Frontend / Spring Boot API -> MongoDB). You can draw this in a tool like Excalidraw or draw.io, or use an architecture diagram generator.]**
> 

A production-grade, single-node deployment of a React movie application consuming a Java Spring Boot REST API, backed by MongoDB. Traffic is routed securely via a containerized Caddy reverse proxy handling automated SSL provisioning alongside existing self-hosted applications (n8n, Evolution API).

### 🛠️ Technology Stack

- **Infrastructure:** Contabo VPS (Linux/Ubuntu)
- **Database:** MongoDB (Secured via localhost binding)
- **Backend:** Java 17, Spring Boot 3, Maven
- **Frontend:** React.js, Vite, Node.js
- **Web Server / Proxy:** Caddy (Dockerized)
- **CI/CD & Version Control:** Git, GitHub

### 🔗 Live URLs & Repositories

- **Production Application:** `https://chill-clips.virusinferno.xyz`
- **Backend API Endpoint:** `http://173.249.41.190:8080/api/v1/movies`
- **Frontend Repository:** `https://github.com/virusinferno911/chill-clips`
- **Backend Repository:** `https://github.com/virusinferno911/chill-clips-backend`

---

## ⚙️ Part 2: The Step-by-Step Deployment Retrospective

This is the exact sequence of events, including the real-world troubleshooting we executed to bring this project from raw code to a live production environment.

### Phase 1: Database Provisioning & Security

1. **MongoDB Installation:** Installed MongoDB directly on the Contabo server.
2. **Firewall Management:** Used `ufw` to restrict port `27017` to prevent unauthorized external access.
3. **The "Vault Door" Data Injection:** * Temporarily modified `/etc/mongod.conf` to bind the IP to `0.0.0.0` (allowing external connections).
    - Opened firewall port `27017` just long enough to connect via MongoDB Compass from a local Windows machine.
    - Imported the `movies.json` data into a database named `moviesDB`.
    - Immediately reverted the bind IP back to `127.0.0.1` and closed the firewall to lock the database down to local-traffic only.

> 📸 **[IMAGE PLACEHOLDER: Screenshot of MongoDB Compass connected to the `moviesDB` database, showing the imported movie documents.]**
> 

### Phase 2: Backend Engine Deployment (Java Spring Boot)

1. **Configuration Debugging:** * Fixed a fatal application crash caused by a stray `${}` variable syntax in the `application.properties` file.
    - Corrected a database mapping mismatch where the Java code was looking for `movieDB` but the database was actually named `moviesDB`.
    - Set the Spring Boot configuration to run on port `8080`.
2. **Compilation:** Used Maven (`mvn clean package -DskipTests`) to compile the raw Java code into an executable `.jar` file.
3. **Background Execution:** Ran the engine continuously in the background using `nohup java -jar target/*.jar > backend.log 2>&1 &`, ensuring it wouldn't die when the terminal closed.

> 📸 **[IMAGE PLACEHOLDER: Screenshot of your terminal showing the Spring Boot logo and the "Started Application in ... seconds" message.]**
> 

### Phase 3: Frontend Build (React/Vite)

1. **Environment Setup:** Installed Node.js v20 and the `npm` package manager directly onto the server.
2. **Repository Management:** Pulled the custom frontend UI code to the server environment.
3. **API Linking:** Configured the `.env` file (`VITE_API_URL=http://173.249.41.190:8080`) so the React app knew exactly where to fetch the movie data.
4. **Production Build:** Ran `npm install` followed by `npm run build` to compress the React code into highly optimized static files inside a `dist/` directory.

> 📸 **[IMAGE PLACEHOLDER: Screenshot of your terminal showing the Vite "✓ built in ...s" success message and the compiled file sizes.]**
> 

### Phase 4: The Web Server Pivot (Nginx to Caddy)

1. **The Port 80 Conflict:** Initially attempted to install Nginx to serve the frontend files. However, `systemctl status nginx` revealed a fatal crash.
2. **Diagnosis:** Used `lsof -i :80` and `docker ps` to discover that a containerized Caddy reverse proxy (handling existing n8n and Evolution API workflows) was already holding Port 80.
3. **The DevOps Pivot:** Instead of killing the existing production services, we removed Nginx and integrated the new React app directly into the existing Caddy infrastructure to maximize server resources.

### Phase 5: Routing & SSL (The Grand Finale)

1. **File Placement:** Created a permanent directory (`/var/www/chill-clips`) and securely copied the compiled React `dist/` files into it.
2. **Docker Volume Mapping:** Edited the existing `docker-compose.yml` file to punch a hole through to the Caddy container, mapping `/var/www/chill-clips:/var/www/chill-clips`.
3. **Reverse Proxy Configuration:** Appended a new block to the `Caddyfile` mapping the `chill-clips.virusinferno.xyz` subdomain to the static file directory, and configured routing for a Single Page Application (`try_files {path} /index.html`).
4. **Recreation & SSL:** Ran `docker compose up -d --force-recreate caddy`. Caddy instantly read the new configuration, reached out to Let's Encrypt, generated a free SSL certificate, and served the site live over HTTPS.

> **The live Chill Clips website showing the movie posters**
> 
> 
> ![image.png](images/mage.png)
> 

### Phase 6: Version Control Immortalization

1. **Repository Management:** Disconnected the local backend folder from the original tutorial's Git origin.
2. **Authentication Security:** Generated a GitHub Personal Access Token (PAT) with `repo` scopes to securely push code from the Linux terminal.
3. **Custom Branding:** Wrote new, professional `README.md` files for both repositories, stamping them with the Virusinferno Digital Studio brand.
4. **Push to Cloud:** Successfully pushed the live, working code directly from the Contabo server up to GitHub to finalize the portfolio.

> **Screenshot of GitHub repository showing my profile, the "Virusinferno Digital Studio" README, and my initial commit messages.**
> 
> 
> ![image.png](images/mage%201.png)
>