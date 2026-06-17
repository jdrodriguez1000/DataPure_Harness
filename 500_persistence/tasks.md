# tasks.md — Tareas del proyecto "DataPure Harness"

> Tablero de tareas. Lee el **Índice**; el detalle de cada tarea está abajo por ID.
> Estados: `HECHA` · `EN CURSO` · `PRÓXIMA` · `BLOQUEADA`.

## Índice (tablero)

| ID | Estado | Tarea (1 línea) |
|----|--------|-----------------|
| T-001 | HECHA | Analizar inputs, harnesses y statement; acordar método de trabajo |
| T-002 | HECHA | Definir y crear la estrategia de persistencia (`500_persistence/` + 4 archivos) |
| T-003 | HECHA | Crear `CLAUDE.md` con descripción del proyecto y protocolo de sesión |
| T-004 | HECHA | Crear los comandos `next` y `progress` (`/pure-next`, `/pure-progress`) |
| T-008 | HECHA | Renombrar las carpetas del proyecto (prefijo numérico) |
| T-007 | HECHA | Git/GitHub (init `main` + remote + autopush en `/pure-progress`) |
| T-005 | HECHA | Decidir la **meta del proyecto** → construir el harness 010 (brief→diseño→plan→construir) |
| T-009 | HECHA | Ciclo de definición del harness 010: brief + diseño + plan (los 3 APROBADOS) |
| T-012 | HECHA | Backlog de ideas (`backlog.md`) + comando `/pure-idea` para capturarlas |
| T-010 | EN CURSO | **Construir el harness 010**: INC-0 HECHO; INC-1 C-8 + C-1 HECHOS; falta cadena (C-2/C-3/C-5/C-6) |
| T-011 | PRÓXIMA | **Flujo de despliegue** (3 comandos install/init/discovery) → `deploy/`; construir tras INC-5 |
| T-006 | PRÓXIMA | Reconciliar nombres/numeración de harnesses (statement §9 ↔ `700_harnesses/`) y mapear el 100 a capas |

---

## T-001 — Analizar inputs, harnesses y statement — HECHA
Se leyeron y analizaron todos los insumos. Resultado: entendimiento compartido de los dos planos
y de la madurez desigual de los harnesses. Detalle en `progress.md` → S-001.

## T-002 — Definir y crear la persistencia — HECHA
Creada la carpeta `500_persistence/` con `progress.md`, `tasks.md`, `lessons.md`, `decisions.md`,
cada uno con índice. Decisión registrada en `decisions.md` → D-001.

## T-003 — Crear `CLAUDE.md` — HECHA
Creado en la raíz del proyecto. Incluye descripción y el protocolo obligatorio de
inicio/cierre de sesión (leer y actualizar los 4 archivos de persistencia).

## T-004 — Crear los comandos `next` y `progress` — HECHA
Construidos como comandos de proyecto en `.claude/commands/`:
- **`/pure-next`** (`pure-next.md`): inicio de sesión; lee la persistencia (solo lectura) y orienta
  sobre estado actual, decisiones que condicionan y próxima tarea.
- **`/pure-progress`** (`pure-progress.md`): cierre de sesión; actualiza los 4 archivos de
  `500_persistence/`.
Objetivo cumplido: automatizar el ritual de orientación/cierre (E10) sin leer todos los archivos.

## T-008 — Renombrar las carpetas del proyecto — HECHA
Aplicado el esquema con prefijo numérico (D-009): `500_persistence/`, `700_harnesses/`,
`800_documents/`, `810_inputs/`. Se actualizaron todas las referencias en 9 archivos. Las
referencias a `persistence/...` dentro de `700_harnesses/` se conservaron (plano de operación,
D-002 / L-004). Detalle en `progress.md` → S-003.

## T-005 — Decidir la meta del proyecto — HECHA
Meta elegida: **construir el harness 010 (Discovery)** siguiendo el orden brief→diseño→plan→construir,
usando `700_harnesses/` como referencia. Resuelve D-007 (FIRME). Detalle en `progress.md` → S-004.

## T-007 — Git/GitHub — HECHA
`git init -b main`, remote `origin` a `https://github.com/jdrodriguez1000/DataPure_Harness.git`,
`.gitignore` + `.gitattributes`, commit inicial y push. `/pure-progress` ahora hace commit+push al
cierre (D-010). Detalle en `progress.md` → S-004.

