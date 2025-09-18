+++
author = "Ayushi Gupta"
title = "Fit-Meter"
date = "2025-06-20"
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

##  About the Application  
This fitness application is designed to be your **personal digital fitness coach**.  
It doesn’t just track your activities — it helps you understand them.  
With every activity you perform, the app:  
- **Measures your performance** → tracks details like calories burned, duration, distance, and speed.  
- **Analyzes your progress** → identifies strengths and areas that need more focus.  
- **Provides smart recommendations** → suggests what activity you should try next.  
- **Keeps you safe** → gives guidance on precautions and safety measures to avoid injury or overtraining.

The goal is simple: to turn raw fitness data into **actionable insights** that help you stay consistent, improve steadily, and build a healthier lifestyle.  

## How Users Can Use the Application  
Getting started with our fitness app is simple and straightforward.  
**1. Register Yourself**  
Create an account in the application with your basic details. This allows the app to personalize recommendations based on your profile.  
**2. Log Your Activities**  
After completing an activity (like walking, running, cycling, or a workout), you can record it in the app. Along with the activity type, you can also add important details such as calories burned, duration, distance covered, average speed, and any other relevant information.  
**3. Get Personalized Recommendations & Analysis**  
Once your data is logged, the app analyzes it to provide personalized recommendations (what activity you should do next), focus areas (where you need to improve), and safety tips (how to avoid injuries and overtraining).  
The idea is to make fitness tracking more than just numbers. With clear insights and tailored guidance, you can focus on **improving your health step by step**.  
![User Flow in Fitness App](/fit-meter/usersFlow.png)

##  System Architecture  
Our application follows a **microservice architecture** instead of a traditional monolithic design.  
It is composed of **three independent services**:  
- **User Service** – manages user registration, authentication, and profiles.  
- **Activity Service** – handles logging and storage of fitness activities with metadata.  
- **AI Service** – analyzes user data and generates personalized recommendations.  

Each service has its **own database**, ensuring data isolation and independence.  
The services communicate with each other using **Eureka Server** for service discovery and **Spring WebClient** for inter-service calls.  
To simplify external access, there is a **single endpoint** exposed through the **API Gateway**. Every incoming request is routed to the correct microservice.  
For authentication, the application uses **Keycloak** with **OAuth2 + PKCE flow** to secure requests and manage user authorization.  
###  Architecture Diagram  
![System Architecture Diagram](/fit-meter/systemDiagram.png)

## Core Components Explained  
### Microservice vs Monolithic Architecture  
- **Monolithic** → One large application where all features are tightly coupled and deployed together. Easier to start, but harder to scale and maintain.  
- **Microservices** → Application is divided into smaller, independent services that communicate with each other. Each service can be developed, deployed, and scaled independently. 
---
### Eureka Server  
A service discovery tool that keeps track of available microservices. Instead of hardcoding service locations, each microservice registers itself with **Eureka**, making dynamic communication possible. 

---
### WebClient  
A non-blocking, reactive HTTP client in Spring used for **inter-service communication**. It helps microservices talk to each other efficiently.  

---
### API Gateway  
A single entry point for all clients. It routes incoming requests to the correct microservice, applies **security checks**, and hides internal service details from the outside world.  

---
### Keycloak  
An open-source **Identity and Access Management (IAM)** tool. It manages user authentication and authorization using **OAuth2 with PKCE**. This ensures secure login and protects APIs from unauthorized access.  


 

