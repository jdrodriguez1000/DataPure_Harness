# Diseño — Harness 010 Discovery (Diseño Agéntico)

> **Tipo:** Diseño agéntico (Fase 1 — `metodology.md` §6). Cierra *cómo* opera el harness; el **plan
> de implementación** va después (orden: brief → diseño → plan → construir).
> **Proyecto:** DataPure Harness — plano de **construcción** (Familia A).
> **Fase:** 010 — Discovery de Producto.
> **Brief de origen:** `900_brief/010_discovery.md` (APROBADO).
> **Fuente guía:** `700_harnesses/010_discovery_harness.md` (referencia de otro proyecto; adaptada, no copiada).
> **Estado:** APROBADO · **Fecha:** 2026-06-16 · **Aprobado por:** dueño/equipo de Sabbia (rol asumido por el usuario).

---

## 0. Nota de planos (no mezclar — D-002)

Este documento **define** la maquinaria del harness 010 (plano de construcción). Los archivos de
estado en runtime (`harness-state.json`, `execution-state.json`, `claude-progress.txt`) y la carpeta
de artefactos `/010_discovery/` **no viven en este repositorio**: se crean cuando el harness **se
ejecuta** en su propia terminal (plano de operación). Aquí solo se especifican.

El "cliente" entrevistado por este harness es el **dueño y el equipo de Sabbia**; la **semilla** es
`800_documents/statement.md`. El harness descubre *el producto Data Pure* (Familia B), no se descubre
a sí mismo.

---

## 1. Patrón de 3 instancias y modelo de ejecución plano (P1, §3 de la metodología)

### 1.1 Restricción de plataforma → modelo plano

En Claude Code, **el único spawner fiable entre versiones es la sesión principal** (el spawn anidado
de subagentes solo existe desde v2.1.172 y con matices: solo *foreground*, requiere `Agent` en
`tools`, y los *background* no spawnean a profundidad ≥5). Como el harness corre en otra terminal de
versión no controlada, se adopta un **modelo plano, robusto a la versión**:

> La **sesión principal de Claude Code ES la Instancia A (Governor)** y **es la única que ejecuta los
> spawns**. B, los Workers y C son **subagentes** que la sesión principal invoca. Esto coincide con
> la propia metodología (§12.1): *"Instancia A es el agente que recibe este comando directamente en
> la sesión activa de Claude Code"*.

### 1.2 Instancias

| Instancia | ¿Quién la encarna? | Rol y responsabilidad | Escribe en |
|-----------|--------------------|-----------------------|------------|
| **A — Governor** | **Sesión principal** (no subagente) | Recibe los comandos humanos; redacta/gestiona el Sprint Contract; gate humano; **ejecuta los spawns** de B, Workers y C siguiendo el plan de B; decide Avanzar/Repetir; consolida knowledge. Único escritor del estado estratégico. | `harness-state.json`, `/knowledge/*` |
| **B — Phase Orchestrator** | Subagente | **Planifica**: lee el contrato, consulta knowledge y **devuelve el `orchestration_plan`** (qué Workers, en qué orden, con qué inputs). No spawnea; A ejecuta su plan. Mantiene contexto técnico propio. | `execution-state.json` (vía A o por sí mismo según herramientas) |
| **C — Phase Evaluator** | Subagente | Cerebro fresco; lee artefactos del filesystem; aplica rúbrica; emite veredicto. No contacta a nadie. | `/eval/verdict.json`, `/eval/metrics_summary.json` |

### 1.3 Reglas inviolables (P1, P3) bajo el modelo plano

- **A (sesión principal) ejecuta todos los spawns**; primero invoca a B para *planificar*, luego
  invoca a los Workers según el `orchestration_plan`, y al final a C para *auditar* — nunca B y C
  simultáneamente.
- **B planifica, no ejecuta spawns** (la plataforma no se lo garantiza). Su valor es el contexto
  técnico separado para descomponer el trabajo (preserva el espíritu de P1).
- **C no contacta a nadie** (solo lee del filesystem) — preserva P3, la regla más importante.
- **Cada subagente corre con contexto fresco** (P4/E2 — `Agent` tool).

`claude-progress.txt` lo escribe la **instancia activa** en cada momento (bitácora narrativa).

---

## 2. Workers especializados (E12)

