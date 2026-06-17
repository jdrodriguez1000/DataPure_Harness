# decisions.md — Decisiones del proyecto "DataPure Harness"

> Registro de decisiones de arquitectura/proceso (DA). Lee el **Índice**; el detalle por ID abajo.
> Estados: `FIRME` (acordada) · `PROVISIONAL` (sujeta a revisión) · `PENDIENTE` (sin decidir).

## Índice

| ID | Decisión | Estado | Resumen (1 línea) |
|----|----------|--------|-------------------|
| D-001 | Persistencia en `500_persistence/` con 4 archivos indexados | FIRME | Fuente de verdad de la construcción del proyecto |
| D-002 | Dos planos separados: construir vs. operar | FIRME | Esta terminal construye; el harness corre en otra terminal con su propia persistencia |
| D-003 | Single-writer de `500_persistence/` = instancia principal | FIRME | Solo la sesión principal actualiza los 4 archivos; los subagentes escriben artefactos |
| D-004 | Índice obligatorio al inicio de cada archivo | FIRME | Ahorro de tokens: leer índice y saltar a la sección (E6) |
| D-005 | `progress.md` es la puerta de entrada (bootstrap) | FIRME | Ritual de inicio: leer progress → derivar a los otros 3 |
| D-006 | `harness-state.json` global, no por fase (para el harness construido) | PROVISIONAL | Permite handoffs verificables entre las 10 fases |
| D-007 | Meta del proyecto: construir el harness 010 | FIRME | brief→diseño→plan→construir; `700_harnesses/` como referencia |
| D-008 | Par de comandos `/pure-next` + `/pure-progress` como ritual de sesión | FIRME | `/pure-next` abre (lee), `/pure-progress` cierra (escribe) la persistencia |
| D-009 | Nomenclatura de carpetas con prefijo numérico | FIRME | `500_persistence`, `700_harnesses`, `800_documents`, `810_inputs` |
| D-010 | Git/GitHub + autopush en `/pure-progress` | FIRME | `main` enlazado a remote; el cierre de sesión hace commit+push |
| D-011 | Modelo de ejecución del harness = **plano** | FIRME | A=sesión principal y único spawner; B planifica; robusto a la versión del runtime |
| D-012 | Convención de fases de construcción por harness | FIRME | `900_brief/` → `905_design/` → `910_plan/` → `920_build/`; orden del método |
| D-013 | Cierre de decisiones abiertas del plan (P-1/P-2/P-3) | FIRME | Ubicación `920_build/010_discovery/`; Governor = CLAUDE.md+1 comando; modelos Opus/Sonnet por agente |
| D-014 | Estructura interna del build del harness 010 | FIRME | 7 subcarpetas: `agents/ schemas/ evaluation/ contract/ skills/ deliverables/ knowledge/`; runtime = MOLDES |
| D-015 | Backlog de ideas (`backlog.md`) + comando `/pure-idea` | FIRME | 5º archivo de persistencia para ideas sin compromiso; descartadas no se borran |
| D-016 | Cómo se cablea la entrevista humano↔dialoguer en el modelo plano | PENDIENTE | El dialoguer posee el guión/transcript, pero la charla en vivo la opera A; resolver en INC-1 |
| D-017 | Governor = sesión principal (no subagente); sus comandos se construyen como Skills | FIRME | C-1 = CLAUDE.md del harness + `skills/*/SKILL.md`; subagentes solo B/workers/C |
| D-018 | Un Governor genérico para las 10 fases + un "paquete de fase" por harness | FIRME | A es la misma sesión (un `harness-state` global, D-006); por fase cambian agentes/contrato/rúbrica/entregables. Generalizar al construir el 020 |

---

## D-001 — Persistencia en `500_persistence/` con 4 archivos indexados
**Fecha:** 2026-06-16 · **Estado:** FIRME
**Contexto:** El proyecto necesita memoria entre sesiones (E1) sin gastar tokens.
**Decisión:** Carpeta `500_persistence/` con `progress.md`, `tasks.md`, `lessons.md`, `decisions.md`.
Cada archivo lleva un índice arriba. Es la única fuente de verdad de *la construcción* del
DataPure Harness.
**Consecuencias:** Mapea con §11 de la metodología (decisions_library / lessons_learned).

## D-002 — Dos planos separados: construir vs. operar
**Fecha:** 2026-06-16 · **Estado:** FIRME
**Contexto:** Confusión entre construir la máquina y correrla.
**Decisión:** *Esta* terminal/carpeta es **exclusiva de construir** el meta-harness. El harness
DataPure, una vez construido, **se ejecuta en otra terminal** con su propia persistencia
(`claude-progress.txt`, `harness-state.json`, `execution-state.json`).
**Consecuencias:** Nunca mezclar los archivos de `500_persistence/` con los del harness en operación.

