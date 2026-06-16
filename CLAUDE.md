# CLAUDE.md — Proyecto "DataPure Harness"

## En qué consiste este proyecto

Este repositorio es la **fábrica** que construye un *meta-harness agéntico* llamado
**DataPure Harness**. Ese harness es la maquinaria (Familia A del `800_documents/statement.md` §9)
que, una vez construida, **fabrica el producto Data Pure** — un SaaS que mide y mejora la salud
de los datos de empresas clientes.

**Distingue siempre dos planos (no los mezcles):**

1. **Plano de construcción (ESTE repositorio/terminal):** aquí se *construye* el DataPure Harness
   (sus definiciones, código, esquemas y agentes). Su persistencia es la carpeta `500_persistence/`.
2. **Plano de operación (OTRA terminal):** el DataPure Harness ya construido se *ejecuta* en otra
   terminal para fabricar Data Pure, con su propia persistencia (`claude-progress.txt`,
   `harness-state.json`, `execution-state.json`). **Esos archivos NO van en este proyecto.**

El método, principios y patrón de 3 instancias que rigen el harness están en `810_inputs/` (
`principles.md`, `metodology.md`). Las definiciones de las 10 fases están en `700_harnesses/`
(010–100). El producto a fabricar está descrito en `800_documents/statement.md`.

## Estructura

```
DataPure_Harness/
├── CLAUDE.md            ← este archivo
├── 500_persistence/     ← fuente de verdad de la construcción (leer/actualizar cada sesión)
│   ├── progress.md      ← avance (PUERTA DE ENTRADA)
│   ├── tasks.md         ← tareas hechas y próximas
│   ├── decisions.md     ← decisiones tomadas
│   └── lessons.md       ← lecciones aprendidas
├── 700_harnesses/       ← definiciones de las 10 fases (010–100)
├── 800_documents/       ← statement.md (el producto Data Pure a fabricar)
└── 810_inputs/          ← principios y metodología universal del harness
```

## REGLA OBLIGATORIA — Protocolo de sesión

Toda sesión de cualquier agente de Claude Code en este proyecto DEBE cumplir:

### Al INICIO de cada sesión (ritual de orientación)
1. Leer **`500_persistence/progress.md`** (empieza por el índice y el "Estado actual").
2. Desde ahí, derivar a `500_persistence/tasks.md`, `500_persistence/decisions.md` y
   `500_persistence/lessons.md` **solo según se necesite** — usa los índices, no leas todo (ahorro de
   tokens).
3. Con eso, orientarse: qué se hizo, qué decisiones rigen, qué sigue.

### Al CIERRE de toda sesión (actualización obligatoria)
Antes de terminar, actualizar los **4 archivos** de `500_persistence/` con lo ocurrido en la sesión:
1. **`progress.md`** — agregar/actualizar el "Estado actual" y una entrada de sesión (S-xxx).
2. **`tasks.md`** — marcar tareas completadas y registrar las próximas (T-xxx).
3. **`decisions.md`** — registrar toda decisión nueva (D-xxx) con su detalle.
4. **`lessons.md`** — registrar lecciones nuevas (L-xxx) con su "Regla para el futuro".

Mantén siempre **actualizado el índice** de cada archivo (es lo que leen los demás agentes).
La sesión principal de Claude Code es el **único escritor** de `500_persistence/`.

> Sin este protocolo, cada sesión arranca ciega y se desperdicia contexto (E1, E10 de la
> metodología). No es opcional.
