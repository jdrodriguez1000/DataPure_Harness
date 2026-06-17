# deliverables/ — Los 5 entregables principales del harness 010 (Discovery)

> **Plano de construcción (D-002).** Aquí viven las **plantillas/moldes** (estructura esperada) de
> los 5 artefactos de Discovery. Las **instancias reales** (rellenas con datos de Sabbia) las produce
> el worker `discovery-synthesizer` **en runtime**, en la terminal de operación
> (`/010_discovery/`), y por `.gitignore` no vuelven a este repo. No mezclar planos.

Los 5 entregables (diseño §3, Criterio de Done §7):

| # | Entregable | Plantilla | Propósito |
|---|-----------|-----------|-----------|
| 1 | Shared Understanding | `shared_understanding.template.md` | Entendimiento aprobado explícitamente por Sabbia (gate). |
| 2 | Scope Boundaries | `scope_boundaries.template.md` | Qué **NO** hará Data Pure en esta etapa. |
| 3 | Glosario de dominio | `domain_glossary.template.md` | Lenguaje ubicuo (salud de datos, tenant, banda, ledger, baseline, S/M/L/XL…). |
| 4 | Failure Behavior | `failure_behavior.template.md` | Comportamiento esperado ante fallos, por escenario → input al 020. |
| 5 | Brief de producto validado | `product_brief_validated.template.md` | Statement confirmado y extendido. |

> Las 5 plantillas son **stubs** en INC-0: encabezados y secciones mínimas. Se completan como
> contenido-guía del synthesizer en INC-1/2.
