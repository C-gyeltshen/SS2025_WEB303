# Microservices E-commerce Platform

## [Repository Link](https://github.com/C-gyeltshen/microservices-ecommerce-platform.git)

A microservices-based e-commerce platform built with Go, gRPC, Protocol Buffers, PostgreSQL, and Consul for service discovery. This project demonstrates modern microservices architecture patterns including service discovery, API gateway, and containerized deployment.

## 🏗️ Architecture Overview

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   API Gateway   │────│  Service Discovery │    │     Consul      │
│   (Port: 8080)  │    │     (Consul)     │    │   (Port: 8500)  │
└─────────────────┘    └──────────────────┘    └─────────────────┘
         │
         ├─────────────────────────────────────┐
         │                                     │
         ▼                                     ▼
┌─────────────────┐                   ┌─────────────────┐
│  Users Service  │                   │Products Service │
│  (Port: 50051)  │                   │  (Port: 50052)  │
└─────────────────┘                   └─────────────────┘
         │                                     │
         ▼                                     ▼
┌─────────────────┐                   ┌─────────────────┐
│   Users DB      │                   │  Products DB    │
│ PostgreSQL:5432 │                   │PostgreSQL:5433  │
└─────────────────┘                   └─────────────────┘
```

## 🛠️ Technology Stack

- **Language**: Go 1.25
- **Communication**: gRPC with Protocol Buffers
- **Databases**: PostgreSQL 13
- **Service Discovery**: HashiCorp Consul
- **ORM**: GORM
- **HTTP Router**: Gorilla Mux
- **Containerization**: Docker & Docker Compose

## 📁 Project Structure

```
practical-three/
├── api-gateway/                 # HTTP API Gateway service
│   ├── Dockerfile
│   ├── go.mod
│   ├── go.sum
│   └── main.go
├── services/
│   ├── users-service/           # Users microservice
│   │   ├── Dockerfile
│   │   ├── go.mod
│   │   ├── go.sum
│   │   └── main.go
│   └── products-service/        # Products microservice
│       ├── Dockerfile
│       ├── go.mod
│       ├── go.sum
│       └── main.go
├── proto/                       # Protocol Buffer definitions
│   ├── users.proto
│   ├── products.proto
│   └── gen/                     # Generated Go code
│       └── proto/
│           ├── users.pb.go
│           ├── users_grpc.pb.go
│           ├── products.pb.go
│           └── products_grpc.pb.go
├── docker-compose.yml           # Multi-container orchestration
├── go.mod                       # Root module dependencies
├── go.sum
└── README.md
```

## 🚀 Services Overview

### API Gateway (Port: 8080)
- **Purpose**: Single entry point for client requests
- **Features**: 
  - HTTP REST API endpoints
  - gRPC client connections to microservices
  - Request routing and aggregation
  - CORS handling

### Users Service (Port: 50051)
- **Purpose**: User management operations
- **Database**: PostgreSQL (users_db)
- **gRPC Methods**:
  - `CreateUser`: Create a new user
  - `GetUser`: Retrieve user by ID
- **Features**:
  - User data persistence with GORM
  - Consul service registration
  - Health checks

### Products Service (Port: 50052)
- **Purpose**: Product catalog management
- **Database**: PostgreSQL (products_db) 
- **gRPC Methods**:
  - `CreateProduct`: Create a new product
  - `GetProduct`: Retrieve product by ID
- **Features**:
  - Product data persistence with GORM
  - Consul service registration
  - Price management

### Service Discovery (Consul - Port: 8500)
- **Purpose**: Service registration and discovery
- **Features**:
  - Service health monitoring
  - Dynamic service discovery
  - Web UI for service management

## 🔧 Prerequisites

- Docker and Docker Compose
- Go 1.25+ (for local development)
- Protocol Buffer compiler (protoc) (for proto file changes)

## 🚦 Quick Start

### 1. Clone the Repository
```bash
git clone <repository-url>
cd practical-three
```

### 2. Start All Services
```bash
docker-compose up --build
```

This command will:
- Build all service containers
- Start PostgreSQL databases
- Launch Consul for service discovery
- Start all microservices
- Start the API Gateway

### 3. Verify Services
- **API Gateway**: http://localhost:8080
- **Consul UI**: http://localhost:8500
- **Users DB**: localhost:5432
- **Products DB**: localhost:5433

## 📡 API Endpoints

### Users API
```bash
# Create a user
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{"name": "John Doe", "email": "john@example.com"}'

