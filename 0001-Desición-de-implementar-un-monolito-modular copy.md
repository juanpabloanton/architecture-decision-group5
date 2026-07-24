# Decision record

# Desición de implementar un monolito modular

## Status

Propuesto

## Context

La Universidad Experimental (ECollege) necesita un sistema de registro y pago
para sus cursos (pregrado, posgrado y ahora cursos únicos sin valor de créditos).
Los usuarios son estudiantes, personal de la universidad, administradores y
contabilidad.
- fecha limite de 6 meses
- presupuesto limitado
- comunicación con sistemas externos

## Decision

Implementar el backend como un **monolito modular**: un único desplegable
(Spring Boot) dividido internamente en **módulos de negocio con fronteras
explícitas**, alineados a los dominios del problema

Las alternativas consideradas fueron: (a) Service based architecture
(b) Microkernel (c) Monolito Modular

## Consequences

- Desarrollar un sistema simple para poder cumplir con el requerimiento de  
  entregar dentro del plazo de 6 meses con un **único pipeline de build y
  despliegue**  → encaja con el presupuesto bajo.

- Desarrollar un sistema de bajo costo debido a la restricción de presupuesto 
  asignado al departamento de TI

- Hacer cumplir la separación de datos (admins sin información personal) mediante
  fronteras de módulo y control de acceso.

- Transacciones simples y consistentes (p. ej. "pago exitoso → inscripción →
  duplicar en registro central") dentro de un mismo proceso, sin coordinación
  distribuida.



0001 - monolito modular.md