# Data_Pure — Statement (documento vivo)

> Documento de entendimiento de **lo que se espera de la solución**. Es la fuente de verdad de
> producto: qué es Data_Pure, para quién, cómo cobra y cómo se va a construir.
> Empresa: **Sabbia Solutions & Services**. Producto: **Data Pure**.
> Última actualización: 2026-06-16.
> Diseño técnico detallado (cómo se implementa): `800_documents/architecture.md`.

---

## 1. Visión

Las empresas quieren hacer proyectos de inteligencia artificial (predecir ventas, evitar churn,
detectar fraude, captar clientes, optimizar marketing), pero casi todas tienen **problemas con sus
datos**. Sin datos sanos, la ciencia de datos no despega.

**Data Pure** es una solución SaaS que:
1. **Mide el nivel de salud** de los datos de cada empresa cliente.
2. La lleva en un **viaje** desde el estado inicial de sus datos hasta quedar **data-driven ready**.
3. Pasando por la **corrección** de esos datos en el camino.

El objetivo final: que el cliente entienda que la salud de sus datos es la **base** para hacer
ciencia de datos que optimice su operación, reduzca gastos y aumente ingresos.

Data Pure lo construye y opera **Sabbia Solutions & Services**, que atiende a muchas empresas de
muchos sectores desde una sola plataforma.

---

## 2. A quién sirve: dos caras sobre un núcleo común

Data Pure tiene **una sola puerta de entrada** (un botón "Data Pure" en la web de Sabbia → login
único) y, según **el rol** del usuario, muestra una de **dos caras**, ambas leyendo el **mismo
núcleo de datos**:

```
            [ Botón "Data Pure" en la web de Sabbia ]
                           │
                     [ Login único ]
                     ¿quién eres? (rol)
                    /                    \
       rol = CLIENTE                   rol = EQUIPO SABBIA
   Portal del cliente                  Backoffice de Sabbia
   (ve SOLO su empresa,                (ve TODOS los clientes,
    mono-tenant + RLS)                  cross-tenant)
                    \                    /
                     \                  /
              [ NÚCLEO / fuente de verdad única ]
        tenants · suscripciones · facturas (ledger)
        · índice de salud · estado de acceso · eventos
```

- **Portal del cliente (Cara 1):** la empresa cliente entra con usuario/contraseña y ve **solo sus
  datos** y la salud de su empresa. Detalle en §6.
- **Backoffice de Sabbia (Cara 2):** un empleado de Sabbia entra por la misma puerta y ve la
  operación **de todos los clientes** (negocio, cartera, finanzas, salud de la app). Detalle en §7.

No son dos aplicaciones: son **dos vistas sobre el mismo núcleo** (§8). El backoffice **cruza
deliberadamente** el aislamiento que protege al cliente, por lo que exige un **plano de roles/auth
separado** (el equipo de Sabbia no es "un tenant más").

---

## 3. Modelo de negocio

### Fase 1 — Diagnóstico (recurrente, valor fijo por tamaño)
- El cliente paga una mensualidad fija **solo por ver** la salud de sus datos.
- Tamaño **S = 200 USD/mes**; M, L, XL escalan hacia arriba.
- Incluye:
  - **Índice de salud** de los datos.
  - Análisis **tipo Pareto** proactivo: "enfócate aquí para resolver el 80% de tus errores con el
    20% del esfuerzo".

### Fase 2 — Corrección (opcional, precio inverso a la salud)
El cliente es **autónomo**: puede corregir sus errores por su cuenta o pagarle a Sabbia para
hacerlo. El cargo adicional (valores para tamaño **S**) baja a medida que mejora la salud:

| Salud de los datos | Cargo de corrección (S) |
|--------------------|-------------------------|
| < 50%              | 500 USD/mes             |
| 50% – 70%          | 400 USD/mes             |
| 70% – 85%          | 300 USD/mes             |
| 85% – 95%          | 200 USD/mes             |
| > 95%              | 0 USD (no se cobra)     |

### Fase 3 — Mantenimiento (al alcanzar 95%)
Al llegar al 95% de salud, el cliente tiene dos caminos:
- **Continuar** con el servicio de auditoría de salud por la mensualidad fija (200 USD/mes en S), o
- **No continuar**.