Se reutiliza la cadena probada de 3 workers (E4 — no sobre-ingeniar), secuencial por dependencia
estricta de datos:

| Worker | Micro-tarea | Inputs | Output (path en runtime) |
|--------|-------------|--------|--------------------------|
| **discovery-dialoguer** | Cuestionamiento socrático al dueño/equipo de Sabbia (de amplio a estrecho, E11). | I-1…I-5 del brief | `/010_discovery/dialogue_transcript.md` |
| **discovery-analyst** | Analiza el transcript: actores, objetivos de valor, contradicciones, vacíos, escenarios de fallo, decisiones §10. | path al transcript | `/010_discovery/analysis_report.md` |
| **discovery-synthesizer** | Produce los artefactos finales a partir del análisis. | path al analysis | los 4 artefactos + brief validado |

**Secuenciación:** dialoguer → analyst → synthesizer (estricta, **no** paralela: cada paso depende
del anterior; aquí E7/paralelismo no aplica). **A (sesión principal) invoca cada Worker** siguiendo
el `orchestration_plan` de B. Cada Worker escribe al filesystem y devuelve **solo el path** (E6).

**Bucle de aclaración:** si analyst reporta `PENDIENTE DE ACLARACIÓN`, **A re-invoca** al dialoguer en
modo aclaración (solo preguntas pendientes) → re-invoca al analyst. A las ≥3 iteraciones sin
resolver, analyst emite `ALERTA` y A lo escala al humano.

---

## 3. Inputs y artefactos

**Inputs** (del brief §4): I-1 statement, I-2 principios/metodología, I-3 referencia 700,
I-4 conversaciones con Sabbia, I-5 decisiones abiertas §10.

**Artefactos de salida** (en `/010_discovery/` del runtime):

| Artefacto | Path | Propósito |
|-----------|------|-----------|
| Shared Understanding | `shared_understanding.md` | Entendimiento aprobado explícitamente por Sabbia. |
| Scope Boundaries | `scope_boundaries.md` | Qué NO hará Data Pure en esta etapa. |
| Glosario de dominio | `domain_glossary.md` | Lenguaje ubicuo (salud de datos, tenant, banda, ledger, baseline, S/M/L/XL…). |
| Failure Behavior | `failure_behavior.md` | Comportamiento esperado ante fallos, por escenario → input al 020. |
| Brief de producto validado | `product_brief_validated.md` | Statement confirmado y extendido. |

---

## 4. Política de herramientas (P7)

Workers de Discovery — **permitido:** `Write`, `Read`, `Edit` (producir/revisar documentos).
**Prohibido en esta fase:** ejecución de código, herramientas externas, web search (Discovery
produce comprensión, no investigación externa; la semilla y el cliente son la fuente).
Excepción documentable: si el discovery requiere consultar un dato externo de Sabbia, se escala a A
(no se habilita la herramienta unilateralmente).

## 5. Política de fallback de herramientas (E5 — 3 niveles)

1. **Reintento** (hasta 2×): operación que falla por error transitorio (I/O, timeout).
2. **Fallback:** si el cliente no responde con claridad, reformular la pregunta con otro ángulo
   (de amplio a estrecho); si una lectura falla, usar la última versión persistida del artefacto.
3. **Escalamiento:** marcar la pregunta como `UNRESOLVED` en el transcript, registrar en
   `execution-state.json` y continuar sin bloquear; reportar a A al cierre. Nunca inventar la
   respuesta del cliente (un vacío explícito es mejor que un dato fabricado).

## 6. Política de escalamiento, paralelismo y reasoning (P6, E7, E8)

- **Escalar al humano (detener flujo)** cuando: contradicciones irresolubles tras 3 rondas; el
  cliente declina responder sobre comportamiento ante fallos; el cliente rechaza aprobar el Shared
  Understanding sin razón articulable; o quedan decisiones §10 que bloquean al 020. A registra el
  bloqueo en `harness-state.json` → `escalations` y notifica con contexto completo.
- **Paralelismo (E7):** **no aplica** dentro del 010 — la cadena de workers es estrictamente
  secuencial. (Si en el futuro hubiera múltiples stakeholders entrevistables en paralelo, se
  reevaluaría; por ahora, mínima complejidad.)
- **Reasoning budget (E8):** extended thinking reservado para el **analyst** (detección de
  contradicciones y vacíos) y para A al redactar el Sprint Contract y decidir el gate. El dialoguer
  y el synthesizer no lo requieren de forma intensiva.

