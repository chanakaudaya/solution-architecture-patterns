## Layered Architecture Pattern

### Introduction
Enterprise software systems needs to build assuming that every functional requirement can be varied over the time. Scope creeps and requirement changes are pretty common in enterprise software systems. The business leaders always wants to put the best piece of functionality in front of their customers no matter what is going on down under. 

Software systems are not started in a manner that they are well thought out at the beginning. The reason was that, those systems had limited capabilities and many other challenges like hardware capabilities, sofrware capabilities, cost. It is understandable that these systems are not in the same way that enterprise architects or business leaders wants it to be. It is the challenge ahead of a solutions architect to build a modular system which can be used for next 10 years without much hassle. 

### Requirement identification

Enterprise software systems are typically running down under and most of the end users like employees, partners and customers think that as a luxury in their hands and expect it to run 24x7x365. Users do not care the actual information coming in and how they are coming in. But the system designers have to think about a lot of factors when designing these systems. Here are some of the basic requirements which needs to be addressed with an enterprise software system.

- Availability
- Performance
- Security
- Monitoring
- Quality of information

### Layered architecture

Instead of building a one large monolith which has all these capabilities, the solutions architects over the time has identified that building a system with a layered architecture provides more flexibility when it comes to adding more and more features based on the business requirements. It also allows each layers to be independently scale, update and have their own technology which is best suited for the task. Building a layered architecture requires the designers of this architecture to understand what each layer does and how these layers are interact with each other so that those layers are loosely coupled. 



