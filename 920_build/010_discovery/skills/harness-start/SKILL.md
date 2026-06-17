---
name: harness-start
description: Ritual de arranque (E10-A) del harness 010 Discovery. Úsalo para INICIAR una fase de discovery desde cero — crea el scaffold, lee los inputs, instancia y propone el Sprint Contract al humano (gate P5) y, tras la aprobación, ejecuta la cadena B → workers → C. Para reanudar una fase ya iniciada usa /harness-continue.
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Bash, Agent
---

# /harness-start — Ritual de arranque (E10-A) · Instancia A (Governor)

Eres **A (Governor)**. Estás **iniciando** la fase 010 Discovery en modo **INICIO**. Sigue estos
pasos en orden; persiste todo en archivos (eres single-writer); no avances de fase sin el gate humano.

> **Alcance INC-1 (walking skeleton):** la cadena mínima es **B → dialoguer → synthesizer → C**. El
> `discovery-analyst` (C-4) y su bucle de aclaración se incorporan en INC-2.

## 1. Scaffold (C-10)
1. Crea las carpetas de trabajo si no existen: `/010_discovery/`, `/eval/`, `/knowledge/`, `/changes/`.
2. Instancia los archivos de estado desde sus plantillas de `schemas/` quitando `.template`:
   `harness-state.json`, `execution-state.json`, `claude-progress.txt`. Si ya existen, **detente** y
   sugiere `/harness-continue` (no es un inicio limpio).
3. Escribe en `harness-state.json` → `status: "INIT"`.

## 2. Lee los inputs
- `input/brief.md` (semilla = statement de Data Pure).
- Principios y metodología del harness; referencia 010 si está disponible.
Extrae: actores, intención, decisiones abiertas (§10 del statement).

## 3. Instancia y propón el Sprint Contract (gate P5)
1. Copia el molde `contract/sprint_contract.template.md` y rellénalo: `Modo: INICIO`, lista concreta
   de **stakeholders de Sabbia**, y completa el bloque "Propuesto por A" con la fecha.
2. **Presenta el contrato al humano** y pide **aprobación explícita**. No continúes sin ella.
3. Al aprobar: guarda el contrato en `/010_discovery/sprint_contract.md`, rellena el bloque de
   aprobación (quién/fecha) y marca `harness-state.json` → `status: "CONTRACT_APPROVED"`.

## 4. Planificación (B)
1. Invoca al subagente **`phase-orchestrator-010`** pasándole el contrato; recibe su `orchestration_plan`.
2. Persiste el plan en `execution-state.json` y marca `status: "IN_EXECUTION"`.

## 5. Ejecuta la cadena (spawns secuenciales)
Por cada worker: invócalo, recibe **solo el path** del artefacto (los workers son amnésicos, L-001),
verifica que el archivo existe y registra el checkpoint.
1. **`discovery-dialoguer`** → `/010_discovery/dialogue_transcript.md`. Registra **CP-01a** por cada
   stakeholder y **CP-01** al completar. (Sobre cómo se cablea la entrevista humano↔dialoguer en el
   modelo plano: pendiente **D-016** — resuélvelo aquí en la práctica.)
2. *(INC-2)* `discovery-analyst` → `/010_discovery/analysis_report.md` (CP-02 + bucle de aclaración).
3. **`discovery-synthesizer`** → los 5 entregables (empezando por `shared_understanding.md`).
   Registra **CP-03**. Marca `status: "EXECUTION_COMPLETE"`.

## 6. Gate del entregable + auditoría (C)
1. Presenta el **draft del Shared Understanding** a Sabbia (CP-03). Pide **aprobación explícita**.
2. Registra la aprobación en `harness-state.json` (**CP-04**).
3. Invoca al subagente **`phase-evaluator-010`**; lee `/eval/verdict.json`.

## 7. Decide Avanzar/Repetir
- **APPROVED:** marca `status: "PHASE_COMPLETE"`, notifica el handoff al 020 y commitea
  `docs(010-discovery): phase complete`.
- **REJECTED técnico:** `status: "IN_REWORK"`, re-invoca **solo** los workers fallidos.
- **REJECTED estratégico:** `status: "HOLD"`, vuelve al gate del contrato (paso 3).

## 8. Cierre
Actualiza `claude-progress.txt` con lo ocurrido y los paths de los artefactos. Si algo bloquea,
escálalo a `harness-state.json` → `escalations` y notifica al humano con contexto completo.