## 7. Checkpoints canónicos (E5)

> En el modelo plano, **A (sesión principal) orquesta la ejecución y persiste los checkpoints** en
> `execution-state.json` (B aportó el `orchestration_plan` inicial). Es la consecuencia de aplanar:
> A asume la escritura del estado de ejecución además del estratégico.

| ID | Momento | Qué se persiste en `execution-state.json` |
|----|---------|-------------------------------------------|
| CP-01a | Por cada stakeholder de Sabbia entrevistado | Path al transcript + ID de stakeholder completado (permite reanudar en el siguiente). |
| CP-01 | Transcript COMPLETO (todos los stakeholders) | Estado final del transcript. |
| CP-02 | Tras analyst → `LISTO PARA SÍNTESIS` | Path al `analysis_report.md` + contador de iteraciones de aclaración. |
| CP-03 | Tras synthesizer (draft) | Paths a los 5 artefactos; A presenta draft a Sabbia. |
| CP-04 | Sabbia aprueba | A registra aprobación en `harness-state.json`; invoca a C. |

## 8. Trigger de context reset (E2)

- **Conductual (primario):** saltarse preguntas del guión, cerrar rondas sin cumplir el Criterio de
  Done, respuestas superficiales, declarar "terminado" sin verificar las condiciones de Done.
- **Cuantitativo (secundario):** ≥70% de tokens.

Acción: reanudar desde el último checkpoint (Ritual E10-B), nunca reiniciar desde cero.

---

## 9. Sprint Contract — plantilla

A lo propone al humano antes de invocar a B; requiere aprobación explícita (P5).

```
SPRINT CONTRACT — 010 Discovery (Data Pure)
===========================================
Objetivo : Validar/extender el statement de Data Pure y producir los 5 artefactos de Discovery
Fase     : 010 — Discovery de Producto
Modo     : [INICIO | CONTINUACIÓN]

Inputs disponibles:
  - Statement (semilla)   : 800_documents/statement.md
  - Principios/metodología : 810_inputs/
  - Referencia 010         : 700_harnesses/010_discovery_harness.md
  - Stakeholders Sabbia    : [dueño, equipo — lista]
  - Decisiones abiertas    : statement.md §10

Workers:
  - discovery-dialoguer   → /010_discovery/dialogue_transcript.md
  - discovery-analyst     → /010_discovery/analysis_report.md
  - discovery-synthesizer → shared_understanding.md, scope_boundaries.md,
                            domain_glossary.md, failure_behavior.md,
                            product_brief_validated.md

Checkpoints  : CP-01a, CP-01, CP-02, CP-03, CP-04

Criterio Done:
  (1) Sabbia aprueba explícitamente el Shared Understanding
  (2) Sin contradicciones nuevas en 2 rondas consecutivas
  (3) Todos los actores con ≥1 objetivo de valor
  (4) ≥1 comportamiento ante fallos registrado
  (5) Decisiones §10 resueltas o diferidas con responsable y motivo

Riesgos:
  - Decisiones §10 sin cerrar bloquean al 020
  - Confusión de planos Familia A vs. B
  - Sabbia como cliente interno (no confundir con la empresa cliente final)

Próxima acción: A invoca a B (planificar) y luego a discovery-dialoguer con I-1…I-5
```

---

## 10. Rúbrica de evaluación (Instancia C — E3)

| ID | Dimensión | Descripción | Score |
|----|-----------|-------------|-------|
| D1 | Cobertura de actores | Todos los actores con ≥1 objetivo de valor | 0.0–1.0 |
| D2 | Claridad de intención | Intención de Sabbia capturada sin ambigüedad | 0.0–1.0 |
| D3 | Gestión de contradicciones | Ninguna contradicción sin resolver al cierre | 0.0–1.0 |
| D4 | Cobertura de fallos | ≥1 escenario de fallo por actor principal | 0.0–1.0 |
| D5 | Aprobación explícita | Sabbia aprobó el Shared Understanding con registro | 0.0–1.0 |
| D6 | Cierre de decisiones §10 | Cada decisión abierta resuelta o diferida con responsable | 0.0–1.0 |

**Gate de paso:** promedio ≥ 0.75 **y** ninguna dimensión < 0.5.
**Veto:** si D5 = 0.0 → rechazo automático (sin aprobación no hay fase).

