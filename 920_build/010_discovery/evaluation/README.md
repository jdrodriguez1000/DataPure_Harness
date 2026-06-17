# evaluation/ — Salidas de la Instancia C (Phase Evaluator) del harness 010

> **Plano de construcción (D-002).** Moldes de los artefactos que produce **C** al auditar la fase.
> Las instancias reales se escriben en runtime en `/eval/` de la terminal de operación (diseño §10).

| Molde | Instancia (runtime) | Contenido |
|-------|---------------------|-----------|
| `verdict.template.json` | `/eval/verdict.json` | APPROVED/REJECTED, scores D1–D6, veto, razones, recomendaciones |
| `metrics_summary.template.json` | `/eval/metrics_summary.json` | Métricas Tipo 1 (operativas por agente) y Tipo 2 (calidad) |

Separadas de `schemas/` (que ahora guarda solo los esquemas de **estado**: harness-state,
execution-state, claude-progress) por ser **salidas de evaluación**, no estado vivo del flujo.
