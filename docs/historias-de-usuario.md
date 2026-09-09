# Historias de usuario

_Presentar al menos una historia de usuario representativa por módulo._
_Cada historia debe incluir formato clásico, criterios de aceptación y validación INVEST._

---

## HU-01 — Inicio de sesión institucional

| Campo | Detalle |
|-------|---------|
| Historia | Como ingeniero agrónomo, quiero ingresar con mi usuario y contraseña institucional, para acceder de forma segura al sistema desde mi celular en la calle. |
| Módulo | Autenticación y acceso |
| Requisitos relacionados | RF-01, RF-02 |

### Criterios de aceptación

1. Si las credenciales son válidas, accedo al Home.
2. Si las credenciales son inválidas, se muestra un mensaje de error.
3. Sin sesión iniciada, no puedo entrar a ninguna página del sistema.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | |
| Negociable | Sí | |
| Valiosa | Sí | |
| Estimable | Sí | |
| Pequeña | Sí | |
| Verificable | Sí | |

---

## HU-02 — Métricas y estado general en el Home

| Campo | Detalle |
|-------|---------|
| Historia | Como ingeniero agrónomo, quiero ver al entrar cuántos reclamos hay ingresados, sin dictaminar y dictaminados, para saber de un vistazo cómo viene la carga de trabajo. |
| Módulo | Home / Dashboard |
| Requisitos relacionados | RF-03, RF-04, RF-05 |

### Criterios de aceptación

1. El Home muestra las tres cifras (ingresados, sin dictaminar, dictaminados).
2. Se muestra un gráfico de reclamos pendientes agrupado por prioridad.
3. Se muestran los dictámenes próximos a vencer y un aviso de casos de tormenta.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | |
| Negociable | Sí | |
| Valiosa | Sí | |
| Estimable | Sí | |
| Pequeña | Parcial | Agrupa métricas + gráficos + alertas en una sola historia; podría dividirse en 2-3 HU más chicas. |
| Verificable | Sí | |

---

## HU-03 — Listado de reclamos priorizado

| Campo | Detalle |
|-------|---------|
| Historia | Como ingeniero agrónomo, quiero ver la lista de reclamos pendientes ordenados por urgencia, para atender primero los más peligrosos. |
| Módulo | Reclamos sin dictaminar |
| Requisitos relacionados | RF-07, RF-08, RF-09 |

### Criterios de aceptación

1. Solo se listan reclamos asignados a la Dirección Técnica y en estado "sin dictaminar".
2. El listado se ordena de urgente a baja por defecto.
3. Es filtrable por zona, prioridad, tipo de intervención y antigüedad.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | |
| Negociable | Sí | |
| Valiosa | Sí | |
| Estimable | Sí | |
| Pequeña | Sí | |
| Verificable | Sí | |

---

## HU-04 — Consulta de N.° SUA y año en la ruta

| Campo | Detalle |
|-------|---------|
| Historia | Como ingeniero agrónomo, quiero consultar el número de SUA y el año de cada reclamo de mi ruta, para anotarlos y poder dictaminarlos después en el formulario. |
| Módulo | Rutas eficientes |
| Requisitos relacionados | RF-27 |

### Criterios de aceptación

1. Desde la página de rutas puedo ver el N.° de SUA y el año de cada caso asignado.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | |
| Negociable | Sí | |
| Valiosa | Parcial | Valor bajo tomada de forma aislada; podría fusionarse como criterio de aceptación de HU-09. |
| Estimable | Sí | |
| Pequeña | Sí | |
| Verificable | Sí | |

---

## HU-05 — Carga del dictamen desde el celular

| Campo | Detalle |
|-------|---------|
| Historia | Como ingeniero agrónomo, quiero completar el dictamen desde el celular frente al árbol, para no volver a la oficina a cargarlo a mano. |
| Módulo | Realizar dictamen técnico |
| Requisitos relacionados | RF-12, RF-13, RF-14, RF-17 |