**Anclas de calibración (few-shot):**
- **0.2** — 1 actor, sin objetivos de valor, sin fallos, sin aprobación; Shared Understanding vago o
  inexistente; decisiones §10 intactas.
- **0.5** — Actores principales pero faltan secundarios; ~50% de objetivos; sin escenarios de fallo;
  documento entregado pero sin aprobación formal; algunas decisiones §10 abiertas.
- **0.8** — Todos los actores con objetivo; ≥1 contradicción resuelta; 1 fallo por actor principal;
  Shared Understanding completo; señales positivas de Sabbia pero sin registro explícito.
- **1.0** — Todos los actores con objetivo; todas las contradicciones resueltas; ≥1 fallo por actor
  principal y ≥1 por secundario; aprobación explícita registrada; glosario sin ambigüedad; Scope
  Boundaries con ≥3 exclusiones; todas las decisiones §10 resueltas o diferidas con responsable.

**Salida de C:** `/eval/verdict.json` (`APPROVED|REJECTED`, scores por dimensión, veto, razones,
recomendaciones) + `/eval/metrics_summary.json` (Tipo 1 y 2).

---

## 11. Handoff → 020 Specification

El 020 **no arranca** sin: `shared_understanding.md`, `scope_boundaries.md`, `domain_glossary.md`,
`failure_behavior.md`, `product_brief_validated.md`, y `harness-state.json` con
`"status": "PHASE_COMPLETE"` para la fase 010.

---

## 12. Flujo del arnés (resumen)

1. **Inicialización (A = sesión principal):** determina modo (Inicio E10-A / Continuación E10-B);
   ritual de arranque; reporte al humano + gate de aprobación del Sprint Contract; al aprobar, escribe
   el contrato e **invoca a B (planificar)**.
2. **Planificación (B):** B devuelve el `orchestration_plan` (E12). **A lo persiste** en
   `execution-state.json` y **ejecuta los spawns** según el plan: dialoguer → analyst (con bucle de
   aclaración) → synthesizer; A registra CP-01a…CP-03 y marca `EXECUTION_COMPLETE`.
3. **Auditoría y gate (C + A):** A presenta draft a Sabbia (CP-03), registra aprobación (CP-04),
   **invoca a C**; C audita con la rúbrica y escribe veredicto; A lee el veredicto y decide
   Avanzar/Repetir.
4. **Rechazo:** técnico → `IN_REWORK`, **A re-invoca** solo a los Workers fallidos (re-pidiendo a B un
   plan ajustado si hace falta); estratégico → `HOLD`, nueva aprobación humana del contrato.
5. **Cierre:** A marca `PHASE_COMPLETE`, C consolida `lessons_learned.md`, A notifica handoff al 020 y
   hace commit `docs(010-discovery): phase complete`.

---

## 13. Decisiones de diseño / adaptaciones frente a la referencia

- **Modelo de ejecución plano (clave):** los subagentes de Claude Code no pueden spawnear de forma
  fiable entre versiones (el anidamiento solo existe desde v2.1.172, foreground y con `Agent` en
  `tools`). Por eso **A = sesión principal y único spawner**; B planifica y A ejecuta sus spawns.
  Coincide con `metodology.md` §12.1 y es robusto a la versión del runtime. (Ver §1.)
- **D6 (cierre de decisiones §10) añadida a la rúbrica:** el statement de Data Pure trae decisiones
  abiertas que el 020 necesita cerradas; se eleva a dimensión evaluable (no estaba en el 700).
- **Paralelismo descartado (E4):** cadena de workers estrictamente secuencial; sin sobre-ingeniería.
- **Cliente = Sabbia (interno):** todo el guión socrático se orienta al dueño/equipo, con la semilla
  `statement.md` como base a validar y extender — no a un cliente genérico.
- **Quinto artefacto `product_brief_validated.md`:** materializa explícitamente el "brief de producto
  validado" que el statement §9 nombra como salida del 010.

### Pendiente de cerrar en el siguiente paso (plan de implementación)
- Lista concreta de stakeholders de Sabbia a entrevistar.
- Esquema mínimo de `harness-state.json` / `execution-state.json` para esta fase.
- Guión inicial de preguntas socráticas (de amplio a estrecho) y orden de las decisiones §10.
