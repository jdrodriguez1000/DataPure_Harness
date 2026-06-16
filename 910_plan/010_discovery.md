# Plan de implementación — Harness 010 Discovery

> **Tipo:** Plan de implementación (Fase de Planeación; va *después* del diseño — orden: brief →
> diseño → plan → construir).
> **Proyecto:** DataPure Harness — plano de **construcción** (Familia A).
> **Fase:** 010 — Discovery de Producto.
> **Insumos:** `900_brief/010_discovery.md` (APROBADO), `905_design/010_discovery.md` (APROBADO).
> **Estado:** APROBADO · **Fecha:** 2026-06-16 · **Aprobado por:** dueño/equipo de Sabbia (rol asumido por el usuario).

---

## 1. Objetivo del plan

Traducir el **diseño** del harness 010 en **componentes construibles**, secuenciados en incrementos
con criterio de Done explícito, de modo que al terminar exista un **harness 010 ejecutable** listo
para operar en su terminal. Se construye con mínima complejidad primero (E4) y se valida temprano
(E9), no de un solo golpe.

## 2. Nota de planos — dónde vive cada cosa (D-002)

| Tipo de artefacto | Plano | Dónde se construye / vive |
|-------------------|-------|---------------------------|
| **Definiciones del harness** (agentes, comandos, plantillas, esquemas, CLAUDE.md del harness) | **Construcción** (este repo) | `920_build/010_discovery/` *(ubicación propuesta — ver D-abierta P-1)* |
| **Instancias de estado en runtime** (`harness-state.json`, `execution-state.json`, `claude-progress.txt`) y artefactos `/010_discovery/`, `/eval/`, `/knowledge/` | **Operación** (otra terminal) | Se crean al ejecutar el harness (ritual E10-A). **No** en este repo. |

> Construimos **plantillas/esquemas** (definición), no **instancias** (runtime). El despliegue copia
> `920_build/010_discovery/agents/*` al `.claude/agents/` de la terminal de operación.

## 3. Inventario de componentes a construir (deliverables)

| ID | Componente | Encarna | Tipo / formato | Path propuesto |
|----|-----------|---------|----------------|----------------|
| C-1 | **CLAUDE.md del harness** + comandos `/harness-start`, `/harness-continue` | A (Governor) | Instrucciones operativas del hilo principal (rituales E10-A/B, gate P5, ejecución de spawns) | `920_build/010_discovery/` + `.claude/commands/` |
| C-2 | **phase-orchestrator-010** | B | Subagente (planificador): devuelve `orchestration_plan`. **Sin** `Agent` en tools | `920_build/010_discovery/agents/` |
| C-3 | **discovery-dialoguer** | Worker | Subagente; tools `Read,Write,Edit` | idem |
| C-4 | **discovery-analyst** | Worker | Subagente; `Read,Write,Edit` + reasoning (E8) | idem |
| C-5 | **discovery-synthesizer** | Worker | Subagente; `Read,Write,Edit` | idem |
| C-6 | **phase-evaluator-010** | C | Subagente independiente; rúbrica embebida | idem |
| C-7 | **Esquemas de estado** | — | Plantillas JSON/`.txt` de `harness-state.json`, `execution-state.json`, `claude-progress.txt` | `920_build/010_discovery/schemas/` |
| C-8 | **Plantilla Sprint Contract** | A | Texto (ya redactada en diseño §9) | `920_build/010_discovery/templates/` |
| C-9 | **Rúbrica de evaluación** | C | Documento + esquema `verdict.json`/`metrics_summary.json` (diseño §10) | `920_build/010_discovery/schemas/` |
| C-10 | **Script/lista de scaffold** | A | Carpetas que crea E10-A: `/010_discovery/`, `/eval/`, `/knowledge/`, `/changes/` | dentro de C-1 |

**Selección de modelos (metodología §9):** Opus para A (gate/contrato), B (planificación) y C
(auditoría con reasoning); Sonnet para los Workers de ejecución documental (dialoguer/synthesizer);
analyst en Opus por la detección de contradicciones (E8).

## 4. Estrategia: vertical slice primero (E4, E9)

No construir los 10 componentes en paralelo. Primero un **walking skeleton** que recorra A→B→1
Worker→C de punta a punta produciendo **un** artefacto real; validar que el flujo plano funciona; y
luego **ensanchar** (resto de workers, durabilidad, rúbrica completa). Así el riesgo del modelo plano
se prueba en INC-1, no al final.

## 5. Incrementos (roadmap)

