# 🅿️ Smart Parking Management System

**AI-Powered Vehicle Detection & Slot Management** *AI212 Project — Indian Institute of Technology Ropar* *Authors: Kush Mistry & Rishi Datt Gupta*

---

## 📖 Table of Contents
- [Project Overview](#project-overview)
- [Core Objectives](#core-objectives)
- [Tech Stack](#tech-stack)
- [System Architecture & Microservices](#system-architecture--microservices)
- [Core Logic Engines](#core-logic-engines)
- [Directory Structure](#directory-structure)
- [Getting Started (Local Setup)](#getting-started-local-setup)
- [Future Scope](#future-scope)

---

## 🌍 Project Overview
The rapid increase in vehicle density has made traditional, manual parking management inefficient and time-consuming. This Smart Parking System replaces manual toll booths, wasted fuel, and high labor costs with an automated, AI-driven solution. It provides faster entry/exit, real-time occupancy tracking, and lower operational costs without relying on expensive hardware ground sensors.

---

## 🎯 Core Objectives
* **AI Detection:** Utilize YOLOv8 to automatically detect vehicles in camera feeds.
* **Real-time Status:** Monitor parking slot availability with zero manual intervention.
* **Optimal Assignment:** Mathematically assign the best available slot to incoming cars.
* **Visual Dashboard:** Provide a user-friendly React interface for live lot monitoring.

---

## 🛠️ Tech Stack
* **Frontend:** React.js, Axios, CSS
* **Backend:** FastAPI, Python, Node.js, Express
* **AI & Computer Vision:** YOLOv8 (Ultralytics), OpenCV, NumPy
* **Optimization & Compute:** Ray (Distributed Processing), Google OR-Tools
* **DevOps & Architecture:** REST APIs, Docker, Kubernetes

---

## 🧩 System Architecture & Microservices

The application is built on an isolated, containerized microservices architecture to ensure seamless scalability and eliminate environment inconsistencies.

### 1. Central API (FastAPI)
Acts as the core orchestrator. It manages the state of all parking slots, handles vehicle entry/exit routing, and runs the OR-Tools optimization logic. It communicates directly with both the Detection and Notification services.
* **Port:** `8000`

### 2. Detection Service (Python/Ray)
Handles the heavy lifting of computer vision. It receives image feeds, decodes them, and processes predefined parking zones simultaneously using Ray remote tasks. It cross-references YOLOv8 bounding boxes with slot coordinates to determine true occupancy.
* **Port:** `5000` / `8001`

### 3. Notification Service (Node.js/Express)
Receives state changes from the Central API and broadcasts formatted events (e.g., `VEHICLE_ENTERED`, `VEHICLE_EXITED`) to the frontend event log via event-driven alerts.
* **Port:** `4000` / `3001`

### 4. Frontend Dashboard (React)
A responsive user interface that auto-refreshes to maintain a live view of the parking lot. It includes tabs for a visual dashboard, manual vehicle entry/exit control, and a historical event log.
* **Port:** `3000`

---

## 🧠 Core Logic Engines

### Computer Vision (YOLOv8)
The system uses YOLOv8 (nano) to identify vehicle types and draw bounding boxes in real-time. The coordinates of these detected vehicles are mathematically compared against pre-defined slot regions to infer true occupancy.

### OR-Tools Optimization Engine
Instead of assigning parking spots randomly, the system utilizes Google OR-Tools to solve the "Best Slot" assignment problem with an assignment speed of 50ms. The CP-SAT solver minimizes a cost function based on:
1. Entrance distance
2. Zone preference (e.g., Zone A > Zone B)
3. Dynamic load balancing

---

## 📂 Directory Structure

```text
.
├── backend/
│   ├── Dockerfile
│   ├── main.py
│   ├── optimizer.py
│   └── requirements.txt
├── detection-service/
│   ├── Dockerfile
│   ├── detector.py
│   ├── main.py
│   └── requirements.txt
├── express-notification/
│   ├── Dockerfile
│   ├── package-lock.json
│   ├── package.json
│   └── server.js
├── frontend/
│   ├── public/
│   │   ├── favicon.ico
│   │   ├── index.html
│   │   ├── manifest.json
│   │   └── robots.txt
│   ├── src/
│   │   ├── App.css
│   │   ├── App.js
│   │   └── index.css
│   ├── Dockerfile
│   ├── package-lock.json
│   └── package.json
├── k8s/
│   ├── backend-deployment.yaml
│   ├── detection-deployment.yaml
│   ├── frontend-deployment.yaml
│   └── notification-deployment.yaml
├── scripts/
│   ├── setup.sh
│   ├── start.sh
│   └── stop.sh
├── LICENSE
└── README.md
```

## 🚀 Getting Started (Local Setup)

### 1️⃣ Clone the Repository

```bash
git clone https://github.com/yourusername/smart-parking-system.git
cd smart-parking-system
```

---

### 2️⃣ Start the Central Backend (FastAPI)

```bash
cd backend
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

---

### 3️⃣ Start the Detection Service

```bash
cd ../detection-service
pip install -r requirements.txt
uvicorn main:app --port 8001
```

---

### 4️⃣ Start the Notification Service

```bash
cd ../express-notification
npm install
node server.js
```

---

### 5️⃣ Start the Frontend (React)

```bash
cd ../frontend
npm install
npm start
```

The application will now be available at:

| Service              | URL                                            | Port     |
| -------------------- | ---------------------------------------------- | -------- |
| Frontend Dashboard   | `http://localhost:3000`                        | **3000** |
| FastAPI Backend      | `http://localhost:8000`                        | **8000** |
| Detection Service    | `http://localhost:8001`                        | **8001** |
| Notification Service | `http://localhost:4000` *(or configured port)* | **4000** |

---

# 🔄 System Workflows

## 🚗 1. Parking Lot Initialization

Before accepting vehicles, the parking layout is initialized using the frontend dashboard.

**Flow**

```
Frontend
      │
      ▼
POST /slots/initialize
      │
      ▼
FastAPI Backend
      │
      ▼
Creates Parking Slots
      │
      ▼
All Slots Marked Available
```

This endpoint creates the default parking zones and initializes every parking slot as **available**.

---

## 🚘 2. Vehicle Entry & Slot Allocation

When a vehicle enters the parking lot, the backend automatically assigns the most suitable parking slot.

### Workflow

```
Vehicle Number
      │
      ▼
Frontend Entry Form
      │
      ▼
FastAPI Backend
      │
      ▼
Find Available Slots
      │
      ▼
Google OR-Tools Optimizer
      │
      ▼
Best Slot Selected
      │
      ▼
Database Updated
      │
      ▼
Notification Event Generated
```

The optimization engine minimizes a cost function based on:

* 📍 Distance from the entrance
* 🅰️ Preferred parking zone
* ⚖️ Dynamic load balancing across the parking lot

The assigned slot is immediately marked as **Occupied**, and a notification is broadcast to the frontend.

---

## 📷 3. Live Detection & Occupancy Update

The parking lot can also update itself automatically using uploaded camera images.

### Workflow

```
Image Upload
      │
      ▼
FastAPI Backend
      │
      ▼
Detection Service
      │
      ▼
YOLOv8 Vehicle Detection
      │
      ▼
Ray Parallel Processing
      │
      ▼
Bounding Box vs Slot Coordinates
      │
      ▼
Occupied / Free Decision
      │
      ▼
Backend Database Updated
      │
      ▼
Frontend Dashboard Refresh
```

### Detection Pipeline

1. The uploaded image is forwarded to the Detection Service.
2. YOLOv8 detects all visible vehicles.
3. Ray launches multiple workers to process parking zones in parallel.
4. Bounding boxes are compared with predefined parking slot coordinates.
5. Slot occupancy is updated in the backend.
6. The dashboard refreshes automatically to reflect the latest parking status.

---

# 🐳 Docker Deployment

Each microservice is containerized independently.

Example:

```bash
docker build -t backend ./backend
docker build -t detection-service ./detection-service
docker build -t notification-service ./express-notification
docker build -t frontend ./frontend
```

Run the containers individually or deploy them together using Docker Compose or Kubernetes.

---

# ☸️ Kubernetes Deployment

Deployment manifests are provided in the `k8s/` directory.

Apply all deployments using:

```bash
kubectl apply -f k8s/
```

This launches independent deployments for:

* Backend Service
* Detection Service
* Notification Service
* Frontend Dashboard

allowing each component to scale independently.

---

# 📌 API Endpoints

| Method | Endpoint             | Description                                                      |
| ------ | -------------------- | ---------------------------------------------------------------- |
| `POST` | `/slots/initialize`  | Initialize parking slots                                         |
| `GET`  | `/slots`             | Retrieve all parking slots                                       |
| `POST` | `/vehicle/enter`     | Register a vehicle and assign parking                            |
| `POST` | `/vehicle/exit`      | Mark vehicle exit and free slot                                  |
| `POST` | `/detect-and-update` | Detect vehicles from an uploaded image and update slot occupancy |

---

# 🔮 Future Scope

The current implementation serves as a strong foundation for an intelligent parking ecosystem. Planned improvements include:

* 📹 Integration with live CCTV streams for continuous monitoring.
* 🗄️ Full PostgreSQL support for persistent storage and analytics.
* 📱 Android and iOS applications for driver bookings and navigation.
* 💳 Digital payment gateway integration for automated billing.
* 🔔 Push notifications for parking availability and reservation reminders.
* 🧠 Advanced AI models for higher detection accuracy under varying weather and lighting conditions.
* ☁️ Cloud-native deployment with automatic scaling and monitoring.

---

# 📄 License

This project is released under the **MIT License**.

Feel free to use, modify, and distribute it in accordance with the license terms.

---

# 👨‍💻 Authors

**Kush Mistry**

**Rishi Datt Gupta**

**Indian Institute of Technology Ropar**

---