## T-009 — Ciclo de definición del harness 010 — HECHA
Creados y APROBADOS los 3 documentos del 010: `900_brief/010_discovery.md`,
`905_design/010_discovery.md`, `910_plan/010_discovery.md`. El diseño fija el modelo de ejecución
plano (D-011). Detalle en `progress.md` → S-004.

## T-010 — Construir el harness 010 — EN CURSO
Ejecutar `910_plan/010_discovery.md`.
- **INC-0 — HECHO (S-005):** andamiaje `920_build/010_discovery/` creado (agents/, schemas/,
  contract/, skills/, deliverables/) con esquemas C-7/C-9 plantilla y los 5 moldes de entregables.
  P-1/P-2/P-3 cerradas en D-013; estructura interna fijada en D-014.
- **INC-1 — EN CURSO:**
  - **C-8 HECHO (S-007):** Sprint Contract materializado en `contract/sprint_contract.template.md`
    (molde, base diseño §9; con `Instancias:` A/B/C y bloque de gate P5). Abre **D-016 PENDIENTE**
    (cableado de la entrevista humano↔dialoguer en el modelo plano).
  - **C-1 HECHO (S-008):** Governor = `920_build/010_discovery/CLAUDE.md` + skills `harness-start` /
    `harness-continue` (no es subagente, D-017; comandos = `SKILL.md`, doc oficial).
  - **Falta la cadena de subagentes:** C-2 (B `phase-orchestrator-010`), C-3 (`discovery-dialoguer`),
    C-5 (`discovery-synthesizer`), C-6 (C `phase-evaluator-010`) — como `.claude/agents/*.md` →
    producir `shared_understanding.md` + `verdict.json` con el gate. Valida el modelo plano y resuelve
    D-016 en la práctica.

## T-011 — Flujo de despliegue del harness 010 (construcción → operación) — PRÓXIMA
Materializa el **criterio Done #6 del plan** ("procedimiento de despliegue"). Flujo de **3 comandos**
que copian/instancian desde `920_build/010_discovery/` hacia una terminal de operación nueva:
1. **`install.ps1`** — infra: copia `agents/`, `skills/`, `contract/` y C-1 (CLAUDE.md del harness +
   comando) a `.claude/*`; crea `input/brief.md` (= semilla `800_documents/statement.md`).
2. **`init.ps1`** — estado: instancia los moldes de `schemas/`, `knowledge/`, `evaluation/` como
   archivos vacíos válidos (`harness-state.json`, `execution-state.json`, `claude-progress.txt`,
   `/knowledge/*`, `/eval/*`) — convierte molde→instancia quitando `.template`.
3. **`discovery`** (comando Governor, C-1) — prepara discovery (moldes de `deliverables/`) e **inicia**
   la ejecución A→B→workers→C, produciendo `/010_discovery/*`.
**Dónde vive:** carpeta nueva `920_build/010_discovery/deploy/` (`install.ps1`, `init.ps1`, `DEPLOY.md`)
— plano de construcción; su efecto es el plano de operación (D-002).
**Cuándo:** **después de INC-5** (E4/E9 — no construir el instalador antes de que el esqueleto corra;
si no, se reharía al cambiar agentes/esquemas en INC-1…INC-5).
**Nota:** elimina la redundancia del flujo previo (agentes solo en `install`, no recopiados en
`discovery`).

## T-006 — Reconciliar harnesses — PRÓXIMA
Alinear nombres y numeración entre `800_documents/statement.md` §9 y `700_harnesses/`; decidir cómo
encaja `100_change_harness` en las dos capas de la metodología.

## T-012 — Backlog de ideas + comando `/pure-idea` — HECHA
Se creó `500_persistence/backlog.md` (5º archivo de persistencia) para ideas sin compromiso, con
estados `No implementada`/`Implementada`/`Descartada` (las descartadas no se borran). Y el comando
de proyecto `/pure-idea` (`.claude/commands/pure-idea.md`) para capturarlas en cualquier momento sin
disparar el cierre completo (relee el archivo antes de escribir y commitea acotado, robusto a una 2ª
terminal). Decisión en D-015; primera idea registrada: I-001 (cerebro global). Detalle en
`progress.md` → S-006.
