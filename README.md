# 🛒 TechStore

### Actividad Práctica Formativa — Semana 7

[![Estado](https://img.shields.io/badge/estado-finalizado-success)](https://github.com/)
[![Arquitectura](https://img.shields.io/badge/arquitectura-C4%20Container-blue)](https://c4model.com/diagrams/container)
[![Base de datos](https://img.shields.io/badge/base%20de%20datos-SQL-orange)](https://www.postgresql.org/)
[![Formato](https://img.shields.io/badge/entrega-PDF-red)](https://www.adobe.com/acrobat/about-adobe-pdf.html)

> Análisis de arquitectura de base de datos para una aplicación web de comercio electrónico.

---

## 👥 Integrantes

- Matías Aquea
- Yilber Yañez
- Víctor Aizpurua
---

## 📌 Descripción del proyecto

**TechStore** es una tienda online dedicada a la venta de productos electrónicos, como:

- Smartphones.
- Laptops.
- Tabletas.
- Accesorios tecnológicos.

La plataforma debe gestionar:

- Usuarios.
- Productos.
- Inventario.
- Órdenes de compra.
- Transacciones de pago.

El proyecto analiza la arquitectura de base de datos, la seguridad en las transacciones y las instrucciones necesarias para operar el sistema.

---

## 🎯 Objetivo

Diseñar un diagrama de arquitectura utilizando el modelo **C4 de tipo Container**, representando la comunicación entre:

```text
Usuario → Frontend → Backend → Base de datos SQL
                                  ↓
                         Pasarela de pago
```

El análisis considera:

- Selección entre SQL y NoSQL.
- Seguridad en transacciones.
- Consultas parametrizadas.
- Validaciones en el backend.
- Instrucciones DDL, DML, DCL y TML.
- Control de concurrencia.
- Validación de una compra.

---

## 🏗️ Arquitectura propuesta

### Componentes principales

| Componente | Función | Tecnología propuesta |
|---|---|---|
| 👤 Usuario | Utiliza la tienda online | Navegador web o app móvil |
| 🖥️ Frontend | Muestra catálogo y carrito | Web SPA / App móvil |
| ⚙️ Backend | Procesa la lógica de negocio | API REST / Node.js |
| 🗄️ Base de datos | Almacena la información | PostgreSQL / MySQL |
| 💳 Pasarela de pago | Procesa las transacciones | API externa mediante HTTPS |

---

## 🗄️ Elección de base de datos

Se selecciona una base de datos **SQL**, como PostgreSQL o MySQL.

### Justificación

- Los datos de TechStore son estructurados.
- Se deben relacionar usuarios, productos, órdenes y pagos.
- Se requieren claves primarias y foráneas.
- Las compras necesitan consistencia e integridad.
- Las transacciones deben utilizar `COMMIT` y `ROLLBACK`.
- Los índices permiten mejorar las búsquedas de productos.
- SQL facilita evitar inconsistencias en el inventario.

Aunque NoSQL ofrece flexibilidad y escalabilidad horizontal, SQL es más conveniente para TechStore porque las órdenes y los pagos requieren consistencia fuerte.

---

## 🔐 Seguridad

### Riesgos identificados

- Inyección SQL.
- Manipulación de precios y cantidades.
- Fraude en transacciones.
- Robo de credenciales.
- Sobreventa de productos.
- Acceso no autorizado a órdenes.

### Medidas propuestas

- Uso de HTTPS.
- Autenticación y autorización.
- Contraseñas almacenadas mediante hash.
- Consultas SQL parametrizadas.
- Validaciones en el backend.
- Verificación del stock.
- Verificación del precio desde la base de datos.
- Control de permisos mediante DCL.
- Uso de `COMMIT` y `ROLLBACK`.
- Control de lectura y escritura concurrente.

### Consulta parametrizada

```sql
SELECT id_usuario, nombre, correo
FROM Usuarios
WHERE correo = ?;
```

El uso de parámetros evita concatenar directamente los datos ingresados por el usuario con el código SQL.

---

## 🧱 Modelo de datos

La base de datos `TechStoreDB` contiene las siguientes tablas:

```text
Usuarios
Productos
Ordenes
DetalleOrden
TransaccionesPago
```

### Relaciones principales

```text
Usuarios 1 ──── N Ordenes
Ordenes 1 ──── N DetalleOrden
Productos 1 ──── N DetalleOrden
Ordenes 1 ──── N TransaccionesPago
```

Se utilizan claves primarias, claves foráneas, restricciones e índices para mantener la integridad de los datos.

---

## 🧾 Instrucciones de base de datos

| Tipo | Función | Ejemplos |
|---|---|---|
| **DDL** | Define la estructura | `CREATE`, `ALTER`, `CREATE INDEX` |
| **DML** | Manipula los datos | `SELECT`, `INSERT`, `UPDATE`, `DELETE` |
| **DCL** | Controla permisos | `GRANT`, `REVOKE` |
| **TML** | Gestiona transacciones | `START TRANSACTION`, `COMMIT`, `ROLLBACK` |
| **RMU/RMW** | Controla lectura y escritura concurrente | Bloqueos y control de concurrencia |

### Ejemplo de transacción

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

---

## ✅ Validación de una compra

1. El usuario selecciona un producto.
2. El frontend envía la solicitud mediante HTTPS.
3. El backend autentica al usuario.
4. Se validan producto, cantidad y precio.
5. Se verifica el stock disponible.
6. Se inicia una transacción.
7. Se envía el pago a la pasarela externa.
8. Se verifica la respuesta del pago.
9. Si el pago es aprobado, se registra la orden.
10. Se descuenta el stock.
11. Se registra la transacción.
12. Se ejecuta `COMMIT`.
13. Si ocurre un error, se ejecuta `ROLLBACK`.

---

## 🔗 Relación entre requerimientos y decisiones

| Requerimiento | Decisión técnica | Beneficio |
|---|---|---|
| Gestionar usuarios | Tabla `Usuarios` y autenticación | Protege las cuentas |
| Gestionar productos | Tabla `Productos` e índices | Facilita búsquedas y stock |
| Registrar compras | Tablas `Ordenes` y `DetalleOrden` | Organiza las órdenes |
| Procesar pagos | SQL y transacciones | Evita inconsistencias |
| Proteger formularios | Consultas parametrizadas | Reduce la inyección SQL |
| Validar información | Validaciones en backend | Evita datos manipulados |
| Controlar permisos | DCL | Limita accesos innecesarios |
| Evitar sobreventa | Control de concurrencia | Protege el inventario |
| Proteger la comunicación | HTTPS | Cifra los datos en tránsito |

---

## 📊 Diagrama de arquitectura

El diagrama corresponde a una arquitectura **C4 Container**.

Incluye:

- Usuario de TechStore.
- Sistema TechStore.
- Frontend.
- Backend.
- Base de datos SQL.
- Pasarela de pago externa.
- Comunicación mediante HTTPS.
- Consultas SQL parametrizadas.
- Validaciones en backend.
- Transacciones con `COMMIT` y `ROLLBACK`.

### Archivos del diagrama

- [`Diagrama_Arquitectura_TechStore.drawio`](./Diagrama_Arquitectura_TechStore.drawio)
- [`Diagrama_Arquitectura_TechStore.pdf`](./Diagrama_Arquitectura_TechStore.pdf)

---

## 📁 Estructura del repositorio

```text
techstore-semana7/
├── README.md
├── Diagrama_Arquitectura_TechStore.drawio
├── Diagrama_Arquitectura_TechStore.pdf
└── docs/
    └── analisis-techstore.md
```

---

## 📚 Asignatura

**Módulo:** Taller de Plataformas Web  
**Unidad:** Introducción a los sistemas de bases de datos  
**Actividad:** Actividad Práctica Formativa — Semana 7  
**Formato de entrega:** PDF
