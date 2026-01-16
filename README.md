# UAV Flight Permission & Management System

> **Live Demo:** [https://aerodps.me](https://aerodps.me) (https://13.49.238.216/)

## 📚 API Documentation

API documentation is provided with Swagger UI.

**Access:** `https://aerodps.me/api-docs` (Production) or `http://localhost:5000/api-docs` (Development)

![Status](https://img.shields.io/badge/Status-Live-success)
![Tech Stack](https://img.shields.io/badge/MERN-Full%20Stack-blueviolet)
![Deployment](https://img.shields.io/badge/AWS-EC2-orange)

## 📌 Project Overview

**Student:** Yasir Eren Çelik (21833039)  
**Course:** GMT 458 – Web GIS Final Project  
**Instructor:** Assoc. Prof. Dr. Berk Anbaroğlu

This project is a **NoSQL-based** and **Cloud-architected Web GIS (Geographic Information System)** application developed for urban Unmanned Aerial Vehicle (UAV) operations. The system enables pilots to request flights on a map and manages these requests by analyzing spatial conflicts with **NOTAM (Restricted Zone)** data.

---

## 📖 Table of Contents

- [Project Overview](#-project-overview)
- [Assignment Requirements and Met Criteria](#-assignment-requirements-and-met-criteria)
- [System Architecture](#-system-architecture)
  - [Why NoSQL and MongoDB?](#why-nosql-and-mongodb)
  - [Spatial Analysis (Client-Side Validation)](#spatial-analysis-client-side-validation)
- [User Roles and Authorization](#-user-roles-and-authorization)
  - [👨‍✈️ Operator (Pilot)](#-operator-pilot)
  - [👮‍♂️ Controller](#-controller)
  - [👑 Admin](#-admin)
- [Security and Authentication](#-security-and-authentication)
  - [Email Verification System](#email-verification-system)
  - [Two-Factor Authentication (2FA)](#two-factor-authentication-2fa)
  - [Password Reset](#password-reset)
  - [JWT Token-Based Authorization](#jwt-token-based-authorization)
- [Email Notification System](#-email-notification-system)
- [Map Features](#-map-features)
- [Tech Stack](#-tech-stack)
- [Installation & Setup](#-installation--setup)
  - [Requirements](#requirements)
  - [Backend Setup](#backend-setup)
  - [Frontend Setup](#frontend-setup)
  - [Environment Variables (.env)](#environment-variables-env)
- [Cloud Deployment (AWS)](#-cloud-deployment-aws)
- [API Documentation](#-api-documentation)
- [Project Structure](#-project-structure)

---

## ✅ Assignment Requirements

This project successfully meets the following requirements specified in the **GMT 458 Final Assignment**:

- [x] **Managing Different User Types (%20):** The system includes 3 different user roles: **Operator (Pilot)**, **Controller**, and **Admin**, with a role-based authorization mechanism (RBAC).
- [x] **NoSQL Database (%25):** **MongoDB Atlas** is used for heterogeneous flight data and GeoJSON geometries.
- [x] **API Development (%25):** RESTful API endpoints (GET, POST, PUT, DELETE) have been developed for both spatial and non-spatial data.
- [x] **Hosting on AWS (%20):** The project is deployed live on AWS EC2 cloud server.
- [x] **Authentication (%15):** JWT (JSON Web Token) based secure login and registration system is integrated.
- [x] **CRUD Operations (%15):** Users can create, update, and delete flight zones on the map.

---

## 🏗 System Architecture

### Why NoSQL and MongoDB?

In this project, **MongoDB** with **NoSQL** architecture is preferred over Relational Database Management System (RDBMS). The main reasons are:

1. **GeoJSON Support:** MongoDB provides native support for geographic data (Point, Line, Polygon) and is optimized for spatial queries.
2. **Flexible Schema (Schema-less):** A JSON-based flexible structure is used to store variable attributes such as different drone models and flight parameters.
3. **Performance:** Spatial queries such as "finding flights within a specific area" are executed with high performance using the `2dsphere` index.
4. **Scalability:** MongoDB Atlas offers cloud-based management and automatic scaling capabilities.

### Spatial Analysis (Client-Side Validation)

The system uses the **Turf.js** library to reduce server load and enhance user experience.

* **Scenario:** When a pilot draws a flight area on the map, the system instantly checks whether this drawing intersects with active **NOTAM (Restricted Zone)** polygons using `booleanIntersects`.
* **Result:** If there is an intersection, the drawing is immediately canceled and the user is warned. Invalid data is prevented before being sent to the server.
* **Advantages:**
  - Instant feedback
  - Reduced server load
  - Faster user experience
  - Potential for offline operation

---

## 👥 User Roles and Authorization

### 👨‍✈️ Operator (Pilot)

**Permissions:**
- Drawing flight zones on the map (Polygon, LineString, Point)
- Creating and submitting flight requests
- Viewing own flight records
- Viewing active NOTAM (restricted zone) areas on the map
- Editing flight requests (only those with `pending` status)
- Deleting flight requests
- Tracking flight approval/rejection status

**Registration and Login Process:**
1. Account creation via normal registration form
2. Email verification (6-digit OTP code valid for 10 minutes)
3. Ability to log in to the system after email verification
4. Normal password login for subsequent logins

**Email Notifications:**
- Notification when flight request is approved
- Notification when flight request is rejected (with rejection reason)

### 👮‍♂️ Controller

**Permissions:**
- Viewing all operators
- Viewing NOTAM areas determined by admin
- Viewing flight requests from operators
- Approving or rejecting flight requests
- Viewing all flight statuses (pending, approved, rejected)
- Region-based filtering (assigned_region)

**Role Assignment:**
- Can be manually assigned by admin
- Can register with invite code (e.g., format: `SHGM-ANK-CONT`)
- Controllers registered with invite code are automatically approved

**Login Process:**
- 2FA (Two-Factor Authentication) is required for every login
- A 6-digit OTP code valid for 10 minutes is sent to email address
- Can log in to the system after OTP verification

**Email Notifications:**
- Receives notification when a new flight request arrives
- Notification includes pilot information, drone model, and request ID

### 👑 Admin

**Permissions:**
- Drawing and deleting NOTAM (restricted zone) on the map
- Editing NOTAM areas (title, reason, date range, active/inactive status)
- Viewing and managing all users
- Changing user roles (operator ↔ controller)
- Approving/rejecting users
- Deleting users
- Viewing system statistics

**Role Assignment:**
- Can only be created by the system
- Can register with invite code (format: `SHGM-ADMIN-REQ`)
- Admin candidates registered with invite code await manual approval

**Login Process:**
- 2FA (Two-Factor Authentication) is required for every login
- A 6-digit OTP code valid for 10 minutes is sent to email address
- Can log in to the system after OTP verification

**NOTAM Management:**
- Can create restricted zones in Polygon or Circle (circle) shape
- Can assign title, reason (Military Exercise, VIP Movement, Emergency Response, Weather Hazard, Other), and date range to NOTAMs
- Can activate/deactivate NOTAMs

---

## 🔐 Security and Authentication

### Email Verification System

**Technology:** Nodemailer + Gmail SMTP / Custom SMTP

**Process:**
1. During new user registration (for Operator role):
   - System generates a 6-digit random OTP (One-Time Password) code
   - OTP code is sent to user's email address
   - OTP code is valid for 10 minutes
   - User verifies account by entering the code from email into the system
   - After verification, `is_verified` field is marked as `true`

**Email Template:**
- Professional HTML email template is used
- OTP code is displayed prominently
- 10-minute validity period warning is included
- Design consistent with brand identity

### Two-Factor Authentication (2FA)

**For Whom:**
- **Controller:** Required for every login
- **Admin:** Required for every login
- **Operator:** Only email verification after initial registration

**Process:**
1. User logs in with email and password
2. If password is correct, system generates a 6-digit OTP code
3. OTP code is sent to user's email address
4. OTP code is valid for 10 minutes
5. User completes login by entering OTP code
6. JWT token is created after OTP verification

**Security Features:**
- OTP codes are stored hashed in the database
- OTP codes are single-use (deleted after use)
- Expired OTP codes are invalid
- Each OTP code is valid only for one user

### Password Reset

**Technology:** Secure token generation with Crypto (Node.js)

**Process:**
1. User goes to "Forgot Password" page
2. Enters email address
3. System creates a secure reset token (32 byte random + SHA256 hash)
4. Reset token is valid for 1 hour
5. User receives password reset link via email
6. User clicking the link sets a new password
7. Reset token becomes invalid after password change

**Security Features:**
- Reset tokens are stored hashed
- Tokens are single-use
- 1-hour validity period
- Secure HTTPS link in email

### JWT Token-Based Authorization

**Technology:** jsonwebtoken (Node.js)

**Features:**
- Stateless authentication (no session stored on server)
- Token duration: 1 day
- User ID is stored in token
- Token is validated on every API request
- Route protection with middleware

**Token Structure:**
```json
{
  "id": "user_mongodb_id",
  "iat": "issued_at_timestamp",
  "exp": "expiration_timestamp"
}
```

---

## 📧 Email Notification System

The system automatically sends email notifications to users for important events.

### Email Sending Technology

**Library:** Nodemailer  
**SMTP Service:** Gmail SMTP or custom SMTP server

**Configuration:**
- For Gmail: App Password is used (for Gmail accounts with 2FA enabled)
- For custom SMTP: Host, port, secure settings can be configured

### Email Notification Scenarios

#### 1. New Flight Request Notification (to Controller)
- **Triggered When:** Operator creates a new flight request
- **Recipient:** All active and approved Controllers
- **Content:**
  - Pilot name
  - Drone model
  - Request date
  - Request ID
  - Redirect message to dashboard

#### 2. Flight Approval Notification (to Operator)
- **Triggered When:** Controller approves flight request
- **Recipient:** Operator who created the flight request
- **Content:**
  - Approval status
  - Flight details (drone model, altitude, duration)
  - Request ID
  - Safety warnings

#### 3. Flight Rejection Notification (to Operator)
- **Triggered When:** Controller rejects flight request
- **Recipient:** Operator who created the flight request
- **Content:**
  - Rejection status
  - Rejection reason
  - Flight details
  - Request ID
  - Suggestion to create new request

#### 4. Email Verification Code (All Roles)
- **Triggered When:**
  - New registration (Operator)
  - Every login (Controller, Admin)
- **Recipient:** Relevant user
- **Content:**
  - 6-digit OTP code
  - 10-minute validity period warning

#### 5. Password Reset Link (All Roles)
- **Triggered When:** User fills out "Forgot Password" form
- **Recipient:** Relevant user
- **Content:**
  - Password reset link (valid for 1 hour)
  - Security warnings

### Email Templates

All emails use professional HTML templates:
- Design consistent with brand identity
- Responsive (mobile-friendly) structure
- Clear and understandable content
- Security warnings
- Automatic footer (copyright information)

---

## 🗺 Map Features

### Map Engine

**Technology:** Leaflet.js + React-Leaflet

**Features:**
- Interactive map display
- Zoom in/out controls
- Drawing tools on map (Leaflet Draw)
- Detailed information display with popups
- Location marking with markers

### Map Layers (Tile Layers)

The system offers different map layers according to user preference:

1. **Normal Map (OpenStreetMap)**
   - Default map layer
   - Street map view
   - Light-colored theme

2. **Satellite Map**
   - Google Satellite or similar satellite imagery
   - Real-world images
   - High-resolution images

3. **Dark Map (Dark Mode)**
   - Dark theme map layer
   - Optimized for night vision
   - Usage in low-light conditions

**Layer Switching:**
- Users can change layers from the control panel on the map
- Selection is saved in localStorage (remembered on next login)

### Theme System (Dark/Light Mode)

**Technology:** React Context API + Tailwind CSS Dark Mode

**Features:**
- **Dark Mode:** Dark background, light-colored text
- **Light Mode:** Light background, dark-colored text
- Automatic detection based on system preference (prefers-color-scheme)
- Manual theme toggle button
- Theme preference saved in localStorage

**Usage:**
- Theme toggle button in header
- All pages are affected by theme change
- Map layers work in harmony with theme

### Drawing Tools

With **Leaflet Draw** library:

1. **Polygon Drawing:**
   - Drawing flight zone in polygon shape
   - Free drawing
   - Editing and deletion

2. **LineString Drawing:**
   - Drawing flight route in line shape
   - Point-to-point drawing
   - Editing and deletion

3. **Point (Point) Marking:**
   - Single point location marking
   - Display with marker

### Spatial Validation

Client-side validation with **Turf.js**:

- Real-time NOTAM check during drawing
- Intersection detection (`booleanIntersects`)
- Instant feedback
- Prevention of invalid drawings

---

## 🛠 Tech Stack

### Frontend

| Technology | Version | Description |
| :--- | :--- | :--- |
| **React.js** | 18.3.1 | User interface framework |
| **Vite** | 6.0.7 | Fast build tool and dev server |
| **React Router** | 6.22.0 | Client-side routing |
| **Tailwind CSS** | 3.4.17 | Utility-first CSS framework |
| **Leaflet** | 1.9.4 | Map visualization library |
| **React-Leaflet** | 4.2.1 | Leaflet wrapper for React |
| **Leaflet Draw** | 1.0.4 | Drawing tools on map |
| **Turf.js** | 7.3.2 | Spatial analysis library |
| **Axios** | 1.13.2 | HTTP client |
| **React Toastify** | 11.0.5 | Notification/toast messages |
| **Framer Motion** | 12.26.2 | Animation library |

### Backend

| Technology | Version | Description |
| :--- | :--- | :--- |
| **Node.js** | - | JavaScript runtime |
| **Express.js** | 5.2.1 | Web framework |
| **MongoDB** | - | NoSQL database |
| **Mongoose** | 9.1.1 | MongoDB object modeling |
| **JWT** | 9.0.3 | JSON Web Token authentication |
| **Bcryptjs** | 3.0.3 | Password hashing |
| **Nodemailer** | 7.0.12 | Email sending |
| **CORS** | 2.8.5 | Cross-Origin Resource Sharing |
| **Swagger** | 6.2.8 | API documentation |

### DevOps & Deployment

| Technology | Description |
| :--- | :--- |
| **AWS EC2** | Cloud server (Ubuntu 24.04 LTS) |
| **Nginx** | Reverse proxy and web server |
| **PM2** | Node.js process manager |
| **MongoDB Atlas** | Cloud-based MongoDB service |
| **Git** | Version control |

---

## ⚙️ Installation & Setup

### Requirements

- **Node.js:** v18.x or higher
- **npm:** v9.x or higher
- **MongoDB:** MongoDB Atlas account or local MongoDB installation
- **Email Service:** Gmail account (App Password) or custom SMTP server

### Backend Setup

1. **Navigate to project directory:**
```bash
cd backend
```

2. **Install dependencies:**
```bash
npm install
```

3. **Configure environment variables:**
Create `.env` file (details below)

4. **Start the server:**
```bash
# Development mode
npm run dev

# Production mode
npm start
```

Backend runs on `http://localhost:5000` by default.

### Frontend Setup

1. **Navigate to project directory:**
```bash
cd frontend
```

2. **Install dependencies:**
```bash
npm install
```

3. **Start development server:**
```bash
npm run dev
```

Frontend runs on `http://localhost:5173` by default.

4. **Production build:**
```bash
npm run build
```

Built files are created in `dist/` folder.


---

## ☁ Cloud Deployment (AWS)

The project runs in production environment on **Amazon Web Services (AWS)**.

### Server Configuration

- **Server:** AWS EC2 (Ubuntu 24.04 LTS)
- **Instance Type:** t3.micro or higher
- **Web Server:** Nginx (Reverse Proxy)
- **Process Manager:** PM2
- **Domain:** aerodps.me (HTTPS with SSL certificate)

### Deployment Steps

1. **Create EC2 Instance:**
   - Create Ubuntu 24.04 LTS instance from AWS Console
   - Open ports 80 (HTTP) and 443 (HTTPS) in Security Group
   - Create and save SSH key pair

2. **Connect to Server:**
```bash
ssh -i your-key.pem ubuntu@your-ec2-ip
```

3. **Install Required Software:**
```bash
# Node.js and npm installation
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# Nginx installation
sudo apt-get update
sudo apt-get install -y nginx

# PM2 installation
sudo npm install -g pm2
```

4. **Transfer Project to Server:**
```bash
# Clone with Git
git clone your-repo-url
cd webtab

# Install backend dependencies
cd backend
npm install --production

# Frontend build
cd ../frontend
npm install
npm run build
```

5. **Nginx Configuration:**
```nginx
# /etc/nginx/sites-available/aerodps.me
server {
    listen 80;
    server_name aerodps.me www.aerodps.me;

    # Frontend (React build)
    location / {
        root /var/www/aerodps.me/frontend/dist;
        try_files $uri $uri/ /index.html;
    }

    # Backend API
    location /api {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

6. **Start Backend with PM2:**
```bash
cd backend
pm2 start ecosystem.config.js --env production
pm2 save
pm2 startup
```

7. **SSL Certificate (Let's Encrypt):**
```bash
sudo apt-get install certbot python3-certbot-nginx
sudo certbot --nginx -d aerodps.me -d www.aerodps.me
```

### PM2 Ecosystem Configuration

`backend/ecosystem.config.js` file:

```javascript
module.exports = {
  apps: [{
    name: 'dps-backend',
    script: './server.js',
    instances: 1,
    exec_mode: 'fork',
    env: {
      NODE_ENV: 'production',
      PORT: 5000
    }
  }]
};
```

---

## 📚 API Documentation

API documentation is provided with Swagger UI.

**Access:** `https://aerodps.me/api-docs` (Production) or `http://localhost:5000/api-docs` (Development)

### Main API Endpoints

#### Authentication (`/api/auth`)
- `POST /api/auth/register` - User registration
- `POST /api/auth/login` - User login
- `POST /api/auth/verify-otp` - OTP verification
- `POST /api/auth/forgot-password` - Password reset request
- `POST /api/auth/reset-password` - Password reset
- `GET /api/auth/me` - Current user information

#### Flights (`/api/flights`)
- `POST /api/flights` - Create new flight request (Operator)
- `GET /api/flights` - Get all flight requests (Controller/Admin)
- `GET /api/flights/my` - Get own flight requests (Operator)
- `PUT /api/flights/:id` - Update flight request (Operator, only pending)
- `PUT /api/flights/:id/status` - Update flight status (Controller)
- `DELETE /api/flights/:id` - Delete flight request (Operator)

#### NOTAMs (`/api/notams`)
- `POST /api/notams` - Create new NOTAM (Admin)
- `GET /api/notams` - Get active NOTAMs
- `GET /api/notams?all=true` - Get all NOTAMs (Admin)
- `PUT /api/notams/:id` - Update NOTAM (Admin)
- `DELETE /api/notams/:id` - Delete NOTAM (Admin)

#### Admin (`/api/admin`)
- `GET /api/admin/users` - Get all users
- `GET /api/admin/users/:id` - User details
- `PUT /api/admin/users/:id` - Update user (role, approval status)
- `PUT /api/admin/users/:id/approve` - Approve user
- `DELETE /api/admin/users/:id` - Delete user

---

## 📁 Project Structure

```
webtab/
├── backend/                 # Backend API
│   ├── config/             # Configuration files
│   │   └── swagger.js      # Swagger API documentation
│   ├── controllers/        # Route controllers
│   │   ├── adminController.js
│   │   ├── authController.js
│   │   ├── droneController.js
│   │   ├── flightController.js
│   │   └── notamController.js
│   ├── middleware/         # Express middlewares
│   │   ├── auth.js         # JWT authentication
│   │   └── checkRole.js     # Role-based authorization
│   ├── models/             # MongoDB models
│   │   ├── User.js
│   │   ├── FlightRequest.js
│   │   ├── Notam.js
│   │   └── Drone.js
│   ├── routes/             # API route definitions
│   │   ├── adminRoutes.js
│   │   ├── authRoutes.js
│   │   ├── droneRoutes.js
│   │   ├── flightRoutes.js
│   │   ├── notamRoutes.js
│   │   └── userRoutes.js
│   ├── utils/              # Helper functions
│   │   ├── sendEmail.js    # Email sending service
│   │   └── emailTemplates.js # Email templates
│   ├── server.js           # Main server file
│   ├── ecosystem.config.js # PM2 configuration
│   └── package.json
│
├── frontend/               # Frontend React application
│   ├── public/            # Static files
│   ├── src/
│   │   ├── components/    # React components
│   │   │   ├── Common/   # Common components
│   │   │   ├── Dashboard/ # Dashboard components
│   │   │   ├── Layout/   # Layout components
│   │   │   └── Map/      # Map components
│   │   ├── context/      # React Context API
│   │   │   ├── AuthContext.jsx
│   │   │   └── ThemeContext.jsx
│   │   ├── pages/        # Page components
│   │   │   ├── LandingPage.jsx
│   │   │   ├── Login.jsx
│   │   │   ├── Register.jsx
│   │   │   ├── ForgotPassword.jsx
│   │   │   ├── ResetPassword.jsx
│   │   │   └── AdminNotamMap.jsx
│   │   ├── config/       # Configuration
│   │   │   └── api.js    # API endpoints
│   │   ├── styles/       # CSS files
│   │   ├── App.jsx       # Main application component
│   │   └── main.jsx      # Entry point
│   ├── package.json
│   └── vite.config.js
│
└── README.md              # This file
```

---

## 🎯 Feature Summary

### ✅ Completed Features

- [x] 3 different user roles (Operator, Controller, Admin)
- [x] Role-based access control (RBAC)
- [x] JWT-based authentication
- [x] Email verification system (10-minute valid OTP)
- [x] Two-factor authentication (2FA) - for Controller and Admin
- [x] Password reset system (1-hour valid token)
- [x] Drawing flight zones on map (Polygon, LineString, Point)
- [x] NOTAM (restricted zone) management
- [x] Spatial validation (client-side with Turf.js)
- [x] Flight request creation, approval, rejection
- [x] Email notification system
- [x] Dark/light mode
- [x] Map layers (Normal, Satellite, Dark)
- [x] Responsive design (mobile-friendly)
- [x] AWS EC2 deployment
- [x] MongoDB Atlas integration
- [x] RESTful API
- [x] Swagger API documentation

---


## 👤 Author

**Yasir Eren Çelik**  
Student No: 21833039  
Course: GMT 458 – Web GIS  


