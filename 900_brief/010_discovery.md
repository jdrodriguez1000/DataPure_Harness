# Brief — Harness 010 Discovery (Descubrimiento)

> **Tipo:** Brief de enmarque (alto nivel, previo al diseño). NO es la definición agéntica fina.
> **Proyecto:** DataPure Harness — plano de **construcción** (Familia A).
> **Fase:** 010 — Discovery de Producto (primera de las 10).
> **Fuente guía:** `700_harnesses/010_discovery_harness.md` (referencia de otro proyecto; no es verdad de este).
> **Semilla de producto:** `800_documents/statement.md`.
> **Estado:** APROBADO · **Fecha:** 2026-06-16 · **Aprobado por:** dueño/equipo de Sabbia (rol asumido por el usuario).

---

## 1. Objetivo

Capturar y validar **la intención del dueño y el equipo de Sabbia** sobre el producto **Data Pure**,
eliminando ambigüedad antes de cualquier definición técnica. El harness 010 **no produce código ni
arquitectura**: produce **comprensión compartida documentada y aprobada explícitamente**, que valida
y extiende el `statement.md` y alimenta la fase 020 (Especificación).

En una frase: *transformar el statement vivo en un **brief de producto validado**, sin huecos ni
contradicciones, listo para especificar.*

## 2. Alcance — qué hace

- **Cuestionamiento socrático** al dueño y al equipo de Sabbia para descubrir "lo que no saben que
  no saben"; no acepta el primer relato como completo.
- **Identifica actores** del sistema (p. ej. empresa cliente, usuarios del portal, roles del equipo
  de Sabbia en el backoffice) y, para cada uno, **≥1 objetivo de valor**.
- **Resuelve las decisiones abiertas** del statement §10 (roles finos del backoffice, impersonación
  con auditoría, roles dentro del portal del cliente, perfiles por industria, SLA de avance,
  conectores, destino del "010 discovery" como feature de onboarding).
- **Explora el comportamiento esperado ante fallos** (insumo directo para la *Error & Exception
  Policy* del 020).
- **Valida y extiende el `statement.md`**: lo deja confirmado como fuente de verdad de producto.

## 3. Alcance — qué NO hace (límites)

- **No** escribe specs funcionales detalladas con criterios de aceptación → eso es el **020**.
- **No** define arquitectura, modelo de datos, stack ni RLS/multi-tenancy → eso es el **030**.
- **No** descompone backlog ni fija orden de construcción → eso es el **040**.
- **No** construye ninguna parte del producto Data Pure → eso es el **050+**.
- **No** diseña la maquinaria agéntica fina del propio harness (instancias, workers, checkpoints,
  rúbrica) → eso es el **diseño del harness**, paso siguiente a este brief.

## 4. Insumos disponibles

| ID | Insumo | Origen |
|----|--------|--------|
| I-1 | Statement de producto (semilla principal) | `800_documents/statement.md` |
| I-2 | Principios y metodología del harness | `810_inputs/principles.md`, `810_inputs/metodology.md` |
| I-3 | Definición de referencia del 010 (otro proyecto) | `700_harnesses/010_discovery_harness.md` |
| I-4 | Conversaciones con el dueño y el equipo de Sabbia | Sesiones de discovery |
| I-5 | Decisiones abiertas a cerrar | `statement.md` §10 |

## 5. Artefactos esperados (salida)

El 010 entrega el paquete que el 020 necesita para arrancar:

| Artefacto | Propósito |
|-----------|-----------|
| **Brief de producto validado** | Statement confirmado y extendido; intención sin ambigüedad. |
| **Shared Understanding** | Resumen en lenguaje natural aprobado explícitamente por Sabbia. |
| **Scope Boundaries** | Qué NO hará Data Pure en esta etapa (exclusiones explícitas). |
| **Glosario de dominio** | Lenguaje ubicuo (salud de datos, tenant, banda, ledger, baseline, S/M/L/XL…). |
| **Failure Behavior** | Comportamiento esperado ante fallos, por escenario. |

> Los *paths* y la mecánica exacta de cada artefacto se fijan en el **diseño del harness**.

## 6. Criterios de éxito (Done)

1. El dueño/equipo de Sabbia **aprueba explícitamente** el entendimiento compartido (statement
   extendido): "Sí, esto es exactamente lo que queremos".
2. **No emergen contradicciones nuevas** en 2 rondas consecutivas de preguntas.
3. **Todos los actores** identificados tienen **≥1 objetivo de valor**.
4. **≥1 comportamiento ante fallos** registrado.
5. Las **decisiones abiertas** del statement §10 quedan resueltas o explícitamente diferidas con
   responsable y motivo.

## 7. Riesgos / advertencias

- **Confusión de planos (Familia A vs. B):** el 010 descubre *el producto a fabricar*, no la
  maquinaria que lo fabrica. No mezclar (ver `CLAUDE.md`, D-002).
- **Reclasificación del "010 discovery" como feature de onboarding** (statement §9, nota; §10):
  decidir si parte del material se reusa como spec/prototipo de Familia B.
- **Decisiones abiertas §10 sin cerrar:** bloquearían la especificación del 020.
- **Sabbia como cliente interno:** el "cliente" entrevistado es el dueño/equipo de Sabbia, no la
  empresa cliente final de Data Pure; no confundir los roles.

## 8. Siguiente paso

Tras aprobar este brief: **diseñar el harness 010** (instancias A/B/C, workers, checkpoints,
rúbrica, contratos), tomando `700_harnesses/010_discovery_harness.md` como guía a adaptar. El **plan
de implementación** del harness viene *después* del diseño (orden del método: brief → diseño → plan →
construir).
