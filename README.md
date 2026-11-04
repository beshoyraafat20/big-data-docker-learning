# 🚀 Big Data & Docker Learning Path

Welcome to the **`big-data-docker-learning`** repository! This project serves as a step-by-step practical guide to mastering **Docker** fundamentals and escalating to multi-container environments running Big Data tools (**Apache Hadoop**, **HDFS**, **MapReduce**, and **Apache HBase**).

---

## 📌 Prerequisites

- A GitHub account to launch **GitHub Codespaces**.
- Basic understanding of Command Line Interface (CLI) / Linux terminal commands.

---

## 🛠️ Phase 1: Docker Basics & Core Concepts

In this section, you learn how to interact with Docker containers interactively.

### 1. Check Docker Installation
Verify that Docker is up and running in your environment:
```bash
docker --version 
```


### 2. Run your First interactive Container
Lanch a fresh Ubuntu 22.04 container:
```bash
docker run -it --name bigdata-node ubuntu:22.04 /bin/bash
```
Verification: Your terminal prompt will change to ```root@<container_id>:/#```

### 3. Re-entry & Managemet
if your container is stopped or you get a container name conflict error, manage it using:
```bash

# List all running Docker containers
#Check which containers are currently active and what services they are running:
docker ps

# Monitor real-time container resource usage
docker stats

# Inspect processes inside a specific container

docker top <container_name_or_id>

# Force-remove existing container with the same name
docker rm -f bigdata-node

# Start an existing container
docker start bigdata-node

# Execute an interactive shell inside the running container
docker exec -it bigdata-node /bin/bash
```

## ☕ Phase 2: System Setup & Dependency Installation
In this phase, you install java 11 and core terminal utilites required by distributed systems.

### 1. Update Package index & install Packages
inside the container terminal (```root@...:/#```):

```bash
apt-get update && apt-get install -y openjdk-11-jdk wget tar nano
```

### 2. Verify java Environment

```bash
java -version
```
verification: Confirm that OpenJDK 11 output is displayed.

## 👤 Phase 3: Dedicated Hadoop User Setup & Permissions

Best practices: run big data daemons under a dedicated user (`hduser`) instead of `root`.

### 1. Create User Group and Hadoop user

```bash
groupadd hadoop
useradd -m -g hadoop -s /bin/bash hduser
passwd hduser
```
(Enter and confirm a password for hduser when prompted).
