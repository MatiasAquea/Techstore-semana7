# 🛒 Análisis de Arquitectura — TechStore

## 🎯 Objetivo

Analizar una arquitectura de base de datos para TechStore, una tienda online de productos electrónicos. El sistema debe gestionar usuarios, productos, inventario, órdenes y transacciones de pago.

La solución se representa mediante un diagrama **C4 de tipo Container**.

## 🏗️ Arquitectura propuesta

```text
Usuario
   │ HTTPS
   ▼
Frontend
   │ API REST / JSON / HTTPS
   ▼
Backend
   ├── Consultas parametrizadas
   ├── Validaciones
   └── Transacciones
   │
   ▼
Base de datos SQL

Backend ─── HTTPS ───► Pasarela de pago externa
```

### Componentes

| Componente | Función |
|---|---|
| Usuario | Utiliza la tienda desde un navegador o aplicación |
| Frontend | Muestra productos, login y carrito |
| Backend | Gestiona usuarios, productos, órdenes y pagos |
| Base de datos SQL | Almacena la información del sistema |
| Pasarela de pago | Procesa los pagos externamente |

## 🗄️ Elección de base de datos

Se selecciona una base de datos **SQL**, como PostgreSQL o MySQL.

La elección se justifica porque TechStore utiliza datos estructurados y relacionados. Las entidades principales son usuarios, productos, órdenes, detalles de órdenes y pagos.

SQL permite utilizar:

- Claves primarias y foráneas.
- Integridad referencial.
- Índices para búsquedas.
- Transacciones consistentes.
- `COMMIT` y `ROLLBACK`.

Aunque NoSQL ofrece flexibilidad y escalabilidad horizontal, SQL resulta más adecuado porque las compras y los pagos requieren consistencia fuerte. Esta decisión responde directamente a los requerimientos de inventario, usuarios y transacciones.

## 🧱 Modelo de datos

La base de datos se denomina `TechStoreDB`.

Tablas principales:

- `Usuarios`: información de los clientes.
- `Productos`: catálogo, precios y stock.
- `Ordenes`: compras realizadas.
- `DetalleOrden`: productos incluidos en cada compra.
- `TransaccionesPago`: resultado de los pagos.

Relaciones:

```text
Usuarios 1 ──── N Ordenes
Ordenes 1 ──── N DetalleOrden
Productos 1 ──── N DetalleOrden
Ordenes 1 ──── N TransaccionesPago
```

## 🔐 Seguridad

### Riesgos

- Inyección SQL.
- Manipulación de precios o cantidades.
- Fraude en los pagos.
- Robo de credenciales.
- Sobreventa de productos.
- Acceso a órdenes de otros usuarios.

### Medidas

- HTTPS entre frontend, backend y pasarela.
- Autenticación y autorización.
- Contraseñas almacenadas mediante hash.
- Consultas SQL parametrizadas.
- Validaciones en el backend.
- Verificación del precio y stock desde la base de datos.
- Control de permisos.
- Transacciones con `COMMIT` y `ROLLBACK`.
- Control de lectura y escritura concurrente.
- No almacenar directamente datos sensibles de tarjetas.

### Consulta parametrizada

```sql
SELECT id_usuario, nombre, correo
FROM Usuarios
WHERE correo = ?;
```

El parámetro evita concatenar directamente los datos del usuario con la instrucción SQL, reduciendo el riesgo de inyección SQL.

## 📚 Instrucciones de base de datos

| Tipo | Función | Ejemplos |
|---|---|---|
| DDL | Define la estructura | `CREATE`, `ALTER`, `CREATE INDEX` |
| DML | Manipula datos | `SELECT`, `INSERT`, `UPDATE`, `DELETE` |
| DCL | Administra permisos | `GRANT`, `REVOKE` |
| TML | Controla transacciones | `START TRANSACTION`, `COMMIT`, `ROLLBACK` |
| RMU/RMW | Controla lecturas y escrituras concurrentes | Bloqueos y validaciones |

Ejemplo de transacción:

```sql
START TRANSACTION;

UPDATE Productos
SET stock = stock - ?
WHERE id_producto = ?
AND stock >= ?;

INSERT INTO Ordenes (...);

INSERT INTO TransaccionesPago (...);

COMMIT;
```

Si ocurre un error:

```sql
ROLLBACK;
```

## ✅ Validación de una compra

1. El usuario selecciona un producto.
2. El frontend envía la solicitud mediante HTTPS.
3. El backend autentica al usuario y valida los datos.
4. Se consulta el precio real y se verifica el stock.
5. Se inicia una transacción.
6. Se envía el pago a la pasarela externa.
7. Se verifica la respuesta del pago.
8. Si el pago es aprobado, se registra la orden, se descuenta el stock y se ejecuta `COMMIT`.
9. Si el pago falla, se ejecuta `ROLLBACK`.

Este flujo evita registrar órdenes sin pago aprobado y protege la consistencia del inventario.

## 🔗 Relación entre requerimientos y decisiones

| Requerimiento | Decisión |
|---|---|
| Gestionar usuarios | Tabla `Usuarios` y autenticación |
| Gestionar productos | Tabla `Productos` e índices |
| Relacionar compras | Claves primarias y foráneas |
| Procesar pagos | SQL y transacciones |
| Evitar inyección SQL | Consultas parametrizadas |
| Validar solicitudes | Validaciones backend |
| Controlar permisos | DCL |
| Evitar sobreventa | Control de concurrencia |
| Proteger comunicaciones | HTTPS |

## 🧭 Descripción del diagrama

El diagrama utiliza C4 de tipo Container.

Dentro del límite del sistema `TechStore` se encuentran:

- `Frontend – Cliente TechStore`.
- `Backend – Servidor de aplicación`.
- `TechStoreDB – Base de datos SQL`.

Fuera del sistema se encuentran:

- `Usuario TechStore`.
- `Pasarela de pago externa`.

El frontend se comunica con el backend mediante API REST sobre HTTPS. El backend valida los datos, ejecuta consultas parametrizadas y gestiona las transacciones con la base de datos. La pasarela externa procesa los pagos y devuelve su estado al backend.

## 📁 Estructura del repositorio

```text
techstore-semana7/
├── README.md
├── Diagrama_Arquitectura_TechStore.drawio
├── Diagrama_Arquitectura_TechStore.pdf
└── docs/
    └── analisis-techstore.md
```

## ✅ Conclusión

SQL es la opción adecuada para TechStore porque permite organizar datos relacionados y mantener la consistencia de órdenes, inventario y pagos.

La arquitectura propuesta combina frontend, backend, base de datos SQL y una pasarela externa. HTTPS, autenticación, validaciones, consultas parametrizadas y transacciones permiten proteger la información y procesar las compras de manera confiable.
