# lessons.md — Lecciones aprendidas del proyecto "DataPure Harness"

> Bitácora de aprendizajes. Cada lección incluye una **Regla para el futuro** (directriz obligatoria).
> Lee el **Índice**; el detalle por ID abajo.

## Índice

| ID | Lección | Regla para el futuro (1 línea) |
|----|---------|--------------------------------|
| L-001 | Los subagentes son amnésicos por diseño | Workers escriben artefactos al filesystem y reportan solo el path; nunca contenido |
| L-002 | Separar el plano de construcción del de operación | No mezclar `500_persistence/` con los state-files del harness en ejecución |
| L-003 | Los harnesses tienen madurez desigual | Tratar 060–100 como esqueléticos: requieren completarse antes de operarse |
| L-004 | No toda mención a `persistence/` es la carpeta de este repo | Al renombrar/refactorizar, distinguir plano de construcción vs. operación antes de tocar referencias |
| L-005 | Verificar capacidades de la plataforma en la doc, no de memoria | Ante restricciones de Claude Code, consultar docs actuales (find-docs/WebFetch) antes de fijar arquitectura |
| L-006 | Los entregables del harness son salidas de runtime | En construcción guardar **moldes** (`*.template`), nunca instancias reales (D-002) |
| L-007 | Hay dos pares de lecciones/decisiones (construir vs. operar) | No confundir `500_persistence/` con la `knowledge/` del harness; son planos distintos (D-002) |
| L-008 | Dos terminales sobre la misma carpeta pueden pisarse al escribir | Serializar escrituras de `500_persistence/` (1 sola a la vez, `git pull`/re-leer antes) o usar un git worktree |
| L-009 | En Claude Code los comandos se fusionaron en skills | `commands/x.md` ≡ `skills/x/SKILL.md`; preferir Skills (frontmatter, archivos de apoyo, invocación controlada) |

---

## L-001 — Los subagentes son amnésicos por diseño
**Contexto:** Surgió al aclarar dónde persisten su trabajo los subagentes.
**Aprendizaje:** En Claude Code, cada llamada al `Agent` tool arranca un subagente con contexto
limpio que no comparte memoria con otros. La persistencia NO está en el agente sino en el
filesystem (E6) y en los state-files que escriben A/B/C.
**Regla para el futuro:** Todo Worker debe (1) escribir su artefacto directamente al disco y
(2) reportar al orquestador únicamente el path. Para reanudar tras un fallo, se lanza un Worker
fresco que lee el artefacto existente — nunca se "recupera la memoria" del Worker.

## L-002 — Separar el plano de construcción del de operación
**Contexto:** La duda del usuario sobre quién registra el avance mientras se construye el harness.
**Aprendizaje:** Hay dos proyectos con persistencias distintas: construir la máquina (aquí) y
operarla (otra terminal). Confundirlos rompe la trazabilidad.
**Regla para el futuro:** En esta terminal solo se toca `500_persistence/`. Los archivos
`harness-state.json` / `execution-state.json` / `claude-progress.txt` pertenecen al harness en
operación y viven fuera de este flujo de construcción.

## L-003 — Los harnesses tienen madurez desigual
**Contexto:** Al inspeccionar `700_harnesses/`, 010–050 están completos (Fase 1 + Workers + Rúbrica +
Flujo) pero 060–100 solo tienen "Fase 0" (inputs/proceso/outputs).
**Aprendizaje:** No todos los harnesses están listos para operarse tal cual.
**Regla para el futuro:** Antes de ejecutar u operacionalizar 060–100, completarlos al nivel de
detalle de 010–050. No asumir que un harness está "listo" por existir su archivo.

## L-004 — No toda mención a `persistence/` es la carpeta de este repo
**Contexto:** Al renombrar `persistence/`→`500_persistence/` (T-008/D-009), las definiciones de
`700_harnesses/` contenían ~50 rutas `persistence/harness-state.json`, `execution-state.json`,
`claude-progress.txt`. Esas pertenecen al **plano de operación** (el harness corriendo en otra
terminal, D-002), no a la carpeta de construcción de este repo.
**Aprendizaje:** Un mismo nombre de ruta puede referirse a planos distintos; un renombrado global
ciego (replace-all) habría corrompido las definiciones del harness.
**Regla para el futuro:** Antes de aplicar un renombrado/refactor de rutas, clasificar cada
referencia por plano (construcción vs. operación, D-002). Solo tocar las del plano que se está
modificando; dejar intactas las del otro.

## L-005 — Verificar capacidades de la plataforma en la doc, no de memoria
**Contexto:** Se asumió que "ningún subagente puede spawnear otros agentes" en Claude Code. Al
verificar la doc oficial de sub-agents, resultó desactualizado: **desde v2.1.172 un subagente SÍ
puede spawnear anidado** (foreground, con `Agent` en tools; los background no a profundidad ≥5).
**Aprendizaje:** Las capacidades de la plataforma cambian entre versiones; decidir arquitectura con
una premisa de memoria puede llevar a restricciones falsas o a diseños frágiles.
**Regla para el futuro:** Ante cualquier restricción de plataforma que condicione el diseño,
**verificar en la documentación actual** (find-docs / WebFetch) antes de fijarla. Cuando el runtime
sea de versión no controlada, preferir el **diseño robusto a la versión** (p. ej. el modelo plano,
D-011) aunque la capacidad nueva exista.

## L-006 — Los entregables del harness son salidas de runtime, no archivos de construcción
**Contexto:** Al andamiar `deliverables/` (INC-0), surgió la duda de si los 5 artefactos del 010
(`shared_understanding.md`, etc.) viven en este repo. Son **salidas que produce el harness al
ejecutarse** (synthesizer, en la terminal de operación), no archivos del plano de construcción.
**Aprendizaje:** Confundir "definir el entregable" con "producir el entregable" reintroduce la mezcla
de planos (D-002): meter las instancias reales en el repo de construcción rompería la separación y
chocaría con el `.gitignore`.
**Regla para el futuro:** En el plano de construcción solo se guardan **moldes/plantillas**
(`*.template.*`) de los entregables; las **instancias** se generan en runtime y permanecen en la
terminal de operación. Antes de crear una carpeta de "salidas", clasificar: ¿molde (construcción) o
instancia (operación)?

## L-007 — Dos pares de lecciones/decisiones: no confundir construir con operar
**Contexto:** Al andamiar `knowledge/` surgió que el harness genera, en runtime, sus propios
`decisions_library.md` y `lessons_learned.md` — homónimos conceptuales de los `decisions.md` /
`lessons.md` de `500_persistence/`, pero de **otro plano** (D-002).
**Aprendizaje:** Existen **dos pares** distintos: (1) `500_persistence/decisions.md`+`lessons.md` =
cómo **construimos** el harness (lo escribe la sesión principal); (2) `knowledge/decisions_library.md`
+`lessons_learned.md` = lo que el harness **aprende al ejecutarse** (lo escriben A y C en runtime).
Tratarlos como uno solo rompe la trazabilidad y mezcla planos.
**Regla para el futuro:** Al hablar de "lecciones" o "decisiones", precisar **de qué plano**:
construcción (`500_persistence/`) u operación (`knowledge/` del harness). Nunca volcar conocimiento de
runtime en `500_persistence/` ni viceversa.

## L-008 — Dos terminales sobre la misma carpeta pueden pisarse al escribir
**Contexto:** Se preguntó si se puede construir el harness en una terminal y desarrollar/registrar
una idea en otra terminal apuntando a la misma carpeta.
**Aprendizaje:** Sí se puede abrir más de una sesión sobre el mismo directorio, pero **no hay
bloqueo** de archivos: si dos sesiones escriben `500_persistence/` a la vez, la última en guardar
pisa a la otra y puede provocar conflictos de git. Esto choca con el single-writer (D-003). Las
lecturas/experimentos en paralelo no son problema; el riesgo es la escritura concurrente.
**Regla para el futuro:** Mantener una sola terminal escribiendo `500_persistence/` a la vez
(serializar; hacer `git pull`/re-leer antes de escribir). Para trabajo paralelo real, usar un **git
worktree** (otra carpeta, misma historia) y luego mergear. Comandos que escriben (como `/pure-idea`)
deben releer el archivo antes de escribir y commitear de forma acotada (D-015).

## L-009 — En Claude Code los comandos se fusionaron en skills
**Contexto:** Al construir los "comandos" del Governor (`harness-start`/`harness-continue`) se
verificó en la doc oficial de skills.
**Aprendizaje:** "Custom commands have been merged into skills": un `.claude/commands/x.md` y un
`.claude/skills/x/SKILL.md` crean el mismo `/x` y funcionan igual. Los `commands/` siguen vigentes,
pero los **Skills** añaden frontmatter (incl. `disable-model-invocation`, `allowed-tools`), un
directorio para archivos de apoyo y carga automática cuando son relevantes. Un Skill es una carpeta
con `SKILL.md` como entrypoint.
**Regla para el futuro:** Para nuevas habilidades de agentes del harness, preferir **Skills**
(`SKILL.md`) sobre `commands/*.md`. Usar `disable-model-invocation: true` para las que dispara el
humano y `allowed-tools` para preaprobar herramientas (p. ej. `Agent` en skills del Governor que
spawnean). Verificar capacidades en la doc oficial antes de fijar formato (ver L-005).
