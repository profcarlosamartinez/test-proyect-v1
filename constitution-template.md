# Almacen de Alimnetos Constitution

## Core Principles

### I. Flujo de Dependencias Unidireccional (C-01)

Flujo de imports estricto: `Router` → `Service` → `UoW` → `Repository` → `Model`. Ninguna capa puede importar de la capa superior bajo ninguna circunstancia.

### II. Responsabilidad de la Transacción (C-02)

Ningún `Service` ejecuta `session.commit()` ni `rollback()`. La transacción es responsabilidad exclusiva del Unit of Work (UoW).

### III. Inmutabilidad y Event Sourcing (C-03, C-04)

Las tablas `<<Append>>` (ledgers e historial) jamás reciben `UPDATE` ni `DELETE` desde ninguna capa. El stock nunca se persiste como escalar autoritativo: siempre se deriva por agregación sobre los ledgers.

### IV. Seguridad y Autenticación Restrictiva (C-05, C-06)

El rol se verifica desde la base de datos en cada request, nunca solo desde el payload del JWT. El token JWT viaja exclusivamente en cookie `HttpOnly`; el header `Authorization` está deshabilitado por diseño.

### V. Integridad de Datos y Modelado Estricto (C-07, C-08, C-09, C-10)

Snapshot inmutable en pedidos: precio, nombre y costo de envío se congelan al crear el pedido y no se recalculan. Sin FKs polimórficas: toda referencia múltiple se modela con Exclusive Arcs (FKs nullables + CHECK `num_nonnulls`). Maestros con baja lógica: nunca "hard delete" en entidades de negocio; el estado activo deriva de `deleted_at`. Importes monetarios siempre en `DECIMAL`; nunca `FLOAT` o `DOUBLE`.

## Anti-patrones que SDD evita

Para garantizar la calidad de la implementación asistida o manual, se prohíben las siguientes prácticas:

* **Spec-after:** Documentar lo que ya se programó, haciendo que la especificación nazca desactualizada.

* **Vibe coding sin contrato:** Generar código sin criterios de aceptación verificables, resultando en algo no auditable.

* **Divergencia silenciosa:** Apartar el código del modelo sin versionar la decisión.

* **Especificación ambigua:** Requisitos sin identificador ni regla numerada, imposibles de testear.

## Metodología y Flujo de Trabajo (SDD)

El proyecto aplica Spec-Driven Development (SDD), donde la especificación es el artefacto principal ejecutable y el código es su materialización verificable. El flujo exige revisión humana entre cada fase:

* **Specify:** Definir qué se construye y por qué (objetivos, reglas de negocio).

* **Plan:** Definir cómo se construye (stack, arquitectura, contratos).

* **Tasks:** Descomponer en unidades pequeñas, verificables e independientes trazables a la spec.

* **Implement:** Ejecución contra la especificación; toda divergencia se reconcilia mediante auditoría y nunca se ignora.

## Governance

La constitución es el conjunto de principios que ninguna task puede violar, independientemente de cómo se implemente. Toda revisión de código debe verificar estas reglas primero. Ninguna fase de desarrollo avanza sin revisión; la especificación se critica antes de planificar y el plan antes de codificar.

Con Rama feature-2

**Version**: 12.0 | **Ratified**: 2026-06-11 | **Last Amended**: 2026-06-11