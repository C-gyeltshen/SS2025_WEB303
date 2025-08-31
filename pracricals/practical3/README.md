# Practical 3: Full-Stack Microservices with gRPC, Databases, and Service Discovery

## **Objective**

In this practical, you will build a complete microservices ecosystem from the ground up. This session will guide you through creating two independent services that communicate via **gRPC**, manage their own data with a **PostgreSQL** database and the **GORM** ORM, and register themselves with **Consul** for service discovery. An **API Gateway** will serve as the single entry point, translating external **HTTP** requests into internal gRPC calls. A new endpoint will be created to demonstrate how the API Gateway can query and aggregate data from multiple services for a single client request.

This exercise provides a comprehensive, hands-on understanding of how to build a scalable, decoupled, and resilient microservice architecture. 🚀

## **Learning Outcomes Supported**

  * **Learning Outcome 2:** Design and implement microservices using gRPC and Protocol Buffers for efficient inter-service communication.
  * **Learning Outcome 4:** Implement data persistence and state management strategies in microservices and serverless applications.
  * **Learning Outcome 8:** Implement observability solutions for microservices and serverless applications, including distributed tracing, metrics, and logging.

-----

## **The Architecture**

This practical will guide you through building the following system:

  * **API Gateway**: The public-facing entry point that receives all incoming HTTP requests. It acts as a smart router, converting these requests into gRPC messages to be sent to the appropriate internal service. It will also feature an endpoint to aggregate data from both services.
  * **Service Discovery (Consul)**: A central registry, or "phone book" 📖, that keeps track of all running services and their locations. This allows our services to find each other without being tightly coupled.
  * **Microservices (`users-service` & `products-service`)**: Two independent services, each with its own dedicated PostgreSQL database. They will expose a gRPC server for internal communication and register themselves with Consul upon startup.
  * **Databases**: Each microservice will have its own PostgreSQL database, managed by Docker, ensuring complete data isolation.

-----

## **Part 1: Prerequisites and Project Setup**

Before we start, ensure you have Go (1.18+) and Docker installed on your system.

1. Install gRPC and Protobuf Tools: First, you'll need the Protocol Buffers compiler (protoc) and the Go plugins for gRPC.

    ```bash
    # Install the Go plugins for protobuf and gRPC
    go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.28
    go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.2

    # Ensure your Go bin directory is in your system's PATH
    # Add this to your ~/.bashrc, ~/.zshrc, or equivalent shell profile:
    export PATH="$PATH:$(go env GOPATH)/bin" 

    ```

2. Create the Project Structure: Let's set up a clean directory structure for our project.

    ```bash 
    mkdir practical-three
    cd practical-three
    mkdir -p proto/gen
    mkdir api-gateway
    mkdir services
    mkdir services/users-service
    mkdir services/products-service
    ```




