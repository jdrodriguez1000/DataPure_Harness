# backlog.md — Ideas del proyecto "DataPure Harness"

> Backlog de **ideas y posibilidades**. A diferencia de `tasks.md` (trabajo comprometido), aquí no
> hay certeza de que se vayan a construir. Sirve para no perder ideas que surgen mientras trabajamos.
> Lee el **Índice**; el detalle de cada idea está abajo por ID.
> Cuando una idea se construye, su detalle se trabaja en `tasks.md` (T-xxx) y aquí pasa a `Implementada`.
> Estados:
> - `No implementada` — registrada, aún no se ha construido (estado por defecto al nacer).
> - `Implementada` — ya se construyó.
> - `Descartada` — se decidió no hacerla **por ahora**. NO se borra: el futuro cambia y podría
>   reactivarse.

## Índice

| ID | Estado | Idea (1 línea) |
|----|--------|----------------|
| I-001 | No implementada | **Cerebro global**: BD cross-project que acumula lecciones/decisiones y un comando `/cerebro` para consultarlas desde cualquier proyecto |

---

## I-001 — Cerebro global (memoria cross-project) — No implementada
**Fecha:** 2026-06-17

**Qué es:** Un almacén transversal ("cerebro") que acumula el **aprendizaje transferible** de todos
los proyectos, alimentado desde los `500_persistence/lessons.md` y `decisions.md` de cada uno, y
consultable por semántica desde cualquier proyecto.

**Cómo funcionaría (arquitectura discutida):**
- **Proyecto `cerebro` (aparte):** contiene **solo código** — un *colector* que parsea
  `lessons.md` + `decisions.md` por ID (L-xxx/D-xxx) y hace **upsert** en la BD (clave única
  `proyecto#ID` para no duplicar al re-subir; las lecciones evolucionan PROVISIONAL→FIRME).
- **BD:** SQLite local (`brain.db`), **fuera del repo** (recomendado `~/.claude/brain.db`) para que
  el comando global la encuentre y para no publicar aprendizaje sensible en git. Migrable a remota
  (Turso/Postgres) si algún día se accede desde varios PCs.
- **Comando `/cerebro` GLOBAL:** vive en `~/.claude/commands/cerebro.md` (ámbito **usuario**, no
  proyecto) para estar disponible en todos los proyectos. Consulta `brain.db` por similitud
  semántica: *"dime lecciones/decisiones que apliquen a lo que hago ahora"*.

**Dos flujos / dos momentos:**
- **Escritura (fin de día):** correr el colector → sube `lessons.md` + `decisions.md` del proyecto.
- **Lectura (inicio de sesión / ante una decisión):** `/cerebro` recupera lo relevante. Encaje
  natural con `/pure-next` (orientación) y al registrar nuevas decisiones.

**Matiz de alcance (clave):** marcar cada entrada como `transferible` vs. `especifico` (de un solo
proyecto) para que `/cerebro` no traiga ruido. Mejor que se etiquete **al registrar** la lección
(campo `alcance:`) que a mano después. Una "Regla para el futuro" genérica es casi por definición
transferible.

**Empezar mínimo (criterio E4/E9):** SQLite + comando manual de consulta primero; subir a MCP con
búsqueda semántica y cablearlo a `/pure-next` solo si demuestra valor. No construir la infra antes
de saber que se usa.

**Relación:** generaliza el patrón de `500_persistence/` (D-001) y se conecta con la base de
conocimiento del propio harness (`knowledge/` — L-007). NO mezclar con la persistencia local de cada
proyecto (D-002): el cerebro guarda lo **transferible**, no el estado específico del proyecto.