### Tamaño de empresa (S / M / L / XL)
Definido por **volumen de datos + ingresos**. Se clasifica en cada eje y se toma **el mayor** (regla
**MAX**), para no subvaluar a quien tiene mucho volumen y pocos ingresos (o viceversa).

| Tamaño | Ingresos anuales (USD) | Volumen (registros transaccionales, 2–3 años) | Multiplicador |
|--------|------------------------|-----------------------------------------------|---------------|
| **S**  | < 1 M                  | < 100 mil                                     | 1.0           |
| **M**  | 1 M – 10 M             | 100 mil – 1 M                                 | 2.0           |
| **L**  | 10 M – 100 M           | 1 M – 10 M                                    | 3.5           |
| **XL** | > 100 M                | > 10 M                                        | 6.0           |

El **multiplicador escala todo**: la mensualidad fija de diagnóstico **y** las bandas de corrección.
Ejemplo (banda < 50%): S=500, M=1.000, L=1.750, XL=3.000.

### Mecánica de cobro de la corrección (mensual flotante por banda)
- La corrección es una **suscripción mensual** cuyo precio se **recalcula cada mes** según la
  **banda de salud vigente**. Al subir de banda, baja la tarifa el mes siguiente; al superar 95% no
  se cobra.
- El **total** depende de cuántos meses y bandas tome. Ejemplo S: 500+400+300+200 = **1.400** en 4
  meses; al 5.º mes (>95%) ya no paga corrección.
- **SLA de avance:** se pacta una meta (ej. subir al menos una banda por mes, o un tope de meses)
  para **alinear incentivos** cuando la corrección la ejecuta Sabbia.

### Suscripción, pasarela y ciclo de cobro

**Dos líneas de suscripción independientes.** Diagnóstico (Fase 1) y Corrección (Fase 2) son
suscripciones separadas: el cliente puede pausar la corrección y seguir viendo el diagnóstico. Ambas
son **prepago mensual** (se cobra por adelantado el periodo).

**Primer mes gratis (onboarding del baseline).** Antes del primer cobro, Data Pure ingiere 2–3 años
de historia y construye la **línea base**. Ese primer periodo es **gratuito**: el cliente ve su
baseline sin pagar y el cobro del Diagnóstico arranca **desde la 2.ª entrega**. Es el principal
gancho comercial: "ve la salud de tus datos gratis el primer mes".

**Pasarela de pagos.** Objetivo: **Stripe** (suscripciones en USD, prorrateo y cambio de banda
automático para la Fase 2 flotante). Restricción conocida: Stripe **no acepta cuentas estándar de
entidades colombianas**, así que se opera vía **entidad en EE.UU.** (USD) o con un **fallback local
LatAm** (Wompi / dLocal / PayU) para clientes que deban pagar en COP. La capa de facturación se
diseña **agnóstica de pasarela** para soportar ambos.

**Acceso según pago (impago).** El portal está **vivo mientras la suscripción esté al día**. Si el
cliente deja de pagar:
- **Gracia de máximo 5 días**, durante la cual el portal sigue accesible con un **aviso prominente**
  de que debe reactivar mediante el pago.
- Vencida la gracia, el portal pasa a **solo-lectura congelado** en la última fecha de corte: **no se
  borra ni se recalcula**. Se **reactiva** automáticamente al pagar.
- La corrección (Fase 2) puede pausarse de forma independiente sin afectar el acceso al diagnóstico.

### Pasarela simulada y contrato `PaymentGateway`

Para iniciar pruebas **sin entidad legal ni cuenta de comercio**, la facturación se diseña
**agnóstica de pasarela** detrás de una interfaz `PaymentGateway`. La **lógica de negocio** (cuándo
cobrar, qué banda, cuándo congelar) vive en Data Pure; la pasarela solo **mueve dinero**.
Implementaciones intercambiables:
- `SimulatedGateway` — primera a construir; emula todo en BD propia, con "avance de reloj" manual
  para demos. Sin dinero real.
- `StripeGateway` / `WompiGateway` — stubs de la misma interfaz, para cuando exista entidad.

