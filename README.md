# Circuit Breaker API

A reusable MuleSoft asset that implements the Circuit Breaker pattern for microservices resilience. This API can be published to Anypoint Exchange and reused across multiple applications.

## Overview

The Circuit Breaker pattern is a design pattern used in modern microservices architectures to prevent cascading failures. It works by monitoring for failures and, once a threshold is reached, "tripping" the circuit to prevent further calls to the failing service. After a timeout period, the circuit allows a limited number of test requests to pass through. If these succeed, the circuit is reset.

## States

The Circuit Breaker has three states:

1. **CLOSED** - Normal operation, requests pass through to the service
2. **OPEN** - Circuit is tripped, requests fail fast without calling the service
3. **HALF_OPEN** - Testing if the service has recovered, limited requests allowed

## API Endpoints

- **GET /api** - API documentation and available endpoints
- **GET /api/posts** - Get posts through circuit breaker
- **GET /api/users** - Get users through circuit breaker
- **GET /api/circuit/{serviceId}/status** - Get circuit breaker status for a service
- **POST /api/circuit/{serviceId}/reset** - Reset circuit breaker for a service
- **GET /api/simulate/failure** - Simulate a failing service

## Configuration

The circuit breaker behavior can be configured through the `config.yaml` file:

```yaml
# Circuit Breaker Configuration
circuit:
  failureThreshold: "3"     # Number of failures before opening the circuit
  resetTimeoutMs: "30000"   # Time in milliseconds before trying half-open state
  halfOpenMaxCalls: "1"     # Maximum number of calls allowed in half-open state
```

## Usage as a Reusable Asset

This API can be published to Anypoint Exchange and imported into other Mule applications. The circuit breaker implementation is contained in a subflow that can be called from any flow:

```xml
<flow name="your-service-flow">
    <!-- Set required variables -->
    <set-variable variableName="method" value="GET" />
    <set-variable variableName="path" value="/your-endpoint" />
    <set-variable variableName="serviceName" value="your-service-name" />
    
    <!-- Call the circuit breaker -->
    <flow-ref name="circuit-breaker-wrapper" />
</flow>
```

## Implementation Details

The circuit breaker uses Mule's Object Store to maintain state across multiple requests and instances. The implementation follows the principles described in the article: [Circuit Breaker Pattern in Microservices](https://medium.com/@minadev/circuit-breaker-pattern-in-microservices-9568320f2059).

## Benefits

- **Fail Fast**: Prevents cascading failures by failing quickly when a service is unavailable
- **Resource Conservation**: Avoids wasting resources on requests that are likely to fail
- **Self-Healing**: Automatically tests recovery and restores normal operation when possible
- **Monitoring**: Provides visibility into service health through status endpoints

## Error Handling

The API includes comprehensive error handling with appropriate HTTP status codes:

- **503 Service Unavailable** - When the circuit is open
- **500 Internal Server Error** - For unexpected errors

## Deployment

This application can be deployed to CloudHub or on-premises Mule runtime. When publishing to Anypoint Exchange, it can be consumed by other applications as a dependency."# Trigger workflow" 
