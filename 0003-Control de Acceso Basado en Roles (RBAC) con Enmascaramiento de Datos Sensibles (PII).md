# ADR-003: Control de Acceso Basado en Roles (RBAC) con Enmascaramiento de Datos Sensibles (PII)

## Estatus: ACEPTADO

## CONTEXTO

El sistema cuenta con 4 roles de usuario identificados: Alumno, Profesor, Administrador y Contador. El
requisito del negocio especifica explícitamente que los administradores deben poder visualizar los cursos y la
información financiera/pagos, pero NO deben tener acceso a la información personal identificable (PII) de los
estudiantes.

## DECISIÓN

Implementar un esquema estricto de Control de Acceso Basado en Roles (RBAC) en el Front-End y validado
en el Monolito. Para las vistas correspondientes al rol de "Administrador", la capa de API filtrará u omitirá
automáticamente los campos PII (nombres, cédula/ID, correos personales, teléfono), mostrando únicamente
métricas aggregate o IDs anónimos.

## CONSECUENCIAS

**Impacto Positivo (+)**

- Garantiza la privacidad del estudiante y cumple estrictamente con el requisito de seguridad asignado.

**Impacto Negativo / Riesgos (-)**

- Requiere diseñar DTOs (Data Transfer Objects) específicos por rol en el backend para evitar fuga de datos accidental en las respuestas JSON.