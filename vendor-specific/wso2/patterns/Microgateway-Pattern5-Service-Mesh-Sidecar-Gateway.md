## Service Mesh sidecar API gateway pattern

### Introduction
If your enterprise is deeply integrated with microservices and wanted a fully fledged microservices implementation, service mesh is a pattern which you cannot ignore. Service mesh defines a network to communicate between microservices with a similar level of protection on top of them as external communications. Some might think this as an overkill but there are enough practical examples where people has implemented microservices with service meshes for better security, visibility and management of the overall microservices implementation. WSO2 API Microgateway can be used as a sidecar proxy in a service mesh kind of microservices architecture. It provides capabilities like authentication, rate limiting, circuit breaker, timeouts as well as monitoring. 

### Architecture
![Service Mesh sidecar API gateway pattern](../images/Microgateway-Pattern5-Service-Mesh-Sidecar-Gateway.png)
Figure 1: Service Mesh sidecar API gateway pattern

In this deployment pattern, each microservice has an API Microgateway running alongside it in the same localhost. In kubernetes, both microservice and the API microgateway runs in the same pod (can be in different containers). All the communications across microservices will go through API microgateway for ingress and egress traffic. WSO2 API Manager analytics component can be used to analyze the interactions happening through the sidecar gateway.
