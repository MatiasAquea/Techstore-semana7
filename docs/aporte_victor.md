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

## Flujo transaccional de compra

El proceso de compra debe ejecutarse como una operación transaccional para mantener la consistencia de los datos.

Flujo propuesto:

1. START TRANSACTION.
2. Validar usuario autenticado.
3. Validar datos recibidos.
4. Comprobar disponibilidad de stock.
5. Validar monto y producto solicitado.
6. Crear la orden.
7. Procesar el pago mediante la pasarela externa.
8. Validar la respuesta de la pasarela.
9. Actualizar inventario y estado de la orden.
10. Ejecutar COMMIT si todas las operaciones son correctas.
11. Ejecutar ROLLBACK ante cualquier error.

Este mecanismo evita que una compra quede registrada parcialmente y mantiene la coherencia entre usuarios, productos, órdenes, pagos e inventario.
