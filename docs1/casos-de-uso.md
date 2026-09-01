# Casos de uso

## Índice de casos de uso

| N.° | ID | Nombre | Actor primario |
| --- | --- | --- | --- |
| 1 | CU-01 | Iniciar sesión | Lector / Operario / Administrador |
| 2 | CU-02 | Consultar dashboard y métricas | Lector / Operario / Administrador |
| 3 | CU-03 | Consultar y filtrar reclamos sin dictaminar | Operario |
| 4 | CU-04 | Emitir dictamen técnico | Operario |
| 5 | CU-05 | Planificar ruta eficiente | Operario |
| 6 | CU-06 | Configurar balanceador de carga | Operario |
| 7 | CU-07 | Gestionar urgencia por tormenta | Operario |
| 8 | CU-08 | Gestionar usuarios y roles | Administrador |
| 9 | CU-09 | Configurar parámetros y conectores del sistema | Administrador |

El diagrama de casos de uso se encuentra en [`diagramas/casos-de-uso.puml`](../diagramas/casos-de-uso.puml).

---

### CU-01 — Iniciar sesión

| Campo | Detalle |
| --- | --- |
| Descripción | El usuario se autentica con sus credenciales institucionales para acceder al sistema según su rol. |
| Actores | Lector, Operario, Administrador |
| Precondición | El usuario debe estar dado de alta por un Administrador. |
| Postcondición | Se genera un token de sesión (JWT) válido y el usuario accede a las funciones habilitadas para su rol. |

**Flujo normal**
1. El usuario ingresa usuario y contraseña institucional.
2. El sistema valida las credenciales contra el adaptador de autenticación.
3. Genera un token de sesión y habilita las funciones según el rol.

**Flujo alternativo:** A1 — Credenciales incorrectas: se muestra un mensaje de error genérico sin indicar cuál campo falló.

**Excepciones:** caída del servicio de autenticación institucional · token expirado durante la sesión.

**Frecuencia de uso:** Muy alta. **Prioridad:** Alta.

---

### CU-02 — Consultar dashboard y métricas

| Campo | Detalle |
| --- | --- |
| Descripción | El usuario visualiza métricas de reclamos, gráficos de prioridad/distrito y alertas operativas. |
| Actores | Lector, Operario, Administrador |
| Precondición | Sesión iniciada. |
| Postcondición | El usuario visualiza el estado agregado del sistema. |

**Flujo normal**
1. El usuario accede al Home.
2. El sistema muestra total de reclamos, sin dictaminar, dictaminados, gráfico de prioridad, gráfico por distrito, dictámenes próximos a vencer y badge de tormenta.

**Flujo alternativo:** A1 — Si no hay casos de tormenta activos, la sección/badge correspondiente no se muestra.

**Excepciones:** falla en el cálculo de métricas por datos inconsistentes.

**Frecuencia de uso:** Muy alta. **Prioridad:** Alta.

---

### CU-03 — Consultar y filtrar reclamos sin dictaminar

| Campo | Detalle |
| --- | --- |
| Descripción | El Operario consulta el listado de reclamos derivados a la Dirección Técnica, filtrando y ordenando según criterios operativos. |
| Actores | Operario (primario) · SUA (secundario, provee los datos) |
| Precondición | Sesión iniciada con rol Operario. |
| Postcondición | El Operario visualiza el listado priorizado, listo para iniciar un dictamen. |

**Flujo normal**
1. El Operario accede al listado.
2. El sistema consulta al adaptador SUA los reclamos del subtipo sin dictaminar.
3. Los muestra ordenados por prioridad descendente.
4. El Operario aplica filtros (zona, prioridad, tipo de intervención, antigüedad).

**Flujo alternativo:** A1 — Sin resultados para el filtro aplicado: se muestra "sin resultados".

**Excepciones:** adaptador SUA no disponible · timeout de conexión con datos móviles.

**Frecuencia de uso:** Muy alta. **Prioridad:** Alta.

---

### CU-04 — Emitir dictamen técnico

| Campo | Detalle |
| --- | --- |
| Descripción | El Operario matriculado completa y firma digitalmente el dictamen técnico de un ejemplar. |
| Actores | Operario (primario) · SUA (secundario) |
| Precondición | El reclamo existe en SUA y está sin dictaminar. |
| Postcondición | El dictamen queda registrado, firmado, inmutable y el reclamo pasa a estado "dictaminado". |

**Flujo normal**
1. Ingresa N.° SUA y año.
2. El sistema valida existencia y estado.
3. El Operario carga datos del ejemplar, tipo de intervención, urgencia/complejidad, fotos y geolocalización.
4. Firma digitalmente ingresando su matrícula.
5. El sistema calcula vencimiento (18 meses), genera hash y sello de tiempo, deja el registro en solo lectura.
6. Actualiza el estado del reclamo en el adaptador SUA.

**Flujo alternativo:** A1 — El sistema sugiere época recomendada según especie/estación (RF-16).

**Excepciones:** N.° SUA/año inexistente o ya dictaminado · combinación de intervención inconsistente (RF-14) · usuario sin matrícula registrada intenta firmar.

