# progress.md — Avance del proyecto "DataPure Harness"

> Puerta de entrada de la persistencia. Cualquier agente de Claude Code empieza aquí.
> Lee primero el **Estado actual** y el **Índice**; salta a `tasks.md`, `decisions.md` o
> `lessons.md` solo si necesitas el detalle. No leas todo: ahorra tokens.

## Estado actual (siempre vigente)

Estamos en la **fase de arranque** de la construcción del *meta-harness* **DataPure Harness**:
la fábrica agéntica (Familia A) que, cuando esté lista, construirá el SaaS **Data Pure**
(ver `800_documents/statement.md`). Aún **no** se ha empezado a construir ningún harness (010–100);
hasta ahora se definió cómo vamos a trabajar, la estrategia de persistencia, y se montó el
andamiaje operativo (`CLAUDE.md`, comandos `/pure-next` y `/pure-progress`, settings).

- **Plano de trabajo de esta terminal:** construir la máquina (definiciones, código, esquemas).
- **El harness DataPure ya construido se ejecutará en OTRA terminal**, con su propia persistencia
  (`claude-progress.txt`, `harness-state.json`, `execution-state.json`). No mezclar.
- **Decisión de meta del proyecto:** PENDIENTE (ver `tasks.md` → próximas).
- **Esquema de carpetas (D-009):** `500_persistence/`, `700_harnesses/`, `800_documents/`,
  `810_inputs/` (renombrado aplicado en S-003).
- **Próximo paso:** decidir la meta del proyecto (T-005) o git/GitHub (T-007).

## Índice de sesiones / hitos

| ID | Fecha | Hito | Resumen (1 línea) |
|----|-------|------|-------------------|
| S-001 | 2026-06-16 | Acuerdo de método y persistencia | Se definieron los 2 planos, la carpeta `500_persistence/` y se andamió el proyecto |
| S-002 | 2026-06-16 | Andamiaje operativo | Comandos `/pure-next` y `/pure-progress`, `CLAUDE.md` y settings de proyecto/local |
| S-003 | 2026-06-16 | Renombrado de carpetas con prefijo numérico | `500_persistence/`, `700_harnesses/`, `800_documents/`, `810_inputs/` + todas las referencias |

---

## S-001 — 2026-06-16 — Acuerdo de método y persistencia

**Qué se hizo:**
- Se leyeron y analizaron `800_documents/statement.md`, `810_inputs/principles.md`,
  `810_inputs/metodology.md` y los 10 harnesses (`700_harnesses/010`…`100`).
- Se aclaró la distinción entre los dos planos: **construir** el harness (esta terminal) vs.
  **operar** el harness (otra terminal).
- Se acordó la estrategia de persistencia del proyecto: carpeta `500_persistence/` con 4 archivos
  indexados (`progress.md`, `tasks.md`, `lessons.md`, `decisions.md`).
- Se creó `CLAUDE.md` con la descripción del proyecto y el protocolo de inicio/cierre de sesión.
- Se andamiaron los 4 archivos de persistencia con las decisiones, lecciones y tareas a la fecha.

**Hallazgos relevantes (detalle en `lessons.md` / `decisions.md`):**
- Los harnesses **010–050 están completos**; **060–100 están esqueléticos** (solo "Fase 0").
- Inconsistencias menores a reconciliar: la metodología dice "9 arneses" pero hay **10**
  (el `100_change_harness` no está mapeado a capas); nombres de fase difieren entre el
  statement (§9) y los archivos de `700_harnesses/`.

**Dónde quedamos:** listos para crear los comandos `next` y `progress` (ver `tasks.md` → T-004).

---

## S-002 — 2026-06-16 — Andamiaje operativo

**Qué se hizo:**
- Se creó `CLAUDE.md` en la raíz con la descripción del proyecto y el **protocolo obligatorio de
  sesión** (leer persistencia al inicio, actualizarla al cierre).
- Se creó la carpeta `.claude/commands/` con dos comandos de proyecto:
  - **`/pure-progress`** — cierre de sesión: actualiza los 4 archivos de `500_persistence/`.
  - **`/pure-next`** — inicio de sesión: lee la persistencia (solo lectura) y orienta sobre estado
    y próxima tarea.
- Se crearon los settings de Claude Code: `.claude/settings.json` (ámbito proyecto, con permisos
  base + comandos git seguros) y `.claude/settings.local.json` (ámbito local, vacío).

**Dónde quedamos:** T-004 completa. La próxima tarea es **renombrar las carpetas del proyecto**
(T-008). Pendiente aún: meta del proyecto (T-005) y git/GitHub (T-007).

---

## S-003 — 2026-06-16 — Renombrado de carpetas con prefijo numérico

**Qué se hizo:**
- Se renombraron las 4 carpetas del proyecto a nomenclatura con prefijo numérico:
  `persistence/`→`500_persistence/`, `Harnesses/`→`700_harnesses/`,
  `documents/`→`800_documents/`, `inputs/`→`810_inputs/` (D-009).
- Se actualizaron **todas** las referencias en 9 archivos: `CLAUDE.md`, los dos comandos
  (`.claude/commands/pure-next.md`, `pure-progress.md`), `810_inputs/metodology.md`
  (`@Harnesses`→`@700_harnesses`), `800_documents/statement.md`, y los 4 de `500_persistence/`.

**Hallazgo / criterio clave (L-004):**
- Las ~50 referencias a `persistence/...json/.txt` dentro de `700_harnesses/` se dejaron
  **intactas**: pertenecen al **plano de operación** (D-002), no a la carpeta de este proyecto.
  Renombrarlas habría mezclado los dos planos.

**Dónde quedamos:** T-008 completa. Próximas: decidir meta del proyecto (T-005, bloqueada por
D-007 PENDIENTE), reconciliar harnesses (T-006) y git/GitHub (T-007).
