# SharpPay: Complete Build & Process Documentation

## 1. Project Overview & Architecture

SharpPay is a full-stack Neo-Banking web application deployed as a decoupled system. The frontend communicates with the backend via a secure REST API.

- **Frontend:** React 18 + Vite (Client-side rendered, deployed via Vercel/Netlify/GitHub Pages)
- **Backend:** Java 17 + Spring Boot 3.x (Server-side API, deployed via AWS/Render/Railway)
- **Database:** PostgreSQL hosted on NeonDB.

---

## 2. The Tech Stack & Tooling

### Frontend Tools

- **Vite & React 18:** For lightning-fast development and optimized production builds.
- **React Router v6:** For secure, protected client-side routing (`/dashboard`, `/transfer`, etc.).
- **Tailwind CSS:** For building the custom Rose, Purple, and Gold design system without writing raw CSS.
- **Framer Motion:** For fluid page transitions, modal pop-ups, and micro-interactions.
- **Axios:** For handling HTTP requests and injecting JWT tokens via interceptors.
- **React Hot Toast:** For beautiful, non-intrusive push notifications.
- **html2canvas & jsPDF:** For capturing UI elements and generating downloadable PDF receipts entirely on the client side.

### Backend Tools

- **Spring Boot 3.x (Java 17):** The core framework for the REST API.
- **Spring Security & JWT:** For stateless authentication and route protection.
- **Spring Data JPA & Hibernate:** For ORM (Object-Relational Mapping) to interact with the PostgreSQL database.
- **HikariCP:** For high-performance JDBC connection pooling.
- **AWS SDK (Rekognition & S3):** For AI-powered facial liveness checks and secure ID document storage.
- **JavaMailSender:** For dispatching 6-digit OTPs for registration and PIN recovery.
- **BCrypt:** For irreversible cryptographic hashing of user passwords and 4-digit transaction PINs.

### Development & Testing Environment

- **IntelliJ IDEA:** The primary IDE used for Java/Spring Boot backend development, managing the local server, and deep console debugging (crucial for catching SQL constraint violations).
- **Neon (Serverless Postgres):** The cloud database platform used to host the live PostgreSQL database, chosen for fast connection pooling and scalable serverless architecture.
- **DBeaver:** The universal database management tool used to connect to Neon, visually inspect tables, write SQL queries, and verify that transactions and virtual cards were successfully committing to the database.
- **Postman:** The API platform used to test all Spring Boot REST endpoints, manage JWT authorization headers during testing, and simulate frontend JSON payloads before wiring them up to the React app.

---

## 3. The Step-by-Step Build Process

### Phase 1: Database Design & Backend Foundation

- **Schema Creation:** Designed relational entities for `User`, `Account`, `Transaction`, and `VirtualCard`.
- **Security Setup:** Configured Spring Security to intercept all requests, requiring a valid Bearer JWT for any endpoint outside of `/api/v1/auth`.
- **User Onboarding Flow:** Built endpoints for Registration, OTP Generation, OTP Verification, and Login. Ensured wallets (`Account` entities) were automatically generated with unique 10-digit account numbers upon successful registration.

### Phase 2: API & Database Validation Workflow

- **Database Inspection:** Connected **DBeaver** directly to the **Neon** PostgreSQL instance to visually monitor schema generation (`hibernate.ddl-auto=update`) and ensure relationships between Users, Accounts, and Transactions were mapping correctly.
- **Endpoint Testing:** Before touching the React frontend, every single backend route was rigorously tested using **Postman** to verify payload structures and JWT authorization.
- **Debugging Workflow:** Relied heavily on **IntelliJ IDEA's** live console logging. When 500 Server Errors occurred during integration, the IntelliJ terminal provided the exact SQL constraint violations, allowing for instant backend patches.

### Phase 3: Core Banking Logic (The Ledger)

- **Transaction Engine:** Created the `TransactionService` to handle double-entry accounting (deducting sender, crediting receiver).
- **Database Constraint Management:** Implemented a UUID-based `session_id` system to prevent race conditions. *Engineering Fix: Sliced the 36-character UUIDs down to 30 characters using `.substring(0, 30)` to satisfy strict PostgreSQL `VARCHAR(30)` constraints caught during IntelliJ debugging.*
- **Security Gates:** Enforced a mandatory 4-digit Transaction PIN verification step before executing any financial movement (Transfers, Bills, or Card Creation).

