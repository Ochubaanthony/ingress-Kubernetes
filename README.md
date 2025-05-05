# ingress-Kubernetes
ingress as load balancer


RUN
minikube ip
curl -L http://192.168.58.2:30007/demo -v

Documentation site
https://kubernetes.io/docs/concepts/services-networking/ingress/

CREATE A INGRESS FILE
nano ingress.yml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-example
spec:
  rules:
  - host: "foo.bar.com"
    http:
      paths:
      - pathType: Prefix
        path: "/bar"
        backend:
          service:
            name: python-django-sample-app
            port:
              number: 80


kubectl apply -f ingress.yml
kubectl get ingress
curl -L http://foo.bar.com/bar -v       	(domain base routing)	this link is not working because the Ingress Controller is not install


Install nginx controller 
https://kubernetes.io/docs/tasks/access-application-cluster/ingress-minikube/

RUN
minikube addons enable ingress

kubectl get pods -A | grep nginx
kubectl logs ingress-nginx-controller-56d7c84fd4-wppwm -n ingress-nginx
kubectl get ingress




SUMMARY
Explains the concept of Kubernetes Ingress, focusing on why it is needed and how to implement it practically.

Why Ingress is Important

Many people struggle with Ingress because:
	1.	They don’t understand why it’s needed.
	2.	Practical setup on local clusters (like Minikube) often fails without proper configuration.

Before Ingress (Pre-Kubernetes v1.1)
	•	Applications were exposed using Services (ClusterIP, NodePort, LoadBalancer).
	•	LoadBalancer services provided external access but had limitations:
	•	Only round-robin load balancing.
	•	No advanced traffic routing like path-based or host-based routing.
	•	High cost: each service with a LoadBalancer required a static public IP, which cloud providers charge for.

Legacy Systems (Before Kubernetes)
	•	Enterprises used advanced load balancers like Nginx, F5 on physical/virtual machines.
	•	Features included:
	•	Path-based routing (e.g., /app1, /app2)
	•	Host-based routing (e.g., app1.example.com)
	•	Sticky sessions
	•	Ratio-based traffic control
	•	Whitelisting/blacklisting
	•	TLS/SSL termination
	•	Web Application Firewall (WAF)

Problems Without Ingress in Kubernetes
	1.	Limited functionality in Kubernetes Services.
	2.	Expensive and inefficient use of cloud load balancers—each service required a separate static IP.

What Ingress Solves
	•	Introduces an Ingress Controller, which acts as a single entry point into the cluster.
	•	Supports:
	•	Advanced routing rules (path, host-based)
	•	TLS termination
	•	Centralized traffic management
	•	Reduces the number of external IPs needed (single LoadBalancer for multiple services)

Practical Tips
	•	Set up can be tricky on local clusters, but Abhishek offers an end-to-end demo video (linked in his description).
	•	Understanding the previous lesson on Kubernetes Services (Day 37) is essential before learning Ingress.

Why Kubernetes Ingress Is Needed – Two Main Problems:
	1.	Lack of Advanced Load Balancing Capabilities in Kubernetes Services:
	•	Traditional Kubernetes services (like ClusterIP, NodePort, LoadBalancer) offer only basic load balancing (mainly round-robin).
	•	Missing features include:
	•	Sticky sessions
	•	TLS/HTTPS support
	•	Path-based and host-based routing
	•	IP whitelisting/blacklisting
	•	Ratio-based traffic distribution
	•	Web application firewall (WAF) capabilities, etc.
	•	In comparison, enterprise load balancers (e.g., Nginx, F5, HAProxy) offered all these and more.
	2.	Costly Use of LoadBalancer-Type Services:
	•	Each LoadBalancer service in Kubernetes gets its own static public IP.
	•	For organizations with hundreds or thousands of services (e.g., Amazon), cloud providers charge for each static IP—significantly increasing costs.
	•	In legacy infrastructure, one load balancer handled multiple routes and apps using domain/path routing.

⸻