## D-003 — Single-writer de `500_persistence/` = instancia principal
**Fecha:** 2026-06-16 · **Estado:** FIRME
**Contexto:** Evitar condiciones de carrera (regla single-writer de la metodología §4.1).
**Decisión:** Solo la sesión principal de Claude Code actualiza los 4 archivos de `500_persistence/`.
Los subagentes que se lancen para tareas de construcción escriben *artefactos*, no la persistencia.
**Consecuencias:** Persistencia consistente; subagentes amnésicos por diseño (ver `lessons.md` L-001).

## D-004 — Índice obligatorio al inicio de cada archivo
**Fecha:** 2026-06-16 · **Estado:** FIRME
**Decisión:** Cada archivo de persistencia empieza con una tabla-índice (ID + resumen 1 línea).
Los agentes leen el índice y saltan solo a la sección necesaria.
**Consecuencias:** Implementa el espíritu de E6 (referencias ligeras) para economía de tokens.

## D-005 — `progress.md` es la puerta de entrada (bootstrap)
**Fecha:** 2026-06-16 · **Estado:** FIRME
**Decisión:** El ritual de inicio de sesión (E10) arranca leyendo `progress.md`; desde ahí se
deriva a `tasks.md`, `decisions.md` o `lessons.md` según se necesite.

## D-006 — `harness-state.json` global, no por fase
**Fecha:** 2026-06-16 · **Estado:** PROVISIONAL
**Contexto:** El harness construido encadenará 10 fases (010–100) sobre un mismo producto.
**Decisión (tentativa):** Un único `harness-state.json` a nivel proyecto que lleve el estado de
las 10 fases; `execution-state.json` y `/eval` viven por fase.
**Por qué provisional:** Se ratifica al construir el harness; aplica al plano de operación, no al de
construcción.

## D-007 — Meta del proyecto: construir el harness 010
**Fecha:** 2026-06-16 · **Estado:** FIRME (era PENDIENTE)
**Contexto:** Había tres opciones (operacionalizar / completar 060–100 / ejecutar 010).
**Decisión:** Construir **primero el harness 010 (Discovery)** end-to-end, siguiendo el orden del
método **brief → diseño → plan → construir**, y usando `700_harnesses/` como **referencia** (material
de otro proyecto), no como verdad. Es un *vertical slice*: levantar un harness real antes de
generalizar.
**Consecuencias:** Origina la convención de carpetas D-012 y el modelo de ejecución D-011. Próximo:
ejecutar el plan (T-010).

## D-008 — Par de comandos `/pure-next` + `/pure-progress` como ritual de sesión
**Fecha:** 2026-06-16 · **Estado:** FIRME
**Contexto:** El protocolo de sesión de `CLAUDE.md` (leer al inicio, actualizar al cierre) necesita
una forma operativa y repetible que ahorre tokens.
**Decisión:** Dos comandos de proyecto en `.claude/commands/`: **`/pure-next`** (inicio, solo
lectura, orienta) y **`/pure-progress`** (cierre, único que escribe `500_persistence/`). Simétricos:
uno abre, otro cierra.
**Consecuencias:** El ritual E10 queda automatizado; refuerza D-003 (single-writer) y D-004
(lectura por índice).

## D-009 — Nomenclatura de carpetas con prefijo numérico
**Fecha:** 2026-06-16 · **Estado:** FIRME
**Contexto:** Las carpetas tenían nombres heterogéneos; se busca un orden consistente y ordenable.
**Decisión:** Carpetas del proyecto con prefijo numérico: `500_persistence/`, `700_harnesses/`,
`800_documents/`, `810_inputs/`. Aplicado el renombrado y actualizadas todas las referencias.
**Alcance (importante):** Solo aplica a las carpetas del **plano de construcción** (este repo).
Las rutas `persistence/...json/.txt` dentro de `700_harnesses/` son del **plano de operación**
(D-002) y NO se renombran. Ver L-004.
**Consecuencias:** Refuerza D-002 (separación de planos). Cualquier carpeta futura del proyecto
debería seguir el patrón `NNN_nombre`.

## D-010 — Git/GitHub + autopush en `/pure-progress`
**Fecha:** 2026-06-16 · **Estado:** FIRME
**Contexto:** La metodología exige git + remote desde el arranque (E1/P8); el repo no existía.
**Decisión:** `git init -b main` + remote `origin` a
`https://github.com/jdrodriguez1000/DataPure_Harness.git`. `.gitignore` excluye `settings.local.json`
y los state-files del plano de operación; `.gitattributes` normaliza a LF. El comando
`/pure-progress` hace `git add -A` + commit (mensaje en español, referencia IDs, `Co-Authored-By`) +
`git push origin main` tras actualizar la persistencia.
**Consecuencias:** Trazabilidad desde el inicio. `gh` CLI no está instalado; se usa `git` directo
(autenticación vía Git Credential Manager).

