# progress.md — Avance del proyecto "DataPure Harness"

> Puerta de entrada de la persistencia. Cualquier agente de Claude Code empieza aquí.
> Lee primero el **Estado actual** y el **Índice**; salta a `tasks.md`, `decisions.md` o
> `lessons.md` solo si necesitas el detalle. No leas todo: ahorra tokens.

## Estado actual (siempre vigente)

Estamos construyendo el **harness 010 (Discovery)**, el primero del *meta-harness* **DataPure
Harness** (Familia A) que fabricará el SaaS **Data Pure** (`800_documents/statement.md`). Ya está
**decidida la meta** (D-007 FIRME) y completado el ciclo de definición del 010 con tres documentos
**APROBADOS**: brief → diseño → plan. **En construcción (T-010):** **INC-0 HECHO** — andamiaje
`920_build/010_discovery/` creado y P-1/P-2/P-3 cerradas. Próximo: **INC-1** (walking skeleton).

- **Repo en git/GitHub (D-010):** `main` enlazado a `https://github.com/jdrodriguez1000/DataPure_Harness.git`.
  `/pure-progress` ahora hace commit + push al cerrar sesión.
- **Documentos del 010 (APROBADOS):** `900_brief/010_discovery.md`, `905_design/010_discovery.md`,
  `910_plan/010_discovery.md`. Orden del método (D-012): **brief → diseño → plan → construir**.
- **Modelo de ejecución del harness (D-011 FIRME):** **plano** — A (Governor) = sesión principal y
  único spawner; B planifica; los subagentes no spawnean de forma fiable entre versiones.
- **Dos planos (D-002):** esta terminal construye; el harness corre en OTRA terminal con su propia
  persistencia (`claude-progress.txt`, `harness-state.json`, `execution-state.json`). No mezclar.
- **Estructura del build (D-014):** `920_build/010_discovery/` con 7 subcarpetas: `agents/`,
  `schemas/` (estado C-7), `evaluation/` (salidas de C: verdict/metrics C-9), `contract/` (solo
  Sprint Contract), `skills/` (habilidades), `deliverables/` (moldes de los 5 entregables) y
  `knowledge/` (moldes de `decisions_library`/`lessons_learned` del harness en runtime). Todo lo de
  runtime se guarda como **moldes** (L-006); no confundir `knowledge/*` con `500_persistence/` (L-007).
- **Próximo paso (T-010 → INC-1):** materializar el Sprint Contract en `contract/` y construir el
  walking skeleton end-to-end (A→B→1 worker→C produce `shared_understanding.md` + `verdict.json`).

## Índice de sesiones / hitos

| ID | Fecha | Hito | Resumen (1 línea) |
|----|-------|------|-------------------|
| S-001 | 2026-06-16 | Acuerdo de método y persistencia | Se definieron los 2 planos, la carpeta `500_persistence/` y se andamió el proyecto |
| S-002 | 2026-06-16 | Andamiaje operativo | Comandos `/pure-next` y `/pure-progress`, `CLAUDE.md` y settings de proyecto/local |
| S-003 | 2026-06-16 | Renombrado de carpetas con prefijo numérico | `500_persistence/`, `700_harnesses/`, `800_documents/`, `810_inputs/` + todas las referencias |
| S-004 | 2026-06-16 | Git/GitHub + meta decidida + brief/diseño/plan del 010 | Repo enlazado y autopush; D-007 resuelta; 3 docs del 010 aprobados; modelo de ejecución plano |
| S-005 | 2026-06-16 | INC-0: andamiaje del harness 010 + cierre P-1/P-2/P-3 | `920_build/010_discovery/` con 7 subcarpetas (schemas/evaluation/contract/skills/deliverables/knowledge/agents); D-013, D-014; L-006, L-007 |

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

---

## S-004 — 2026-06-16 — Git/GitHub, meta del proyecto y ciclo de definición del harness 010

**Qué se hizo:**
- **T-007 (git/GitHub) completada:** `git init -b main`; remote `origin` →
  `https://github.com/jdrodriguez1000/DataPure_Harness.git`; `.gitignore` (excluye
  `settings.local.json` y los state-files del plano de operación) y `.gitattributes` (LF); commit
  inicial y push. Se modificó `/pure-progress` para que tras actualizar persistencia haga
  **commit + push** automático (D-010).