| Inc | Entregable | Componentes | Done (criterio verificable) |
|-----|-----------|-------------|------------------------------|
| **INC-0** | Andamiaje y decisiones | Estructura `920_build/010_discovery/`, esquemas mínimos C-7, decisión P-1 | Carpeta creada; esquemas vacíos válidos; ubicación confirmada |
| **INC-1** | **Walking skeleton end-to-end** | C-1 (mínimo), C-2 (B trivial), C-3 (dialoguer), C-5 (synthesizer mínimo), C-6 (C con rúbrica mínima) | En seco, A invoca B→dialoguer→synthesizer→C y se produce `shared_understanding.md` + `verdict.json`; el gate humano aparece |
| **INC-2** | Cadena de workers completa | C-4 (analyst + bucle de aclaración), los **5 artefactos** | Se generan los 5 artefactos; bucle PENDIENTE→aclaración funciona; ALERTA escala a A |
| **INC-3** | Gate y evaluación robustos | C-9 rúbrica completa (D1–D6, veto), `metrics_summary.json`, protocolo de rechazo 12.4 | C emite APPROVED/REJECTED con scores; REJECTED dispara re-invocación solo de workers fallidos |
| **INC-4** | Durabilidad y reanudación | Checkpoints CP-01a…CP-04, context reset (E2), ritual E10-B, fallback de herramientas (E5) | Interrumpir a mitad y reanudar desde el último CP sin reiniciar; UNRESOLVED se registra sin bloquear |
| **INC-5** | **Evaluación temprana (E9)** | Muestra representativa + ajuste | C evalúa el slice; si score < 0.7, se ajusta contrato/spec antes de declarar completo |

## 6. Orden y dependencias

```
INC-0 ─▶ INC-1 ─▶ INC-2 ─▶ INC-3 ─▶ INC-4 ─▶ INC-5
(base)   (slice)  (workers) (gate)   (durab.)  (eval+ajuste)
```
Dependencia estricta: cada incremento construye sobre el anterior. INC-1 es el hito de mayor riesgo
(valida el modelo plano); por eso va temprano.

## 7. Criterio de Done del harness 010 (listo para operar)

1. Los 10 componentes (C-1…C-10) existen en `920_build/010_discovery/` y cargan sin error.
2. El flujo completo A→B→Workers→C corre de punta a punta y produce los **5 artefactos** + veredicto.
3. El **gate humano** (P5) y el **protocolo de rechazo** (12.4) funcionan.
4. La **reanudación desde checkpoint** (E5) y el **context reset** (E2) están probados.
5. La **evaluación temprana** (E9) dio score ≥ 0.7 o se aplicó el ajuste correspondiente.
6. Existe un **procedimiento de despliegue** a la terminal de operación (copiar agentes/comandos).

## 8. Pruebas y evaluación temprana (E9)

- **Banco de prueba "Sabbia simulada":** como el cliente real es el dueño/equipo de Sabbia, para
  probar el harness sin ocupar a una persona se usa el `statement.md` + respuestas guionizadas como
  *stand-in* del cliente, suficientes para ejercitar el flujo y la rúbrica.
- **Qué se mide:** que los artefactos cumplan las dimensiones D1–D6 sobre ese caso; latencia y
  consumo de tokens por agente (métricas Tipo 1).
- **Cuándo:** al cerrar INC-1 (skeleton) y de nuevo en INC-5, antes de declarar Done.

## 9. Riesgos del plan y decisiones abiertas

**Decisiones a confirmar antes/durante INC-0:**
- **P-1 — Ubicación de los artefactos construidos:** ¿`920_build/010_discovery/` (propuesto) u otra
  convención? Afecta todos los paths del inventario.
- **P-2 — Forma del Governor (C-1):** ¿comando(s) `/harness-start`+`/harness-continue`, o un único
  comando, o CLAUDE.md del harness con el ritual? (Recomendado: CLAUDE.md del harness + 1 comando.)
- **P-3 — Modelo por agente:** confirmar la asignación Opus/Sonnet de §3.

**Riesgos:**
- Que el runtime de operación sea < v2.1.172: cubierto por el modelo plano (no dependemos de spawn
  anidado).
- Mezcla de planos al construir esquemas (crear instancias en vez de plantillas) — vigilar D-002.
- Sobre-ingeniería: respetar INC-1 mínimo antes de ensanchar (E4).

## 10. Pendientes heredados del diseño (asignados)

- *Lista concreta de stakeholders de Sabbia* → se cierra al preparar INC-1 (entrada del dialoguer).
- *Esquema mínimo de `harness-state.json`/`execution-state.json`* → **C-7, INC-0**.
- *Guión inicial de preguntas socráticas + orden de decisiones §10* → contenido del **C-3, INC-1/2**.

---

> Tras aprobar este plan: ejecutar **INC-0** (andamiaje + cerrar P-1/P-2/P-3) y luego **INC-1**
> (walking skeleton). Recién ahí empieza la **construcción** del harness.
