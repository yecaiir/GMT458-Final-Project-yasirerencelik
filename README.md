# GMT458-Final-Project-yasirerencelik

https://drive.google.com/drive/folders/1ayoNiKqkzDsSIDoHCuaUoqWpsOR-H04j?usp=sharing

# UAV Flight Permission & Management System (Web GIS)

> **Live Demo:** [https://aerodps.me](https://aerodps.me)



## 📌 Project Overview
**Student:** Yasir Eren Çelik (21833039)  
**Course:** GMT 458 – Web GIS Final Project  


This project is a comprehensive **Web GIS application** designed to manage Unmanned Aerial Vehicle (UAV) flight permissions in urban environments. It utilizes modern web technologies and geospatial algorithms to automate the authorization process, ensuring flights do not interfere with restricted zones (NOTAMs).

---

## 📖 Table of Contents
- [Project Overview](#-project-overview)
- [Live Demo](#-live-demo)
- [Key Features](#-key-features)
- [System Architecture](#-system-architecture)
  - [Geospatial Analysis (Turf.js)](#geospatial-analysis-turfjs)
  - [Database Design](#database-design)
- [Tech Stack](#-tech-stack)
- [Installation & Setup](#-installation--setup)
- [Deployment](#-deployment)
- [Project Gallery](#-project-gallery)

---

## 🚀 Live Demo
The project is deployed on AWS EC2 and is publicly accessible:
👉 **[https://aerodps.me](https://aerodps.me)**

---

## ✨ Key Features

### 👨‍✈️ For Pilots (Operators)
- **Interactive Map:** Draw flight zones (Polygon) or paths (LineString) using Leaflet Draw tools.
- **Smart Validation:** Immediate feedback if the drawn area intersects with a restricted **NOTAM** zone.
- **Flight Management:** View past requests and their status (Pending, Approved, Rejected).
- **Mobile Responsive:** Fully functional on mobile devices for field operations.

### 👮‍♂️ For Controllers (Admins)
- **Airspace Monitoring:** Visualize all active flight requests and NOTAMs on a single dashboard.
- **Decision Support:** Approve or Reject flight requests based on spatial data.
- **NOTAM Management:** Create and manage dynamic restricted zones (e.g., for emergencies).

---

## 🏗 System Architecture

### Geospatial Analysis (Turf.js)
To ensure flight safety, the system implements a robust client-side validation mechanism using **Turf.js**.

1.  **Input:** Pilot draws a flight zone.
2.  **Process:** The system instantly converts the drawing to GeoJSON.
3.  **Validation:** `turf.booleanIntersects` checks for overlap between the flight zone and active NOTAM polygons.
4.  **Outcome:** If an intersection is detected, the drawing is blocked, and the pilot is warned immediately.

### Database Design
- **MongoDB Atlas** is used for storing flexible geospatial data.
- **GeoJSON:** All flight zones and NOTAMs are stored in standard GeoJSON format.
- **2dsphere Index:** Enabled for high-performance geospatial queries (e.g., "Find all flights near this point").

---

## 🛠 Tech Stack

| Component | Technology | Description |
| :--- | :--- | :--- |
| **Frontend** | React.js, Vite | Interactive UI with fast HMR |
| **Styling** | Tailwind CSS | Mobile-first, responsive design |
| **Map Engine** | Leaflet, React-Leaflet | Map rendering and drawing tools |
| **Geospatial** | Turf.js | Client-side spatial analysis |
| **Backend** | Node.js, Express | RESTful API services |
| **Database** | MongoDB Atlas | NoSQL database with GeoJSON support |
| **Auth** | JWT (JSON Web Token) | Secure stateless authentication |

---

## ⚙ Installation & Setup

Follow these steps to run the project locally.

### 1. Clone the Repository
```bash
git clone [https://github.com/YasirEren/drone-permission-system.git](https://github.com/YasirEren/drone-permission-system.git)
cd drone-permission-system
