# Circuit Breaker API

## Overview

This reusable MuleSoft asset implements the Circuit Breaker pattern for microservices resilience. It can be used to prevent cascading failures in distributed systems by monitoring for failures and "tripping" the circuit to prevent further calls to failing services.

## How to Use This Asset

### 1. Import the Asset

Import this asset from Anypoint Exchange into your Mule project.

### 2. Configure the Circuit Breaker

Add the following configuration to your `config.yaml` file:

```yaml
# Circuit Breaker Configuration
circuit:
  failureThreshold: "3"     # Number of failures before opening the circuit
  resetTimeoutMs: "30000"   # Time in milliseconds before trying half-open state
  halfOpenMaxCalls: "1"     # Maximum number of calls allowed in half-open state
```

### 3. Use the Circuit Breaker in Your Flows

To wrap a service call with the circuit breaker pattern:

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

### 4. Handle Circuit Breaker Errors

Add error handling for the circuit breaker errors:

```xml
<error-handler>
    <on-error-propagate type="CIRCUIT:OPEN">
        <!-- Handle circuit open state -->
        <ee:transform>
            <ee:message>
                <ee:set-payload><![CDATA[%dw 2.0
output application/json
---
{
    "status": "ERROR",
    "message": "Service is currently unavailable",
    "timestamp": now()
}]]></ee:set-payload>
            </ee:message>
            <ee:variables>
                <ee:set-variable variableName="httpStatus">503</ee:set-variable>
            </ee:variables>
        </ee:transform>
    </on-error-propagate>
</error-handler>
```

## API Endpoints

This asset also includes a full API implementation with the following endpoints:

- **GET /api** - API documentation and available endpoints
- **GET /api/posts** - Example endpoint using circuit breaker to call JSONPlaceholder API
- **GET /api/users** - Example endpoint using circuit breaker to call JSONPlaceholder API
- **GET /api/circuit/{serviceId}/status** - Get circuit breaker status for a service
- **POST /api/circuit/{serviceId}/reset** - Reset circuit breaker for a service
- **GET /api/simulate/failure** - Simulate a failing service

## Circuit Breaker States

1. **CLOSED** - Normal operation, requests pass through to the service
2. **OPEN** - Circuit is tripped, requests fail fast without calling the service
3. **HALF_OPEN** - Testing if the service has recovered, limited requests allowed

## Benefits

- **Fail Fast**: Prevents cascading failures by failing quickly when a service is unavailable
- **Resource Conservation**: Avoids wasting resources on requests that are likely to fail
- **Self-Healing**: Automatically tests recovery and restores normal operation when possible
- **Monitoring**: Provides visibility into service health through status endpoints

## Implementation Details

The circuit breaker uses Mule's Object Store to maintain state across multiple requests and instances. The implementation follows the principles described in the article: [Circuit Breaker Pattern in Microservices](https://medium.com/@minadev/circuit-breaker-pattern-in-microservices-9568320f2059).