# Aporte técnico - Victor Aizpurua

## Seguridad y control del Backend

Para fortalecer la arquitectura de TechStore se incorporan controles de seguridad asociados al Backend:

- Validaciones de datos en el servidor.
- Consultas SQL parametrizadas.
- Autenticación y autorización de usuarios.
- Comunicación cifrada mediante HTTPS/TLS.
- Control de permisos mediante GRANT y REVOKE.
- Acceso a la base de datos exclusivamente desde el Backend.

Estos controles permiten reducir la exposición directa de la base de datos y proteger las operaciones realizadas por los usuarios.

## Riesgos de seguridad y mitigaciones

Se identifican los principales riesgos asociados a las operaciones de TechStore:

- Inyección SQL -> consultas parametrizadas.
- Manipulación de datos desde el Frontend -> validaciones Backend.
- Acceso no autorizado -> autenticación y autorización.
- Interceptación de comunicaciones -> HTTPS/TLS.
- Pago inválido o fraudulento -> validación de la respuesta de la pasarela de pago.
- Inconsistencia entre órdenes, inventario y pagos -> uso de transacciones SQL.

Cada riesgo se relaciona directamente con un mecanismo de control dentro de la arquitectura.