How Kubernetes Ingress Solves These Problems:
	•	Kubernetes introduced Ingress to allow advanced routing using a single external IP, reducing costs and enabling enterprise-grade features.
	•	Ingress Resource:
	•	A Kubernetes object where users define routing rules (e.g., path-based, host-based).
	•	Written in YAML—acts as a configuration document.
	•	Ingress Controller:
	•	A software component that interprets and implements the Ingress rules.
	•	Not built into Kubernetes core—you must install it.
	•	Examples include:
	•	Nginx Ingress Controller
	•	HAProxy Ingress
	•	Traefik
	•	Ambassador, etc.
	•	Can be installed via Helm or manifests.

⸻

Architecture Flow Recap:
	1.	User creates:
	•	Pod
	•	Service
	•	Ingress resource with routing rules (e.g., /api → service1, /web → service2)
	2.	Ingress Controller:
	•	Watches for changes in the Ingress resource
	•	Configures its internal load balancer accordingly
	•	Routes external traffic to the correct service/pod

⸻

Key Takeaways (Especially for Interviews):
	•	Kubernetes services are limited in routing and security features.
	•	Ingress provides:
	•	Cost efficiency (single IP)
	•	Advanced routing (path, host-based)
	•	Integration with existing enterprise-grade load balancers
	•	You must install and configure an Ingress Controller—it’s not available by default.
	•	Popular use case: Using Nginx Ingress Controller to expose multiple services under a single IP/domain with different paths.

What You (the User) Need to Do – Step-by-Step Overview:
	1.	Choose and Deploy an Ingress Controller (one-time setup):
	•	Example: If you used Nginx in the virtual machine world, you can continue with the Nginx Ingress Controller.
	•	Visit the load balancer’s GitHub page (e.g., Nginx) to get deployment instructions (via Helm or YAML).
	•	Install the Ingress Controller into your Kubernetes cluster.
	2.	Create Ingress Resources (for each routing need):
	•	Define how external traffic should be routed to internal services.
	•	Based on needs, you can define:
	•	Path-based routing (/api → service1, /web → service2)
	•	Host-based routing (api.example.com → service1, web.example.com → service2)
	•	TLS-based routing (HTTPS with certificates)
	•	One Ingress resource can handle multiple services—no need for one-to-one mappings.

⸻

Core Concept Recap:
	•	Ingress Controller = The actual load balancer implementation (Nginx, F5, Traefik, etc.)
	•	Ingress Resource = A YAML configuration that defines routing rules
	•	This solves two major problems:
	1.	Lack of enterprise-grade load balancing in native Kubernetes services.
	2.	High cloud costs from exposing too many LoadBalancer-type services.

⸻

Final Thought:

Ingress is essential in production Kubernetes environments. It brings back the power of traditional load balancers while being cost-efficient and flexible in modern cloud-native setups.

Recap of Problem Number Two:
	•	Problem 2:
When using Kubernetes Service of type LoadBalancer, cloud providers charge for each public IP address.
	•	If you have 100 services, that could mean 100 IPs—and high cost.
	•	Ingress solves this by letting you expose multiple services via one IP.

⸻

Why Ingress Controller Is Essential:
	•	Creating an Ingress resource alone does nothing unless you have an Ingress Controller running on your cluster.
	•	The Ingress Controller is what actually processes Ingress rules and routes traffic to services.

⸻

What Is an Ingress Controller?
	•	It’s a load balancer you deploy inside Kubernetes.
	•	Options include:
	•	NGINX Ingress Controller
	•	F5 BIG-IP
	•	HAProxy
	•	Traefik, etc.
	•	You choose one based on your requirements and infrastructure.

⸻

How It Works:
	1.	DevOps engineers deploy an Ingress Controller (e.g., NGINX).
	2.	Developers create Ingress resources (YAML files with routing rules).
	3.	The Ingress Controller watches these resources.
	4.	Based on the configuration (e.g., path-based, host-based), it routes traffic accordingly.

⸻

Key Takeaway for Interviews:
	•	Ingress = smart, centralized routing layer in Kubernetes.
	•	It solves cost and functionality gaps left by LoadBalancer services.