**Operaciones del contrato `PaymentGateway`:**
| Operación | Qué hace |
|-----------|----------|
| `create_subscription(tenant, linea, tamaño)` | Abre suscripción (línea = `diagnostico` o `correccion`). Aplica primer mes gratis. |
| `set_amount(subscription, banda)` | Fija/recalcula el monto del próximo ciclo según banda (solo `correccion`). |
| `advance_cycle(subscription)` | Cierra el periodo y **emite factura** del siguiente (en el simulador, avanza el reloj). |
| `pay_invoice(invoice)` / `fail_invoice(invoice)` | Marca el resultado del cobro (en el simulador, botones de demo). |
| `pause(subscription)` / `resume(subscription)` | Pausa/reactiva una línea sin tocar la otra. |
| `cancel(subscription)` | Da de baja la línea. |

**Estados de factura:** `pendiente → pagada` | `pendiente → fallida`.
**Ciclo de acceso al portal (derivado del estado de cobro):**
`al_dia` → (factura fallida) → `en_gracia` (máx 5 días, aviso prominente) → (sin pago) →
`congelado` (solo-lectura en la última fecha de corte) → (pago) → `al_dia`.

**Primer mes gratis:** la primera factura del Diagnóstico se emite en **cero**; el cobro real
arranca en el segundo ciclo (2.ª entrega).

---

## 4. Concepto de "salud de los datos"

- Se expresa como un **índice global 0–100%** que rige el precio y el viaje.
- Acompañado de un **desglose por dominio**, para que el cliente vea dónde está fuerte y dónde débil,
  y perciba avance en todos los frentes.

### Dimensiones de la salud
- **Completitud** — campos vacíos / faltantes.
- **Unicidad** — duplicados.
- **Validez** — formatos y valores correctos.
- **Consistencia** — contradicciones entre sistemas/registros.
- **Actualidad** — datos al día.
- **Integridad** — relaciones / referencias no rotas.

### Enfoque holístico por dominios
- Se cubren **todos los dominios** del negocio: Clientes, Ventas, Finanzas, Productos, Inventario,
  Proveedores, RRHH, etc.
- Razón de negocio: el cliente quiere ver que **toda la empresa** mejora. Si un dominio está sano
  pero otro no, su atención se va al débil; quiere "todo al día".
- **Pareto** no significa ignorar dominios, sino: dentro del panorama completo, resaltar los errores
  que más suben el índice global con menos esfuerzo (foco sin abandonar nada).
- **Arquitectura extensible de dominios:** un motor común de salud + "paquetes de dominio"
  (reglas/plantillas por vertical) que se agregan y enriquecen con el tiempo. Al cliente se le
  muestra el panorama completo desde el día 1; la profundidad por dominio crece iterativamente.

### Cómo se calcula el índice global (ponderación entre dominios)

- **Promedio ponderado por importancia**, normalizado **solo sobre los dominios que el cliente
  entrega** (un dominio ausente no penaliza): `Global = Σ(peso_i × salud_i) / Σ(peso_i)`
- **Pesos por importancia (perfil por defecto):**

  | Importancia | Peso | Dominios |
  |-------------|------|----------|
  | Core        | 3    | Clientes, Ventas, Finanzas |
  | Soporte     | 2    | Productos, Inventario |
  | Periférico  | 1    | Proveedores, RRHH |

- **Perfiles por industria:** cada industria puede sobrescribir estos pesos (ej. retail sube
  Inventario a Core). El perfil por defecto es el punto de partida.
- **Guardia "todo al día":** el **nivel** global no puede superar en más de 1 al nivel del **peor
  dominio Core**. Así un dominio crítico podrido no se enmascara y se fuerza a sanear toda la
  empresa, no solo lo fácil.

### Niveles de madurez (el viaje, tono descriptivo)

Cinco niveles **alineados exactamente con las bandas de precio de corrección**: cruzar un umbral
significa **subir de nivel y, a la vez, bajar la tarifa** de corrección.

