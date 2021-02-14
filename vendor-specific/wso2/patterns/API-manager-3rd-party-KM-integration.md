## Introduction
WSO2 API Manager comes with a built-in key management server that is used for OAuth2 based security within the product. Sometimes, customers like to use their existing Identity Provider (IdP) as the OAuth2 key management server. There can be several reasons for this type of requirement. Some of them are

- Having a central control over user identities and tokens rather than splitting that into application-specific components
- High-level compliance requirements like certain hashing algorithms, encryption methods used for storing the tokens and keys
- Feature level mismatches of the default key manager

If the requirement is just to share the same user base with the API platform, going with an approach like identity federation is recommended over implementing a 3rd party key manager.

## Architecture
WSO2 API Manager comes with a componentized architecture where each component can be deployed separately if required. Even within a single runtime, these components run with clear separation by communicating over well defined APIs. This API driven product design allows the product to be extended by using these interfaces. Using an external IdP as the key management component of the WSO2 API Manager is possible with this design. Let’s take a look at how this can be achieved.

![WSO2 APIM 3rd party KM](../images/WSO2APIM-3rdParty-Authorization-Server-Integration.png)
Figure 1: WSO2 API Manager 3rd party key manager integration

The above figure depicts the standard integration of an external Identity Provider as the 3rd party key manager for WSO2 API Manager. There are 4 main components of API Manager is depicted here along with the users of those profiles.
Let’s try to understand the integration in detail. There are certain functionalities provided by the key manager within the WSO2 API Manager.

- API Publisher will register API resources at the key manager (which acts as the authorization server) so that these resources can be protected with OAuth2
- API Store will register clients (apps) within the key manager and provide clientID/clientSecret pair which can be used to generate access tokens for API consumption
- Client applications will register themselves with the key manager as well as generate access tokens for API consumption
- API Gateway will communicate with key manager to introspect the tokens for validity as well as to validate the API subscriptions

A detailed explanation of the requests and the responses of the above interactions can be found in[2]. Implementing a custom key manager component requires to override these core functionalities with this custom implementation.
In addition to the aforementioned functionalities, the key manager component also provides support for subscription validation, JWT generation, token caching, etc.

## How does it work?
WSO2 API Manager provides 2 main extension points to implement the functionalities required by the key manager. Those interfaces are

- Key Manager interface — This interface is used to implement the resource registration, dynamic client registration, token generation/revocation related functionalities
- Key Validation handler — This interface is used to implement the token introspection capabilities at the key manager with advanced capabilities like scope validation, subscription validation, and consumer token generation

In a typical workflow of API management, here is how this custom implementation works and how it connects with other components like user store, database and the rest of the API management components.

1) API developers will log into the API publisher and create APIs. This process will invoke the key manager component and register the resources at the resource server (key manager) side. Custom implementation will route this request to the external IdP and register the resources at the IdP side.

2) Once the API is published to the store, API consumers (application developers) log into the API store and create applications to subscribe to these APIs. This application creation will trigger the dynamic client registration endpoint of the key manager component and it will delegate the functionality to the external IdP through the custom extension. The application IDs and secrets are stored at the external IdP side. In addition to that, functionalities like the update, delete applications are also supported by this implementation.

3) Once the application is registered, the user needs to subscribe to APIs using this application. This subscription information will be stored in the API management database which is shared between store, publisher and key manager components. Once subscribed, users can generate access tokens using a supported grant type. At this point, the client will call the /token endpoint of the key manager which will eventually route this request to the external IdP using the custom implementation. This external IdP will generate the access token and store them at IdP side.

4) The client application will use this token to consume the resources by calling the resource server which is the gateway component in this case.

5) API gateway will call the introspection endpoint (/tokeninfo) of the key manager to validate the token and the subscription details. Custom implementation will call the introspection endpoint of the external IdP for token validation and get the status of the token with any additional details like scope, userId, clientId, and validity.

5a) At the same time, the key manager default implementation will validate the subscription details by checking on the API management database which is connected to the key manager. Additional customization can be done with the extension of the KeyValidationHanlder interface as mentioned previously.

Note: In the above figure, it has connected the same user store (LDAP/AD/JDBC) to both WSO2 API Manager and the IdP. This will make the integration flowless since additional user-level information can be extracted for the users with this approach. If we don’t use the same user store, we can’t support scope validation and backend JWT with user claims. But this is optional and based on the grant type and the backend implementation, you can decide on whether to share the user store or not.
A sequence diagram view of the above mentioned workflow (starting from login into API Store) is depicted in the below figure.

![APIM 3rd party KM integration workflow](../images/API-Store-integrate-with-3rd-party-KM.png)
Figure 2: Sequence diagram view of component interaction

## Reference implementation
You can find a reference implementation of a custom key manager with SurfOauth as an external IdP in the below medium post. You can follow the instructions mentioned there to test the functionality.

https://medium.com/@nuwandiwickramasinghe/using-wso2-api-manager-store-with-third-party-key-manager-50e2428ba76f

The GitHub repository mentioned in the above article had a small bug in the implementation and it is fixed in the following repository (a PR has also been sent to the original repository). If you are building the sample code, make sure you use the below repository. The rest of the steps mentioned in the above article are working fine.

https://github.com/chanakaudaya/surf-oauth-demo

You can also find another implementation of 3rd party key manager for Okta in the GitHub repository mentioned below.

https://github.com/wso2-extensions/apim-keymanager-okta

### References:
[1] https://medium.com/@nuwandiwickramasinghe/using-wso2-api-manager-store-with-third-party-key-manager-50e2428ba76f

[2] http://blog.facilelogin.com/2014/10/revamping-wso2-api-manager-key.html

[3] https://wso2.com/library/article/2019/09/architecture-for-third-party-authorization-server-integration/

[4] https://github.com/wso2-extensions/apim-keymanager-okta

[5] https://github.com/chanakaudaya/surf-oauth-demo
