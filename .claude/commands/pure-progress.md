---
description: Actualiza los 4 archivos de persistencia del proyecto DataPure Harness (progress, tasks, decisions, lessons)
---

Estás ejecutando el comando de **cierre/actualización de persistencia** del proyecto
**DataPure Harness**. Tu trabajo es dejar los 4 archivos de `500_persistence/` reflejando fielmente
todo lo ocurrido en la sesión actual, para que la próxima sesión (de cualquier agente) arranque
orientada sin gastar tokens.

## Reglas
- La sesión principal de Claude Code es el **único escritor** de `500_persistence/` (D-003).
- Mantén **siempre actualizado el índice** (tabla superior) de cada archivo: es lo que leen los
  demás agentes. Cada entrada nueva debe aparecer en su índice.
- Usa IDs incrementales sin reutilizar: sesiones `S-xxx`, tareas `T-xxx`, decisiones `D-xxx`,
  lecciones `L-xxx`. Revisa el último ID usado antes de asignar uno nuevo.
- No dupliques: si una entrada ya existe, actualízala en lugar de crear otra.
- Sé conciso: 1 línea por fila de índice; el detalle abajo, breve y accionable.

## Pasos
1. **Lee** los 4 archivos actuales para conocer el último estado y los últimos IDs:
   `500_persistence/progress.md`, `500_persistence/tasks.md`, `500_persistence/decisions.md`,
   `500_persistence/lessons.md`.
2. **Revisa** lo ocurrido en la sesión actual: qué se hizo, qué se decidió, qué se aprendió, qué
   quedó pendiente. Si hay argumentos en `$ARGUMENTS`, úsalos como resumen/foco de lo que el
   usuario quiere registrar.
3. **Actualiza `progress.md`**: refresca el bloque "Estado actual" y agrega/edita una entrada de
   sesión `S-xxx` con qué se hizo, hallazgos y dónde quedamos.
4. **Actualiza `tasks.md`**: marca como `HECHA` las tareas completadas, ajusta las `EN CURSO` y
   registra las `PRÓXIMA` que surgieron. Refleja todo en el índice/tablero.
5. **Actualiza `decisions.md`**: registra cada decisión nueva como `D-xxx` (FIRME / PROVISIONAL /
   PENDIENTE) con contexto, decisión y consecuencias.
6. **Actualiza `lessons.md`**: registra cada lección nueva como `L-xxx` con su **Regla para el
   futuro**.
7. **Verifica** que los 4 índices quedaron coherentes con el detalle de abajo.
8. **Sube al repositorio** los cambios de la sesión (commit + push a GitHub):
   - Repo: `https://github.com/jdrodriguez1000/DataPure_Harness.git`, rama `main`.
   - `git add -A` (versiona toda la sesión, no solo `500_persistence/`).
   - `git commit` con un mensaje en español que resuma la sesión y referencie los IDs tocados
     (p. ej. `docs(persistence): cierre S-xxx — <resumen>` y mencionando T/D/L afectados).
     Termina el mensaje con la línea `Co-Authored-By: Claude Opus 4.8 <noreply@anthropic.com>`.
   - `git push origin main`. Si el push falla por autenticación, NO reintentes en bucle: informa al
     usuario y sugiérele ejecutar `! git push origin main` en su sesión para autenticarse.
   - Si no hubo nada que commitear (`git status` limpio), indícalo y omite el push.
   - **No** uses `--no-verify` ni fuerces el push.
9. **Reporta** al usuario un resumen corto de qué entradas creaste/actualizaste (IDs) y el hash del
   commit subido, sin volcar el contenido completo.

Si en la sesión no hubo cambios en alguna de las 4 dimensiones, indícalo explícitamente y no
inventes entradas.
