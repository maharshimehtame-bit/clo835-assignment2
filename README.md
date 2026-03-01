# CLO835 Assignment 2 – Kubernetes Deployment on EC2 using kind

## Overview
This project deploys a containerized Flask web application and MySQL database on a Kubernetes cluster created using kind on an Amazon Linux EC2 instance.

---

## Step 1: EC2 Setup
- Amazon Linux EC2 instance deployed
- Docker installed
- kind installed
- kubectl installed

---

## Step 2: Create kind Cluster

```bash
kind create cluster --name clo835


