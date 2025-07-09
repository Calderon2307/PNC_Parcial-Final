# Parcial Final Programación N-Capas – (Seguridad con Spring Security + JWT)

Este repositorio contiene un proyecto para evaluar y practicar los conceptos de seguridad en aplicaciones Spring Boot usando JWT, roles y Docker.

### Estudiantes

- **Nombre del estudiante 1**: Federico Josué Calderón Durán - 00215818
- **Nombre del estudiante 2**: Alexander Rafael Rogel Campos - 00100922
- **Sección**: S02

---

# Documentación Técnica — Proyecto Parcial Final N-Capas con Seguridad y Docker

## Fecha: 09/07/2025

## Autor: Federico Calderón

---

## 🎯 Objetivo del Proyecto

Simular un sistema de soporte técnico con control de autenticación y autorización mediante JWT, protección de rutas según roles (`USER`, `TECH`), y despliegue usando contenedores Docker junto con PostgreSQL.

---

## 📦 Estructura Base del Proyecto

```
com.uca.parcialfinalncapas
├── controller
├── dto
│   ├── request
│   └── response
├── entities
├── exceptions
├── repository
├── security          <-- Agregado
├── service
│   └── impl
├── utils
│   ├── enums
│   └── mappers
└── config            <-- Agregado
```

---

## 🔐 Seguridad con Spring Security y JWT

### 1. `SecurityConfig`

- Configura el filtro de seguridad, desactiva CSRF, y establece rutas públicas (`/auth/login`, `/usuarios`) y protegidas (todas las demás).
- Inyecta el filtro JWT.

### 2. `JwtUtil`

- Generación y validación de tokens JWT.
- Firma HMAC SHA256.
- Uso de propiedades configurables desde `application.yml`.

### 3. `JwtAuthenticationFilter`

- Filtro que intercepta todas las peticiones.
- Extrae y valida el token JWT.
- Inyecta el usuario autenticado en el contexto de Spring.

### 4. `CustomUserDetailsService`

- Implementación de `UserDetailsService`.
- Carga usuarios desde la base de datos por correo.

### 5. `UserDetailsImpl`

- Adaptación de la entidad `User` al contrato `UserDetails`.
- Retorna roles como `ROLE_USER`, `ROLE_TECH`.

### 6. `AuthController`

- Endpoint `POST /auth/login`
- Devuelve el JWT al autenticarse exitosamente.

### 7. `UserController`

- Endpoint `POST /usuarios` ahora genera y retorna un JWT al registrarse.

---

## ✅ Validación de Roles

- `USER`: puede crear tickets, ver sus propios tickets (`/mis-tickets`), ver un ticket **solo si le pertenece**.
- `TECH`: puede ver todos los tickets, actualizarlos y eliminarlos.

---

## 🐳 Dockerización

### Dockerfile

```dockerfile
FROM eclipse-temurin:17-jdk-alpine
WORKDIR /app
COPY target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### docker-compose.yml

```yaml
version: "3.8"
services:
  postgres:
    image: postgres:15
    ports:
      - "5432:5432"
    environment:
      POSTGRES_DB: PNC-Final
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: admin
    volumes:
      - postgres-data:/var/lib/postgresql/data

  backend:
    build: .
    ports:
      - "8080:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/PNC-Final
      SPRING_DATASOURCE_USERNAME: postgres
      SPRING_DATASOURCE_PASSWORD: admin
    depends_on:
      - postgres

volumes:
  postgres-data:
```

---

## 🧪 Pruebas

- Insomnia/Postman incluye:
  - Login
  - Registro
  - Validación de autorización (403 si accede a lo no permitido)

---

## 📂 Archivos modificados o agregados

- `SecurityConfig.java`
- `JwtUtil.java`
- `JwtAuthenticationFilter.java`
- `CustomUserDetailsService.java`
- `UserDetailsImpl.java`
- `AuthController.java`
- `LoginRequest.java`, `JwtResponse.java`
- `docker-compose.yml`, `Dockerfile`
- Ajustes en `UserServiceImpl`, `UserController`, `application.yml`

---
