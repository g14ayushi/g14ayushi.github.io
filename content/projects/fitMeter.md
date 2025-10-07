+++
author = "Ayushi Gupta"
title = "Fit-Meter"
date = "2025-09-20"
description = "Fit-Meter"
tags = [
    "Microservice",
    "SpringBoot",
    "API Gateway",
    "Eureka Server",
    "Keycloak",
    "AI Assistant"
]
+++

## <span style="color:gray;">About the Application</span>  

This fitness application is designed to be your **personal digital fitness coach**.  
It doesn’t just track your activities — it helps you understand them.  
With every activity you perform, the app:  

- **Measures your performance** → tracks details like calories burned, duration, distance, and speed.  
- **Analyzes your progress** → identifies strengths and areas that need more focus.  
- **Provides smart recommendations** → suggests what activity you should try next.  
- **Keeps you safe** → gives guidance on precautions and safety measures to avoid injury or overtraining.

The goal is simple: to turn raw fitness data into **actionable insights** that help you stay consistent, improve steadily, and build a healthier lifestyle.  

## <span style="color:gray;">Project Setup Guide</span>

This guide helps you set up and run the full backend project locally, including all microservices, Eureka server, API Gateway, RabbitMQ, and Keycloak using Docker.

---
### <span style="color:teal;">1. Prerequisites</span>

Make sure you have the following installed:

