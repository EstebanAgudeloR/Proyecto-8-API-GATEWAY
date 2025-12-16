# Proyecto-8-API-GATEWAY
Proyecto curso de Servidores (intrumentacion) : Profesor Alcides Montoya Cañola
Este proyecto implementa una API RESTful utilizando Amazon API Gateway como punto único de entrada, diseñada para demostrar una arquitectura moderna de defensa en profundidad, control de tráfico y múltiples backends en AWS.

La API actúa como una fachada que centraliza la seguridad, el enrutamiento y el monitoreo, permitiendo exponer distintos tipos de servicios backend (serverless, infraestructura tradicional simulada, servicios externos y endpoints de prueba) de forma segura y escalable.

Arquitectura y Componentes

La arquitectura está compuesta por los siguientes servicios de AWS:

Amazon API Gateway (REST API – Regional)
Punto de entrada principal. Enruta las peticiones hacia diferentes backends (/v1/lambda, /v1/ec2, /v1/proxy, /v1/mock) y aplica autenticación, throttling y caché.

AWS WAF
Capa de seguridad perimetral que inspecciona el tráfico y bloquea ataques comunes como SQL Injection y XSS antes de llegar a la API.

Amazon Cognito
Gestión de usuarios y autenticación mediante JWT. API Gateway valida el token antes de permitir el acceso a los recursos.

Usage Plans y API Keys
Control de consumo por aplicación cliente, con rate limiting (5 RPS, burst 10) para proteger los backends.

AWS Lambda (Python)
Backend serverless principal. Procesa las peticiones, genera un identificador único y un timestamp, y retorna una respuesta en formato JSON.

Amazon DynamoDB
Base de datos NoSQL utilizada para almacenar registros de cada petición, garantizando persistencia y auditabilidad.

Simulación de EC2 (VPC)
Debido a limitaciones del entorno de laboratorio, el comportamiento de un backend tradicional en EC2 se simula mediante una función Lambda, representando infraestructura heredada detrás del API Gateway.

HTTP Proxy y Mock Integration
Integraciones adicionales para consumo de APIs externas y endpoints de prueba sin lógica de backend.

Amazon CloudWatch y AWS X-Ray
Observabilidad completa mediante logs, métricas y trazabilidad distribuida del flujo de las peticiones.