- **Meta del proyecto decidida (D-007 → FIRME):** construir el harness 010 siguiendo el orden
  **brief → diseño → plan → construir**, usando `700_harnesses/` como **referencia** (de otro
  proyecto), no como verdad.
- **Ciclo de definición del 010 (3 documentos APROBADOS):**
  - `900_brief/010_discovery.md` — brief de enmarque (alto nivel, "opción B").
  - `905_design/010_discovery.md` — diseño agéntico (instancias A/B/C, workers, checkpoints,
    rúbrica, Sprint Contract).
  - `910_plan/010_discovery.md` — plan de implementación (10 componentes C-1…C-10, incrementos
    INC-0…INC-5, vertical slice, decisiones abiertas P-1/P-2/P-3).

**Hallazgo clave (L-005):** Se verificó en la doc de Claude Code que **los subagentes SÍ pueden
spawnear anidado desde v2.1.172** (foreground, con `Agent` en tools) — la premisa "ningún subagente
spawnea" estaba desactualizada. Aun así se adoptó el **modelo de ejecución plano** (D-011) por ser
robusto a la versión del runtime.

**Dónde quedamos:** Definición del 010 cerrada y aprobada. La próxima tarea (T-010) es **ejecutar el
plan**: INC-0 (andamiaje `920_build/010_discovery/` + cerrar P-1/P-2/P-3) y luego INC-1 (walking
skeleton end-to-end).

---

## S-005 — 2026-06-16 — INC-0: andamiaje del harness 010 y cierre de decisiones abiertas

**Qué se hizo:**
- **INC-0 del plan ejecutado.** Se creó la estructura `920_build/010_discovery/` con `README.md` y
  subcarpetas: `agents/` (marcador), `schemas/`, `contract/`, `skills/`, `deliverables/`.
- **Esquemas mínimos C-7/C-9 (PLANTILLAS, no instancias — D-002):** `harness-state.template.json`,
  `execution-state.template.json`, `claude-progress.template.txt`, `verdict.template.json`,
  `metrics_summary.template.json`. Los 4 JSON validados sintácticamente (criterio Done de INC-0).
- **Cierre de P-1/P-2/P-3 (D-013):** ubicación `920_build/010_discovery/`; Governor = CLAUDE.md del
  harness + 1 comando; modelos Opus (B/analyst/C) y Sonnet (dialoguer/synthesizer).
- **Ajustes de estructura solicitados por el usuario (D-014):** `templates/`→`contract/` (solo el
  Sprint Contract); nueva `skills/` (habilidades de agentes); nueva `deliverables/` con **moldes** de
  los 5 entregables (`*.template.md`); nueva `knowledge/` con moldes de `decisions_library` y
  `lessons_learned` del harness; nueva `evaluation/` a la que se **movieron** `verdict` y
  `metrics_summary` (vía `git mv`), dejando `schemas/` solo con esquemas de **estado**.

**Hallazgos clave:**
- **L-006:** los 5 entregables (y la base de conocimiento) del harness son **salidas de runtime**;
  en construcción se guardan como **moldes**, no como instancias.
- **L-007:** existen **dos pares** de lecciones/decisiones — `500_persistence/` (construir) vs.
  `knowledge/*` del harness (operar). No confundirlos (D-002).

**Flujo de despliegue discutido (→ T-011):** se mapeó el flujo previo de **3 comandos**
(install/init/discovery) a la estructura del build: `install` copia infra (`agents/ skills/ contract/`
+ C-1), `init` instancia moldes de `schemas/ knowledge/ evaluation/`, `discovery` (Governor) ejecuta.
La partición de las 7 subcarpetas mapea 1:1. Se **difiere a después de INC-5** (E4/E9) para no
construir el instalador antes de que corra el esqueleto; irá en `920_build/010_discovery/deploy/`.

**Dónde quedamos:** INC-0 completo (estructura del build estabilizada). La próxima es **INC-1**
(T-010 sigue EN CURSO): materializar el Sprint Contract en `contract/` y construir el walking
skeleton end-to-end.