| Nivel | Nombre | Salud | Estado real de los datos |
|-------|--------|-------|--------------------------|
| 1 | **Datos en Caos**       | < 50%    | Dispersos, incompletos, duplicados y contradictorios. No confiables. |
| 2 | **Datos Conscientes**   | 50–70%   | Se sabe dónde están los problemas; inicia el saneamiento por Pareto. |
| 3 | **Datos Estructurados** | 70–85%   | Lo crítico corregido; los datos tienen forma y reglas; los reportes cuadran. |
| 4 | **Datos Confiables**    | 85–95%   | Calidad alta y sostenida; pocos errores residuales en todos los dominios. |
| 5 | **Data-Driven Ready**   | > 95%    | Datos limpios, completos y consistentes. Listos para IA/ciencia de datos. |

Nombres en **lenguaje de dueño de negocio** (no técnico). El nivel 5 es la meta del viaje.

### Motor de salud (cómo se calcula el % por dominio)

Cadena de cálculo: **Check → Dimensión → Dominio → Índice global**.

- **Check:** regla concreta con tasa de cumplimiento 0–100% y una **severidad** (alta/media/baja).
- **6 dimensiones:** Completitud, Unicidad, Validez, Consistencia, Actualidad, Integridad.
- **Roll-up:** check→dimensión por **cobertura** (registros evaluados); dimensión→dominio por pesos
  (iguales por defecto); dominio→global ya definido (importancia + guardia Core).
- **Severidad** prioriza el **Pareto** (`impacto = severidad × peso_dim × peso_dominio ×
  proporción_de_fallos`), no afecta el score.

**Plantillas declarativas por dominio (YAML):** el motor es **genérico** y lee un archivo por
dominio en `domains/`. Agregar un dominio = escribir un YAML. Formato en `domains/SCHEMA.md`;
plantilla de referencia en `domains/clientes.yaml`.

---

## 5. Ingesta de datos

- **Historia mínima de 2 años** (ideal 3, o más si el cliente lo desea).
- **No** nos conectamos directamente al ERP / base de datos del cliente. Vías de entrada:
  - **Conector / integrador enchufable** que extrae la información (ERPs como SAP, Siigo y otros;
    posiblemente CRMs; o bases de datos propias del cliente — aún sin definir).
  - **Archivos CSV / XLSX (XLS)** entregados por el cliente (camino siempre disponible).
- La **capa de ingesta** normaliza todas las fuentes a un formato interno antes de medir.

---

## 6. Cara 1 — Portal del cliente (entregable y ciclo de vida)

### Entregable: Portal de Salud de Datos (web vivo)
Como el cobro es mensual y recurrente, el entregable es un **portal vivo** (no un informe). Cinco
vistas, con la **evolución (el viaje)** como vista héroe:
1. **Portada** — nivel de madurez + índice global en semáforo/gauge + distancia a la meta.
2. **Evolución (héroe)** — línea de tiempo desde el baseline, con hitos y saltos de nivel.
3. **Mapa por dominio** — semáforo por dominio, con drill-down a las 6 dimensiones.
4. **Plan de acción (Pareto)** — qué corregir primero, ordenado por impacto.
5. **Valor generado** — traducción a negocio (tarifa que baja, casos de IA desbloqueados).

El **baseline** (foto del estado inicial a una fecha de corte) es el punto cero del viaje. El cliente
ve **solo su empresa** (aislamiento mono-tenant + RLS, §8).

### Salud del Stock vs Salud del Flujo
- **Stock** = todo el histórico acumulado (manda en la corrección, Fase B).
- **Flujo** = solo el período más reciente entregado (manda en mantenimiento, Fase D).
- Si el Stock mejora pero el Flujo sigue sucio → el problema es del **proceso de captura**, que es lo
  que hay que arreglar para ser data-driven sostenible.

### Ciclo de vida (medición por fecha de corte, no por reloj)
La salud se mide a una **fecha de corte explícita** y se recalcula **con cada entrega** de datos.

| Fase | Qué pasa | Datos |
|------|----------|-------|
| **A — Línea base** | Histórico 2–3 años a un corte; entender la data y la forma de trabajo. | Archivo CSV/XLSX |
| **B — Corrección (batch)** | Entregas sucesivas; diff a nivel de registro distingue corrección / regresión / dato nuevo. | Archivos por entrega |
| **C — Salud sostenida** | Se alcanza la meta (Data-Driven Ready) y se mantiene en ≥1 entrega. | Archivos |
| **D — Conexión en vivo** | Se ofrece el **conector** para mantener el Flujo al día (Mantenimiento). | Conector/integrador |

