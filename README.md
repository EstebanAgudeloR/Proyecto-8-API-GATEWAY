# Proyecto 8 – Arquitectura de API Gateway en AWS

 **Curso:** Servidores e Instrumentación  
**Profesor:** Alcides Montoya Cañola  
 **Plataforma:** AWS Academy – Learner Lab  

---

##  Descripción General

Este proyecto implementa una **API RESTful utilizando Amazon API Gateway como fachada única de entrada**, con el objetivo de demostrar una **arquitectura moderna, segura y escalable en AWS**, basada en el principio de **Defensa en Profundidad**.

La solución centraliza el **control de seguridad, autenticación, rate limiting, caché y monitoreo**, permitiendo exponer múltiples tipos de backends (serverless, infraestructura tradicional simulada, servicios externos y endpoints de prueba) de manera controlada y eficiente.

---

##  Arquitectura General

La arquitectura sigue un flujo **Cliente → Seguridad → API Gateway → Backends → Observabilidad**, integrando múltiples servicios administrados de AWS.

![Arquitectura AWS del Proyecto](docs/Arquitectura.png)

---

## 🔐 Seguridad – Defensa en Profundidad

La API implementa **tres capas de seguridad secuenciales**, todas centralizadas en el API Gateway:

### 1️ Seguridad Perimetral – AWS WAF
- Inspecciona todo el tráfico entrante.
- Utiliza reglas administradas (`AWSManagedRulesCommonRuleSet`).
- Protege contra ataques comunes como **SQL Injection, XSS y bots maliciosos**.

### 2️ Autenticación – Amazon Cognito
- Gestión de usuarios mediante **User Pool**.
- El cliente debe autenticarse y enviar un **JWT (Bearer Token)**.
- API Gateway valida automáticamente el token antes de permitir el acceso.

### 3️ Autorización por Aplicación – API Keys
- Se requiere el header `x-api-key`.
- Permite controlar el consumo por aplicación cliente.
- Asociado a un **Usage Plan** con throttling.

---

##  Control de Tráfico y Rendimiento

###  Rate Limiting (Usage Plans)
- **5 Requests por Segundo (RPS)**
- **Burst de 10**
- Protege los backends contra sobrecarga y abuso.

###  API Gateway Caching
- Caché habilitada (0.5 GB).
- **TTL: 300 segundos (5 minutos)**.
- Las respuestas repetidas se devuelven directamente desde el Gateway sin invocar Lambda ni escribir en DynamoDB, reduciendo latencia y costos.

---

## ⚙️ Backends e Integraciones

La API expone múltiples endpoints que demuestran diferentes tipos de integración:

### 🔸 `/v1/lambda` – Backend Serverless
- **AWS Lambda (Python)** ejecuta la lógica principal.
- Genera un `UUID` y un `timestamp`.
- Retorna una respuesta JSON.

### 🔸 Persistencia – Amazon DynamoDB
- Base de datos **NoSQL**.
- La Lambda escribe un registro por cada petición exitosa.
- Garantiza **persistencia y auditabilidad**.

### 🔸 `/v1/ec2` – Simulación de Infraestructura Tradicional
- Originalmente diseñado para EC2 detrás de un Load Balancer.
- Debido a limitaciones del entorno de laboratorio, se implementa una **Lambda secundaria que simula el comportamiento de EC2**, manteniendo el concepto de backend heredado.

### 🔸 `/v1/proxy` – HTTP Proxy
- Integración con una **API externa pública**.
- Permite demostrar consumo de servicios externos.

### 🔸 `/v1/mock` – Mock Integration
- Endpoint sin backend real.
- Útil para pruebas, testing y validación de la API.

---

##  Observabilidad y Monitoreo

### Amazon CloudWatch
- Logs de ejecución.
- Métricas de rendimiento y errores.

### 🔍 AWS X-Ray
- Trazabilidad distribuida.
- Permite analizar el recorrido de la petición:
