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
| T-010 | PRÓXIMA | **Construir el harness 010** ejecutando el plan: INC-0 (andamiaje + P-1/P-2/P-3) → INC-1 (walking skeleton) |
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

## T-010 — Construir el harness 010 — PRÓXIMA
Ejecutar `910_plan/010_discovery.md`. Empezar por **INC-0**: crear el andamiaje
`920_build/010_discovery/` y **cerrar las decisiones abiertas P-1** (ubicación de artefactos
construidos), **P-2** (forma del Governor: CLAUDE.md del harness + comando) y **P-3** (modelo por
agente). Luego **INC-1**: walking skeleton end-to-end (A→B→1 worker→C produce un artefacto) para
validar el modelo plano antes de ensanchar.

## T-006 — Reconciliar harnesses — PRÓXIMA
Alinear nombres y numeración entre `800_documents/statement.md` §9 y `700_harnesses/`; decidir cómo
encaja `100_change_harness` en las dos capas de la metodología.
