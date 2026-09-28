# Diseño UI

_Presentar al menos un wireframe por pantalla o módulo relevante._
_Los wireframes en imagen o PDF van en `diagramas/wireframes/`; acá se documenta la
justificación de cada uno._

> **Nota general:** el sistema es una PWA instalable, pensada en primer lugar para el
> celular del ingeniero en el campo (captor). Todas las pantallas están diseñadas
> mobile-first, pero el diseño es responsive: en pantallas más anchas (desktop), las
> listas de tarjetas pasan a **Tabla con paginación** (el mismo patrón que las tarjetas,
> pero en su variante de escritorio — mismo contenido, distinta disposición), la barra de
> navegación inferior se convierte en un menú lateral fijo, y las secciones de un
> formulario pueden mostrarse con más contexto visible a la vez. Esto es particularmente
> relevante para el Dashboard y la Gestión de usuarios, que probablemente se consulten
> también desde una computadora de oficina.
>
> **Sobre el logo y el nombre de marca:** en el wireframe de Login aparecen como
> placeholder ("LOGO", sin nombre comercial). Es intencional: el proyecto todavía no tiene
> una identidad visual definida más allá de la descripción funcional
> ("Sistema de Dictaminación Técnica de Arbolado Público"), y la rúbrica de esta etapa no
> exige una marca — evalúa patrones de interacción y accesibilidad, no identidad visual.
> No le dedicamos tiempo a definirla porque no aporta a lo que se evalúa acá.

---

## Pantalla / Módulo 1 — Login

**Wireframe:** `diagramas/wireframes/01-login.svg`

**Patrones de diseño utilizados:** Formulario simple centrado (Card).

