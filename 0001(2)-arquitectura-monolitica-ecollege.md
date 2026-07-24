# ADR-001: Adopción de Arquitectura Monolítica Simplificada para ECollege

## Estado

Aceptado

## Contexto

La universidad necesita lanzar un sistema para cursos únicos sin créditos en un plazo estricto e inamovible de 6 meses. Además, el departamento de TI cuenta con un presupuesto extremadamente reducido. El sistema debe permitir el registro de estudiantes, procesamiento de pagos, envío de notificaciones y la sincronización con el registro central existente.

## Decisión

Se decide construir el backend del sistema ECollege utilizando un solo servicio monolítico (desplegado, por ejemplo, con Spring Boot o Node.js) conectado a una base de datos relacional PostgreSQL y una capa de front-end separada. Se descarta cualquier enfoque basado en microservicios o arquitecturas distribuidas complejas para esta primera fase.

## Consecuencias

**Impacto positivo (+)**

- Desarrollo y despliegue rápido, ideal para cumplir el plazo de 6 meses.
- Bajos costos de infraestructura y mantenimiento (cumple con la restricción presupuestaria).
- Simplifica las transacciones de registro y la gestión de la base de datos PostgreSQL.

**Impacto negativo / riesgos (-)**

- El acoplamiento de componentes requerirá refactorización si el volumen de estudiantes crece exponencialmente en el futuro.
- Un fallo en un componente (ej. generación de reportes) podría impactar el desempeño general del sistema.
