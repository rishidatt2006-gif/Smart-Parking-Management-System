# Smart Parking Management System

An AI-powered, full-stack microservices application designed for real-time vehicle detection and automated parking slot management.

## Features
* **AI Vehicle Detection:** Utilizes YOLOv8 (nano) for high-speed, real-time vehicle identification within parking boundaries.
* **Distributed Processing:** Leverages Ray to process multiple parking zones in parallel, drastically reducing image inference time.
* **Optimal Slot Assignment:** Integrates Google OR-Tools to mathematically assign the best available parking slot to incoming vehicles.
* **Interactive Dashboard:** A React-based frontend providing a live overview of slot availability, vehicle control, and event logging.
* **Microservices Architecture:** Independently scalable services for the frontend, central API, AI detection, and notifications.

## Tech Stack
* **Frontend:** React.js, Axios, CSS
* **Backend:** FastAPI, Python, Pydantic
* **AI & Computer Vision:** YOLOv8 (Ultralytics), OpenCV, NumPy
* **Optimization & Compute:** Ray, Google OR-Tools
* **Architecture:** REST APIs, Docker, Kubernetes

## Microservices Overview

### 1. Central API (FastAPI)
Acts as the core orchestrator (`main.py`). It manages the state of all parking slots, handles vehicle entry/exit routing, and runs the OR-Tools optimization logic. It communicates directly with both the Detection and Notification services.

### 2. Detection Service (Python/Ray)
Handles the heavy lifting of computer vision (`detector.py`). It receives image feeds, decodes them, and processes predefined parking zones simultaneously using Ray remote tasks. It cross-references YOLOv8 bounding boxes with slot coordinates to determine occupancy.

### 3. Notification Service (Express/Node.js)
Receives state changes from the Central API and broadcasts formatted events (like `VEHICLE_ENTERED` and `VEHICLE_EXITED`) to the frontend event log.

### 4. Frontend Dashboard (React)
A responsive user interface (`App.js`, `App.css`) that auto-refreshes to maintain a live view of the parking lot. It includes tabs for a visual dashboard, manual vehicle entry/exit control, and a historical event log.

## Getting Started

### Prerequisites
* Node.js & npm
* Python 3.8+

### Local Development Setup

**1. Clone the repository**
```bash
git clone [https://github.com/yourusername/smart-parking-system.git](https://github.com/yourusername/smart-parking-system.git)
cd smart-parking-system
```
**2. Start the Central Backend (FastAPI)**
```bash
cd backend
pip install fastapi uvicorn requests
uvicorn main:app --reload --port 8000
```
**3. Start the Detection Service**
```bashcd detection-service
pip install ray ultralytics opencv-python pillow
uvicorn server:app --port 8001
```
**4. Start the Frontend (React)**
```bashcd detection-servicecd frontend
npm install
npm start
```

System Workflows
Initialization: The parking lot can be populated with default empty zones (e.g., Zone A, Zone B) via the frontend dashboard using the /slots/initialize endpoint.

Vehicle Entry: A vehicle license plate is entered into the system. The FastAPI backend filters for free slots and uses OR-Tools to assign the optimal location. It flags the slot as occupied in the central database and fires a notification event.

Live Detection Updates: When an image is uploaded to /detect-and-update, it is passed to the YOLOv8 service. Ray spawns parallel tasks for each parking zone, checks bounding box intersections with slot coordinates, and updates the central database dynamically.
