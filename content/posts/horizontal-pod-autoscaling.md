---
title: "Horizontal Pod Autoscaling with Kubernetes: A Hands-On Guide"
draft: false
date: 2024-09-08T09:16:45.000Z
description: "This covers setting up Horizontal Pod Autoscaling (HPA) in Kubernetes, including configuring the Metrics Server, dockerizing a Node.js app, and deploying it with HPA. Learn to create Kubernetes configurations and monitor autoscaling with Apache Benchmark to ensure efficient scaling of your application."
categories:
  - Tech
tags:
  - Kubernetes
---

Kubernetes, the powerful orchestration platform for containerized applications, offers numerous features to ensure that your applications are running efficiently and scaling as needed. One of the most impactful features is Horizontal Pod Autoscaling (HPA). In this blog, we'll walk through setting up HPA for a simple Node.js application on a Kubernetes cluster running on Docker Desktop for Windows. We'll cover everything from setting up your environment to deploying a scalable application and testing its autoscaling behavior.

## Prerequisites

- **Docker Desktop for Windows**: Ensure Docker Desktop is installed and Kubernetes is enabled.
- **Metrics Server**: Required for HPA to work. It collects resource usage data.
- **Apache Benchmark**: For load testing.

## Setting Up Metrics Server

**Download and Edit `components.yaml`**:

   - Get the latest `components.yaml` from the [Metrics Server releases page](https://github.com/kubernetes-sigs/metrics-server/releases).
   - Open `components.yaml` in your text editor.
   - Add `--kubelet-insecure-tls` under the `args` section to bypass TLS verification.
   - Save the file.

**Apply the Configuration**:

   ```bash
   kubectl apply -f components.yaml
   ```

**Verify Metrics Server**:
   - Check if the metrics server is collecting data:
   ```bash
   kubectl top node
   kubectl top pod -A
   ```

## Creating and Deploying a Dockerized Node.js Application

**Write a Dockerfile**:
   Create a `Dockerfile` in the root directory of your Node.js application:

   ```Dockerfile
   # Use the official Node.js image
   FROM node:14

   # Create and set the working directory
   WORKDIR /usr/src/app

   # Copy package.json and install dependencies
   COPY package*.json ./
   RUN npm install

   # Copy the rest of the application code
   COPY . .

   # Expose the port the app runs on
   EXPOSE 3000

   # Command to run the application
   CMD [ "node", "index.js" ]
   ```

**Build the Docker Image**:

   ```bash
   docker build -t test/node-example .
   ```

**Push the Image to Docker Hub**:
   ```bash
   docker tag test/node-example your-dockerhub-username/node-example
   docker push your-dockerhub-username/node-example
   ```

## Kubernetes Configuration Files

**Deployment Configuration (`k8s/deployment.yml`)**:

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: node-example
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: node-example
     template:
       metadata:
         labels:
           app: node-example
       spec:
         containers:
           - name: node-example
             image: your-dockerhub-username/node-example
             imagePullPolicy: Always
             ports:
               - containerPort: 3000
             resources:
               limits:
                 cpu: "0.5"
               requests:
                 cpu: "0.25"
   ```

**Service Configuration (`k8s/service.yml`)**:

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: node-example
     labels:
       app: node-example
   spec:
     selector:
       app: node-example
     ports:
       - port: 3000
         protocol: TCP
         nodePort: 30001
     type: LoadBalancer
   ```

**Horizontal Pod Autoscaler Configuration (`k8s/hpa.yml`)**:

   ```yaml
   apiVersion: autoscaling/v1
   kind: HorizontalPodAutoscaler
   metadata:
     name: node-example
   spec:
     maxReplicas: 4
     minReplicas: 1
     scaleTargetRef:
       apiVersion: apps/v1
       kind: Deployment
       name: node-example
     targetCPUUtilizationPercentage: 50
   ```

**Apply Configurations**:
   ```bash
   kubectl apply -f k8s
   ```

## Monitoring Autoscaling

**Terminal 1 - Monitor Deployments**:

   ```powershell
   while ($true) {
       cls
       kubectl get deployments
       Start-Sleep -Seconds 1
   }
   ```

**Terminal 2 - Monitor HPA**:
   ```powershell
   while ($true) {
       cls
       kubectl get hpa
       Start-Sleep -Seconds 1
   }
   ```

## Testing Autoscaling

**Install Apache Benchmark**:

Apache Benchmark is often included with Apache HTTP Server. Install it if you haven’t already.

**Run Load Test**:

   ```bash
   ab -c 5 -n 1000 -t 100000 http://127.0.0.1:3000/
   ```

   This command will generate load on your Node.js application and trigger autoscaling based on CPU usage.

## Conclusion

Congratulations! You've successfully set up Horizontal Pod Autoscaler for a Node.js application on Kubernetes. By monitoring the autoscaling behavior, you can ensure that your application remains responsive under varying loads, leveraging Kubernetes' powerful scaling capabilities.

Happy Scaling! 🚀

---

Feel free to adjust any specific details according to your setup or preferences.