### Criterios de aceptación

1. Primero ingreso N.° de SUA y año.
2. El sistema verifica que el reclamo exista y esté sin dictaminar.
3. Completo los datos del ejemplar, tipo de intervención, fotos y observaciones.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | |
| Negociable | Sí | |
| Valiosa | Sí | |
| Estimable | Parcial | |
| Pequeña | Parcial | Historia "paraguas"; conviene dividir en sub-HU (datos del ejemplar, tipo de intervención, adjuntos). |
| Verificable | Sí | |

---

## HU-06 — Bloqueo de intervenciones contradictorias

| Campo | Detalle |
|-------|---------|
| Historia | Como ingeniero agrónomo, quiero que el sistema me impida cargar intervenciones contradictorias, para no generar un dictamen incoherente. |
| Módulo | Realizar dictamen técnico |
| Requisitos relacionados | RF-14 |

### Criterios de aceptación

1. Al elegir extracción, se bloquean poda y corte de raíces (y viceversa).
2. No puedo confirmar el dictamen hasta resolver la inconsistencia.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | |
| Negociable | Sí | |
| Valiosa | Sí | |
| Estimable | Sí | |
| Pequeña | Sí | |
| Verificable | Sí | |

---

## HU-07 — Época recomendada de intervención

| Campo | Detalle |
|-------|---------|
| Historia | Como ingeniero agrónomo, quiero que el sistema tenga en cuenta la estación del año y la especie, para programar intervenciones en el momento más conveniente para el ejemplar. |
| Módulo | Realizar dictamen técnico |
| Requisitos relacionados | RF-16 |

### Criterios de aceptación

1. El dictamen registra/sugiere la época recomendada según especie y estación.
2. Esa época queda disponible como criterio al planificar la ruta.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | |
| Negociable | Sí | |
| Valiosa | Parcial | Valor secundario: es una sugerencia, no un paso bloqueante; de todos modos la historia es válida. |
| Estimable | Sí | |
| Pequeña | Sí | |
| Verificable | Sí | |

---

## HU-08 — Firma digital del dictamen

| Campo | Detalle |
|-------|---------|
| Historia | Como ingeniero agrónomo, quiero firmar digitalmente el dictamen, para que quede como documento válido e inalterable. |
| Módulo | Realizar dictamen técnico |
| Requisitos relacionados | RF-18, RF-19, RF-20 |

### Criterios de aceptación

1. Necesito tener matrícula registrada para poder firmar.
2. Tras firmar, el dictamen queda en solo lectura (inmutable).
3. Se guarda fecha, matrícula y hash, y se calcula el vencimiento a 18 meses.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | |
| Negociable | Sí | |
| Valiosa | Sí | |
| Estimable | Sí | |
| Pequeña | Sí | |
| Verificable | Sí | |

---

## HU-09 — Planificación de ruta eficiente

| Campo | Detalle |
|-------|---------|
| Historia | Como ingeniero agrónomo, quiero que el sistema me arme la ruta más eficiente según una zona y mis horas o cantidad de casos, para perder menos tiempo viajando y dictaminar más. |
| Módulo | Rutas eficientes |
| Requisitos relacionados | RF-21, RF-22, RF-26 |

### Criterios de aceptación

1. Elijo zona, planifico por horas o por cantidad de casos, y elijo modo de traslado.
2. El cálculo usa 10 min por dictamen (parámetro configurable).
3. La ruta parte y vuelve a Parques y Paseos; veo mapa, cronograma y % de eficiencia.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | |
| Negociable | Sí | |
| Valiosa | Sí | |
| Estimable | Parcial | |
| Pequeña | Parcial | Complejidad algorítmica alta; conviene un spike técnico antes de estimar. |
| Verificable | Sí | |

---

## HU-10 — Elección de modo de traslado

| Campo | Detalle |
|-------|---------|
| Historia | Como ingeniero agrónomo, quiero elegir entre auto, a pie o bicicleta, para adaptar la jornada a la concentración de reclamos, priorizando la eficiencia del recorrido. |
| Módulo | Rutas eficientes |
| Requisitos relacionados | RF-22 |

