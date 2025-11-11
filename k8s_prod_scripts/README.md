# Spring Boot + MySQL Kubernetes Deployment

**Designed and Developed by: sak_shetty**

This project demonstrates deploying a **Spring Boot application** with a **MySQL database** on **Kubernetes**, using **Jenkins pipelines** for automation. The setup ensures that the application pod starts **only after MySQL is healthy**, and all resources can be deployed or removed with parameterized pipeline stages.

---

## Features

* Spring Boot application containerized and hosted on DockerHub: `sakit333/webapp_dev`.
* MySQL database deployed with Kubernetes `Deployment` and `ClusterIP` service.
* Spring Boot service exposed via `LoadBalancer` with NodePort `30088`.
* Namespace scoped resources: `sak-shetty-namespace`.
* `initContainer` ensures the app starts only when MySQL is ready.
* Jenkins pipeline with parameterized **actions** (`deploy` / `remove`) and **multi-version Spring app** boolean.
* Fully automated deployment and cleanup of all Kubernetes resources.
* Secrets stored securely using Kubernetes `Secret`.

---

## Prerequisites

* **Kubernetes cluster** with kubectl access.
* **Jenkins server** with credentials for Kubeconfig (`kubeconfig-credentials-id`).
* Docker image available publicly: `sakit333/webapp_dev`.
* `kubectl` and `nc` commands available for initContainers.

---

## Kubernetes Files

1. **namespace.yml** – Creates namespace `sak-shetty-namespace`.
2. **db_deploy_svc.yml** – MySQL deployment + `ClusterIP` service.
3. **app_deploy_svc.yml** – Spring Boot deployment + `LoadBalancer` service with NodePort 30088 and initContainer for MySQL readiness.

---

## Deployment

### Step 1: Apply Namespace

```bash
kubectl apply -f namespace.yml
```

### Step 2: Deploy MySQL

```bash
kubectl apply -f db_deploy_svc.yml
```

### Step 3: Deploy Spring Boot App (after MySQL is ready)

```bash
kubectl apply -f app_deploy_svc.yml
```

### Step 4: Verify Resources

```bash
kubectl get all -n sak-shetty-namespace
```

---

## Jenkins Pipeline

* Parameterized **actions**:

  * `deploy` – deploy all resources
  * `remove` – remove all resources
* Boolean flag to deploy multiple versions of the Spring Boot app.
* Stages:

  1. Create namespace (skip if exists)
  2. Deploy MySQL
  3. Deploy Spring Boot app (waits for MySQL)
  4. Show resources
  5. Remove Spring Boot app
  6. Remove MySQL
  7. Remove namespace
  8. Show resources

---

## Cleanup

Remove all deployed resources:

```bash
kubectl delete -f app_deploy_svc.yml
kubectl delete -f db_deploy_svc.yml
kubectl delete -f namespace.yml
```

---

## Notes

* MySQL credentials are stored in a Kubernetes `Secret`. Default username: `root`, password: `password`.
* NodePort `30088` exposes the Spring Boot app externally.
* Application will not start until MySQL pod passes its readiness probe.

---

