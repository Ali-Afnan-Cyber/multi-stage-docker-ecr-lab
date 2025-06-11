# Lab 1: Build, Optimize & Push a Multi-Stage Docker Image to AWS ECR

## Objective

The goal of this lab is to:
- Create a simple Node.js
- Write a multi-stage Dockerfile to optimize image size and security.
- Build, tag, and push the image to Amazon Elastic Container Registry (ECR).

---

## Prerequisites

- AWS CLI installed and configured.
- IAM user or role with the ECR permissions.
- Docker installed and running.

# Dockerfile Explaination
  ![image](https://github.com/user-attachments/assets/00ddd54a-641b-4499-9f76-62438d865b44)

# ------------ Builder Stage ------------
FROM node:18-alpine as builder
We start with Node.js 18 based on Alpine Linux. It’s lightweight and perfect for building the app.

WORKDIR /app
Sets the working directory to /app. Everything we do next happens here.

COPY package*.json ./
Copies the dependency files (package.json and package-lock.json) into the container.

RUN npm install
Installs all the project dependencies.

COPY . .
Copies the entire project into the container, including source code.

# ------------ Production Stage ------------
FROM node:18-alpine
We use a fresh Alpine image again, this time just for running the app—without build tools.

WORKDIR /app
Same working directory as before.

COPY --from=builder /app ./
Takes the built app from the builder stage and brings it into this clean production image.

EXPOSE 3000
Tells Docker the app runs on port 3000.

CMD ["npm", "start"]
Runs npm start when the container starts.

# Docker Push Logs
![image](https://github.com/user-attachments/assets/64f92210-3d4f-41b6-9f2b-cc9c7259c1dc)
# ECR Repo Screenshot
![image](https://github.com/user-attachments/assets/f91cebd1-62d1-4db8-a87b-ed5fe5b49efa)

# How Multi-Stage Docker Reduces Image Size and Improves Security
1. Smaller Image Size
Only the essential files are included in the final image—no build tools, no test files, no node_modules used for development.

This reduces the overall image size, making it faster to build, push, and pull.

Smaller images also use less storage and bandwidth, which is ideal in CI/CD pipelines and production deployments.

2. Improved Security
The final image does not contain compilers, package managers, or source code, so there's less for attackers to exploit.

It minimizes the attack surface by excluding development tools and temporary files.

Smaller images mean fewer vulnerabilities since fewer packages and dependencies are present in production.

3. Separation of Concerns
Build tools stay in the builder stage, isolated from the production environment.

This separation ensures the production container runs cleanly with only what it needs to start the application.



