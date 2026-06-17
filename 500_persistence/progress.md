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
- **Backlog de ideas (D-015):** nuevo `500_persistence/backlog.md` para ideas sin compromiso
  (estados `No implementada`/`Implementada`/`Descartada`; las descartadas NO se borran) + comando
  `/pure-idea` para capturarlas en cualquier momento. Primera idea: **I-001 cerebro global**.
- **INC-1 en curso (T-010):** **C-8 HECHO** (Sprint Contract en `contract/`) y **C-1 HECHO** — el
  **Governor** construido como `920_build/010_discovery/CLAUDE.md` + skills `harness-start` /
  `harness-continue` (D-017: comandos = skills `SKILL.md`). **Pendiente D-016** (entrevista
  humano↔dialoguer, se resuelve al construir el dialoguer).
- **Escalado a las 10 fases (D-018):** el Governor (A) es **único y genérico**; lo construido para el
  010 es 010-específico a propósito (slice, D-007). Por fase cambia solo el **"paquete de fase"**
  (agentes + contrato + rúbrica + entregables); las skills se generalizan **al construir el 020**.
- **Próximo paso (T-010 → INC-1):** construir los **subagentes** de la cadena (estos sí como
  `.claude/agents/*.md`, doc oficial): C-2 B `phase-orchestrator-010`, C-3 `discovery-dialoguer`,
  C-5 `discovery-synthesizer`, C-6 C `phase-evaluator-010` → producir `shared_understanding.md` +
  `verdict.json` con el gate humano.

## Índice de sesiones / hitos

| ID | Fecha | Hito | Resumen (1 línea) |
|----|-------|------|-------------------|
| S-001 | 2026-06-16 | Acuerdo de método y persistencia | Se definieron los 2 planos, la carpeta `500_persistence/` y se andamió el proyecto |
| S-002 | 2026-06-16 | Andamiaje operativo | Comandos `/pure-next` y `/pure-progress`, `CLAUDE.md` y settings de proyecto/local |
| S-003 | 2026-06-16 | Renombrado de carpetas con prefijo numérico | `500_persistence/`, `700_harnesses/`, `800_documents/`, `810_inputs/` + todas las referencias |
| S-004 | 2026-06-16 | Git/GitHub + meta decidida + brief/diseño/plan del 010 | Repo enlazado y autopush; D-007 resuelta; 3 docs del 010 aprobados; modelo de ejecución plano |
| S-005 | 2026-06-16 | INC-0: andamiaje del harness 010 + cierre P-1/P-2/P-3 | `920_build/010_discovery/` con 7 subcarpetas (schemas/evaluation/contract/skills/deliverables/knowledge/agents); D-013, D-014; L-006, L-007 |
| S-006 | 2026-06-17 | Backlog de ideas + comando `/pure-idea` + idea cerebro global | Nuevo `backlog.md` (5º archivo de persistencia) y `/pure-idea`; I-001 cerebro global; D-015; L-008 (concurrencia 2 terminales) |
| S-007 | 2026-06-17 | INC-1 paso 1: Sprint Contract (C-8) materializado | `contract/sprint_contract.template.md` (molde + `Instancias:` A/B/C + gate P5); D-016 PENDIENTE (entrevista humano↔dialoguer) |
| S-008 | 2026-06-17 | INC-1 paso 2: Governor (C-1) construido | `CLAUDE.md` del harness + skills `harness-start`/`harness-continue` (doc oficial sub-agents/skills); D-017; L-009 |
| S-009 | 2026-06-17 | Decisión: Governor genérico + paquete de fase | D-018 (A único para las 10 fases; por fase cambia el paquete; generalizar al 020) |

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

---

## S-006 — 2026-06-17 — Backlog de ideas, comando `/pure-idea` e idea del cerebro global

**Qué se hizo:**
- **Conversación de arquitectura sobre un "cerebro global"** (memoria cross-project): subir el
  aprendizaje transferible (`lessons.md`/`decisions.md`) de cada proyecto a una BD y consultarlo
  desde cualquier proyecto. Se aclaró: BD ≠ "leíble" por el agente (necesita puente: comando hoy,
  MCP mañana); el valor está en **recuperar en el momento correcto** (inicio de sesión / ante una
  decisión); distinguir aprendizaje **transferible** vs. **específico** (etiqueta de alcance);
  `brain.db` en SQLite **fuera del repo** (`~/.claude/`) y comando `/cerebro` a **nivel usuario**
  para que sea global. Se registró como **I-001** en el nuevo backlog (no se construye ahora).
- **Nuevo archivo de persistencia `500_persistence/backlog.md`** (5º archivo): ideas sin compromiso,
  con estados `No implementada`/`Implementada`/`Descartada` (las descartadas **no se borran**).
  Diferencia con `tasks.md`: aquí no hay certeza de que se construyan (D-015).
- **Nuevo comando `/pure-idea`** (`.claude/commands/pure-idea.md`, ámbito proyecto): captura ideas
  en cualquier momento sin disparar el cierre completo; relee `backlog.md` antes de escribir y hace
  un commit **acotado** solo a ese archivo (robusto a una segunda terminal).