El **conector va en la Fase D** (no antes): es costoso, ya entendemos la data, el cliente ya vio el
valor, y mantener la salud exige datos continuos.

---

## 7. Cara 2 — Backoffice de Sabbia (el equipo)

El backoffice es el panel interno desde el cual el **equipo de Sabbia** opera el negocio: ve a
**todos los clientes a la vez** (cross-tenant) sobre el mismo núcleo (§8). Cubre cuatro frentes:

- **Clientes y suscripciones** — cuántos clientes hay, tamaños S/M/L/XL, **fase del viaje** de cada
  uno (Caos → Data-Driven Ready), líneas activas/pausadas (diagnóstico / corrección), altas y bajas.
- **Cartera y cobros** — quién pagó y quién no, facturas pendientes/fallidas, clientes **en gracia**
  (≤5 días) o **congelados**, gestión de morosidad (dunning).
- **Finanzas** — **MRR** e ingresos separados por línea (diagnóstico fijo por tamaño vs. corrección
  flotante por banda), proyección, y cuántos clientes están en **primer-mes-gratis** en curso.
- **Salud / observabilidad de la app** — errores, ingestas procesadas, uso del portal, latencia:
  cómo se comporta la aplicación.

### Roles y seguridad del backoffice
- El backoffice **cruza deliberadamente** el aislamiento (RLS) que protege a los clientes → vive en
  un **plano de identidad/roles separado**; el equipo de Sabbia **no** es un tenant más.
- **Roles finos por frente** (decisión abierta, §10): p. ej. finanzas/cartera visibles solo a
  dirección, observabilidad para el equipo técnico, soporte con acceso operativo.
- **Impersonación con auditoría** (decisión abierta, §10): que Sabbia pueda "ver como" un cliente
  para dar soporte, dejando traza de quién, cuándo y por qué.

---

## 8. Arquitectura de la solución (multi-cliente, multi-sector)

El reto: **N empresas × M sectores**, con esquemas y comportamientos distintos, servidos por **dos
caras** sin explotar en customización. Se resuelve con:

### Núcleo / fuente de verdad (ledger)
Un **núcleo único** que ambas caras leen: catálogo de **tenants**, **suscripciones**, **facturas**
(modeladas como **ledger** / registro append-only de eventos de cobro), **índice de salud** y su
historia, **estado de acceso** del portal, y un **log de eventos**. Que la facturación sea un ledger
(no un estado que se sobrescribe) es lo que permite responder las preguntas del backoffice (MRR,
morosidad, churn) sin reconstruir nada.

### Multi-tenancy (aislamiento)
Una sola base con **`tenant_id` + Row-Level Security**: aislamiento lógico fuerte, simple de operar.
El **portal de cliente** opera bajo RLS (un cliente nunca ve datos de otro). El **backoffice** opera
en un plano que **agrega across-tenant** con su propio control de acceso (§7).

### Configuración en 3 capas (con herencia)
El motor **compila la config efectiva** por cliente: `base → industria → cliente` (cada capa
sobrescribe la anterior).
- **Base:** plantilla canónica del dominio (ej. `domains/clientes.yaml`).
- **Overlay por industria:** ajusta pesos, checks, severidades y cadencias por sector (retail, salud,
  banca…). Agregar un sector = escribir un overlay, no reescribir el dominio.
- **Config por cliente:** mapeo de columnas y excepciones puntuales.

### Mapeo de esquemas asistido (el cuello de botella de escala)
Cada empresa entrega columnas distintas (`nit`, `cedula`, `doc_cliente` → `documento_id`). En el
onboarding, el sistema **sugiere** el mapeo (`field_aliases` + inferencia por tipo y patrón) y un
humano lo **valida una vez**. El **perfil de mapeo** se guarda por cliente y se **reutiliza** en cada
entrega → mapear una vez, medir automático para siempre.

### El activo que se capitaliza
Mientras más empresas de un sector se atienden, **más ricos** se vuelven los overlays de industria y
los aliases → cada nuevo cliente del sector entra **más rápido y barato**. Economía de escala que
vuelve el negocio defendible.

---

## 9. Cómo se construye: harness agéntico (Familia A)