**Justificación:** No hace falta un patrón complejo acá: es un formulario de dos campos,
y la prioridad es que sea legible en la calle, a veces con poca visibilidad por el sol.
Por eso el botón principal es grande y de alto contraste, y el campo de contraseña incluye
un ícono para mostrar/ocultar el valor — útil cuando el ingeniero escribe con guantes o con
una sola mano mientras sostiene el celular. El mensaje de error es genérico ("usuario o
contraseña incorrectos"), sin indicar cuál de los dos falló, siguiendo la misma lógica de
seguridad que ya definimos en Requisitos (RF-01 a RF-03).

**Formulario (si aplica):**
- Cantidad de campos: 2 (usuario, contraseña).
- Flujo: todo en una pantalla.
- Validaciones relevantes: campos obligatorios antes de habilitar el botón; feedback de
  error después del intento fallido (RF-03), sin distinguir si el usuario no existe o la
  contraseña es incorrecta.

---

## Pantalla / Módulo 2 — Listado de solicitudes

**Wireframe:** `diagramas/wireframes/02-listado-solicitudes.svg`

**Patrones de diseño utilizados:** Tarjetas (Card Layout), Navegación en pestañas (Tab
Navigation), Paginación explícita.

**Justificación:** Se usa Card Layout en lugar de una tabla clásica porque en una pantalla
angosta una tabla con varias columnas (ID, estado, dirección, prioridad) obliga a scroll
horizontal, que es incómodo con una sola mano en el campo; la tarjeta permite apilar esa
misma información verticalmente y sigue siendo escaneable de un vistazo. Es la misma
información que en un panel de escritorio se vería como "Tabla con paginación" — acá se
adapta a tarjetas por el contexto de uso (mobile, campo), no porque el patrón de listado
largo sea otro.

Las pestañas (Todas / Pendientes / En revisión) resuelven el filtro más usado sin abrir un
menú adicional. Se suma un **filtro por año**, porque un mismo distrito puede acumular
solicitudes de años distintos (algunas re-derivadas por vencimiento), y buscar "las de
2026" es una necesidad real y frecuente.

Se reemplazó el scroll infinito que había en una versión anterior por **paginación
explícita de 30 solicitudes por página**, con indicador "Mostrando 1–30 de 84" y control de
página. El motivo del cambio: en el campo, con datos móviles limitados, cargar de a 30
resultados concretos y saber cuántas páginas quedan es más predecible que un scroll que no
comunica cuánto falta ni cuánto se consumió de conexión — algo que sí importa cuando el
paquete de datos del captor no es ilimitado.

**Sobre el ícono de sincronización** (badge verde en el encabezado): indica si los
dictámenes generados en el dispositivo ya se enviaron al SUA o si hay alguno pendiente de
sincronizar (RF-33, RF-34). No es un elemento decorativo: es la única señal visual
persistente de que el trabajo hecho sin conexión todavía no llegó al sistema central, así
que tiene que ser visible sin necesidad de entrar a ninguna otra pantalla.

**Formulario (si aplica):** No aplica (es un listado, no un formulario).

---

## Pantalla / Módulo 3 — Generar ruta de trabajo

**Wireframe:** `diagramas/wireframes/03-generar-ruta.svg`

**Patrones de diseño utilizados:** Formulario agrupado en secciones (no step-by-step),
Modal.

**Justificación:** A diferencia del dictamen (pantalla 4), este formulario **no es largo**:
son solo 3 criterios independientes (distrito, prioridad, zona en el mapa), todos
opcionales. Dividirlo en pasos secuenciales sería una fricción innecesaria para algo que el
ingeniero completa en segundos y puede querer ajustar de un lado a otro sin "avanzar" y
"retroceder" entre pantallas. Por eso se agrupan las tres secciones en una sola pantalla,
con un resumen en vivo ("8 solicitudes cumplen estos criterios") que se actualiza a medida
que se ajustan los filtros.

El Modal de confirmación sí se mantiene, porque cumple la función exacta que define el
patrón: una acción puntual (confirmar la generación) que no amerita una pantalla nueva,
pero que necesita una confirmación explícita porque tiene un efecto colateral importante —
reserva esas solicitudes y las saca de la disponibilidad de otros ingenieros (RF-16).

**Formulario (si aplica):**
- Cantidad de campos: 3 criterios combinables (distrito, prioridad, zona en mapa), todos
  opcionales salvo que al menos uno esté definido.
- Flujo: todos los criterios juntos en una sola pantalla (no por pasos), con confirmación
  final en un Modal.
- Validaciones relevantes: si no hay solicitudes que cumplan los criterios elegidos, el
  sistema lo indica en el resumen antes de habilitar "Generar ruta" (ver CU-04, excepción
  E2).

---

## Pantalla / Módulo 4 — Formulario de dictamen técnico

**Wireframe:** `diagramas/wireframes/04-formulario-dictamen.svg`

**Patrones de diseño utilizados:** Formulario en pasos (Step-by-Step Form), indicador de
estado offline, campos deshabilitados condicionalmente.

**Justificación:** A diferencia de "Generar ruta", este sí es un formulario largo: replica
los ~25 campos del formulario físico de "Dictamen Técnico de Arbolado" (ver
`dictamen-tecnico-campos.md`), agrupados en 5 pasos con indicador de progreso. Dividirlo así
evita que el ingeniero pierda datos ya cargados si se distrae o lo interrumpen en medio de
una visita, y reduce la sensación de formulario interminable en una pantalla chica. El
wireframe muestra en detalle el **Paso 3 (Intervención)**, porque es el que ilustra mejor
una de las reglas de validación más importantes del módulo (ver más abajo); los otros 4
pasos no se dibujan campo por campo porque siguen exactamente la misma lógica visual de
formulario simple con campos obligatorios marcados, pero se detallan a continuación para que
quede claro que el formulario completo no es corto:

1. **Datos del ejemplar y ubicación** — Distrito (precargado desde la Solicitud, solo
   lectura), Nota N.°, Exp N.°, Fecha, SUA N.° (solo lectura), Domicilio de solicitud (solo
   lectura), Domicilio del ejemplar\* (editable, puede diferir del anterior), Calle
   esquina / Número esquina, Referencia de ubicación, Especie\*, Distancia a
   medianera/referencia, Cantidad de ejemplares al frente.
2. **Diagnóstico** — Bloque 1: nivel de daño en vereda (Alto/Medio/Bajo); Bloque 2:
   casillero "Extracción" + perímetro del tronco + motivo (12 opciones, aparecen solo si se
   marca Extracción); Bloque 5: casillero "Sin trabajo" + motivo (6 opciones).
3. **Intervención** (pantalla dibujada en el wireframe) — Bloque 3: trabajos subterráneos
   (3 opciones, selección múltiple, una con campo de distancia); Bloque 4: trabajos aéreos
   (12 opciones, selección múltiple). **Si en el Paso 2 se marcó "Extracción", ambos
   bloques aparecen deshabilitados** (ver justificación de la regla debajo).
4. **Información adicional y fotografías** — Complejidad de la intervención\*
   (Baja/Media/Alta/Máxima), casilleros Urgente / Árbol frente a garage / Media tensión /
   De oficio, Plantar (Cazuela / Construir cazuela / Vereda jardín / ninguna), y el
   selector de **fotografías del ejemplar\* (mínimo 1, obligatorio)**.
5. **Observaciones y firma** — **Nivel de prioridad de la intervención\*** (Alta/Media/Baja:
   la urgencia con la que hay que *ejecutar* lo dictaminado, no confundir con la prioridad
   de triage de la Solicitud que ya se usó para armar la ruta — ver Decisión 6 en
   `er_modelo.md`), Observaciones (texto libre), resumen de lo cargado en los 4 pasos
   anteriores, y el botón que dispara la firma digital (hash + timestamp + usuario). El
   nivel de prioridad se completa deliberadamente acá y no junto a "Complejidad" del Paso 4:
   es la última decisión técnica que el ingeniero toma, ya con el diagnóstico completo,
   justo antes de firmar — un paso propio en vez de un campo más entre los demás.

El indicador de "SIN SEÑAL" en el encabezado no es decorativo: comunica en todo momento si
el dictamen que está por firmar se va a guardar localmente en estado pendiente de
sincronizar (RF-31, RF-32), algo que el ingeniero necesita saber mientras trabaja, no recién
al intentar enviar.

**Sobre el deshabilitado de trabajos aéreos/subterráneos al marcar Extracción:** no tiene
sentido de negocio dictaminar que se va a extraer un ejemplar y, en el mismo dictamen, cargar
una poda o una intervención de raíces sobre ese mismo ejemplar (RF-46). En vez de dejar
que el ingeniero complete esos campos y recién avisarle con un error al confirmar el envío,
el formulario los deshabilita apenas se marca "Extracción" en el Paso 2, con un aviso
explícito en el Paso 3 (franja roja "Extracción marcada en el Paso 2"). Esto evita carga de
datos que de todas formas se van a rechazar, y hace visible la regla en el momento en que el
ingeniero está tomando la decisión, no varios pasos después.

**Formulario (si aplica):**
- Cantidad de campos: ~25 distribuidos en 5 pasos (detallados arriba).
- Flujo: por pasos (5: Datos → Diagnóstico → Intervención → Adicional → Firma).
- Validaciones relevantes: campos obligatorios marcados con asterisco y feedback inmediato
  si falta completar alguno (RF-20); verificación de que no exista ya un dictamen activo
  para el mismo ejemplar al momento de confirmar el envío (RF-21); bloqueo de trabajos
  aéreos/subterráneos si se marcó Extracción (RF-46); al menos una fotografía cargada antes
  de poder firmar (RF-45); nivel de prioridad de la intervención obligatorio antes de
  habilitar el botón de firma (Paso 5).

---

## Pantalla / Módulo 5 — Dashboard

**Wireframe:** `diagramas/wireframes/05-dashboard.svg`

**Patrones de diseño utilizados:** Tarjetas (Card Layout) para KPIs, Navegación en
pestañas (Tab Navigation).

**Justificación:** Las tarjetas de KPI (derivadas / dictaminadas / sin dictaminar) buscan
que el Jefe o el Director puedan leer el estado general sin interpretar una tabla —
resuelve directamente el objetivo de "ver de un vistazo" que motivó el pedido original del
dashboard. Las pestañas General/Tormenta separan las dos vistas que definimos en Alcance
(2.6 y 2.7) sin duplicar la pantalla: la información de tormenta está incluida en las
métricas generales y además tiene su propio desglose (RF-44), y la pestaña es la forma más
directa de alternar entre ambas lecturas sin perder el filtro de mes/año ya seleccionado.

**Formulario (si aplica):** No aplica (el único control de entrada es el selector de
período, no un formulario de carga).

---

## Pantalla / Módulo 6 — Protocolo por tormenta

**Wireframe:** `diagramas/wireframes/06-protocolo-tormenta.svg`

**Patrones de diseño utilizados:** Tarjetas (Card Layout), Modal, indicador visual de
alerta.

**Justificación:** Reutiliza el mismo patrón de tarjetas del listado general (consistencia
funcional: la misma información se lee siempre de la misma forma), pero le suma una franja
de alerta fija y un color de acento distinto (rojo institucional de emergencia) para que
sea inconfundible que se trata de una sección de prioridad máxima, tal como pedimos en
Alcance 2.7. El Modal para generar la ruta de tormenta es deliberadamente más simple que el
de rutas normales (un solo campo: cantidad), porque en un evento climático el objetivo es
que el ingeniero pueda actuar en el menor tiempo posible, sin la fricción de un formulario
con más de un criterio cuando la única regla es "las más antiguas primero" (RF-41).

**Formulario (si aplica):**
- Cantidad de campos: 1 (cantidad de solicitudes a incluir en la ruta).
- Flujo: todo en una pantalla (un modal simple, un solo campo).
- Validaciones relevantes: si la cantidad pedida supera las solicitudes de tormenta
  disponibles, el sistema arma la ruta con todas las disponibles e informa la diferencia
  (ver CU-12, excepción E2).

---

## Pantalla / Módulo 7 — Gestión de usuarios (Administrador)

**Wireframe:** `diagramas/wireframes/07-gestion-usuarios.svg`

**Patrones de diseño utilizados:** Tarjetas/listado (equivalente mobile de Tabla con
paginación), Modal para alta de usuario.

**Justificación:** Este módulo es, en esencia, el mismo caso que un panel de
Usuarios/Roles administrativo clásico — el candidato natural para el patrón **Tabla con
paginación**. En mobile se resuelve con tarjetas por el mismo motivo que en el listado de
solicitudes (pantalla 2): evitar columnas apretadas y scroll horizontal en una pantalla
angosta. En la versión de escritorio, donde el Administrador del CIL probablemente gestione
usuarios con más comodidad, esta misma pantalla se vería como una tabla con columnas
(usuario, rol, estado) y paginación clásica — es el mismo patrón, dos disposiciones según
el dispositivo. El alta se resuelve en un Modal en lugar de una pantalla completa nueva
porque es una acción breve y acotada (tres campos), y mantenerla como superposición evita
que el Administrador pierda el listado de contexto mientras la completa. El campo de
usuario se muestra como de solo lectura con el formato ya definido (inicial + apellido +
número incremental), para que quede claro que no lo tipea manualmente.

**Formulario (si aplica):**
- Cantidad de campos: 3 (usuario autogenerado — solo lectura, contraseña, rol).
- Flujo: todo en una pantalla (modal).
- Validaciones relevantes: la contraseña se valida en tiempo real contra las reglas de
  complejidad (RNF-05) antes de habilitar el botón "Crear usuario", con el texto de ayuda
  siempre visible debajo del campo (no solo como mensaje de error posterior).

---

## Pantalla / Módulo 8 — Perfil

**Wireframe:** `diagramas/wireframes/08-perfil.svg`

**Patrones de diseño utilizados:** Lista simple de opciones.

**Justificación:** A diferencia de las 7 pantallas anteriores, esta no surge de ningún RF
puntual — es infraestructura típica de cualquier sistema con login (cerrar sesión, ver
quién está usando el dispositivo). Se agrega igual porque aparece referenciada en la
navegación de las demás pantallas y, sin ella, "cerrar sesión" no tendría un lugar propio.
Se diseñó de forma simple y genérica: no está basada en ninguna referencia visual concreta,
porque la imagen que se iba a usar como modelo para esta pantalla no llegó a compartirse en
la conversación. Si en algún momento se define una referencia puntual, esta ficha es la
que hay que actualizar. Muestra además el estado de sincronización con más detalle que el
ícono del encabezado (cantidad exacta de dictámenes pendientes de enviar), útil como lugar
de consulta cuando el ingeniero quiere confirmar que no dejó nada sin sincronizar antes de
terminar la jornada.

**Formulario (si aplica):** No aplica (no hay campos de carga, solo navegación y una
acción de cierre de sesión).

---

## Consideraciones de accesibilidad

- **Contraste para uso a la intemperie:** los ingenieros trabajan en la calle, a menudo
  bajo sol directo, donde el brillo ambiente reduce la legibilidad de la pantalla. Por eso
  los estados críticos (Pendiente, Dictaminada, Tormenta, Sin señal) se distinguen no solo
  por color sino también por texto y forma (badges con etiqueta escrita, no solo un punto
  de color), para que sigan siendo legibles aunque el contraste percibido baje por el
  reflejo del sol.
- **Tamaño de tap targets para uso con guantes o con una sola mano:** los botones
  principales (Ingresar, Generar ruta, Siguiente, Confirmar) ocupan todo el ancho
  disponible y tienen una altura mínima de 44-48 px, pensados para tocarse con precisión
  mientras el ingeniero sostiene una libreta, una vara de medición o está parado en una
  posición incómoda junto al árbol — no para un uso de escritorio con mouse.
- **Indicador de conectividad siempre visible, no solo textual:** dado que buena parte del
  trabajo ocurre sin señal, el estado de sincronización no se comunica únicamente con
  texto (que puede pasarse por alto en una pantalla chica), sino con un badge de color fijo
  en el encabezado, visible en todo momento sin necesidad de scrollear, y ampliado en la
  pantalla de Perfil para quien quiera el detalle exacto.