- [Java 17+](https://www.oracle.com/java/technologies/javase-jdk17-downloads.html)
- [Maven](https://maven.apache.org/install.html)
- [Docker & Docker Compose](https://www.docker.com/get-started)
- [Postman](https://www.postman.com/downloads/) (optional, for testing APIs)
- **Ports**: Ensure the following ports are free:
  - Keycloak: 8080
  - RabbitMQ: 5672
  - Eureka Server: 8761
  - Config Server: 8888
  - Gateway: 8081
  - Microservices: 8082, 8083, 8084 (users, activity, AI)

---

### <span style="color:teal;">2. Project Structure</span>

Project contains the following modules:

<div style="padding:16px; border-radius:8px; font-family:monospace; font-size:14px; line-height:1.5; overflow-x:auto; border:1px solid #ddd; background:#ffffff !important; color:#000000 !important;">
<pre style="margin:0; background:#ffffff !important; color:#000000 !important;">
backend/
├── config-server
├── eureka-server
├── api-gateway
├── user-service
├── activity-service
├── ai-service
├── docker images running in backgroud
│   ├── rabbitmq
│   └── keycloak
</pre>
</div>

- **config-server:** Centralized configuration for all microservices.  
- **eureka-server:** Service registry for microservices.  
- **api-gateway:** Gateway for routing API requests to services.  
- **user-service:** Handles user registration and authentication.  
- **activity-service:** Tracks fitness activities.  
- **ai-service:** Provides AI-powered analysis and recommendations.  
- **docker:** Contains Docker setup for RabbitMQ and Keycloak.

### <span style="color:teal;">3. Setup Docker Services</span>

1. **RabbitMQ**

Run RabbitMQ container:

```bash
docker run -d --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3-management
```
- Management UI: http://localhost:15672
- Default credentials: guest / guest

2. **KeyCloak**

Run KeyCloak container:

```bash
docker run -d --name keycloak -p 8181:8080 \
-e KEYCLOAK_USER=admin \
-e KEYCLOAK_PASSWORD=admin \
quay.io/keycloak/keycloak:20.0.3 start-dev
```

- Admin console → http://localhost:8080
- Default credentials → admin/admin <br>

Configure realms, clients, and users via Keycloak UI.

### <span style="color:teal;">4. Configure the Project</span>

1. **Clone the Repository**
```bash
git clone https://github.com/g14ayushi/FitMeter
cd FitMeter
```

2. **Configure AI Service**

Inside configserver/src/main/resources/config/ai-service.yml,
you will see the following **environment variables**:

```yaml
gemini:
  api:
    url: ${GEMINI_API_URL}
    key: ${GEMINI_API_KEY}
```
Set the environment variables before running the service:<br>
For MacOs:
```bash
  export GEMINI_API_URL=https://api.gemini.com/v1
  export GEMINI_API_KEY=your-secret-key
```
These will be used by the AI Service for external API integration.

### <span style="color:teal;">5. Start Config Server and Eureka Server</span>

1. **Run Config Server:**
```bash
cd configserver
mvn spring-boot:run
```
Default Port: 8888<br>

2. **Run Eureka Server:**
```bash
cd eurekaserver
mvn spring-boot:run
```
Once it starts, you can access the them Dashboard at:
```
http://localhost:8761
```

### <span style="color:teal;">6. Start API Gateway</span>
```bash
cd gateway
mvn spring-boot:run
```
- Gateway port: 8081
- All requests are routed via the gateway.

### <span style="color:teal;">7. Start Microservices</span>

Start the microservices in this order:

1. **User Service**

```bash
cd user-service
mvn spring-boot:run
```

2. **Activity Service**

```bash
cd activity-service
mvn spring-boot:run
```

3. **AI Service**
```bash
cd ai-service
mvn spring-boot:run
```
- Ensure each service `application.yml` has the correct configuration

### <span style="color:teal;">8. Access RabbitMQ</span>

- Management UI → [http://localhost:15672](http://localhost:15672)  
- Default credentials: **guest / guest**

Microservices communicate asynchronously through RabbitMQ queues.

### <span style="color:teal;">9. Access Keycloak</span>

- Admin Console → [http://localhost:8080](http://localhost:8080)  
- Login with the admin account you created during setup.  

Steps:
1. Create a **Realm** (e.g., `fitmeter`).  
2. Add **Clients** (microservices and gateway).  
3. Configure **Roles** (e.g., `USER`, `ADMIN`).  
4. Create **Test Users** and assign roles.  

Each microservice validates JWT tokens issued by Keycloak for OAuth2 authentication.

### <span style="color:teal;">10. Test APIs</span>
Use **Postman** or **cURL** to test endpoints via the API Gateway (`http://localhost:8081`).
1. **Register a User**
```bash
POST http://localhost:8081/users/register
```
2. **Add an Activity**
```bash
POST http://localhost:8081/activities/register
```
3. **Get AI Recommendations for an Activity**
```bash
GET http://localhost:8081/recommendations/activity/<activityId>
```

- All requests must include the **Authorization header** with a valid Keycloak token:  
`Authorization: Bearer <access_token>`


## <span style="color:gray;">How Users Can Use the Application </span>

Getting started with our fitness app is simple and straightforward.  

**<span style="color:teal;">1. Register Yourself</span>**  

<div style="margin-left:20px;">
  Create an account in the application with your basic details. This allows the app to personalize recommendations based on your profile.
</div>  

**<span style="color:teal;">2. Log Your Activities</span>**  

<div style="margin-left:20px;">
  After completing an activity (like walking, running, cycling, or a workout), you can record it in the app. Along with the activity type, you can also add important details such as calories burned, duration, distance covered, average speed, and any other relevant information.
</div>
  
**<span style="color:teal;">3. Get Personalized Recommendations & Analysis</span>**  

<div style="margin-left:20px;">
Once your data is logged, the app analyzes it to provide personalized recommendations (what activity you should do next), focus areas (where you need to improve), and safety tips (how to avoid injuries and overtraining).  
The idea is to make fitness tracking more than just numbers. With clear insights and tailored guidance, you can focus on **improving your health step by step**.  
</div>

![User Flow in Fitness App](/fit-meter/usersFlow.png)

## <span style="color:gray;">System Architecture</span>  

Our application follows a **microservice architecture** instead of a traditional monolithic design.  
It is composed of **three independent services**:  

- **User Service** – manages user registration, authentication, and profiles.  
- **Activity Service** – handles logging and storage of fitness activities with metadata.  
- **AI Service** – analyzes user data and generates personalized recommendations.  

Each service has its **own database**, ensuring data isolation and independence.  
The services communicate with each other using **Eureka Server** for service discovery and **Spring WebClient** for inter-service calls.  
To simplify external access, there is a **single endpoint** exposed through the **API Gateway**. Every incoming request is routed to the correct microservice.  
For authentication, the application uses **Keycloak** with **OAuth2 + PKCE flow** to secure requests and manage user authorization.  

### <span style="color:teal;">Architecture Diagram</span>  

![System Architecture Diagram](/fit-meter/systemDiagram.png)

## <span style="color:gray;">Core Components Explained</span>

### <a href="https://www.geeksforgeeks.org/software-engineering/monolithic-vs-microservices-architecture/" style="color:teal;">Microservice vs Monolithic Architecture</a>  

- **Monolithic** → One large application where all features are tightly coupled and deployed together. Easier to start, but harder to scale and maintain.  
- **Microservices** → Application is divided into smaller, independent services that communicate with each other. Each service can be developed, deployed, and scaled independently.

---

### <a href="https://cloud.spring.io/spring-cloud-netflix/reference/html/" style="color:teal;">Eureka Server</a>  

<div style="margin-left:20px;">
  A service discovery tool that keeps track of available microservices. Instead of hardcoding service locations, each microservice registers itself with Eureka, making dynamic communication possible.
</div>  

---

### <a href="https://docs.spring.io/spring-framework/reference/web/webflux-webclient.html" style="color:teal;">WebClient</a>  

<div style="margin-left:20px;">
 A non-blocking, reactive HTTP client in Spring used for inter-service communication. It helps microservices talk to each other efficiently.
</div>

---

### <a href="https://spring.io/projects/spring-cloud-gateway" style="color:teal;">API Gateway</a>  

<div style="margin-left:20px;">
  A single entry point for all clients. It routes incoming requests to the correct microservice, applies security checks, and hides
  internal service details from the outside world.
</div>

---

### <a href="https://www.keycloak.org/documentation" style="color:teal;">Keycloak</a>  
  
<div style="margin-left:20px;">
  An open-source Identity and Access Management (IAM) tool. It manages user authentication and authorization using OAuth2 with PKCE. This ensures secure login and protects APIs from unauthorized access.
</div>

---

### <a href="https://www.rabbitmq.com/" style="color:teal;">RabbitMQ</a>  

<div style="margin-left:20px;">
  A message broker that enables asynchronous communication between microservices. It allows services to exchange data
  through queues, ensuring reliable delivery, decoupling service dependencies, and improving system scalability.  
</div>

## <span style="color:gray;">Backend APIs</span>

#### <span style="color:teal;">User Service APIs</span>

- **Register a new user** </br>

<!-- <div style="margin-left:40px;">
  <strong style="color:teal;">POST</strong>
  <span style="color:gray;">/api/users/register</span><br>
  Authorization Header: Bearer &lt;access_token&gt;<br>
  Request Body:
  <pre><code>{
    "firstName" : "user1",
    "lastName" : "LastName1",
    "email" : "user1@gmail.com",
    "password": "Password1"
  }</code></pre>
</div>

<div style="margin-left:40px;">
  Response:
  <pre><code>{
    "firstName": "user1",
    "lastName": "LastName1",
    "email": "user1@gmail.com",
    "password": "dummyPassword123",
    "id": "86e4fffb-6570-4b2b-a85f-cde77d8103d6",
    "createdAt": "2025-09-15T18:14:20.732206",
    "updatedAt": "2025-09-15T18:14:20.732403",
    "keyCloakId": "49d45cbb-a7b0-46c6-a198-6728ce1dca52"
  }</code></pre>
</div> -->

<div style="padding:16px; border-radius:8px; font-family:monospace; font-size:14px; line-height:1.5; overflow-x:auto; border:1px solid #ddd;">
<pre style="background:#ffffff; color:#000000; margin:0; padding:16px; border-radius:8px;">
<span style="color:teal; font-weight:bold;">POST</span> <span style="color:gray;">/api/users/register</span>

<span style="color:darkblue;">Authorization</span> Header: Bearer &lt;access_token&gt;

<span style="color:darkblue;">Request Body:</span>
{
    <span style="color:brown;">"firstName"</span> : <span style="color:green;">"user1"</span>,
    <span style="color:brown;">"lastName"</span> : <span style="color:green;">"LastName1"</span>,
    <span style="color:brown;">"email"</span> : <span style="color:green;">"user1@gmail.com"</span>,
    <span style="color:brown;">"password"</span>: <span style="color:green;">"Password1"</span>
}

<span style="color:darkblue;">Response:</span>
{
    <span style="color:brown;">"firstName"</span>: <span style="color:green;">"user1"</span>,
    <span style="color:brown;">"lastName"</span>: <span style="color:green;">"LastName1"</span>,
    <span style="color:brown;">"email"</span>: <span style="color:green;">"user1@gmail.com"</span>,
    <span style="color:brown;">"password"</span>: <span style="color:green;">"dummyPassword123"</span>,
    <span style="color:brown;">"id"</span>: <span style="color:green;">"86e4fffb-6570-4b2b-a85f-cde77d8103d6"</span>,
    <span style="color:brown;">"createdAt"</span>: <span style="color:green;">"2025-09-15T18:14:20.732206"</span>,
    <span style="color:brown;">"updatedAt"</span>: <span style="color:green;">"2025-09-15T18:14:20.732403"</span>,
    <span style="color:brown;">"keyCloakId"</span>: <span style="color:green;">"49d45cbb-a7b0-46c6-a198-6728ce1dca52"</span>
}
</pre>
</div>




  <!-- ![Request Body](/fit-meter/user__register_request.png) -->
  <!-- ![Response](/fit-meter/user_register_response.png) -->

- **Fetch User Details** </br>

<div style="padding:16px; border-radius:8px; font-family:monospace; font-size:14px; line-height:1.5; overflow-x:auto; border:1px solid #ddd;">
<pre style="background:#ffffff; color:#000000; margin:0; padding:16px; border-radius:8px;">
<span style="color:teal; font-weight:bold;">GET</span> <span style="color:gray;">/api/users/&lt;id&gt;</span>

<span style="color:darkblue;">Authorization</span> Header: Bearer &lt;access_token&gt;

<span style="color:darkblue;">Response:</span>
{
    <span style="color:brown;">"firstName"</span>: <span style="color:green;">"user1"</span>,
    <span style="color:brown;">"lastName"</span>: <span style="color:green;">"LastName1"</span>,
    <span style="color:brown;">"email"</span>: <span style="color:green;">"user1@gmail.com"</span>,
    <span style="color:brown;">"password"</span>: <span style="color:green;">"dummyPassword123"</span>,
    <span style="color:brown;">"id"</span>: <span style="color:green;">"86e4fffb-6570-4b2b-a85f-cde77d8103d6"</span>,
    <span style="color:brown;">"createdAt"</span>: <span style="color:green;">"2025-09-15T18:14:20.732206"</span>,
    <span style="color:brown;">"updatedAt"</span>: <span style="color:green;">"2025-09-15T18:14:20.732403"</span>,
    <span style="color:brown;">"keyCloakId"</span>: <span style="color:green;">"49d45cbb-a7b0-46c6-a198-6728ce1dca52"</span>
}
</pre>
</div>

- **Validate User** </br>

<div style="padding:16px; border-radius:8px; font-family:monospace; font-size:14px; line-height:1.5; overflow-x:auto; border:1px solid #ddd;">
<pre style="background:#ffffff; color:#000000; margin:0; padding:16px; border-radius:8px;">
<span style="color:teal; font-weight:bold;">GET</span> <span style="color:gray;">/api/users/validate/&lt;keyCloakId&gt;</span>

<span style="color:darkblue;">Authorization</span> Header: Bearer &lt;access_token&gt;

<span style="color:darkblue;">Response:</span>
<span style="color:green; font-weight:bold;">true</span>
</pre>
</div>

#### <span style="color:teal;">Activity Service APIs</span>

- **Register an activity** </br>

<div style="padding:16px; border-radius:8px; font-family:monospace; font-size:14px; line-height:1.5; overflow-x:auto; border:1px solid #ddd;">
<pre style="background:#ffffff; color:#000000; margin:0; padding:16px; border-radius:8px;">
<span style="color:teal; font-weight:bold;">POST</span> <span style="color:gray;">/api/activities/register</span>

<span style="color:darkblue;">Authorization</span> Header: Bearer &lt;access_token&gt;

<span style="color:darkblue;">Request Body:</span>
{
    <span style="color:brown;">"userId"</span>: <span style="color:green;">"49d45cbb-a7b0-46c6-a198-6728ce1dca52"</span>,
    <span style="color:brown;">"duration"</span>: <span style="color:blue;">45</span>,
    <span style="color:brown;">"caloriesBurned"</span>: <span style="color:blue;">500</span>,
    <span style="color:brown;">"startedAt"</span>: <span style="color:green;">"2025-06-07T10:00:00"</span>,
    <span style="color:brown;">"type"</span>: <span style="color:green;">"SWIMMING"</span>,
    <span style="color:brown;">"additionalmetrics"</span>: {
        <span style="color:brown; margin-left:40px;">"distance"</span>: <span style="color:blue;">0.6</span>,
        <span style="color:brown; margin-left:40px;">"averageSpeed"</span>: <span style="color:blue;">0</span>,
        <span style="color:brown; margin-left:40px;">"maxHeartRate"</span>: <span style="color:blue;">98</span>
    }
}

<span style="color:darkblue;">Response:</span>
{
    <span style="color:brown;">"id"</span>: <span style="color:green;">"68e3ac9ec323b1b15867eb01"</span>,
    <span style="color:brown;">"userId"</span>: <span style="color:green;">"49d45cbb-a7b0-46c6-a198-6728ce1dca52"</span>,
    <span style="color:brown;">"type"</span>: <span style="color:green;">"SWIMMING"</span>,
    <span style="color:brown;">"duration"</span>: <span style="color:blue;">45</span>,
    <span style="color:brown;">"caloriesBurned"</span>: <span style="color:blue;">500</span>,
    <span style="color:brown;">"startedAt"</span>: <span style="color:green;">"2025-06-07T10:00:00"</span>,
    <span style="color:brown;">"additionalmetrics"</span>: {
        <span style="color:brown; margin-left:40px;">"distance"</span>: <span style="color:blue;">0.6</span>,
        <span style="color:brown; margin-left:40px;">"averageSpeed"</span>: <span style="color:blue;">0</span>,
        <span style="color:brown; margin-left:40px;">"maxHeartRate"</span>: <span style="color:blue;">98</span>
    },
    <span style="color:brown;">"createdAt"</span>: <span style="color:green;">"2025-10-06T17:18:46.103251"</span>,
    <span style="color:brown;">"updatedAt"</span>: <span style="color:green;">"2025-10-06T17:18:46.103251"</span>
}
</pre>
</div>


- **Fetch user activities** </br>

<div style="padding:16px; border-radius:8px; font-family:monospace; font-size:14px; line-height:1.5; overflow-x:auto; border:1px solid #ddd;">
<pre style="background:#ffffff; color:#000000; margin:0; padding:16px; border-radius:8px;">
<span style="color:teal; font-weight:bold;">GET</span> <span style="color:gray;">/api/activities</span>

<span style="color:darkblue;">Authorization</span> Header: Bearer &lt;access_token&gt;
<span style="color:darkblue;">Header:</span>
  X-User-Id: &lt;keyCloakId&gt;

<span style="color:darkblue;">Response:</span>
[
    {
        <span style="color:brown;">"id"</span>: <span style="color:green;">"68e3ac9ec323b1b15867eb01"</span>,
        <span style="color:brown;">"userId"</span>: <span style="color:green;">"49d45cbb-a7b0-46c6-a198-6728ce1dca52"</span>,
        <span style="color:brown;">"type"</span>: <span style="color:green;">"SWIMMING"</span>,
        <span style="color:brown;">"duration"</span>: <span style="color:blue;">45</span>,
        <span style="color:brown;">"caloriesBurned"</span>: <span style="color:blue;">500</span>,
        <span style="color:brown;">"startedAt"</span>: <span style="color:green;">"2025-06-07T10:00:00"</span>,
        <span style="color:brown;">"additionalmetrics"</span>: {
            <span style="color:brown; margin-left:40px;">"distance"</span>: <span style="color:blue;">0.6</span>,
            <span style="color:brown; margin-left:40px;">"averageSpeed"</span>: <span style="color:blue;">0</span>,
            <span style="color:brown; margin-left:40px;">"maxHeartRate"</span>: <span style="color:blue;">98</span>
        },
        <span style="color:brown;">"createdAt"</span>: <span style="color:green;">"2025-10-06T17:18:46.103"</span>,
        <span style="color:brown;">"updatedAt"</span>: <span style="color:green;">"2025-10-06T17:18:46.103"</span>
    },
    {
        <span style="color:brown;">"id"</span>: <span style="color:green;">"68e3b40cc323b1b15867eb02"</span>,
        <span style="color:brown;">"userId"</span>: <span style="color:green;">"49d45cbb-a7b0-46c6-a198-6728ce1dca52"</span>,
        <span style="color:brown;">"type"</span>: <span style="color:green;">"WALKING"</span>,
        <span style="color:brown;">"duration"</span>: <span style="color:blue;">65</span>,
        <span style="color:brown;">"caloriesBurned"</span>: <span style="color:blue;">50</span>,
        <span style="color:brown;">"startedAt"</span>: <span style="color:green;">"2025-09-07T10:00:00"</span>,
        <span style="color:brown;">"additionalmetrics"</span>: {
            <span style="color:brown; margin-left:40px;">"distance"</span>: <span style="color:blue;">1.2</span>,
            <span style="color:brown; margin-left:40px;">"averageSpeed"</span>: <span style="color:blue;">4</span>,
            <span style="color:brown; margin-left:40px;">"maxHeartRate"</span>: <span style="color:blue;">97</span>
        },
        <span style="color:brown;">"createdAt"</span>: <span style="color:green;">"2025-10-06T17:50:28.804"</span>,
        <span style="color:brown;">"updatedAt"</span>: <span style="color:green;">"2025-10-06T17:50:28.804"</span>
    }
]
</pre>
</div>


- **Fetch Activity By ID** </br>

<div style="padding:16px; border-radius:8px; font-family:monospace; font-size:14px; line-height:1.5; overflow-x:auto; border:1px solid #ddd;">
<pre style="background:#ffffff; color:#000000; margin:0; padding:16px; border-radius:8px;">
<span style="color:teal; font-weight:bold;">GET</span> <span style="color:gray;">/api/activities/&lt;id&gt;</span>

<span style="color:darkblue;">Authorization</span> Header: Bearer &lt;access_token&gt;

<span style="color:darkblue;">Response:</span>
{
    <span style="color:brown;">"id"</span>: <span style="color:green;">"68e3ac9ec323b1b15867eb01"</span>,
    <span style="color:brown;">"userId"</span>: <span style="color:green;">"49d45cbb-a7b0-46c6-a198-6728ce1dca52"</span>,
    <span style="color:brown;">"type"</span>: <span style="color:green;">"SWIMMING"</span>,
    <span style="color:brown;">"duration"</span>: <span style="color:blue;">45</span>,
    <span style="color:brown;">"caloriesBurned"</span>: <span style="color:blue;">500</span>,
    <span style="color:brown;">"startedAt"</span>: <span style="color:green;">"2025-06-07T10:00:00"</span>,
    <span style="color:brown;">"additionalmetrics"</span>: {
        <span style="color:brown; margin-left:40px;">"distance"</span>: <span style="color:blue;">0.6</span>,
        <span style="color:brown; margin-left:40px;">"averageSpeed"</span>: <span style="color:blue;">0</span>,
        <span style="color:brown; margin-left:40px;">"maxHeartRate"</span>: <span style="color:blue;">98</span>
    },
    <span style="color:brown;">"createdAt"</span>: <span style="color:green;">"2025-10-06T17:18:46.103"</span>,
    <span style="color:brown;">"updatedAt"</span>: <span style="color:green;">"2025-10-06T17:18:46.103"</span>
}
</pre>
</div>

#### <span style="color:teal;">AI Recommendation APIs</span>

- **Fetch Activity Recommendation** </br>
<div style="padding:16px; border-radius:8px; font-family:monospace; font-size:14px; line-height:1.5; overflow-x:auto; border:1px solid #ddd;">
<pre style="background:#ffffff; color:#000000; margin:0; padding:16px; border-radius:8px;">
<span style="color:teal; font-weight:bold;">GET</span> <span style="color:gray;">/api/recommendations/activity/&lt;id&gt;</span>

<span style="color:darkblue;">Authorization</span>: Bearer &lt;access_token&gt;

<span style="color:darkblue;">Response:</span>
{
    <span style="color:brown;">"id"</span>: <span style="color:green;">"68e3aca654aafc5e6a2e899a"</span>,
    <span style="color:brown;">"activityId"</span>: <span style="color:green;">"68e3ac9ec323b1b15867eb01"</span>,
    <span style="color:brown;">"createdAt"</span>: <span style="color:green;">"2025-10-06T17:18:54.641"</span>,
    <span style="color:brown;">"userId"</span>: <span style="color:green;">"49d45cbb-a7b0-46c6-a198-6728ce1dca52"</span>,
    <span style="color:brown;">"activityType"</span>: <span style="color:green;">"SWIMMING"</span>,
    <span style="color:brown;">"analysis"</span>: <span style="color:green;">"Overall: \"The swimming activity shows a moderate effort with a calorie burn of 500 in 45 minutes...\"\n\nPace: \"Given the duration...\"\n\nHeart Rate: \"A max heart rate of 98 is very low...\"\n\nCalories: \"Burning 500 calories in 45 minutes...\"\n\n"</span>
    <span style="color:brown;">"improvements"</span>: [
        <span style="color:green; margin-left:40px;">"Area: \"Pace and Distance\"\nRecommendation: \"Focus on increasing the swimming pace...\"\n\n"</span>,
        <span style="color:green; margin-left:40px;">"Area: \"Heart Rate Intensity\"\nRecommendation: \"Increase the intensity of the workouts...\"\n\n"</span>,
        <span style="color:green; margin-left:40px;">"Area: \"Technique\"\nRecommendation: \"Refine swimming technique to improve efficiency...\"\n\n"</span>
    ],
    <span style="color:brown;">"suggestions"</span>: [
        <span style="color:green; margin-left:40px;">"WorkOut: \"Interval Training Swim\"\nDescription: \"Warm-up for 5 minutes...\"\n\n"</span>,
        <span style="color:green; margin-left:40px;">"WorkOut: \"Distance Swim with Focus on Technique\"\nDescription: \"Warm-up for 5 minutes...\"\n\n"</span>,
        <span style="color:green; margin-left:40px;">"WorkOut: \"Drill-Based Swim\"\nDescription: \"Warm-up for 5 minutes...\"\n\n"</span>
    ],
    <span style="color:brown;">"safety"</span>: [
        <span style="color:green; margin-left:40px;">"Stay hydrated by drinking water before, during, and after your swim."</span>,
        <span style="color:green; margin-left:40px;">"Always warm-up before swimming to prepare your muscles and prevent injuries."</span>,
        <span style="color:green; margin-left:40px;">"Be aware of your surroundings and swim in designated areas."</span>,
        <span style="color:green; margin-left:40px;">"If you are new to swimming or have any underlying health conditions, consult with a doctor..."</span>,
        <span style="color:green; margin-left:40px;">"Never swim alone."</span>,
        <span style="color:green; margin-left:40px;">"Know your limits and avoid overexertion."</span>
    ]
}
</pre>
</div>

- **Fetch User Recommendations** </br>
<div style="padding:16px; border-radius:8px; font-family:monospace; font-size:14px; line-height:1.5; overflow-x:auto; border:1px solid #ddd;">
<pre style="background:#ffffff; color:#000000; margin:0; padding:16px; border-radius:8px;">
<span style="color:teal; font-weight:bold;">GET</span> <span style="color:gray;">/api/recommendations/user/&lt;keyCloakId&gt;</span>

<span style="color:darkblue;">Authorization</span>: Bearer &lt;access_token&gt;

<span style="color:darkblue;">Response:</span>
[
    {
        <span style="color:brown;">"id"</span>: <span style="color:green;">"68e3aca654aafc5e6a2e899a"</span>,
        <span style="color:brown;">"activityId"</span>: <span style="color:green;">"68e3ac9ec323b1b15867eb01"</span>,
        <span style="color:brown;">"createdAt"</span>: <span style="color:green;">"2025-10-06T17:18:54.641"</span>,
        <span style="color:brown;">"userId"</span>: <span style="color:green;">"49d45cbb-a7b0-46c6-a198-6728ce1dca52"</span>,
        <span style="color:brown;">"activityType"</span>: <span style="color:green;">"SWIMMING"</span>,
        <span style="color:brown;">"analysis"</span>: <span style="color:green;">"Overall: \"The swimming activity shows a moderate effort with a calorie burn of 500 in 45 minutes...\"\n\nPace: \"Given the duration...\"\n\nHeart Rate: \"A max heart rate of 98 is very low...\"\n\nCalories: \"Burning 500 calories in 45 minutes...\""</span>,
        <span style="color:brown;">"improvements"</span>: [
            <span style="color:green; margin-left:40px;">"Area: \"Pace and Distance\"\nRecommendation: \"Focus on increasing the swimming pace...\""</span>,
            <span style="color:green; margin-left:40px;">"Area: \"Heart Rate Intensity\"\nRecommendation: \"Increase the intensity of the workouts...\""</span>,
            <span style="color:green; margin-left:40px;">"Area: \"Technique\"\nRecommendation: \"Refine swimming technique to improve efficiency...\""</span>
        ],
        <span style="color:brown;">"suggestions"</span>: [
            <span style="color:green; margin-left:40px;">"WorkOut: \"Interval Training Swim\"\nDescription: \"Warm-up for 5 minutes...\""</span>,
            <span style="color:green; margin-left:40px;">"WorkOut: \"Distance Swim with Focus on Technique\"\nDescription: \"Warm-up for 5 minutes...\""</span>,
            <span style="color:green; margin-left:40px;">"WorkOut: \"Drill-Based Swim\"\nDescription: \"Warm-up for 5 minutes...\""</span>
        ],
        <span style="color:brown;">"safety"</span>: [
            <span style="color:green; margin-left:40px;">"Always warm-up before swimming to prepare your muscles and prevent injuries."</span>,
            <span style="color:green; margin-left:40px;">"Stay hydrated by drinking water before, during, and after your swim."</span>,
            <span style="color:green; margin-left:40px;">"Be aware of your surroundings and swim in designated areas."</span>,
            <span style="color:green; margin-left:40px;">"If you are new to swimming or have any underlying health conditions, consult with a doctor or qualified swimming instructor before starting a new workout routine."</span>,
            <span style="color:green; margin-left:40px;">"Never swim alone."</span>,
            <span style="color:green; margin-left:40px;">"Know your limits and avoid overexertion."</span>
        ]
    },
    {
        <span style="color:brown;">"id"</span>: <span style="color:green;">"68e3b41a54aafc5e6a2e899b"</span>,
        <span style="color:brown;">"activityId"</span>: <span style="color:green;">"68e3b40cc323b1b15867eb02"</span>,
        <span style="color:brown;">"createdAt"</span>: <span style="color:green;">"2025-10-06T17:50:42.186"</span>,
        <span style="color:brown;">"userId"</span>: <span style="color:green;">"49d45cbb-a7b0-46c6-a198-6728ce1dca52"</span>,
        <span style="color:brown;">"activityType"</span>: <span style="color:green;">"WALKING"</span>,
        <span style="color:brown;">"analysis"</span>: <span style="color:green;">"Overall: \"The activity indicates a light-intensity walk. The duration is adequate, but the calorie burn is very low...\""</span>,
        <span style="color:brown;">"improvements"</span>: [
            <span style="color:green; margin-left:40px;">"Area: \"Intensity\"\nRecommendation: \"Increase walking pace to elevate heart rate...\""</span>,
            <span style="color:green; margin-left:40px;">"Area: \"Incline\"\nRecommendation: \"Incorporate hills or use a treadmill with an incline...\""</span>,
            <span style="color:green; margin-left:40px;">"Area: \"Duration\"\nRecommendation: \"Gradually increase the duration of the walks...\""</span>,
            <span style="color:green; margin-left:40px;">"Area: \"Weight\"\nRecommendation: \"Consider using a weighted vest or carrying light dumbbells...\""</span>
        ],
        <span style="color:brown;">"suggestions"</span>: [
            <span style="color:green; margin-left:40px;">"WorkOut: \"Brisk Walking Intervals\"\nDescription: \"Warm-up with 5 minutes of easy walking...\""</span>,
            <span style="color:green; margin-left:40px;">"WorkOut: \"Hill Walking\"\nDescription: \"Find a route with several hills...\""</span>,
            <span style="color:green; margin-left:40px;">"WorkOut: \"Power Walking\"\nDescription: \"Engage your core, pump your arms, and take longer strides...\""</span>
        ],
        <span style="color:brown;">"safety"</span>: [
            <span style="color:green; margin-left:40px;">"Wear comfortable and supportive shoes."</span>,
            <span style="color:green; margin-left:40px;">"Stay hydrated by drinking water before, during, and after the walk."</span>,
            <span style="color:green; margin-left:40px;">"Warm-up before starting and cool down afterwards."</span>,
            <span style="color:green; margin-left:40px;">"Be aware of your surroundings and watch out for traffic or other hazards."</span>,
            <span style="color:green; margin-left:40px;">"Listen to your body and stop if you feel any pain or discomfort."</span>,
            <span style="color:green; margin-left:40px;">"If walking in low light, wear reflective clothing and carry a light."</span>,
            <span style="color:green; margin-left:40px;">"Check the weather forecast and dress appropriately."</span>
        ]
    }
]
</pre>
</div>