### Phase 4: Advanced Cloud Integrations

- **Virtual Cards:** Built the `VirtualCardService` to randomly generate valid 16-digit PANs and CVVs, deducting a ₦1,000 creation fee and logging it to the transaction ledger.
- **KYC & Liveness:** Integrated AWS Rekognition. Built logic to compare real-time webcam captures against stored S3 identity documents, allowing automated upgrades from Tier 1 User to Tier 3 Merchant.

### Phase 5: Frontend UI/UX Development

- **Design System:** Configured Tailwind `index.css` to handle the custom gradient backgrounds and glassmorphism (backdrop-blur) effects.
- **Component Architecture:** Built reusable components like `<BottomNav />`, `<Modal />`, and the highly interactive `<PinInput />` for the 4-digit security flows.
- **API Integration:** Set up `src/services/api.js` with an Axios instance. Configured interceptors to automatically attach the `sp_token` from `localStorage` to every request, and redirect to `/login` if a 401 Unauthorized error occurs.

### Phase 6: Complex Frontend Flows & Polish

- **Optimistic UI:** Programmed the Virtual Card page to instantly grey out and mask sensitive PAN/CVV data the moment a user clicks "Freeze", providing immediate visual feedback before the backend confirms the state change.
- **Dynamic Liveness Triggers:** Built logic into the Transfer Page to intercept the "Proceed to Pay" button. If the transfer amount exceeds the user's custom `livenessTransferLimit`, it bypasses the PIN modal and triggers the `<LivenessCamera />` component instead.
- **PDF Generation:** Attached a hidden `ref` to the Transaction Receipt UI, allowing `html2canvas` to screenshot the div and `jsPDF` to package it into a downloadable document, completely bypassing the need for a backend PDF generator.

### Phase 7: Integration, Testing, & Deployment

- **CORS Configuration:** Whitelisted the production frontend domain (`https://app.virusinferno.xyz`) in the Spring Boot `SecurityConfig.java` to prevent cross-origin resource sharing blocks.
- **Environment Variables:** Swapped the local development URL (`http://localhost:8080`) to the live production backend URL (`https://sharp-pay.virusinferno.xyz/api/v1`) inside the Axios configuration.
- **Production Push:** Committed the final polished code to GitHub, triggering CI/CD pipelines for live deployment.

### Phase 8: Automated CI/CD Deployment Pipeline

To eliminate manual server management, we architected a true zero-touch deployment pipeline. Pushing code to the `main` branch on GitHub automatically rebuilds, containers, and deploys the application to the live VPS.

**1. Backend CI/CD Workflow (GitHub Actions + AWS ECR):**

- **Dockerization:** We containerized the Spring Boot backend using a `Dockerfile`.
- **AWS ECR:** Created a private Elastic Container Registry on AWS to hold our built Docker images.
- **The GitHub Action:** We wrote a `.github/workflows/deploy.yml` file that triggers on every push to `main`. This action:
    1. Injects the secure `application.properties` from GitHub Secrets.
    2. Logs into AWS ECR.
    3. Builds the new Docker image and pushes it to the ECR repository (`sharppay-backend:latest`).
    4. Uses the `appleboy/ssh-action` to securely connect to our Ubuntu VPS and trigger the deployment script.

**2. The VPS Deployment Script:**
Instead of manually pulling code, the GitHub Action executes a script (`/root/deploy-sharppay.sh`) residing on the VPS. This script automates the container swap with zero downtime:

Bash

`#!/bin/bash
set -e
# Log into AWS ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin YOUR_ECR_URI
# Pull the latest image built by GitHub Actions
docker pull YOUR_ECR_URI:latest
# Stop and remove the old container
docker stop sharppay-api || true
docker rm sharppay-api || true
# Spin up the new container on port 8080
docker run -d --name sharppay-api -p 8080:8080 --restart always YOUR_ECR_URI:latest
echo "Deployed Successfully"`

**3. Frontend CI/CD Workflow:**
Similarly, the React frontend relies on an automated pipeline. Pushing to GitHub triggers a workflow that runs `npm run build` to generate the static `dist` folder. The pipeline then uses SCP/Rsync (or a platform like Vercel/Netlify) to instantly push the compiled assets to the live frontend domain (`app.virusinferno.xyz`), ensuring frontend and backend deploy seamlessly together.