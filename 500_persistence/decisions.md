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