## D-011 — Modelo de ejecución del harness = plano
**Fecha:** 2026-06-16 · **Estado:** FIRME
**Contexto:** El patrón de 3 instancias implica A→B→Workers. En Claude Code el spawn anidado de
subagentes solo existe desde v2.1.172 (foreground, con `Agent` en tools); el runtime del harness
corre en otra terminal de versión no controlada (ver L-005).
**Decisión:** Modelo **plano**: la **sesión principal ES la Instancia A (Governor) y el único
spawner**. B (subagente) **planifica** y devuelve el `orchestration_plan`; A ejecuta los spawns de
Workers y de C. C sigue siendo subagente independiente (P3 intacto).
**Consecuencias:** Robusto a la versión del runtime; coincide con `metodology.md` §12.1. A asume
también la escritura de `execution-state.json` (checkpoints). Aplicado en `905_design/010_discovery.md`.

## D-012 — Convención de fases de construcción por harness
**Fecha:** 2026-06-16 · **Estado:** FIRME
**Contexto:** Construir cada harness requiere pasos ordenados y carpetas predecibles.
**Decisión:** Por cada harness NNN se produce, en este orden: **`900_brief/NNN.md` → `905_design/NNN.md`
→ `910_plan/NNN.md` → `920_build/NNN/`** (artefactos construidos). El plan de implementación va
*después* del diseño (no se planea lo que no está diseñado).
**Consecuencias:** Reusable para 020–100. Los artefactos de `920_build/` son definiciones (plano de
construcción) que se despliegan al `.claude/agents/` de la terminal de operación; las *instancias* de
estado se crean en runtime (D-002).

## D-013 — Cierre de decisiones abiertas del plan (P-1/P-2/P-3)
**Fecha:** 2026-06-16 · **Estado:** FIRME
**Contexto:** El plan `910_plan/010_discovery.md` §9 dejaba 3 decisiones a cerrar en INC-0.
**Decisión:**
- **P-1 (ubicación de artefactos construidos):** `920_build/010_discovery/` (coincide con D-012).
- **P-2 (forma del Governor, C-1):** **CLAUDE.md del harness + 1 comando** (no varios comandos).
- **P-3 (modelo por agente):** **Opus** para B (phase-orchestrator), analyst y C (evaluator);
  **Sonnet** para dialoguer y synthesizer (ejecución documental).
**Consecuencias:** Desbloquea INC-0/INC-1. Los modelos se fijan al construir cada agente en `agents/`.

## D-014 — Estructura interna del build del harness 010
**Fecha:** 2026-06-16 · **Estado:** FIRME
**Contexto:** Al andamiar INC-0 el usuario pidió ajustes a la organización de `920_build/010_discovery/`.
**Decisión:** **7 subcarpetas** en `920_build/010_discovery/`:
- **`agents/`** — subagentes B/workers/C.
- **`schemas/`** — esquemas de **estado** C-7: `harness-state`, `execution-state`, `claude-progress`.
- **`evaluation/`** — salidas de la Instancia C (C-9): `verdict`, `metrics_summary`. *(Movidas desde
  `schemas/` para separar estado vivo de salidas de evaluación.)*
- **`contract/`** — SOLO el Sprint Contract C-8 (renombrada desde la propuesta `templates/`).
- **`skills/`** — habilidades reutilizables de los agentes.
- **`deliverables/`** — moldes de los 5 entregables principales.
- **`knowledge/`** — moldes de `decisions_library.md` y `lessons_learned.md` (base de conocimiento
  que el harness acumula en runtime).

**Alcance (importante, D-002):** `deliverables/`, `knowledge/` y `evaluation/` guardan **moldes**
(`*.template.*`), NO instancias reales: esas son salidas de runtime y viven en la terminal de
operación (ver L-006). No confundir `knowledge/*` con `500_persistence/` (ver L-007). Si en el futuro
se quisiera que alguna de estas carpetas fuera el destino real de los documentos, habría que revisar
la separación de planos y el `.gitignore` (decisión pendiente, no tomada).
**Consecuencias:** Extiende D-012. Patrón reusable para 020–100.

