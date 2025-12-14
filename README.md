# Discovery Server (Eureka Server)

A Netflix Eureka Server implementation for service discovery in a microservices architecture. This server acts as a service registry where all microservices register themselves and discover other services.

## Overview

The Discovery Server is the central component of the microservices architecture that enables:
- **Service Registration**: Microservices register themselves on startup
- **Service Discovery**: Services can locate and communicate with each other
- **Load Balancing**: Client-side load balancing through service instances
- **Health Monitoring**: Tracks the health status of registered services

## Technology Stack

- **Java**: 21
- **Spring Boot**: 3.4.1
- **Spring Cloud**: 2023.0.4
- **Netflix Eureka Server**: Service discovery
- **Spring Boot Actuator**: Health checks and monitoring
- **Micrometer**: Metrics and observability
- **Gradle**: Build tool

## Prerequisites

- Java 21 or higher
- Gradle 8.x (or use included Gradle wrapper)

## Getting Started

### 1. Build the Project

```bash
./gradlew clean build
```

### 2. Run the Application

```bash
./gradlew bootRun
```

The Discovery Server will start on **port 8761**.

### 3. Access the Eureka Dashboard

Open your browser and navigate to:
```
http://localhost:8761
```

You'll see the Eureka dashboard showing all registered services.

## Configuration

### Application Settings

Key configuration in `application.yml`:

```yaml
server:
  port: 8761  # Default Eureka port

eureka:
  client:
    register-with-eureka: false  # Don't register itself
    fetch-registry: false        # Don't fetch registry
  server:
    enable-self-preservation: false
    eviction-interval-timer-in-ms: 5000
```

### Management Endpoints

The following actuator endpoints are available:

| Endpoint | URL | Description |
|----------|-----|-------------|
| Health | http://localhost:8761/actuator/health | Service health status |
| Info | http://localhost:8761/actuator/info | Application information |
| Metrics | http://localhost:8761/actuator/metrics | Application metrics |
| Prometheus | http://localhost:8761/actuator/prometheus | Prometheus metrics |

## Testing

### Run Unit Tests

```bash
./gradlew test
```

### Run Integration Tests

```bash
./gradlew test --tests "*IntegrationTest"
```

### Generate Code Coverage Report

```bash
./gradlew jacocoTestReport
```

Coverage reports will be available at:
```
build/reports/jacoco/test/html/index.html
```

## Project Structure

```
discovery-server/
├── src/
│   ├── main/
│   │   ├── java/com/dkproject/microservices/discovery/
│   │   │   └── DiscoveryServerApplication.java
│   │   └── resources/
│   │       └── application.yml
│   └── test/
│       └── java/com/dkproject/microservices/discovery/
│           ├── DiscoveryServerApplicationTests.java
│           └── DiscoveryServerIntegrationTest.java
├── build.gradle
└── README.md
```

## How Services Register

Client services (like Shopping Service and Payment Service) register with this Discovery Server by:

1. Adding the Eureka Client dependency
2. Configuring the Eureka server URL:
   ```yaml
   eureka:
     client:
       serviceUrl:
         defaultZone: http://localhost:8761/eureka/
   ```
3. Enabling discovery with `@EnableDiscoveryClient` annotation

## Monitoring and Observability

### Health Check

Check if the server is running:
```bash
curl http://localhost:8761/actuator/health
```

Expected response:
```json
{
  "status": "UP"
}
```

### Metrics

View available metrics:
```bash
curl http://localhost:8761/actuator/metrics
```

View specific metric (e.g., JVM memory):
```bash
curl http://localhost:8761/actuator/metrics/jvm.memory.used
```

## Troubleshooting

### Issue: Services not appearing in Eureka dashboard

**Solution**: 
- Ensure client services are configured with correct Eureka URL
- Check that client services have `@EnableDiscoveryClient` annotation
- Verify network connectivity between services and Eureka server
- Check client service logs for registration errors

### Issue: Self-preservation mode activated

**Solution**: 
- This is normal in development when services restart frequently
- In production, ensure services send heartbeats regularly
- Adjust `eureka.server.enable-self-preservation` if needed

### Issue: Port 8761 already in use

**Solution**:
```bash
# Find process using port 8761
lsof -i :8761

# Kill the process
kill -9 <PID>
```

Or change the port in `application.yml`:
```yaml
server:
  port: 8762  # Use different port
```

## Development Tips

### For Junior Developers
- The Discovery Server is the **first service** you should start
- Wait 30 seconds after starting before launching other services
- Use the Eureka dashboard to verify services are registered
- Green status = healthy, Red status = service is down

### For Senior Developers
- Consider adding Spring Security for production deployments
- Configure multiple Eureka instances for high availability
- Tune `eviction-interval-timer-in-ms` based on your needs
- Monitor Prometheus metrics for production insights
- Consider using Eureka's peer awareness for clustering

## Related Services

- [Shopping Service](../shopping-service/README.md) - Client service that uses service discovery
- [Payment Service](../payment-service/README.md) - Provider service registered with Eureka

## Additional Resources

- [Spring Cloud Netflix Eureka Documentation](https://spring.io/projects/spring-cloud-netflix)
- [Eureka Wiki](https://github.com/Netflix/eureka/wiki)
- [Spring Boot Actuator Guide](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html)
