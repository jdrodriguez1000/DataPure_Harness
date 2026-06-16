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
| T-005 | PRÓXIMA | Decidir la **meta del proyecto** (operacionalizar vs. completar 060–100 vs. ejecutar 010) |
| T-006 | PRÓXIMA | Reconciliar nombres/numeración de harnesses (statement §9 ↔ `700_harnesses/`) y mapear el 100 a capas |
| T-007 | PRÓXIMA | Decidir git/GitHub (init local + remote) para trazabilidad (E1/P8) |

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

## T-005 — Decidir la meta del proyecto — PRÓXIMA
Elegir el enfoque: (a) operacionalizar el harness en Claude Code, (b) completar primero las
definiciones 060–100, o (c) empezar a ejecutar desde el 010. Bloquea el plan de construcción.

## T-006 — Reconciliar harnesses — PRÓXIMA
Alinear nombres y numeración entre `800_documents/statement.md` §9 y `700_harnesses/`; decidir cómo
encaja `100_change_harness` en las dos capas de la metodología.

## T-007 — Decidir git/GitHub — PRÓXIMA
La metodología exige git + remote desde el arranque (E1/P8). Hoy el repo no está inicializado.
Definir: `git init` local ahora y conectar remote GitHub cuando el usuario lo provea.
