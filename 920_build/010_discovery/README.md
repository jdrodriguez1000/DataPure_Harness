# 920_build/010_discovery/ — Artefactos construidos del harness 010 (Discovery)

> **Plano de construcción (D-002).** Aquí viven las **definiciones** del harness 010 (agentes,
> esquemas, plantillas, comandos). **No** son instancias de estado en runtime: esas se crean cuando
> el harness se ejecuta en su propia terminal (plano de operación) y **no** van en este repo.
>
> Insumos: `900_brief/010_discovery.md`, `905_design/010_discovery.md`, `910_plan/010_discovery.md`
> (los 3 APROBADOS).

## Estructura

```
920_build/010_discovery/
├── README.md       ← este archivo
├── agents/         ← subagentes (se llenan en INC-1+): B, workers, C
│                     C-2 phase-orchestrator-010, C-3 dialoguer, C-4 analyst,
│                     C-5 synthesizer, C-6 phase-evaluator-010
├── schemas/        ← C-7 esquemas de estado + C-9 esquema de veredicto/métricas (PLANTILLAS)
│   ├── harness-state.template.json
│   ├── execution-state.template.json
│   ├── claude-progress.template.txt
│   ├── verdict.template.json
│   └── metrics_summary.template.json
├── contract/       ← C-8: SOLO el Sprint Contract (se materializa en INC-1)
├── skills/         ← habilidades (skills) reutilizables de los agentes (INC-1+)
└── deliverables/   ← plantillas de los 5 entregables principales del harness (moldes)
    ├── shared_understanding.template.md
    ├── scope_boundaries.template.md
    ├── domain_glossary.template.md
    ├── failure_behavior.template.md
    └── product_brief_validated.template.md
```

> **Nota de planos (D-002):** `deliverables/` contiene **moldes** (construcción). Las instancias
> reales de los 5 artefactos las produce el synthesizer en runtime (operación) y no vuelven al repo.

## Estado de construcción (incrementos del plan §5)

| Inc | Entregable | Estado |
|-----|-----------|--------|
| INC-0 | Andamiaje + esquemas mínimos C-7 + cerrar P-1/P-2/P-3 | **EN CURSO** (este commit) |
| INC-1 | Walking skeleton end-to-end | PENDIENTE |
| INC-2 | Cadena de workers completa | PENDIENTE |
| INC-3 | Gate y evaluación robustos | PENDIENTE |
| INC-4 | Durabilidad y reanudación | PENDIENTE |
| INC-5 | Evaluación temprana (E9) | PENDIENTE |

## Decisiones cerradas en INC-0

- **P-1 (ubicación):** confirmada `920_build/010_discovery/` (D-012).
- **P-2 (forma del Governor C-1):** CLAUDE.md del harness + 1 comando.
- **P-3 (modelo por agente):** Opus para A/B/C y analyst; Sonnet para dialoguer/synthesizer.

## Despliegue (futuro)

El despliegue a la terminal de operación copia `agents/*` al `.claude/agents/` de esa terminal y
usa las plantillas de `schemas/` para inicializar las **instancias** de estado en runtime.