## D-015 — Backlog de ideas (`backlog.md`) + comando `/pure-idea`
**Fecha:** 2026-06-17 · **Estado:** FIRME
**Contexto:** Surgen ideas que tal vez se construyan o tal vez no; mezclarlas en `tasks.md` (trabajo
comprometido) confunde el tablero, y esperar al cierre `/pure-progress` para anotarlas hace perder
ideas que aparecen a mitad de sesión.
**Decisión:** Quinto archivo de persistencia `500_persistence/backlog.md` para ideas sin compromiso,
con índice por ID `I-xxx` y estados `No implementada` (por defecto) · `Implementada` · `Descartada`.
Las **descartadas no se borran** (el futuro cambia y podrían reactivarse). Se añade el comando de
proyecto `/pure-idea` (`.claude/commands/pure-idea.md`) que captura ideas en cualquier momento sin
disparar el cierre completo: relee `backlog.md` antes de escribir (no asume el último ID de memoria)
y hace un commit **acotado solo a ese archivo** para ser robusto a una segunda terminal (ver L-008).
Mover estados sigue siendo parte de `/pure-progress` al cierre.
**Consecuencias:** El protocolo de sesión (CLAUDE.md) ahora tiene 5 archivos de persistencia, no 4
(pendiente reflejarlo en CLAUDE.md si se desea formalizar). El single-writer (D-003) sigue vigente.

## D-016 — Cómo se cablea la entrevista humano↔dialoguer en el modelo plano
**Fecha:** 2026-06-17 · **Estado:** PENDIENTE
**Contexto:** El diseño (§3/§4) asigna la entrevista socrática al worker `discovery-dialoguer`
(C-3), que posee el guión I-1…I-5 y produce `dialogue_transcript.md`. Pero una entrevista es
**interactiva y multironda**, y en el modelo plano (D-011) los workers son subagentes **amnésicos**
(L-001) que corren, producen un artefacto y vuelven: la única instancia que conversa con el humano
en tiempo real es **A (Governor)**.
**Opciones:**
1. **A media la conversación:** el dialoguer genera/estructura las preguntas; A las presenta a
   Sabbia, recoge respuestas y las realimenta; el dialoguer arma el transcript. (Coherente con el
   modelo plano; preferida a priori.)
2. **El dialoguer entrevista en vivo:** solo si el runtime permite que un subagente prompt-ee al
   humano directamente. Más frágil entre versiones (motivo de D-011 / L-005).
**Por qué pendiente:** Se resuelve al construir el **walking skeleton de INC-1**, que debe ejercitar
la interacción humano↔dialoguer y validar empíricamente cuál opción sostiene el modelo plano.
**Consecuencias:** Condiciona el cableado de C-1 (A) y C-3 (dialoguer) y los checkpoints CP-01a/CP-01.

## D-017 — Governor = sesión principal (no subagente); sus comandos se construyen como Skills
**Fecha:** 2026-06-17 · **Estado:** FIRME
**Contexto:** Al construir C-1 se pidió "crear el agente Governor" siguiendo la doc oficial de
subagentes. Pero el Governor (A) es, por D-011/D-013, la **sesión principal** y único spawner — no un
subagente. Además la doc oficial de skills indica que "custom commands have been merged into skills".
**Decisión:** C-1 se materializa como (1) `920_build/010_discovery/CLAUDE.md` (instrucciones del hilo
principal) + (2) skills `skills/harness-start/SKILL.md` y `skills/harness-continue/SKILL.md` (con
`disable-model-invocation: true` y `allowed-tools: Read, Write, Edit, Bash, Agent`). Los **subagentes**
(`.claude/agents/*.md`) se reservan para B (`phase-orchestrator-010`), los workers (dialoguer/analyst/
synthesizer) y C (`phase-evaluator-010`). La doc de sub-agents aplica a esos, no al Governor.
**Consecuencias:** Coherente con D-011/D-013 y con el patrón de skills recomendado (ver L-009). El
instalador (T-011) copia `CLAUDE.md` a la raíz de operación y `skills/` a `.claude/skills/`.

## D-018 — Un Governor genérico para las 10 fases + un "paquete de fase" por harness
**Fecha:** 2026-06-17 · **Estado:** FIRME
**Contexto:** Se preguntó si el Governor construido para el 010 se reutiliza en otras fases (p. ej.
020 Specification) y si hay que crearle habilidades por fase.
**Decisión:** El **rol A (Governor) es invariante** y la **misma sesión principal** gobierna las 10
fases encadenadas (coherente con D-006: un solo `harness-state.json` global). NO se crea un Governor
nuevo por fase. Lo que cambia por fase es un **"paquete de fase"**: sus subagentes
(`phase-orchestrator-0X0`, workers, `phase-evaluator-0X0`), su Sprint Contract, su rúbrica y sus
plantillas de entregables. Las skills genéricas `harness-start` / `harness-continue` deben volverse
**parametrizadas por fase** (mismos rituales E10-A/B; invocan los agentes/artefactos de la fase
activa).
**Alcance temporal (D-007):** lo construido hoy para el 010 es **010-específico a propósito** (vertical
slice). La factorización "Governor genérico + paquete de fase" es un **paso de generalización
posterior**, a ejecutar **al construir el 020**, no ahora.
**Consecuencias:** Para el 020 se construye solo su paquete de fase (no un Governor). Implica una tarea
futura de refactor: extraer lo genérico del `CLAUDE.md`/skills del 010 cuando arranque el 020.
