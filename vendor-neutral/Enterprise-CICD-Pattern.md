## Enterprise Continous Integration Continous Deployment (CICD) Pattern

### Introduction
Enterprise deployments used to span across several teams and took more time than it was expected. The mismatch of timelines, goals, resources and skills made it really hard to do a release within the enterprise within a desired time/date. It would have been fine when there was a little competition in the market. But that time has come to an end and the competition has become ever so increasing that some competitors do deployments and releases several times within a day. These modern enterprises have followed up the "Continous Integration(CI)/ Continous Deployment (CD)" pattern when implementing enterprise software. 

Within an enterprise software system, there are many types of software running. The below list contains a few types which are used within enterprise.
- On premise software developed in-house 
- On premise software running on top of a server runtime (e.g. tomcat, ESB, etc.)
- Commercial Off The Shelf (COTS) software developed by a 3rd party vendor
- Cloud based SaaS solutions
- Hybrid applications which runs part of their solution on cloud and the rest on premise

The above list contains a fraction of different types of software running in the enterprise. As enterprise architects or engineers, you should identify which components of which software needs to be maintained and managed by your team. Most of the time, cloud based solutions are maintained and managed by the cloud vendor and COTS systems also has less maintenance overhead. But the on premise systems requires more attention from you since those systems are maintained and managed by the teams within the enterprise. 

### Advantages of CI/CD within enterprise
By implementing a proper CI/CD process within your enterprise, you can achieve 
- Flexible and Reliable product/service releases
- Less impact with human errors
- Short time to market 
- Be on the edge of the technology
- Instant recovery of system errors/failures

### Solutions architecture for a CICD process 
Designing a continous integration continous deployment process for an enterprise requires an understanding of existing software systems within the enterprise. In any type of system which was described above, there are 2 types of development activities take place. 

1. Development of use cases with business logic using one of the below methods
- write code using a programming language 
- write code using a DSL (Domain Specific Language)
- Configure product using a UI and generate configurations and metadata

2. Configure the server runtimes by either of the below methods
- modifying the server configuration files
- modifying the server libraries for update/upgrade

A properly implemented CICD process should capture any of the above development activities as a system change and execute the entire CICD pipeline in such a scenario. The below figure depicts a process which can be used to implement a CICD process within your enterprise.

![Enterprise-CICD-Pattern](images/Enterprise-CICD-Pattern.png)

As depicted in the above diagram, the sequence of events occurs when there is a development activity happens is mentioned below.

1. The developers use an IDE or a code/configuration editor to make the change to the source code or a configuration file. It is advisable to use a configuration change to apply any change in the binary (e.g. patch, update or upgrade). Users can keep a configuration file with binary timestamp. When there is a new binary, modifying this timestamp will trigger the process.
2. The change in the source repository triggers a process in the CI server and it builds the developed artefacts. These artefacts are deployed into a artefact repository like nexus. 
3. CI server will trigger another process to run the configuration management tool and create a runtime with environment specific parameters and artefacts blended into the binariesalong with the configurations. This runtime is then packaged into a docker image.
- 3b). This docker image is deployed into one of the environments (dev, uat, staging, etc.)
4. CI server will deploy the test scripts to the server which is running the test client and trigger a test run.
5. Test client will execute the tests against the environment which has the latest runtime artefacts deployed in step 3b.
6. Once the testing is passed, CI server will trigger the config management tool and build the docker image with relevant environment specific configurations and artefacts for the following environment.
- 6b). Deploy the docker image to the next environment

The above mentioned process can be extended and implemented to support various kinds of software development within the enterprise. Based on the type of software, you may need to modify some components in this architecture. 
