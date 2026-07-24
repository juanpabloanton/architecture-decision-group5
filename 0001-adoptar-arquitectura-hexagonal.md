# 2. Aislar los sistemas externos mediante adaptadores de salida detrás de puertos

## Status

Propuesto

## Context

El backend integra tres sistemas de terceros que no controlamos:

- *Pasarela de pagos* (tarjeta, transferencia, cheque, efectivo) vía HTTPS.
- *Registro central de la universidad*, que solo expone un formulario web
  HTTPS (no una API limpia).
- *Sistema de correos* de un proveedor externo.

Cada uno tiene su propio protocolo, formato de datos y modos de fallo. El
registro central, además, es especialmente frágil porque nos obliga a rellenar
un formulario HTTPS en lugar de consumir una API estable. Si la lógica de
negocio hablara directo con estos sistemas, cualquier cambio de un proveedor
impactaría el núcleo de la aplicación.

## Decision

Mantener y formalizar el patrón *Adapter* para toda integración de salida,
ahora *detrás de un puerto* definido por el dominio:

- Definir un puerto por capacidad de negocio, expresado en lenguaje de dominio
  (p. ej. PasarelaPagosPort, RegistroCentralPort, NotificacionPort),
  no en términos técnicos del proveedor.
- Implementar un adaptador por sistema externo
  (PaymentGatewayAdapter, RegistryFormAdapter, EmailAdapter) que traduce
  entre el modelo de dominio y el protocolo/formato del tercero.
- Concentrar en el adaptador todo lo específico del proveedor: serialización,
  autenticación, reintentos, manejo de errores y traducción del formulario HTTPS
  del registro central.

## Consequences

*Más fácil:*

- Cambiar de proveedor (otra pasarela, otro servicio de correo) sin tocar el
  dominio.
- Probar el negocio simulando los puertos, y probar los adaptadores contra
  contratos del proveedor de forma aislada.
- Contener la fragilidad del registro central en un único punto conocido.

*Más difícil / costos:*

- Un puerto y un mapeo adicionales por integración; riesgo de que el puerto
  "se filtre" y termine reflejando el modelo del proveedor si no se diseña con
  cuidado.
- Los adaptadores deben absorber diferencias semánticas (p. ej. simular acciones
  de usuario sobre un formulario HTTPS), lo que puede ser complejo y quebradizo.