# ToDo Application: Deployment and Testing Guide

This guide provides instructions on how to apply Kubernetes manifests and verify the application's functionality using different access methods.

## 1. Applying Manifests

To deploy the infrastructure, run the following commands from the root directory:

1. **Create the Namespace:**
```bash
kubectl apply -f .infrastructure/namespace.yml
```


2. **Deploy the Services (ClusterIP and NodePort):**
```bash
kubectl apply -f .infrastructure/clusterIP.yml
kubectl apply -f .infrastructure/nodePort.yml
```


3. **Deploy the Pods and Helper Tools:**
```bash
kubectl apply -f .infrastructure/todoapp-pod.yml
kubectl apply -f .infrastructure/busybox.yml
```



---

## 2. Testing the Application

### Method A: Testing ClusterIP via Busybox (Internal DNS)

This method verifies that the service correctly balances traffic between the two pods inside the cluster.

1. **Execute a curl command from the busybox container:**
```bash
kubectl exec -it busybox -n todoapp -- curl http://todoapp-service/api/health/
```


*Note: `todoapp-service` is the DNS name of your ClusterIP service. Kubernetes automatically resolves this to the service's internal IP.*

### Method B: Testing via NodePort (Node Level Access)

The NodePort service exposes the application on a specific port of your machine (Node).

1. **Access the application using your browser or curl:**
```bash
curl http://localhost:30008/api/health/
```


*Note: Since you are using Docker Desktop, `localhost` acts as your Node IP. The port `30008` is defined in your `nodePort.yml`.*

### Method C: Testing via Port-Forward

This is a direct way to tunnel traffic from your local machine to the service.

1. **Start the port-forwarding for the service:**
```bash
kubectl port-forward service/todoapp-service 8080:80 -n todoapp
```


2. **Open the application:**
Visit [http://localhost:8080/api/health/](https://www.google.com/search?q=http://localhost:8080/api/health/) in your browser.
---

## 3. Verification Commands

To ensure all components are working correctly and labels are properly applied:

* **Check Pod labels:**
```bash
kubectl get pods -n todoapp --show-labels
```


* **Verify Service Endpoints (Should show 2 IP addresses):**
```bash
kubectl get endpoints todoapp-service -n todoapp
```


* **Check Environment Variables in a Pod:**
```bash
kubectl exec todoapp -n todoapp -- printenv | grep DEBUG
```
