---
description: Ritual de inicio de sesión del proyecto DataPure Harness — lee la persistencia y orienta sobre el estado y la próxima tarea
---

Estás ejecutando el comando de **inicio de sesión** del proyecto **DataPure Harness**. Tu trabajo
es orientarte leyendo la persistencia y dejar claro en qué vamos y qué sigue, para poder continuar
el proyecto sin arrancar ciego ni gastar tokens de más.

## Reglas
- Empieza por la **puerta de entrada** `500_persistence/progress.md` (índice + "Estado actual").
- Usa los **índices** de cada archivo: lee solo las secciones que necesites, no los archivos
  completos (ahorro de tokens — D-004).
- No modifiques ningún archivo de persistencia en este comando: es de **solo lectura/orientación**.
  La actualización se hace al cierre con `/pure-progress`.

## Pasos
1. **Lee `500_persistence/progress.md`**: el bloque "Estado actual" y la última entrada de sesión
   (S-xxx) del índice.
2. **Lee `500_persistence/tasks.md`**: identifica las tareas `EN CURSO` y la(s) `PRÓXIMA(S)` según el
   tablero. Lee el detalle solo de esas.
3. **Consulta según necesidad**:
   - `500_persistence/decisions.md` → si necesitas saber qué decisiones (D-xxx) rigen el trabajo que
     sigue, especialmente las `PENDIENTE` o `PROVISIONAL` que puedan bloquear.
   - `500_persistence/lessons.md` → si la próxima tarea se relaciona con alguna lección (L-xxx) y su
     "Regla para el futuro".
4. Si hay argumentos en `$ARGUMENTS`, enfoca la orientación hacia ese tema.

## Reporta al usuario (conciso)
Presenta un resumen estructurado:
1. **Estado actual**: dónde está el proyecto en 2–3 líneas.
2. **Última sesión**: qué se hizo (S-xxx).
3. **Decisiones que bloquean o condicionan**: cualquier `PENDIENTE`/`PROVISIONAL` relevante.
4. **Próxima tarea propuesta**: el ID y título de la siguiente tarea prioritaria (T-xxx) y qué
   implica.
5. **Pregunta de arranque**: confirma con el usuario si seguimos con esa próxima tarea o ajustamos.

No vuelques el contenido completo de los archivos; resume y referencia por ID.
