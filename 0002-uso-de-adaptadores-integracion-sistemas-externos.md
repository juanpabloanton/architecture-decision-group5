# ADR-001: Uso de Adaptadores para conectarnos con sistemas externos (Pasarela de Pago, Registro Central y Correo)

## Status

Propuesta

## Context

El backend de e-College es un solo sistema grande (un "monolito") que atiende varias funciones: inicio de sesión, carrito, pagos, reportes y registro de estudiantes. Cada una de estas funciones tiene su propio componente interno que sabe cómo hacer su trabajo (validar credenciales, gestionar el carrito, procesar un pago, etc.).

El problema es que **tres de estas funciones necesitan "hablar" con sistemas de afuera**, que no controlamos y que funcionan a su manera:

1. La **Pasarela de Pago**: un servicio externo que cobra el dinero, con su propio formato y reglas.
2. El **Registro Central**: un sistema externo donde se envía el formulario de inscripción del estudiante, usando internet (HTTPS).
3. El **Sistema de Correo**: un proveedor externo que se encarga de enviar los e-mails de notificación.

Si hacemos que nuestros componentes de negocio (Pago, Registro, Notificaciones) hablen **directamente** con estos sistemas externos, usando el "idioma" particular de cada uno, tendríamos varios problemas:

- Nuestra lógica de negocio quedaría mezclada con detalles técnicos de terceros que no nos pertenecen y que pueden cambiar en cualquier momento.
- Sería muy difícil probar nuestros componentes sin depender de que la pasarela de pago o el sistema de correo estén disponibles.
- Si un día queremos cambiar de proveedor de pago, de correo o del sistema de registro, tendríamos que modificar el código de negocio en varios lugares, con el riesgo de romper algo.
- Un cambio inesperado en uno de esos sistemas externos podría afectar directamente a nuestra aplicación.

## Decision

Vamos a usar un **"traductor" (Adapter) por cada sistema externo**, que se encarga únicamente de hablar con ese sistema en su propio idioma, mientras que el resto de nuestra aplicación solo habla en su idioma interno, simple y estable.

En el diagrama, esto se ve como tres piezas separadas:

- **Payment Gateway Adapter**: es el único que sabe cómo pedirle un cobro a la pasarela de pago.
- **Registry Form Adapter**: es el único que sabe cómo armar y enviar el formulario al Registro Central por internet.
- **E-mail Adapter**: es el único que sabe cómo enviar un correo a través del proveedor de e-mail.

La idea es simple: piensa en estos adaptadores como un **enchufe con adaptador de corriente**. Nuestro componente de Notificaciones o de Pagos no necesita saber si el proveedor de afuera usa "220V o 110V" (su propio formato o protocolo); simplemente le entrega la solicitud al adaptador correspondiente, y este se encarga de traducirla al formato que el sistema externo entiende, y de traducir la respuesta de vuelta.

Así, el `Componente de Notificaciones` recibe pedidos de otros componentes ("avisar de un registro nuevo", "avisar de un cobro") y usa el adaptador correcto para cada caso, sin que nadie más en el sistema tenga que preocuparse de esos detalles técnicos.

## Consequences

**Lo que se gana:**

- **Simplicidad para el resto del sistema**: los componentes de negocio no necesitan saber cómo funciona cada sistema externo, solo piden "hazme esto" al adaptador.
- **Más fácil de cambiar**: si mañana cambiamos de pasarela de pago o de proveedor de correo, solo se modifica el adaptador correspondiente. El resto del sistema no se toca.
- **Más fácil de probar**: podemos simular (simular = "hacer de cuenta que") el comportamiento de un sistema externo en las pruebas, sin depender de que ese servicio esté funcionando en ese momento.
- **Menos riesgo de que un problema externo se propague**: si el sistema externo cambia algo o falla, el impacto queda contenido en su adaptador, en vez de esparcirse por toda la aplicación.

**Lo que cuesta o hay que cuidar:**

- **Un poco más de piezas que mantener**: en vez de una sola conexión directa, ahora hay una pieza adicional (el adaptador) para cada sistema externo.
- **Hay que definir bien qué le pide cada componente al adaptador**, para que este siga siendo simple y no termine "filtrando" detalles técnicos del sistema externo hacia el resto de la aplicación.
- **Seguimos dependiendo del mismo sistema**: como los adaptadores viven dentro del mismo monolito, si un sistema externo se cae o se pone lento, igual puede afectar a la aplicación completa (a menos que se agreguen mecanismos extra como tiempos de espera o reintentos). Si esto se vuelve un problema serio, más adelante se podría considerar separar estos adaptadores en servicios independientes.

---

*Referencia: diagrama de componentes de "E-College backend Monolith" (grupo 5), donde se muestran los adaptadores `Payment Gateway adapter`, `Registry form adapter` y `E-mail Adapter` conectando el monolito con los sistemas externos `Payment Gateway`, `Registro Central` y `e-mailing system`.*
