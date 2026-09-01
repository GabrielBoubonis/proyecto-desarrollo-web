# Historias de usuario

| ID | Rol | Historia | Criterios de aceptación |
| --- | --- | --- | --- |
| HU-01 | Ingeniero Agrónomo | Como ingeniero agrónomo, quiero ingresar con mi usuario y contraseña institucional, para acceder de forma segura al sistema desde mi celular en la calle. | Credenciales válidas → accedo al Home; inválidas → mensaje de error; sin sesión → no puedo entrar a ninguna página. |
| HU-02 | Ingeniero Agrónomo | Como ingeniero agrónomo, quiero ver al entrar cuántos reclamos hay ingresados, sin dictaminar y dictaminados, para saber de un vistazo cómo viene la carga de trabajo. | El Home muestra las tres cifras, gráfico por prioridad, dictámenes por vencer y aviso de casos de tormenta. |
| HU-03 | Ingeniero Agrónomo | Como ingeniero agrónomo, quiero ver la lista de reclamos pendientes ordenados por urgencia, para atender primero los más peligrosos. | Solo reclamos asignados y sin dictaminar; ordenados de urgente a baja; filtrables por zona, prioridad, tipo de intervención y antigüedad. |
| HU-04 | Ingeniero Agrónomo | Como ingeniero agrónomo, quiero consultar el número de SUA y el año de cada reclamo de mi ruta, para anotarlos y poder dictaminarlos después en el formulario. | Desde la página de rutas veo esos datos de cada caso asignado. |
| HU-05 | Ingeniero Agrónomo | Como ingeniero agrónomo, quiero completar el dictamen desde el celular frente al árbol, para no volver a la oficina a cargarlo a mano. | Primero ingreso SUA y año; el sistema verifica que exista y esté sin dictaminar; luego completo datos del ejemplar, intervención, fotos y observaciones. |
| HU-06 | Ingeniero Agrónomo | Como ingeniero agrónomo, quiero que el sistema me impida cargar intervenciones contradictorias, para no generar un dictamen incoherente. | Al elegir extracción se bloquean poda y corte de raíces; no puedo confirmar hasta resolverlo. |
| HU-07 | Ingeniero Agrónomo | Como ingeniero agrónomo, quiero que el sistema tenga en cuenta la estación del año y la especie, para programar intervenciones en el momento más conveniente para el ejemplar. | El dictamen registra/sugiere la época recomendada y puede usarse al planificar la ruta. |
| HU-08 | Ingeniero Agrónomo | Como ingeniero agrónomo, quiero firmar digitalmente el dictamen, para que quede como documento válido e inalterable. | Necesito matrícula registrada; tras firmar queda en solo lectura; se guarda fecha, matrícula y hash, y se calcula el vencimiento a 18 meses. |
| HU-09 | Ingeniero Agrónomo | Como ingeniero agrónomo, quiero que el sistema me arme la ruta más eficiente según una zona y mis horas o cantidad de casos, para perder menos tiempo viajando y dictaminar más. | Elijo zona, planifico por horas o cantidad, modo de traslado; 10 min por dictamen; la ruta parte y vuelve a Parques y Paseos; veo mapa, cronograma y eficiencia. |
| HU-10 | Ingeniero Agrónomo | Como ingeniero agrónomo, quiero elegir entre auto, a pie o bicicleta, para adaptar la jornada a la concentración de reclamos, priorizando la eficiencia del recorrido. | En cualquier modo el sistema optimiza la ruta por tiempo total. |
| HU-11 | Ingeniero Agrónomo | Como ingeniero agrónomo, quiero ver aparte los casos de tormenta de los últimos días, para responder a la emergencia con una ruta de mínima distancia. | La sección aparece solo si hay casos etiquetados; todos con igual urgencia; ruta de mínima distancia; badge indica cuántos hay. |
| HU-12 | Jefe / Coordinador | Como jefe de la Dirección Técnica, quiero usar un balanceador para fijar qué proporción de cada prioridad se dictamina por sesión, para orientar el trabajo según la estrategia del momento. | Ajusto porcentajes por prioridad con controles deslizables; si falta stock de una prioridad, el sistema redistribuye. |
| HU-13 | Jefe / Coordinador | Como jefe de la Dirección Técnica, quiero configurar rápido un perfil de distribución cuando hay una directiva superior, para responder a una política puntual (ej. no dejar ningún caso urgente pendiente). | Puedo asignar el 100% de la jornada a una sola prioridad en pocos pasos y guardar/reusar perfiles. |
| HU-14 | Procesamiento de Datos | Como área de Procesamiento de Datos, quiero que los reclamos que derivó (normales o con etiqueta de tormenta) aparezcan en el sistema, para que los ingenieros puedan trabajarlos. | Los derivados se listan como "sin dictaminar"; los de tormenta van a la sección de urgencia; el sistema no genera reclamos propios, solo consume los derivados. |

## Tabla de validación INVEST

Criterios: Independiente · Negociable · Valiosa · Estimable · Small (pequeña) · Testeable.

| HU | I | N | V | E | S | T | Observación |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HU-01 Login | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ | Sin observaciones. |
| HU-02 Dashboard | ✔ | ✔ | ✔ | ✔ | ⚠ | ✔ | Agrupa métricas + gráficos + alertas; podría dividirse en 2-3 HU más chicas. |
| HU-03 Listado priorizado | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ | Sin observaciones. |
| HU-04 Consultar N.° SUA/año en ruta | ✔ | ✔ | ⚠ | ✔ | ✔ | ✔ | Valor bajo aislada; podría fusionarse como criterio de aceptación de HU-09. |
| HU-05 Completar dictamen desde el celular | ✔ | ✔ | ✔ | ⚠ | ⚠ | ✔ | Historia "paraguas"; conviene dividir en sub-HU (datos del ejemplar, tipo de intervención, adjuntos). |
| HU-06 Bloqueo de intervenciones contradictorias | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ | Sin observaciones. |
| HU-07 Época recomendada según especie/estación | ✔ | ✔ | ⚠ | ✔ | ✔ | ✔ | Valor secundario (sugerencia, no bloqueante); es válida igual. |
| HU-08 Firma digital | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ | Sin observaciones. |
| HU-09 Ruta eficiente | ✔ | ✔ | ✔ | ⚠ | ⚠ | ✔ | Complejidad algorítmica alta; conviene spike técnico antes de estimar. |
| HU-10 Modo de traslado | ⚠ | ✔ | ✔ | ✔ | ✔ | ✔ | Depende de HU-09 (no se puede probar aislada). |
| HU-11 Casos de tormenta | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ | Sin observaciones. |
| HU-12 Balanceador de carga | ✔ | ✔ | ✔ | ⚠ | ⚠ | ✔ | Podría dividirse en "ajustar porcentajes" y "modos predefinidos". |
| HU-13 Perfiles / bajada de línea | ⚠ | ✔ | ✔ | ✔ | ✔ | ✔ | Depende de HU-12 (reutiliza el balanceador). |
| HU-14 Reclamos derivados visibles | ✔ | ✔ | ✔ | ✔ | ✔ | ⚠ | Criterio de aceptación algo genérico ("para que puedan trabajarlos"); conviene precisar qué campos deben llegar completos. |
