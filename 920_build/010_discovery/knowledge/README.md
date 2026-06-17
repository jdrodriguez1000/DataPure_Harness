# knowledge/ — Base de conocimiento del harness 010 (en runtime)

> **Plano de construcción (D-002).** Aquí viven las **plantillas/moldes** de los dos archivos de
> conocimiento que el harness acumula **al ejecutarse**. Las **instancias reales** se escriben en
> runtime, en `/knowledge/` de la terminal de operación, y por `.gitignore` no vuelven a este repo.

## ⚠️ No confundir planos (dos pares distintos)

| Archivo | Carpeta | Plano | De qué trata |
|---------|---------|-------|--------------|
| `decisions.md` / `lessons.md` | `500_persistence/` | **construcción** | Cómo construimos el harness |
| `decisions_library.md` / `lessons_learned.md` | `/knowledge/` (runtime) | **operación** | Lo que el harness aprende al ejecutarse produciendo Data Pure |

Estos moldes son del **segundo** par.

## Archivos

| Molde | Instancia (runtime) | Quién la escribe |
|-------|---------------------|------------------|
| `decisions_library.template.md` | `/knowledge/decisions_library.md` | **A** (Governor) consolida decisiones de la fase |
| `lessons_learned.template.md` | `/knowledge/lessons_learned.md` | **C** consolida al cerrar la fase (diseño §12.5) |

Referencias en el diseño: §1.2 (A escribe `/knowledge/*`), §12.5 (C consolida `lessons_learned.md`),
y `harness-state.json` → `knowledge.decisions_library` / `knowledge.lessons_learned`.
