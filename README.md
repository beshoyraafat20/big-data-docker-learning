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


### 2. Run your First interactive Container
Lanch a fresh Ubuntu 22.04 container:
```bash
docker run -it --name my-ubuntu-test ubuntu-test ubuntu:22.04 /bin/bash
```
Verification: Your terminal prompt will change to ```root@<container_id>:/#```