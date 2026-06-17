---
name: harness-continue
description: Ritual de continuación (E10-B) del harness 010 Discovery. Úsalo para REANUDAR una fase ya iniciada (tras una interrupción, un context reset E2, o una nueva sesión) — lee el estado persistido y continúa desde el último checkpoint sin reiniciar. Para iniciar una fase desde cero usa /harness-start.
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Bash, Agent
---

# /harness-continue — Ritual de continuación (E10-B) · Instancia A (Governor)

Eres **A (Governor)**. Estás **reanudando** la fase 010 Discovery. **Nunca reinicies desde cero**:
recupera el estado del filesystem y continúa desde el último checkpoint.

## 1. Orientación (lee el estado, no la memoria)
1. Lee `harness-state.json` (`status`, aprobaciones, `escalations`).
2. Lee `execution-state.json` (`orchestration_plan` + último checkpoint CP-xx).
3. Lee `claude-progress.txt` (bitácora) y verifica qué artefactos ya existen en `/010_discovery/`.

Si no existen los archivos de estado, **detente**: no hay nada que reanudar → usa `/harness-start`.

## 2. Determina el punto de retome según `status`
- `INIT` / `CONTRACT_APPROVED` → retoma en la planificación/ejecución (paso 4–5 de `harness-start`).
- `IN_EXECUTION` → continúa la cadena **desde el último checkpoint** registrado (no repitas workers
  ya completados; verifica el path del artefacto antes de re-invocar).
- `EXECUTION_COMPLETE` → ve al gate del entregable + auditoría (paso 6 de `harness-start`).
- `IN_AUDIT` → lee `/eval/verdict.json` y decide Avanzar/Repetir (paso 7).
- `IN_REWORK` → re-invoca solo los workers fallidos.
- `HOLD` → vuelve al gate del contrato (requiere nueva aprobación humana).
- `PHASE_COMPLETE` → nada que reanudar: reporta el handoff al 020.

## 3. Continúa y persiste
Sigue el flujo de `harness-start` desde el punto determinado, registrando cada checkpoint en
`execution-state.json` y actualizando `claude-progress.txt`. Mantén el gate humano (P5) intacto:
si reanudas justo antes de un gate, vuelve a pedir la aprobación explícita.
