# Istio Service Mesh with Kubernetes Demo

This repository demonstrates how to implement a service mesh using Istio in a Kubernetes environment, featuring a microservices-based e-commerce application with monitoring and observability through Kiali, Grafana, and other tools.

## Project Overview

This project showcases:

- Istio service mesh implementation
- Microservices architecture for an e-commerce application
- Advanced traffic management patterns
- Service mesh observability and monitoring
- Security features with mTLS

## Architecture

The e-commerce application consists of the following microservices:

- UI Service (`ecommerce-ui`)
- Product Catalog Service
- Product Inventory Service
- Order Management Service
- Profile Management Service
- Shipping and Handling Service
- Team Contact Support Service

## Prerequisites

- Kubernetes cluster
- Helm
- kubectl CLI
- curl

## Installation

### 1. Install Istio

```bash
# Create istio-system namespace
kubectl create namespace istio-system

# Add Istio Helm repository
helm repo add istio https://istio-release.storage.googleapis.com/charts

# Install Istio base
helm install istio-base istio/base -n istio-system --version 1.25.2

# Install istiod
helm install istiod istio/istiod -n istio-system --version 1.25.2 --wait

# Install Istio ingress gateway
helm install istio-ingress istio/gateway -n istio-system --version 1.25.2

```

### 2. Verify Installation

```bash
# Check pods status
kubectl get pods -n istio-system

# Check services status
kubectl get svc -n istio-system
```

### 3. Install Monitoring Add-ons

```bash
# Download add-on manifests
mkdir -p istio-addons
curl -L https://raw.githubusercontent.com/istio/istio/release-1.25/samples/addons/prometheus.yaml -o istio-addons/prometheus.yaml
curl -L https://raw.githubusercontent.com/istio/istio/release-1.25/samples/addons/grafana.yaml -o istio-addons/grafana.yaml
curl -L https://raw.githubusercontent.com/istio/istio/release-1.25/samples/addons/kiali.yaml -o istio-addons/kiali.yaml
curl -L https://raw.githubusercontent.com/istio/istio/release-1.25/samples/addons/jaeger.yaml -o istio-addons/jaeger.yaml

# Apply add-on manifests
kubectl apply -f istio-addons/prometheus.yaml
kubectl apply -f istio-addons/grafana.yaml
kubectl apply -f istio-addons/kiali.yaml
kubectl apply -f istio-addons/jaeger.yaml
```

### 4. Deploy Application

```bash
# Create and label namespace for automatic sidecar injection
kubectl create namespace ecommerce
kubectl label namespace ecommerce istio-injection=enabled

# Deploy all microservices
kubectl apply -f microservices/
```

## Features

### 1. Traffic Management

- Gateway and Virtual Service configurations for ingress traffic
- Canary deployments (see `canary-deployment/` directory)
- Circuit breaker implementation (see `circuit-breaker-func/` directory)

### 2. Security

- Mutual TLS (mTLS) encryption between services
- Service-to-service authentication

### 3. Observability

- Kiali dashboard for service mesh visualization
- Grafana dashboards for metrics
- Jaeger for distributed tracing
- Prometheus for metrics collection

## Directory Structure

- `canary-deployment/`: Canary deployment configurations
- `circuit-breaker-func/`: Circuit breaker implementations
- `istio/`: Istio configurations (Gateway, Virtual Services, mTLS)
- `microservices/`: Kubernetes manifests for all microservices
- `setup/`: Installation and setup commands

## Accessing Dashboards

After deployment, you can access the following dashboards:

```bash
# Access Kiali dashboard
kubectl port-forward svc/kiali -n istio-system 20001:20001

# Access Grafana dashboard
kubectl port-forward svc/grafana -n istio-system 3000:3000

# Access Jaeger UI
kubectl port-forward svc/jaeger-query -n istio-system 16686:16686
```

Then open your browser and navigate to:

- Kiali: http://localhost:20001
- Grafana: http://localhost:3000
- Jaeger: http://localhost:16686

## Contributing

Feel free to submit issues, fork the repository, and create pull requests for any improvements.

## License

This project is licensed under the MIT License - see the LICENSE file for details.
