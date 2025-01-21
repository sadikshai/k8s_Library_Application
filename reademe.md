# Library Management System - Kubernetes Deployment

This repository contains Kubernetes configuration files to deploy a Library Management System. The system includes services for managing books, users, and a web interface.

## Architecture Overview

1. **Books Database**
   - PostgreSQL StatefulSet to store book-related data.
   - Exposed via a ClusterIP service.

2. **Users Database**
   - PostgreSQL StatefulSet to store user-related data.
   - Exposed via a ClusterIP service.

3. **Books Service**
   - Deployment to manage book-related API operations.
   - Connected to the books database.
   - Exposed via a ClusterIP service.

4. **Users Service**
   - Deployment to manage user-related API operations.
   - Connected to the users database.
   - Exposed via a ClusterIP service.

5. **Web Interface**
   - React-based frontend for library management.
   - Communicates with the backend services.
   - Exposed via a LoadBalancer service.

6. **Ingress Controller**
   - Nginx Ingress to manage routing for frontend and backend APIs.

---

## Deployment Instructions

### Prerequisites
- Kubernetes cluster (e.g., Minikube, GKE, EKS).
- kubectl installed and configured.

### Steps

1. **Deploy Books Database**
   ```bash
   kubectl apply -f books-database.yaml
   ```
   This creates:
   - A StatefulSet (`bkdbrs`) for the PostgreSQL database.
   - A headless service (`booksdbsvc`) to allow access.

2. **Deploy Users Database**
   ```bash
   kubectl apply -f users-database.yaml
   ```
   This creates:
   - A StatefulSet (`userdb`) for the PostgreSQL database.
   - A headless service (`dbsvc`) to allow access.

3. **Deploy Books Service**
   ```bash
   kubectl apply -f books-service.yaml
   ```
   This creates:
   - A Deployment (`depbook`) to manage the API.
   - A ClusterIP service (`booksrc`) to expose the service internally.

4. **Deploy Users Service**
   ```bash
   kubectl apply -f users-service.yaml
   ```
   This creates:
   - A Deployment (`depuser`) to manage the API.
   - A ClusterIP service (`svcuser`) to expose the service internally.

5. **Deploy Web Interface**
   ```bash
   kubectl apply -f web-frontend.yaml
   ```
   This creates:
   - A Deployment (`webdeploy`) to host the React app.
   - A LoadBalancer service (`libservice`) to expose the frontend.

6. **Deploy Ingress**
   ```bash
   kubectl apply -f ingress.yaml
   ```
   This creates:
   - An Ingress resource (`nginx`) to route traffic.

---

## Configuration Details

### Books Database
- **StatefulSet Name:** bkdbrs
- **Service Name:** booksdbsvc
- **Port:** 5432
- **Storage:** 1Gi (ReadWriteOnce)

### Users Database
- **StatefulSet Name:** userdb
- **Service Name:** dbsvc
- **Port:** 5432
- **Storage:** 1Gi (ReadWriteOnce)

### Books Service
- **Deployment Name:** depbook
- **Service Name:** booksrc
- **Port:** 8000
- **Probes:**
  - Liveness: TCP socket check on port 8000
  - Readiness: HTTP GET on `/docs`

### Users Service
- **Deployment Name:** depuser
- **Service Name:** svcuser
- **Port:** 8000
- **Probes:**
  - Liveness: TCP socket check on port 8000
  - Readiness: HTTP GET on `/docs`

### Web Interface
- **Deployment Name:** webdeploy
- **Service Name:** libservice
- **Port:** 80 (Frontend exposed on port 3000 internally)
- **Environment Variables:**
  - `REACT_APP_BACKEND_API_URL`
  - `REACT_APP_BOOKS_API_URL`
  - `REACT_APP_USERS_API_URL`

### Ingress
- **Ingress Name:** nginx
- **Routes:**
  - `/users` → `svcuser` (Port: 8000)
  - `/books` → `booksrc` (Port: 8000)
  - `/web` → `libservice` (Port: 3000)

---

## Monitoring and Scaling

### Monitoring
- Use Kubernetes-native tools like `kubectl top` or Prometheus/Grafana.

### Scaling
- Modify the `replicas` field in the deployment files to scale pods.
  ```yaml
  spec:
    replicas: <desired_count>
  ```
- Apply the updated configuration:
  ```bash
  kubectl apply -f <file.yaml>
  ```

---

## Troubleshooting

1. **Check Pod Logs**
   ```bash
   kubectl logs <pod_name>
   ```

2. **Inspect Resource Status**
   ```bash
   kubectl get all
   ```

3. **Debugging Probes**
   - Check liveness, readiness, and startup probe logs for issues.

---

## Cleanup
To delete all resources:
```bash
kubectl delete -f books-database.yaml
kubectl delete -f users-database.yaml
kubectl delete -f books-service.yaml
kubectl delete -f users-service.yaml
kubectl delete -f web-frontend.yaml
kubectl delete -f ingress.yaml
```

---