**Frecuencia de uso:** Media-alta. **Prioridad:** Alta.

---

### CU-05 — Planificar ruta eficiente

| Campo | Detalle |
| --- | --- |
| Descripción | El Operario genera una ruta optimizada de visitas para su jornada de campo. |
| Actores | Operario |
| Precondición | Existen reclamos sin dictaminar disponibles en la zona seleccionada. |
| Postcondición | Se genera una ruta con orden de visita, cronograma estimado y mapa. |

**Flujo normal**
1. Selecciona zona, criterio (horas/cantidad de casos) y modo de traslado.
2. El sistema calcula la ruta más eficiente (10 min/dictamen configurable), partiendo y volviendo a Parques y Paseos.
3. Muestra mapa, cronograma, desglose de tiempos y porcentaje de eficiencia.

**Flujo alternativo:** A1 — Aplica el balanceador de carga vigente (CU-06) para seleccionar qué reclamos entran en la ruta.

**Excepciones:** sin reclamos suficientes en la zona · falla del servicio de mapas.

**Frecuencia de uso:** Alta. **Prioridad:** Alta.

---

### CU-06 — Configurar balanceador de carga

| Campo | Detalle |
| --- | --- |
| Descripción | El Operario ajusta la distribución porcentual de prioridades a incluir en una ruta, o aplica/guarda un perfil predefinido. |
| Actores | Operario |
| Precondición | Sesión iniciada con rol Operario. |
| Postcondición | La configuración queda aplicada a la próxima planificación de ruta (CU-05). |

**Flujo normal**
1. El Operario ajusta porcentajes por prioridad con controles deslizables, o selecciona un modo predefinido (urgentes primero / por porcentaje / automático equilibrado).
2. El sistema valida y guarda la configuración de la sesión.

**Flujo alternativo:** A1 — Guardar el ajuste actual como perfil reutilizable · A2 — Si una prioridad no tiene stock suficiente, el sistema redistribuye automáticamente con otras prioridades.

**Excepciones:** suma de porcentajes inconsistente.

**Frecuencia de uso:** Media. **Prioridad:** Alta.

---

### CU-07 — Gestionar urgencia por tormenta

| Campo | Detalle |
| --- | --- |
| Descripción | El Operario visualiza y genera una ruta de emergencia para los reclamos etiquetados como Protocolo de Tormenta. |
| Actores | Operario · SUA (secundario) |
| Precondición | Existen reclamos derivados con etiqueta de tormenta en los últimos 3 días. |
| Postcondición | Se genera una ruta de emergencia de mínima distancia entre los casos seleccionados. |

**Flujo normal**
1. El Operario accede a la sección (visible solo si hay casos).
2. Visualiza los casos, todos con igual prioridad.
3. Genera la ruta de emergencia de mínima distancia/tiempo.

**Flujo alternativo:** no aplica distribución por niveles ni balanceador (RF-29).

**Excepciones:** no hay casos de tormenta activos → la sección no se muestra.

**Frecuencia de uso:** Baja (eventual, ligada a eventos climáticos). **Prioridad:** Alta.

---

### CU-08 — Gestionar usuarios y roles

| Campo | Detalle |
| --- | --- |
| Descripción | El Administrador crea, edita o desactiva usuarios y les asigna rol y, si corresponde, matrícula. |
| Actores | Administrador |
| Precondición | Sesión iniciada con rol Administrador. |
| Postcondición | El usuario queda habilitado con el rol y permisos correspondientes. |

**Flujo normal**
1. El Administrador crea un usuario.
2. Asigna rol (Lector/Operario/Administrador).
3. Si es Operario, opcionalmente carga matrícula profesional.
4. El sistema guarda el perfil y habilita el acceso.

**Flujo alternativo:** A1 — Desactivar un usuario existente revoca su acceso inmediatamente.

**Excepciones:** matrícula con formato inválido · usuario duplicado.

**Frecuencia de uso:** Baja. **Prioridad:** Media.

---

### CU-09 — Configurar parámetros y conectores del sistema

| Campo | Detalle |
| --- | --- |
| Descripción | El Administrador configura parámetros de negocio y los adaptadores de conexión externa (SUA, autenticación municipal), y deja preparado el módulo de certificación de firma digital. |
| Actores | Administrador |
| Precondición | Sesión iniciada con rol Administrador. |
| Postcondición | Los parámetros/conectores quedan actualizados sin necesidad de modificar código. |

**Flujo normal**
1. El Administrador accede al panel de configuración.
2. Ajusta parámetros (tiempo por dictamen, umbrales de escalamiento, perfiles) y/o credenciales de los adaptadores externos.
3. El sistema guarda y aplica los cambios.

**Flujo alternativo:** A1 — Configurar el placeholder de certificación de firma digital (sin certificar realmente, por ser un proyecto académico).

**Excepciones:** credenciales de conector inválidas · parámetro fuera de rango permitido.

**Frecuencia de uso:** Baja. **Prioridad:** Media.
