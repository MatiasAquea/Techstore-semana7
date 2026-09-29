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