Data Pure se **construye con agentes de IA** que trabajan bajo un **harness agéntico** (sistema meta
de orquestación). Es clave distinguir dos planos para no confundirlos:

- **Harnesses de construcción (Familia A):** el SDLC agéntico que **fabrica** Data Pure (discovery →
  spec → arquitectura → código → pruebas → despliegue). Producen **software**.
- **Workflows de runtime (Familia B):** features que viven **dentro** de Data Pure y operan con
  agentes/automatización una vez construido el producto (onboarding del cliente, medición de salud,
  ciclo de corrección, facturación). Son **salida** de la Familia A, no parte de ella.

> Nota: el trabajo previo de "010 discovery" (entrevista → tamaño → suscripciones → tenant) es en
> realidad una **feature de Familia B** (onboarding de cliente), reutilizable como spec/prototipo que
> alimenta la fase de Construcción. La **maquinaria agéntica** ya probada (patrón de 3 instancias
> governor/orchestrator/workers/evaluator, single-writer, checkpoints, rúbrica calibrada) se reusa
> como motor de **cada** harness de la Familia A.

### Mapa de harnesses de construcción (Familia A)

| # | Nombre | Qué hace (y qué produce) |
|---|--------|--------------------------|
| **010** | **Discovery de Producto** | Entrevista al dueño y al equipo de Sabbia: visión, alcance, prioridades, restricciones, criterios de éxito. Valida y extiende este statement. → **Brief de producto validado.** |
| **020** | **Especificación Funcional** | Convierte el discovery en spec por superficie (portal de cliente, backoffice, motor de salud, ingesta, facturación) con criterios de aceptación. → **Specs + criterios.** |
| **030** | **Arquitectura y Modelo de Datos** | Diseño técnico: núcleo/ledger, multi-tenancy + RLS, plano de roles/auth de las dos caras, contrato de pasarela, motor de salud, stack. → **Arquitectura + esquema.** |
| **040** | **Planeación / Backlog** | Descompone la spec en incrementos construibles priorizados y fija el orden de construcción. → **Roadmap de construcción.** |
| **050** | **Construcción** | Bucle iterativo: los agentes escriben el código de cada incremento contra la spec (spec → prueba → código). → **Código del producto.** |
| **060** | **QA / Pruebas y Evaluación** | Pruebas automatizadas + evaluación independiente (Instancia C) por incremento. → **Suite de pruebas + veredictos.** |
| **070** | **Seguridad y Aislamiento** | Verifica el aislamiento multi-tenant y el riesgo cross-tenant del backoffice; privacidad y cumplimiento. → **Informe de seguridad + gates.** |
| **080** | **Integración y Paquetes de Dominio** | Cablea la ingesta (CSV/XLSX, normalización, mapeo) + paquetes de dominio YAML y overlays por industria. → **Motor de dominios poblado.** |
| **090** | **Despliegue / Release** | Empaquetado, entornos, swap de pasarela (Simulada → stub Stripe/Wompi), publicación. → **Producto desplegado.** |
| **100** | **Operación y Observabilidad** | Monitoreo, salud de la app, mantenimiento — alimenta el backoffice. → **Operación viva.** |

> El conjunto, granularidad y nombres del mapa son la versión vigente acordada; pueden refinarse al
> diseñar cada harness (E4 — mínima complejidad).

---

## 10. Pendientes / decisiones abiertas

- **Roles finos del backoffice** (qué ve dirección vs. equipo técnico vs. soporte) e **impersonación
  con auditoría** (§7).
- **Roles dentro del portal de cliente** (¿la empresa cliente tiene varios usuarios con permisos
  distintos?).
- **Perfiles de ponderación por industria** (overrides del perfil por defecto, §4).
- **Detalle del SLA de avance** de la corrección (meta exacta y penalizaciones/garantías, §3).
- **Conectores** concretos a construir según los primeros clientes (Fase D, §6).
- **Destino del "010 discovery" actual** (feature de onboarding de Familia B): reclasificar y
  reusar; decisión pendiente.
- **Implementación pendiente:** motor de salud, plantillas de los demás dominios, portal de cliente,
  backoffice y el resto del producto — se abordan vía los harnesses de la Familia A (§9).
