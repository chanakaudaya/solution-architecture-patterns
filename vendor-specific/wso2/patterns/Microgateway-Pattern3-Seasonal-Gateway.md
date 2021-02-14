## Seasonal API Gateway pattern

### Introduction
If you have already deployed WSO2 API Manager (all-in-one) in a centralized manner and you are happy with the setup, deploying that in a fully distributed manner would be an overkill. But the drawback of that approach is that, if your traffic fluctuates based on the seasons, scaling all the components would add additional complexity and will take more time than expected. 

### Architecture

![Seasonal API Gateway pattern](../images/Microgateway-Pattern3-Seasonal-Gateway.png)
Figure 1: Seasonal API Gateway pattern

With the API Microgatway, you can quickly set up a seasonal gateway with a selected set of APIs and scale them up and down as necessary without impacting the existing deployment. 
