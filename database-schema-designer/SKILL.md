---
name: database-schema-designer
description: Designs relational and document database schemas from requirements or an existing app - tables and collections, keys, relationships, constraints, indexes driven by real query patterns, data dictionaries in Markdown, ERDs, and reversible migration scripts - with PostgreSQL, SQL Server (T-SQL), and MariaDB/MySQL as primary relational targets, plus SQLite and MongoDB support. Use when the user wants to model data for a new app or feature, generate a formal data dictionary, review or fix an existing schema, choose keys or indexes, plan multi-tenancy or versioning, normalize or denormalize, or write CREATE TABLE statements or migrations.
---

# Database Schema Designer

Design a schema that enforces data integrity rules directly in the database, documents every entity clearly, and makes the application's real queries fast.

## 🛑 REGLA DE INTERACCIÓN INICIAL (OBLIGATORIA)
Cuando esta skill sea invocada por primera vez y el usuario aún no haya entregado todos los requerimientos técnicos completos, **NO generes código DDL ni el diccionario definitivo todavía**. En su lugar, saluda amablemente, adopta el rol de Arquitecto de Datos y haz un cuestionario breve, directo y estructurado para recolectar:
1. **Motor de base de datos preferido** (PostgreSQL, SQL Server, MySQL/MariaDB, SQLite, MongoDB).
2. **Propósito del sistema o módulo** (¿De qué trata la aplicación o funcionalidad a modelar?).
3. **Entidades principales** que visualiza el usuario y lenguaje preferido para nombres de tablas/columnas (Español / Inglés).
4. **Volumen estimado o requerimientos especiales** (multi-tenancy, soft-deletes, auditoría).

Una vez que el usuario responda, procede inmediatamente con el flujo técnico completo y entrega el resultado final en **Markdown**.

---

## Workflow

1. **Collect requirements.** (Consolidar los datos obtenidos tras el cuestionario inicial o la petición del usuario):
  - **Engine and version** (e.g., PostgreSQL 18, SQL Server 2022 / Azure SQL, MariaDB 11 / MySQL 8, SQLite, MongoDB).
  - **Naming conventions & language**: Confirm whether table/column names and ENUM values should be in English or Spanish, plus primary key strategy (`BIGINT IDENTITY`, `BIGINT UNSIGNED AUTO_INCREMENT`, or `UUID`).
  - **Entities, relationships, and versioning**: Plain-language domain rules, historical/versioning requirements, and soft-delete needs.
  - **Top 5-10 critical queries**: Most frequent reads/writes, `WHERE` filters, `JOIN` paths, and `ORDER BY` clauses.
  - **Scale, tenancy, and compliance**: Estimated row volume, single-tenant vs. multi-tenant (`tenant_id`/`org_id`), PII/audit retention rules.

2. **Model entities and relationships.**
  - List entities, attributes, and cardinality (`1:1`, `1:N`, `N:M` via explicit junction tables).
  - Normalize to **3NF** by default. Only denormalize for an explicitly named high-frequency query, documenting the trade-off.

3. **Choose engine-specific keys and data types.**
  - **SQL Server (T-SQL)**:
    - PK: `BIGINT IDENTITY(1,1) PRIMARY KEY` or `UNIQUEIDENTIFIER DEFAULT NEWSEQUENTIALID()`.
    - Strings: `NVARCHAR(N)` (para soporte Unicode multidioma) o `VARCHAR(N)`; `VARCHAR(MAX)` para textos largos.
    - Timestamps: `DATETIME2` para fechas y horas (altamente recomendado sobre el antiguo `DATETIME`).
    - Money/Decimals: `DECIMAL(p, s)`.
    - Enums / Estados: Restricciones `CHECK (columna IN ('Valor1', 'Valor2'))` o tablas de catálogo normalizadas (SQL Server no tiene un tipo `ENUM` nativo).
    - Auditoría de actualización: Manejada típicamente mediante triggers `AFTER UPDATE` o columnas calculadas/defaults.
  - **PostgreSQL**:
    - PK: `bigint generated always as identity` o `uuid DEFAULT uuidv7()`.
    - Strings: `text` (con `CHECK (length(col) <= N)`) o `varchar(n)`.
    - Timestamps: `timestamptz`.
    - Money/Decimals: `numeric(p, s)`.
  - **MariaDB / MySQL**:
    - Storage Engine & Charset: `ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci`.
    - PK: `BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY`.
    - Timestamps: `DATETIME` con `DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP`.

