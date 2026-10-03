# Dockerized Flask Application with Nginx Load Balancing

A containerized Python Flask web application deployed using Docker and Docker Compose, with Nginx configured as a reverse proxy and load balancer to distribute incoming requests across multiple Flask backend containers.

##  Project Overview

This project demonstrates how to run multiple instances of the same Flask application using Docker and distribute incoming HTTP requests across them using Nginx.

Three Flask backend containers are created:

* `backend1`
* `backend2`
* `backend3`

Nginx acts as the entry point and load balancer. When a user sends a request, Nginx forwards the request to one of the available Flask backend containers.

The Flask application returns the container hostname, making it possible to visually verify that requests are being distributed across different containers.

---

##  Architecture

```text
                         User / Browser
                               |
                               | HTTP :80
                               v
                    +----------------------+
                    |       NGINX          |
                    |   Load Balancer      |
                    +----------+-----------+
                               |
              +----------------+----------------+
              |                |                |
              v                v                v
        +-----------+    +-----------+    +-----------+
        | Backend 1 |    | Backend 2 |    | Backend 3 |
        |   Flask   |    |   Flask   |    |   Flask   |
        |   :5000   |    |   :5000   |    |   :5000   |
        +-----------+    +-----------+    +-----------+
              |                |                |
              +----------------+----------------+
                               |
                         Docker Network
```

---

##  Technologies Used

* Python
* Flask
* Docker
* Docker Compose
* Nginx
* Git
* GitHub
* Linux

---

##  Project Structure

```text
docker-nginx-load-balancer/
│
├── app.py
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
├── nginx.conf
└── README.md
```

### File Description

| File                 | Purpose                                              |
| -------------------- | ---------------------------------------------------- |
| `app.py`             | Flask web application                                |
| `requirements.txt`   | Python dependencies                                  |
| `Dockerfile`         | Builds the Flask Docker image                        |
| `docker-compose.yml` | Creates and manages the containers                   |
| `nginx.conf`         | Nginx reverse proxy and load-balancing configuration |
| `README.md`          | Project documentation                                |

---

##  How the Project Works

### 1. Flask Application

The Flask application runs on port `5000`.

It returns the hostname of the container handling the request.

Example:

```text
Hello from Flask Backend: abc123...
```

The hostname changes depending on which container processes the request.

---

### 2. Docker

The Flask application is packaged into a Docker image using the `Dockerfile`.

The same image is used to create three backend containers:

```text
backend1
backend2
backend3
```

Each container runs the same Flask application independently.

---

### 3. Docker Compose

Docker Compose manages all application services.

The project contains:

```text
nginx
backend1
backend2
backend3
```

Docker Compose also creates a network that allows the containers to communicate with each other using service names.

For example:

```text
backend1:5000
backend2:5000
backend3:5000
```

---

### 4. Nginx Load Balancer

Nginx listens for incoming HTTP requests on port `80`.

The Nginx configuration defines the three Flask backend servers:

```nginx
upstream backend {
    server backend1:5000;
    server backend2:5000;
    server backend3:5000;
}
```

Nginx then forwards incoming requests to the backend group.

By default, Nginx uses a round-robin method for distributing requests.

---

## 🔧 Prerequisites

Before running the project, make sure the system has:

* Docker
* Docker Compose
* Git

---

##  Running the Project

### 1. Clone the repository

```bash
git clone https://github.com/magaresneha/docker-nginx-load-balancer.git
```

Change into the project directory:

```bash
cd docker-nginx-load-balancer
```

### 2. Build and start the containers

```bash
docker compose up -d --build
```

If using Docker Compose V1:

```bash
docker-compose up -d --build
```

### 3. Check running containers

```bash
docker ps
```

You should see:

```text
nginx-lb
backend1
backend2
backend3
```

### 4. Test the application

Open:

```text
http://localhost
```

Or use:

```bash
curl http://localhost
```

Expected response:

```text
Hello from Flask Backend: <container-hostname>!
```

---

##  Testing Load Balancing

Send multiple requests:

```bash
for i in {1..10}; do curl -s http://localhost; echo; done
```

Example:

```text
Hello from Flask Backend: abc123...
Hello from Flask Backend: def456...
Hello from Flask Backend: ghi789...
Hello from Flask Backend: abc123...
Hello from Flask Backend: def456...
```

Different hostnames indicate that different backend containers are handling requests.

### Request Flow

```text
Request 1 → Nginx → Backend 1
Request 2 → Nginx → Backend 2
Request 3 → Nginx → Backend 3
Request 4 → Nginx → Backend 1
...
```

The exact order may vary.

---

##  Testing Backend Availability

Stop one backend:

```bash
docker stop backend1
```

Check the running containers:

```bash
docker ps
```

Then send requests:

```bash
for i in {1..10}; do curl -s http://localhost; echo; done
```

The remaining backend containers can continue serving requests.

Start the backend again:

```bash
docker start backend1
```

---

##  Useful Docker Commands

### View running containers

```bash
docker ps
```

### View all containers

```bash
docker ps -a
```

### View Nginx logs

```bash
docker logs nginx-lb
```

### View backend logs

```bash
docker logs backend1
docker logs backend2
docker logs backend3
```

### Stop the project

```bash
docker compose down
```

or:

```bash
docker-compose down
```

### Rebuild the project

```bash
docker compose up -d --build
```

---

##  Port Configuration

Only Nginx is exposed to the host:

```text
Host Port 80 → Nginx Port 80
```

The Flask containers use port `5000` internally.

```text
backend1 → 5000
backend2 → 5000
backend3 → 5000
```

The backend containers do not need to expose port `5000` directly to the host because Nginx communicates with them through the Docker network.

---

##  Project Objectives

The main objectives of this project are:

* Understand Docker containerization
* Create and run Docker images
* Run multiple application containers
* Learn Docker Compose
* Understand Docker networking
* Configure Nginx as a reverse proxy
* Implement basic load balancing
* Understand communication between containers
* Test backend availability
* Manage the project using Git and GitHub

---

##  Key DevOps Concepts Demonstrated

### Containerization

The Flask application is packaged into a Docker image so it can run consistently across environments.

### Reverse Proxy

Nginx receives client requests and forwards them to the appropriate backend service.

### Load Balancing

Nginx distributes incoming requests among multiple Flask backend containers.

### Docker Networking

Docker Compose allows Nginx to communicate with the backend containers using service names.

### Scalability

Multiple instances of the application can handle incoming requests instead of relying on a single application container.

---

##  Future Improvements

Possible improvements to this project include:

* Deploy the application on AWS EC2
* Push Docker images to Docker Hub
* Add HTTPS using SSL/TLS
* Add health checks
* Configure Nginx advanced load-balancing strategies
* Add monitoring using Prometheus and Grafana
* Add CI/CD using GitHub Actions
* Add automatic deployment to AWS

---

## 👩‍💻 Author

**Sneha Magare**

Computer Science and Engineering Student

GitHub: https://github.com/magaresneha

---

## ⭐ Project Summary

This project demonstrates a practical DevOps workflow in which a Python Flask application is containerized using Docker, replicated across multiple backend containers using Docker Compose, and placed behind an Nginx reverse proxy/load balancer.

The project provides hands-on experience with containerization, networking, reverse proxying, load balancing, Linux, Git, and GitHub.
