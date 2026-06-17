# Sprint Contract — 010 Discovery (PLANTILLA)

> **C-8 del harness 010.** Molde (plano de construcción, D-002). La **instancia** la produce el
> Governor (Instancia A) en runtime: rellena los `<campos>`, la **propone al humano** y requiere
> **aprobación explícita** antes de invocar a B (gate P5). No confundir este molde con el contrato
> real de un sprint (salida de operación, no va en este repo). Base: `905_design/010_discovery.md` §9.
>
> Cómo se usa: A copia este molde, sustituye cada `<…>` por el valor del sprint concreto, presenta el
> resultado a Sabbia y solo continúa con aprobación registrada.

```
SPRINT CONTRACT — 010 Discovery (Data Pure)
===========================================
Objetivo : Validar/extender el statement de Data Pure y producir los 5 artefactos de Discovery
Fase     : 010 — Discovery de Producto
Modo     : <INICIO | CONTINUACIÓN>

Instancias:
  - A (Governor)     : gobierna el sprint, propone este contrato y opera el gate (C-1)
  - B (Orchestrator) : planifica/secuencia la fase — phase-orchestrator-010 (C-2)
  - C (Evaluator)    : audita contra el Criterio Done con la rúbrica — phase-evaluator-010 (C-6)

Inputs disponibles:
  - Statement (semilla)    : 800_documents/statement.md
  - Principios/metodología : 810_inputs/
  - Referencia 010         : 700_harnesses/010_discovery_harness.md
  - Stakeholders Sabbia    : <dueño, equipo — lista concreta>
  - Decisiones abiertas    : 800_documents/statement.md §10

Workers:
  - discovery-dialoguer    → /010_discovery/dialogue_transcript.md
  - discovery-analyst      → /010_discovery/analysis_report.md
  - discovery-synthesizer  → shared_understanding.md, scope_boundaries.md,
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

Aprobación del contrato (gate P5):
  - Propuesto por A   : <fecha iso8601>
  - Aprobado por      : <nombre / rol Sabbia>
  - Fecha aprobación  : <iso8601>

Próxima acción: A invoca a B (planificar) y luego a discovery-dialoguer con I-1…I-5
```
