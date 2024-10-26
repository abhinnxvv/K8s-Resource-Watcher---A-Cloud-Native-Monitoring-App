# K8s Resource Watcher - A Cloud Native Monitoring App

This project is a comprehensive guide to building, containerizing, and deploying a cloud-native Python application for real-time resource monitoring on Kubernetes (K8s). It’s built with Flask, containerized with Docker, pushed to AWS ECR, and deployed on an AWS EKS cluster.

## Project Overview

The application monitors system resource usage (CPU and memory) and visualizes metrics using Plotly.js. It is designed for Kubernetes deployments, allowing resource metrics to be visualized within a containerized environment and scaled up when high resource usage is detected.

## Features

- **Real-Time Resource Monitoring**: Tracks CPU and memory usage, displaying metrics on a web-based dashboard.
- **Alert Mechanism**: Notifies users when CPU or memory utilization exceeds 80%.
- **Containerized Deployment**: Dockerized application for easy portability and deployment.
- **Kubernetes Deployment**: Deployed on AWS EKS, with automated deployments through Python scripts.

## Application Components

1. **Flask App (`app.py`)**: 
   - Provides routes to display system metrics on a web dashboard.
   - Uses `psutil` to retrieve system stats and Plotly.js to create gauges for CPU and memory utilization.
   
2. **Dockerization**:
   - Dockerfile defines the container setup, installing required dependencies and configuring the Flask server.

3. **AWS ECR Integration (`ecr.py`)**:
   - Uses Boto3 to interact with AWS ECR, creating a repository to store the Docker image.

4. **Kubernetes Deployment on EKS (`eks.py`)**:
   - Defines and deploys a Kubernetes Deployment and Service, using the `kubernetes` Python client to interface with the EKS cluster.

## Directory Structure

```plaintext
Cloud-Native-Resource-Monitoring-App/
├── app.py                # Flask application code
├── Dockerfile            # Dockerfile to containerize the app
├── requirements.txt      # Python dependencies
├── ecr.py                # Script for pushing the Docker image to AWS ECR
├── eks.py                # Script for deploying on AWS EKS
├── templates/
│   └── index.html        # HTML template for the dashboard
└── README.md             # Project README file
```

## Getting Started

### Prerequisites

- **Python 3.9**
- **Docker**: for containerization
- **AWS CLI**: configured with permissions for ECR and EKS
- **kubectl**: to manage Kubernetes clusters
- **AWS EKS Cluster**: setup and configured

### Setup Instructions

1. **Clone the Repository**:

   ```bash
   git clone https://github.com/yourusername/Cloud-Native-Resource-Monitoring-App.git
   cd Cloud-Native-Resource-Monitoring-App
   ```

2. **Install Python Dependencies**:

   ```bash
   pip3 install -r requirements.txt
   ```

3. **Run the Flask App Locally**:

   ```bash
   python3 app.py
   ```

   Access the app at `http://localhost:5000`.

### Dockerization

1. **Build the Docker Image**:

   ```bash
   docker build -t monitoring_app .
   ```

2. **Run the Docker Image Locally**:

   ```bash
   docker run -p 5000:5000 monitoring_app
   ```

### AWS ECR Integration

1. **Push the Docker Image to ECR**:

   Run `ecr.py` to create an ECR repository and push the Docker image to ECR.

   ```bash
   python3 ecr.py
   ```

   This script outputs the URI of the ECR repository.

2. **Tag and Push the Docker Image**:

   Replace `<repository-uri>` with the URI from `ecr.py`:

   ```bash
   docker tag monitoring_app <repository-uri>:latest
   docker push <repository-uri>:latest
   ```

### Deploying on AWS EKS

1. **Create and Deploy on EKS**:

   Run `eks.py` to create the Kubernetes Deployment and Service in the default namespace:

   ```bash
   python3 eks.py
   ```

2. **Access the App on Kubernetes**:

   To access the app, forward the Kubernetes service to your localhost:

   ```bash
   kubectl port-forward service/my-flask-service 5000:5000
   ```

   Access the app at `http://localhost:5000`.

## Project Details

### Dockerfile

```dockerfile
FROM python:3.9-buster

WORKDIR /app

COPY requirements.txt .

RUN pip3 install --no-cache-dir -r requirements.txt

COPY . .

ENV FLASK_RUN_HOST=0.0.0.0

EXPOSE 5000

CMD ["flask", "run"]
```

### Sample Code

**app.py**

```python
import psutil
from flask import Flask, render_template

app = Flask(__name__)

@app.route("/")
def index():
    cpu_metric = psutil.cpu_percent()
    mem_metric = psutil.virtual_memory().percent
    Message = None
    if cpu_metric > 80 or mem_metric > 80:
        Message = "High CPU or Memory Detected, scale up!!!"
    return render_template("index.html", cpu_metric=cpu_metric, mem_metric=mem_metric, message=Message)

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
```

**index.html**

The HTML template `index.html` uses Plotly.js to display CPU and memory utilization gauges.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request or open an Issue for any improvements.
