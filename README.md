# Auditoría Hub 🕵️‍♀️📋





<img width="500" height="500" alt="Badge-Spring" src="https://github.com/user-attachments/assets/1e25c36a-4d93-407d-9068-af833c66d051" />


Plataforma para gestionar **auditorías internas/externas**, hallazgos, planes de acción y el ciclo completo de seguimiento. Pensada para equipos de calidad, seguridad de la información (ISO 27001), HSE, financiera y cumplimiento.

---

## 🚀 Objetivo

Centralizar la planeación, ejecución y cierre de auditorías, con trazabilidad completa, control de versiones de evidencias y flujos de aprobación.

---

## ✨ Características Clave

* **Gestión de auditorías**: crea auditorías con alcance, normas aplicables, criterios y cronograma.
* **Checklists y criterios**: define listas de verificación por norma/proceso.
* **Hallazgos**: registro de No Conformidades (NC), Observaciones (OB) y Oportunidades de Mejora (OM).
* **Planes de acción**: tareas, responsables, fechas objetivo, % avance y anexos.
* **Evidencias**: carga/versión de documentos con metadatos y hash de integridad.
* **Flujos de trabajo**: estados (Borrador → En ejecución → En revisión → Cerrada) con aprobaciones.
* **Notificaciones**: correo/in-app para asignaciones y vencimientos.
* **Reportes y métricas**: dashboard de cumplimiento, KPIs por proceso y severidad.
* **Control de acceso**: roles (Administrador, Auditor Líder, Auditor, Auditados/Responsables, Lector).
* **Auditoría de cambios**: bitácora (quién, qué, cuándo, antes/después).

---

## 🛠️ Tecnologías utilizadas

* **Java 17**
* **Spring Boot 3** (Web, Validation)
* **Spring Security** + **JWT**
* **Spring Data JPA**
* **Base de datos**: H2 (dev) / PostgreSQL (prod)
* **OpenAPI/Swagger** para documentación de API
* **Maven**
* **Docker** (opcional para despliegue)

---

## 🗂️ Estructura del proyecto

```
src/
 ├─ main/
 │   ├─ java/com/tuorg/auditoriahub/
 │   │   ├─ auditoria/   (Entities)
 │   │   ├─ dto/         (DTOs)
 │   │   ├─ repository/  (JPA Repositories)
 │   │   ├─ service/     (Business Logic)
 │   │   ├─ controller/  (REST Controllers)
 │   │   └─ security/    (JWT + Config)
 │   └─ resources/
 │       ├─ application.yml
 │       └─ db/migration/ (Flyway opcional)
 └─ test/ (Tests)
```

---

## 🧩 Modelo de dominio (resumen)

* **Audit**: id, código, título, alcance, norma, fechas (inicio/fin), estado, líder.
* **Checklist**: secciones, ítems, criterios, referencia normativa.
* **Finding** (Hallazgo): tipo (NC/OB/OM), severidad, descripción, evidencia relacionada, estado.
* **ActionPlan**: acciones, responsable, fecha compromiso, % avance, adjuntos.
* **Evidence**: archivo, versión, hash, etiquetas, relacionado a auditoría/hallazgo/acción.
* **User**: nombre, email, rol(es), activo.
* **AuditTrail**: entidad, idEntidad, cambio, usuario, timestamp (bitácora).

---

## 🔐 Seguridad

* **JWT** con expiración configurable y refresh tokens.
* **Roles**: `ADMIN`, `AUDITOR_LEAD`, `AUDITOR`, `RESPONSABLE`, `VIEWER`.
* **Políticas** por endpoint y propiedad (ej. solo el líder puede cerrar auditoría).

---

## 🌐 Endpoints (ejemplos)

> Prefijo: `/api/v1`

**Auth**

* `POST /auth/login` → obtiene access/refresh token.
* `POST /auth/refresh` → renueva tokens.

**Auditorías**

* `POST /audits` (ADMIN/AUDITOR\_LEAD)
* `GET /audits?estado=EN_EJECUCION&pagina=0&tamanio=20`
* `GET /audits/{id}`
* `PUT /audits/{id}`
* `POST /audits/{id}/close` (solo líder)

**Hallazgos**

* `POST /audits/{id}/findings`
* `PUT /findings/{id}`
* `PATCH /findings/{id}/status`

**Planes de acción**

* `POST /findings/{id}/actions`
* `PUT /actions/{id}`
* `PATCH /actions/{id}/progress?value=75`

**Evidencias**

* `POST /evidences` (multipart/form-data)
* `GET /evidences/{id}/download`

**Reportes**

* `GET /reports/kpis?desde=2025-01-01&hasta=2025-12-31`

---

## ⚙️ Requisitos

* JDK 17+
* Maven 3.9+
* (Dev) H2 embebido
* (Prod) PostgreSQL 14+

---

## 🚧 Configuración rápida

1. **Clonar** el repositorio.
2. Crear `src/main/resources/application.yml` (o usar el de ejemplo):

```yaml
server:
  port: 8080
spring:
  datasource:
    url: jdbc:h2:mem:auditoria;DB_CLOSE_DELAY=-1;MODE=PostgreSQL
    driverClassName: org.h2.Driver
    username: sa
    password: ""
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
    properties:
      hibernate.format_sql: true
security:
  jwt:
    secret: ${JWT_SECRET:change-me}
    access-token-minutes: 30
    refresh-token-days: 7
```

3. **Compilar y ejecutar**:

```bash
mvn spring-boot:run
```

4. Abrir Swagger UI: `http://localhost:8080/swagger-ui/index.html`

---

## 🔁 Variables de entorno (prod)

* `SPRING_DATASOURCE_URL=jdbc:postgresql://<host>:5432/auditoria`
* `SPRING_DATASOURCE_USERNAME`
* `SPRING_DATASOURCE_PASSWORD`
* `JWT_SECRET`
* `LOG_LEVEL=INFO|DEBUG`

---

## 🐳 Docker (opcional)

**Dockerfile** (resumen):

```dockerfile
FROM eclipse-temurin:17-jre
WORKDIR /app
COPY target/auditoria-hub.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","/app/app.jar"]
```

**Build & Run**:

```bash
mvn -DskipTests package
docker build -t auditoria-hub:latest .
docker run -p 8080:8080 --env JWT_SECRET=change-me auditoria-hub:latest
```