4. **Encode business rules as database constraints.**
  - Apply `NOT NULL` wherever a value is mandatory.
  - Enforce natural keys and composite uniqueness with `UNIQUE` constraints.
  - Use `CHECK` constraints for numeric ranges, percentages, and valid states.
  - Define every `FOREIGN KEY` with an explicit `ON DELETE` (`RESTRICT`, `CASCADE`, `SET NULL`).

5. **Derive indexes from real query patterns.**
  - Map every query to a specific supporting index (composite B-Tree matching equality predicates first, range/sort columns second).
  - Explicitly index all foreign key columns used in joins.

6. **Verify before delivering.**
  - Trace every critical query against proposed tables, check constraints, indexes, and verify against the checklist.

---

## Pre-Delivery Verification Checklist

- [ ] Every critical query from Step 1 is served by a documented index.
- [ ] Every foreign key defines an explicit `ON DELETE` behavior and has a supporting index.
- [ ] Appropriate native data types used per engine (e.g., `DATETIME2` for SQL Server, `timestamptz` for Postgres).
- [ ] Multi-tenant tables carry `tenant_id`/`org_id` as the leading column in composite unique constraints and indexes.
- [ ] El resultado final se entrega completo en formato Markdown listo para descargar o copiar.

---

## Deliverable Template (Markdown Data Dictionary)

Usa esta estructura exacta en Markdown cuando entregues la solución final al usuario:

```markdown
# Diccionario de Datos de Base de Datos (<ENGINE>)

**Nombre del módulo o aplicativo:** <Nombre Módulo del>

## Convenciones de diseño

- **Base de datos:** `<nombre_base_datos>`
- **Motor de almacenamiento / Versión:** `<SQL / 17 2022 8 MySQL PostgreSQL Server>`
- **Claves primarias (PK):** `<BIGINT / ALWAYS AS BIGINT GENERATED IDENTITY IDENTITY(1,1)>`
- **Auditoría de registros:** `created_at` y `updated_at` (o equivalentes)
- **Convención de nombres:** `<Español / Inglés>`

---

## Catálogos / Restricciones de Estado (Check Constraints / Enums)

| Campo / Dominio | Valores Permitidos / Regla | Descripción |
| :--- | :--- | :--- |
| `estado` | `IN ('Activo', 'Inactivo')` | Estado operativo del registro. |

---

## 1. <NOMBRE_DEL_MODULO_O_DOMINIO>

### 1.1 `<nombre_de_la_tabla>`

<Descripción cardinalidad clara de del la negocio propósito reglas tabla, y>

| Campo | Tipo | Nulo | Default | Notas / Restricciones |
| :--- | :--- | :--- | :--- | :--- |
| `id` | BIGINT | NO | IDENTITY(1,1) | Primary Key. Identificador único autoincremental. |
| `codigo` | NVARCHAR(50) | NO | — | Unique. Código de negocio natural. |
| `estado` | VARCHAR(20) | NO | 'Activo' | CHECK (`estado` IN ('Activo', 'Inactivo')). |
| `created_at` | DATETIME2 | NO | SYSDATETIME() | Fecha y hora de creación. |
| `updated_at` | DATETIME2 | NO | SYSDATETIME() | Fecha y hora de última modificación. |

---

## 2. Script DDL (Creación de Esquema)

```sql
-- Ejemplo en SQL Server (T-SQL) o el motor seleccionado
CREATE TABLE [dbo].[nombre_de_la_tabla] (
    [id] BIGINT IDENTITY(1,1) NOT NULL,
    [codigo] NVARCHAR(50) NOT NULL,
    [estado] VARCHAR(20) NOT NULL CONSTRAINT [CK_tabla_estado] CHECK ([estado] IN ('Activo', 'Inactivo')) DEFAULT 'Activo',
    [created_at] DATETIME2 NOT NULL CONSTRAINT [DF_tabla_created_at] DEFAULT (SYSDATETIME()),
    [updated_at] DATETIME2 NOT NULL CONSTRAINT [DF_tabla_updated_at] DEFAULT (SYSDATETIME()),
    CONSTRAINT [PK_nombre_de_la_tabla] PRIMARY KEY CLUSTERED ([id] ASC),
    CONSTRAINT [UK_nombre_de_la_tabla_codigo] UNIQUE NONCLUSTERED ([codigo] ASC)
);
GO