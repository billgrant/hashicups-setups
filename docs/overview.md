## Introduction 

HashiCups is a coffee order management system for fictional customers. 
The HashiCups application is only used for demonstration purposes, such as tutorials, webinars, and demos.
HashiCups was designed in a microservices architecture with the intent to help you showcase the strength of HashiCorp products.

This page will provide you with an overview of the HashiCups architecture and a better understand of the services that makeup HashiCups.


## Architecture

HashiCups is made up by five different microservices; **frontend**, **public-api**, **product-api**, **payments**, and **postgress**. 
Each service depends on another service. As a result, you must deploy all services to have a functional HashiCups application.

The following image displays the information and connectivity flow of HashiCups.

![Architecture](../public/img/architecture.png)


### Frontend service

The frontend service is a React.js application that interacts with the **public-api** through GraphQL API requests. Users are, by default, unauthenticated when the application loads in the browser.  As an unauthenticated user, you can only browse the coffee selection list. To purchase a coffee, the user must log into the application.

To purchase a coffee or a set of coffees, the user must log in to the application. HashiCups does not come with a default user id created. Therefore users must make their user through the login option in the righthand side navigation menu.

The frontend service is designed to be a client application. Thus, all API calls from the frontend service will originate from the user's browser. The frontend service can be configured to be consumed with a reverse proxy, so API requests to the **public-api** are sourced from the reverse proxy instance. An example of this setup is available in the [docker-compose-deployment-reverse-proxy-nginx](../docker-compose-deployment-reverse-proxy-nginx/README.md) folder.

The `frontend` service is not dependent on other services to boot up successfully. However, should any of the the downstream services be unavaiable, then the landing page will not display a list of coffees. Instead the user will be shown an error message stating, `Unable to query all coffees
Check the console for error messages`.

Visit the [frontend](frontend.md) page to learn more about the frontend service and how to configure the service.

### Public-api service

The **public-api** service is the primary and only target endpoint to which the **front-end** service connects. The **public-api** service is responsible for facilitating API calls to two other downstream services; **product-api**, and the **payments**. The `public-api` is not dependent on other services to boot up successfully. 

Visit the [public-api](public-api.md) page to learn more about the ***public-api** service and how to configure the service.


### Product-api service

The **product-api** is a downstream service of the **public-api**, and the service is responsible for interacting with the Postgres database, also known as the **postgres** service.
The **product-api** is a REST API, unlike the **public-api**, which is a GraphQL API. The main purpose of this API is to query the database for available coffees, create orders, and update/gather the ingredients in a coffee. The **product-api** plays a critical role in the authentication logic. It's the service that validates login information (id/password), and the service is responsible for creating users and storing the user information in the database.  

Unlike other services, the **product-api** cannot boot up successfully if the **postgres** service service is unavailable. We recommend you deploy the **postgres** service before deploying the **product-api** service.

Visit the [product-api](product-api.md) page to learn more about the ***product-api** service and how to configure the service.

### Payments service

### Postgres service