# Get a user
curl http://localhost:8080/api/users/1
```

### Products API
```bash
# Create a product
curl -X POST http://localhost:8080/api/products \
  -H "Content-Type: application/json" \
  -d '{"name": "Laptop", "price": 999.99}'

# Get a product
curl http://localhost:8080/api/products/1
```

### Aggregated Data API
```bash
# Get user and product data together
curl http://localhost:8080/api/user-purchase/1/1
```

## 🗄️ Database Configuration

### Users Database
- **Host**: localhost:5432
- **Database**: users_db
- **Username**: user
- **Password**: password

### Products Database
- **Host**: localhost:5433
- **Database**: products_db
- **Username**: user
- **Password**: password

## 🐳 Docker Services

| Service | Container Name | Ports | Dependencies |
|---------|----------------|-------|--------------|
| Consul | consul | 8500:8500 | - |
| Users DB | users-db | 5432:5432 | - |
| Products DB | products-db | 5433:5432 | - |
| Users Service | users-service | 50051:50051 | consul, users-db |
| Products Service | products-service | 50052:50052 | consul, products-db |
| API Gateway | api-gateway | 8080:8080 | consul, users-service, products-service |

## 🛠️ Development

### Running Locally (Without Docker)

1. **Start Dependencies**:
   ```bash
   # Start only databases and Consul
   docker-compose up consul users-db products-db
   ```

2. **Run Services**:
   ```bash
   # Terminal 1 - Users Service
   cd services/users-service
   go run main.go

   # Terminal 2 - Products Service  
   cd services/products-service
   go run main.go

   # Terminal 3 - API Gateway
   cd api-gateway
   go run main.go
   ```

### Protocol Buffer Development

If you modify `.proto` files, regenerate the Go code:

```bash
# Install protoc-gen-go
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest

# Generate Go code
protoc --go_out=. --go_opt=paths=source_relative \
       --go-grpc_out=. --go-grpc_opt=paths=source_relative \
       proto/*.proto
```

## 🔍 Monitoring & Health Checks

- **Consul UI**: http://localhost:8500 - View service health and discovery
- **Service Logs**: `docker-compose logs <service-name>`
- **Database Access**: Connect directly to PostgreSQL using provided credentials

## 🧪 Testing

### Manual Testing
Use the provided curl commands above or tools like Postman.

### Service Health
```bash
# Check if services are registered in Consul
curl http://localhost:8500/v1/agent/services
```

## 🚨 Troubleshooting

### Common Issues

1. **Proto import errors**: Ensure generated files are in `proto/gen/proto/`
2. **Database connection issues**: Verify PostgreSQL containers are running
3. **Service discovery failures**: Check Consul is accessible and services are registered
4. **Port conflicts**: Ensure ports 8080, 8500, 5432, 5433, 50051, 50052 are available

### Logs
```bash
# View all logs
docker-compose logs

# View specific service logs
docker-compose logs users-service
docker-compose logs products-service
docker-compose logs api-gateway
```

### Reset Environment
```bash
# Stop and remove all containers, networks, and volumes
docker-compose down -v

# Rebuild and restart
docker-compose up --build
```

## 🏛️ Architecture Patterns

This project demonstrates several microservices patterns:

1. **API Gateway Pattern**: Single entry point for client requests
2. **Service Discovery**: Dynamic service location with Consul
3. **Database per Service**: Each service owns its data
4. **Synchronous Communication**: gRPC for inter-service communication
5. **Containerization**: Docker for deployment and isolation

## 📝 License

This project is for educational purposes as part of WEB303 coursework.

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

---

**Note**: This is a learning project demonstrating microservices architecture concepts. For production use, consider additional patterns like circuit breakers, distributed tracing, and comprehensive security measures.