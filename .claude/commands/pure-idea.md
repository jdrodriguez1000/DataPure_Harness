---
description: Registra una idea nueva en el backlog del proyecto DataPure Harness (500_persistence/backlog.md)
---

Estás ejecutando el comando de **captura de ideas** del proyecto **DataPure Harness**. Tu trabajo es
registrar de forma rápida una idea en `500_persistence/backlog.md` para no perderla, sin disparar el
ritual de cierre de sesión (`/pure-progress`).

## Qué es el backlog (y qué NO)
- `backlog.md` guarda **ideas y posibilidades**: cosas que quizá construyamos en el futuro, sin
  certeza. Es distinto de `tasks.md` (trabajo **comprometido**).
- Estados de una idea: `No implementada` (por defecto al nacer) · `Implementada` · `Descartada`
  (se decidió no hacerla por ahora, pero **no se borra**: puede reactivarse).

## Reglas
- Este comando **solo agrega ideas nuevas** (o, si se pide, actualiza el estado de una existente).
  No toca los otros archivos de persistencia.
- **Relee `backlog.md` antes de escribir** (no asumas el último `I-xxx` de memoria): puede haber
  otra terminal trabajando sobre la misma carpeta. Asigna el siguiente `I-xxx` incremental, sin
  reutilizar IDs.
- Mantén **actualizado el índice** (tabla superior): cada idea nueva debe aparecer ahí (1 línea).
- No dupliques: si la idea ya existe, actualízala en vez de crear otra.
- Sé conciso: el índice 1 línea; el detalle abajo, breve y accionable.

## Pasos
1. **Lee** `500_persistence/backlog.md` para conocer el último `I-xxx` y evitar duplicados.
2. **Toma la idea** desde `$ARGUMENTS`. Si está vacío o es ambiguo, pídele al usuario una frase
   breve que describa la idea antes de registrar.
3. **Agrega la idea**:
   - Nueva fila en el índice: `| I-xxx | No implementada | <idea en 1 línea> |`.
   - Bloque de detalle abajo: título `## I-xxx — <título> — No implementada`, `**Fecha:**` (la de
     hoy), y una descripción breve de **qué es**, **por qué** podría valer y, si aplica, cómo
     encajaría. Enlaza decisiones/lecciones relacionadas por ID si las hay.
4. **Sube solo este cambio** al repositorio (acotado, para no chocar con otra terminal):
   - `git add 500_persistence/backlog.md`.
   - `git commit` con mensaje en español, p. ej. `docs(backlog): registra I-xxx — <resumen>`.
     Termina con la línea `Co-Authored-By: Claude Opus 4.8 <noreply@anthropic.com>`.
   - `git push origin main`. Si el push falla por autenticación, NO reintentes en bucle: informa al
     usuario y sugiérele `! git push origin main`.
   - **No** uses `--no-verify` ni fuerces el push.
5. **Reporta** al usuario el `I-xxx` asignado, su título y el hash del commit, sin volcar todo el
   archivo.

Si el usuario en vez de agregar pide **cambiar el estado** de una idea existente (a `Implementada` o
`Descartada`), localízala por su `I-xxx`, actualiza el estado en el índice y en su bloque de detalle
(añadiendo una nota corta del porqué), y haz el mismo commit acotado.
