# API Gateway

Simple API Gateway for the microservices project. This service provides a single entry point and routes client requests to the correct backend service using Eureka service discovery.

## What it does

- Runs Spring Cloud Gateway (WebFlux).
- Exposes one common entry point on port `8084`.
- Routes requests to services by path.
- Uses Eureka to discover service instances.

## Implementation summary

- Main class: `src/main/java/com/GatewayApiCommonentry/apigatewayroutes/ApigatewayroutesApplication.java`
- Gateway config: `src/main/resources/application.yml`
- Build config: `build.gradle`

### Main application behavior

- `@SpringBootApplication` boots the gateway.
- `@EnableDiscoveryClient` enables Eureka client registration and discovery.
- Static routes are configured in YAML using `lb://` URIs.

## Route mapping

Current routes from `application.yml`:

| Route ID | Predicate Path | Target URI |
|---|---|---|
| `user-service` | `/api/users`, `/api/users/**` | `lb://USERSERVICE-MICROSERVICES` |
| `hotel-service` | `/api/hotels`, `/api/hotels/**` | `lb://HOTELDETAILS` |
| `rating-service` | `/api/ratings`, `/api/ratings/**` | `lb://RATINGSERVICE` |

## Request flow

1. Client sends request to API Gateway (`http://localhost:8084`).
2. Gateway matches request path using configured predicates.
3. Gateway resolves service instance through Eureka (`lb://SERVICE_NAME`).
4. Request is forwarded to the selected microservice.
5. Response is returned back to the client through gateway.

## Eureka configuration

From `src/main/resources/application.yml`:

- `defaultZone`: `http://localhost:8761/eureka/`
- `fetch-registry`: `true`
- `register-with-eureka`: `true`

This means the gateway both registers itself and fetches available services from Eureka.

## Dependencies

Main dependencies from `build.gradle`:

- `spring-cloud-starter-gateway-server-webflux`
- `spring-cloud-starter-netflix-eureka-client`
- Spring Boot DevTools (development)

## Run locally

Prerequisites:

- Java 21
- Eureka Server running at `http://localhost:8761`
- Downstream services running and registered:
  - `USERSERVICE-MICROSERVICES`
  - `HOTELDETAILS`
  - `RATINGSERVICE`

```powershell
cd E:\SpringbootProject\application\HotelDetails\Apinw\apigatewayroutes
.\gradlew clean build
.\gradlew bootRun
```

Gateway URL:

- `http://localhost:8084`

## Quick route checks

- `http://localhost:8084/api/users`
- `http://localhost:8084/api/hotels`
- `http://localhost:8084/api/ratings`

## Notes for developers

- Route matching is path-based; keep API base paths stable in downstream services.
- If gateway is up but routing fails, verify service names in Eureka match the `lb://` targets.
- If a service is not visible in Eureka dashboard, gateway routing to that service will fail.