**Hallazgo clave (L-008):** Se puede trabajar con **dos terminales sobre la misma carpeta**, pero
escribir `500_persistence/` en paralelo rompe el single-writer (D-003): sin bloqueo, la última
escritura pisa a la otra y puede generar conflictos de git. Mitigación: serializar escrituras (solo
una terminal escribe a la vez, `git pull`/re-leer antes) o usar un git worktree para trabajo
paralelo real.

**Dónde quedamos:** Tooling de persistencia ampliado (backlog + `/pure-idea`). T-010 (INC-1) sigue
siendo la próxima tarea de construcción del harness, sin cambios.

---

## S-007 — 2026-06-17 — INC-1 (paso 1): Sprint Contract materializado y duda de la entrevista

**Qué se hizo:**
- **C-8 (Sprint Contract) materializado** como molde en
  `920_build/010_discovery/contract/sprint_contract.template.md`, base en diseño §9. Es **plantilla**
  (D-002/L-006): A la instancia, propone al humano y aprueba en runtime (gate P5).
- Se añadió a petición del usuario una línea **`Instancias:`** (A=Governor C-1 · B=phase-orchestrator-010
  C-2 · C=phase-evaluator-010 C-6) y un bloque **"Aprobación del contrato (gate P5)"**.
- Se aclaró la diferencia **molde vs. instancia** del contrato y que su "salida" es el **acuerdo
  aprobado** (gobernanza), no los entregables: los 5 artefactos + transcript/analysis los producen
  los **workers**, no el contrato.
- Se aclaró **quién entrevista**: el `discovery-dialoguer` (C-3) posee el guión socrático y el
  `dialogue_transcript.md`, pero en el modelo plano (D-011) el canal en vivo con el humano lo opera A.

**Decisión registrada (D-016, PENDIENTE):** cómo se cablea exactamente la entrevista humano↔dialoguer
en el modelo plano (A media vs. el dialoguer pregunta en vivo). Se resuelve al construir el skeleton.

**Dónde quedamos:** INC-1 paso 1 completo (C-8). Falta el grueso de INC-1: el **walking skeleton**
(C-1, C-2, C-3, C-5, C-6) que produce `shared_understanding.md` + `verdict.json` con el gate.

---

## S-008 — 2026-06-17 — INC-1 (paso 2): Governor (C-1) construido

**Qué se hizo:**
- Se consultó la **documentación oficial de Claude Code** (sub-agents y skills) que pidió el usuario.
- Se construyó el **Governor (C-1)** en tres archivos dentro de `920_build/010_discovery/`:
  - `CLAUDE.md` — instrucciones del **hilo principal** (A): patrón de 3 instancias, estado/persistencia,
    gate P5, checkpoints CP-01a…CP-04, flujo §12, escalamiento P6, scaffold C-10.
  - `skills/harness-start/SKILL.md` — ritual E10-A (arranque: scaffold, inputs, contrato+gate, cadena
    B→workers→C).
  - `skills/harness-continue/SKILL.md` — ritual E10-B (reanudar desde el último checkpoint).
- Skills con `disable-model-invocation: true` (los dispara el humano) y `allowed-tools: Read, Write,
  Edit, Bash, Agent` (A necesita `Agent` para spawnear). Se marcó el **alcance INC-1** (analyst C-4
  llega en INC-2). Se quitó el `.gitkeep` de `skills/`.

**Hallazgos / decisiones:**
- **D-017 (FIRME):** el Governor **no es subagente** (es la sesión principal, D-011) y sus comandos
  se construyen como **Skills** (`SKILL.md`), no como `.claude/commands/*.md`.
- **L-009:** la doc oficial confirma que "custom commands have been merged into skills" (un
  `/x` por `commands/x.md` o por `skills/x/SKILL.md` es equivalente; los skills son lo recomendado).
- El `harness-start` deja explícito que **D-016** se resuelve al construir el dialoguer.

**Dónde quedamos:** C-1 y C-8 hechos. Falta la **cadena de subagentes** del skeleton (C-2 B, C-3
dialoguer, C-5 synthesizer, C-6 C) — esos sí como `.claude/agents/*.md` por la doc oficial.

---

## S-009 — 2026-06-17 — Decisión: Governor genérico + paquete de fase (D-018)

**Qué se hizo:** Conversación de arquitectura sobre la reutilización del Governor en otras fases
(p. ej. 020 Specification). Se registró como decisión (a pedido del usuario).

**Decisión registrada (D-018, FIRME):** el rol **A (Governor) es único y genérico** para las 10
fases (misma sesión, un `harness-state.json` global, D-006). NO se crea un Governor por fase; lo que
cambia es el **"paquete de fase"** (subagentes `phase-*-0X0`, Sprint Contract, rúbrica, plantillas de
entregables). Las skills `harness-start`/`harness-continue` deben volverse **parametrizadas por
fase**. Lo construido para el 010 es 010-específico a propósito (vertical slice, D-007); la
**generalización** (extraer lo genérico) se hace **al construir el 020**, no ahora.

**Dónde quedamos:** Sin cambios en la construcción; INC-1 sigue igual (falta la cadena de subagentes).