### Criterios de aceptación

1. En cualquier modo de traslado elegido, el sistema optimiza la ruta por tiempo total.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Parcial | Depende de HU-09 (no se puede probar de forma aislada). |
| Negociable | Sí | |
| Valiosa | Sí | |
| Estimable | Sí | |
| Pequeña | Sí | |
| Verificable | Sí | |

---

## HU-11 — Casos de tormenta

| Campo | Detalle |
|-------|---------|
| Historia | Como ingeniero agrónomo, quiero ver aparte los casos de tormenta de los últimos días, para responder a la emergencia con una ruta de mínima distancia. |
| Módulo | Urgencia por Tormenta |
| Requisitos relacionados | RF-28, RF-29, RF-30 |

### Criterios de aceptación

1. La sección aparece solo si hay casos etiquetados como tormenta.
2. Todos los casos se tratan con igual urgencia.
3. Se genera una ruta de mínima distancia; el badge indica cuántos casos hay.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | |
| Negociable | Sí | |
| Valiosa | Sí | |
| Estimable | Sí | |
| Pequeña | Sí | |
| Verificable | Sí | |

---

## HU-12 — Balanceador de carga por prioridad

| Campo | Detalle |
|-------|---------|
| Historia | Como jefe de la Dirección Técnica, quiero usar un balanceador para fijar qué proporción de cada prioridad se dictamina por sesión, para orientar el trabajo según la estrategia del momento. |
| Módulo | Rutas eficientes (balanceador de carga) |
| Requisitos relacionados | RF-23, RF-25 |

### Criterios de aceptación

1. Ajusto porcentajes por prioridad con controles deslizables.
2. Si falta stock de una prioridad, el sistema redistribuye automáticamente.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | |
| Negociable | Sí | |
| Valiosa | Sí | |
| Estimable | Parcial | |
| Pequeña | Parcial | Podría dividirse en "ajustar porcentajes" y "modos predefinidos". |
| Verificable | Sí | |

---

## HU-13 — Perfiles de distribución (bajada de línea)

| Campo | Detalle |
|-------|---------|
| Historia | Como jefe de la Dirección Técnica, quiero configurar rápido un perfil de distribución cuando hay una directiva superior, para responder a una política puntual (ej. no dejar ningún caso urgente pendiente). |
| Módulo | Rutas eficientes (balanceador de carga) |
| Requisitos relacionados | RF-24 |

### Criterios de aceptación

1. Puedo asignar el 100% de la jornada a una sola prioridad en pocos pasos.
2. Puedo guardar y reutilizar perfiles de distribución.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Parcial | Depende de HU-12 (reutiliza el balanceador). |
| Negociable | Sí | |
| Valiosa | Sí | |
| Estimable | Sí | |
| Pequeña | Sí | |
| Verificable | Sí | |

---

## HU-14 — Visibilidad de reclamos derivados

| Campo | Detalle |
|-------|---------|
| Historia | Como área de Procesamiento de Datos, quiero que los reclamos que derivó (normales o con etiqueta de tormenta) aparezcan en el sistema, para que los ingenieros puedan trabajarlos. |
| Módulo | Reclamos sin dictaminar / Urgencia por Tormenta |
| Requisitos relacionados | RF-07, RF-28 |

### Criterios de aceptación

1. Los reclamos derivados normales se listan como "sin dictaminar".
2. Los reclamos derivados con etiqueta de tormenta van a la sección de urgencia.
3. El sistema no genera reclamos propios, solo consume los derivados del SUA.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | |
| Negociable | Sí | |
| Valiosa | Sí | |
| Estimable | Sí | |
| Pequeña | Sí | |
| Verificable | Parcial | Criterio de aceptación algo genérico ("para que puedan trabajarlos"); conviene precisar qué campos deben llegar completos. |