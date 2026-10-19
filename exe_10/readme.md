# Exercise 10: Multi-Node Kubernetes Cluster with Multiple Applications

## Objective
Set up a multi-node Kubernetes cluster using Minikube and deploy two Flask applications with multiple replicas distributed across three nodes. Expose the applications using Kubernetes NodePort Services and test their REST APIs.

## Architecture

- **Kubernetes Cluster:** Minikube with 3 nodes (1 control-plane and 2 worker nodes)
- **Application A:** Product Catalog — 2 replicas
- **Application B:** Shopping Cart — 3 replicas
- **Containerization:** Docker
- **Application Framework:** Python Flask
- **Service Type:** NodePort

## Project Structure

```text
exe_10/
├── product-catalog/
│   ├── product_catalog.py
│   ├── Dockerfile
│   ├── deployment.yaml
│   └── service.yaml
├── shopping-cart/
│   ├── shopping_cart.py
│   ├── Dockerfile
│   ├── deployment.yaml
│   └── service.yaml
└── README.md
```

## Step 1: Create the Multi-Node Cluster

```powershell
minikube start --nodes 3 -p devops-multinode --force
```

Verify the nodes:

```powershell
kubectl --context=devops-multinode get nodes -o wide
```

Expected: Three nodes in `Ready` status.

## Step 2: Build Docker Images

Build the Product Catalog image:

```powershell
docker build -t product-catalog:latest -f .\product-catalog\Dockerfile .\product-catalog
```

Build the Shopping Cart image:

```powershell
docker build -t shopping-cart:latest -f .\shopping-cart\Dockerfile .\shopping-cart
```

Load the images into Minikube:

```powershell
minikube -p devops-multinode image load product-catalog:latest
minikube -p devops-multinode image load shopping-cart:latest
```

## Step 3: Deploy the Applications

Apply the Kubernetes Deployments:

```powershell
kubectl --context=devops-multinode apply -f .\product-catalog\deployment.yaml
kubectl --context=devops-multinode apply -f .\shopping-cart\deployment.yaml
```

The Product Catalog Deployment runs 2 replicas, while the Shopping Cart Deployment runs 3 replicas.

Both Deployments use **pod anti-affinity** with `kubernetes.io/hostname` to schedule replicas of the same application on different nodes.

Verify the pods:

```powershell
kubectl --context=devops-multinode get pods -o wide
```

## Step 4: Expose Applications Using Services

Create the NodePort Services:

```powershell
kubectl --context=devops-multinode apply -f .\product-catalog\service.yaml
kubectl --context=devops-multinode apply -f .\shopping-cart\service.yaml
```

Verify the Services:

```powershell
kubectl --context=devops-multinode get services
```

| Service | Application | Port |
|---|---|---|
| `product-catalog-service` | Product Catalog | 80 |
| `shopping-cart-service` | Shopping Cart | 80 |

## Step 5: Access and Test the Applications

Get the Product Catalog Service URL:

```powershell
minikube -p devops-multinode service product-catalog-service --url
```

Test the product endpoint:

```powershell
Invoke-RestMethod "http://127.0.0.1:<PORT>/products"
```

The endpoint returns a JSON list of products:

- Laptop — ₹75,000
- Phone — ₹30,000
- Headphones — ₹2,000

Get the Shopping Cart Service URL:

```powershell
minikube -p devops-multinode service shopping-cart-service --url
```

Retrieve the cart:

```powershell
Invoke-RestMethod "http://127.0.0.1:<PORT>/cart"
```

Add an item to the cart:

```powershell
$cartUrl = "http://127.0.0.1:<PORT>"
$body = @{ id = 1; name = "Laptop"; quantity = 1 } | ConvertTo-Json

Invoke-RestMethod `
  -Uri "$cartUrl/cart" `
  -Method Post `
  -ContentType "application/json" `
  -Body $body
```

Verify the updated cart:

```powershell
Invoke-RestMethod "$cartUrl/cart"
```

Replace `<PORT>` with the port in the URL returned by Minikube.

## Step 6: Verify the Final Deployment

```powershell
kubectl --context=devops-multinode get deployments
kubectl --context=devops-multinode get pods -o wide
kubectl --context=devops-multinode get services
```

Expected deployment replica counts:

- Product Catalog: **2/2 ready**
- Shopping Cart: **3/3 ready**

## Key Concepts Learned

- Creating a multi-node Kubernetes cluster with Minikube
- Building Docker images for Flask applications
- Deploying applications using Kubernetes Deployments
- Managing replicas and pod anti-affinity
- Distributing application pods across multiple nodes
- Exposing applications using NodePort Services
- Testing REST APIs using PowerShell
- Inspecting Kubernetes pods, nodes, Deployments, and Services

## Note

The Shopping Cart stores items in application memory. Its data is not persistent and can be lost when a pod restarts. This exercise demonstrates basic application deployment and API functionality rather than persistent cart storage.
