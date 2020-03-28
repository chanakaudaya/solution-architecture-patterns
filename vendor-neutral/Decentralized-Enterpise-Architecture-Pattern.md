## Decentralized Enterprise Architecture Pattern

### Introduction
Enterprise software has been used to run on mainframes or server racks with multiple CPU units and heaps of memory. The amount of energy needed to run these machines as well as the high maintenance costs has become a concern for the CIOs and CTOs. Then they decided to go with a virtualization platform where it allows the enterprise software to be run on much lesser resources. After sometime, these virtual machines also looked premature and wasting a lot of resources which can be used for other purposes. That is where the container runtimes become useful. With the rise of docker, people started thinking about running applications with the same level of isolation as virtual machines without sacrificing the compute resources to run an entire operating system on a VM. 

Container based deployments are becoming more and more common within enterprise software ecosystem and there are many cloud service providers offer managed container services. This solutions architecture pattern explains how an entire enterprise software system can be built using a container based deployment model. Some of the concepts mentioned in this document may use terminology from some specific technologies. But that does not mean that this pattern can be applied in a vendor neutral manner. 

### Architecture

![Decentralized-Enterprise-Architecture-Pattern](images/Decentralized-Enterprise-Architecture-Pattern.png)

Let's start deciphering the above architecture diagram from the left hand side where we can find the most important entities for an enterprise which is the end users or the consumers who uses this nicely architected enterprise software system. These users can come from different channels like mobile, web and partner systems. To provide business functionality to these different channels, we can use REST APIs so that it is easier to implement from the client side. 


#### No API Manager
The well-known approach to expose a set of REST APIs is to use a fully blown API Management platform and start creating APIs in an API gateway. But in this architecture pattern, we are proposing somewhat different style of architecture without using any monolithic API Management platform. Instead, we can use one of the following components in place of ingress controller block

- Ingress Controller (Kubernetes)
- Load balancer
- Reverse Proxy

The most common questions which arises with this design choice is that where do we apply the standard QOS functionalities to the services. 

#### Everything is a service (in most cases a micro one)
Instead of putting a layer which is capable of providing centralized QOS capabilities, we are using a decentralized component called a sidecar proxy (or micro gateway or edge gateway) to provide the functionalities like

- Authentication
- Authorization
- Throttling
- Monitoring

In container world, you can package your application code along with any dependencies into a container image using a container descriptor file (e.g. Dockerfile) and it became a container in the runtime which runs your application. Let's consider every application which you develop as a Service (in MSA) or a Function (in Serverless). All these services needs some form of QOS capabilities which are mentioned above. Instead of implementing these capabilities at the service level or using a centralized API gateway, we can use a component called "Sidecar Proxy" which can provide the same functionality. Regardless of the technology you used to implement your service (.Net, Java, Go, Node, etc.), these QOS functionalities will be provided through this sidecar proxy. If your applications requires a database to store some persistent data, you may need a supportive or dependent service or function. So every service can consist of 

- Main Service or Function
- Sidecar Proxy
- Supportive Service or Function

All the above components can be run in separate containers or within the same container if there is no container orchestration capability available. In a platform like kubernetes, these 3 components can be run within the same "Pod" as depicted in the figure.

#### Scaling services
Once we have the service deployed in a Pod, we need to look at the next level functionality like

- Availability
- Performance
- Scalability

This is where the container orchestration systems comes into rescue. By using a container orchestration system like kubernetes, pods can be scaled to achieve the above mentioned capabilities. The scaling the "Pods" will make it harder to the ingress controller to route traffic since it has to modify the endpoints when the containers come and go. That is where the concept like "Service" helps out. 

Grouping multiple pods into a "Service" make it easier to the consumers. Instead of calling multiple endpoints, any external party can call the service URL to make a call to any of the "Pods". Service layer will do the required load balancing across the pods based on a given algorithm. 

These services can be mapped into an externally accessible IP address with the usage of a ingress controller. It allows external users like web, mobile and partner systems to consumer the services or functions which are deployed in containers within Pods and exposed through Services. 

#### Controlling the services
Since we decided to get rid of an API Management platform at the beginning, we should have some layer of functionality which can control the configurations of each and every sidecar proxy as and when necessary and capture valuable analytics information. A control plane is the place where this configuration of various QOS capabilities are handled. The deployment model we are proposing here is a service mesh and the role of a control plane is to control and configure the mesh. 

#### Monitoring and Analytics
In a large enterprise implementation, this architecture may consists of 100s or 1000s of services which are interacting with each other as well as consumed by external consumers. Having a proper monitoring and analytics capability is crucial when we want to 

- Troubleshoot issues in services
- Analyze the usage of services
- Plan the business operations

Monitoring and analytics can be implemented as an independent component. The standard tracing technologies like "Opentracing" can be used to publish analytics data so that services developed in polyglot manner can be monitored through a centralized analytics component which is independent of any technology used for service implementations. 

### Conclusion
This solutions architecture pattern is quite new and challenge some of the well established concept like API Management platforms while offering the freedom to the developers to use any technology they like when implementing the business logic. All the supportive functionalities are provided through other components. There are new set of technology vendors who are developing entire platforms which abstract out all the functionalities except service development and sell the entire platform using this type of architecture pattern underneath. 
