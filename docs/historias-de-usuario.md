# Historias de usuario
_Presentar al menos una historia de usuario representativa por módulo._
_Cada historia debe incluir formato clásico, criterios de aceptación y validación INVEST._

---

## HU-01 — Inicio de sesión en el sistema

| Campo | Detalle |
|-------|---------|
| Historia | Como usuario del sistema, quiero iniciar sesión con mi usuario y contraseña, para acceder a las solicitudes y funciones habilitadas para mi rol. |
| Módulo | Autenticación y gestión de usuarios |
| Requisitos relacionados | RF-01, RF-02, RF-03, RF-04 |

### Criterios de aceptación

1. Dado que ingreso un usuario y contraseña válidos y registrados localmente, cuando envío el formulario de login, el sistema valida contra la Autenticación Institucional y me otorga acceso con un token JWT.
2. Dado que ingreso una contraseña incorrecta, cuando envío el formulario, el sistema rechaza el acceso y muestra un mensaje de error genérico, sin indicar si el usuario existe o no.
3. Dado que mi usuario no existe en la base local del sistema, cuando intento iniciar sesión, el sistema rechaza el acceso aunque mis credenciales sean válidas en la Autenticación Institucional.
4. Dado que inicié sesión correctamente, cuando pasan 30 minutos sin que interactúe con el sistema, mi sesión se cierra automáticamente.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Parcial | Es la base de la que dependen el resto de las historias (nadie puede dictaminar ni generar rutas sin loguearse antes). Es una dependencia estructural típica del módulo, no una dependencia de negocio arbitraria. |
| Negociable | Sí | El mecanismo interno de validación (JWT, API key) puede ajustarse sin cambiar el valor entregado al usuario. |
| Valiosa | Sí | Sin esto, ningún otro módulo del sistema es accesible. |
| Estimable | Sí | El equipo cuenta con la información necesaria para dimensionar el esfuerzo. |
| Pequeña | Sí | Se resuelve en un sprint corto. |
| Verificable | Sí | Cada criterio de aceptación es un caso de prueba concreto. |

---

## HU-02 — Gestión de usuarios y roles

| Campo | Detalle |
|-------|---------|
| Historia | Como Administrador, quiero crear, modificar y dar de baja usuarios del sistema y asignarles un rol, para controlar quién puede acceder y qué puede hacer cada persona. |
| Módulo | Autenticación y gestión de usuarios |
| Requisitos relacionados | RF-06, RF-07, RF-08 |

### Criterios de aceptación

1. Dado que soy Administrador, cuando creo un nuevo usuario con una contraseña que cumple los requisitos de complejidad (8 caracteres, mayúscula, minúscula, número y carácter especial), el sistema lo da de alta y le asigna el rol seleccionado (Administrador, Jefe, Operador o Lector).
2. Dado que intento crear un usuario con una contraseña que no cumple los requisitos de complejidad, el sistema rechaza la creación e indica qué requisito falta.
3. Dado que un usuario existente perdió su contraseña, cuando la restablezco desde mi panel de Administrador, el sistema genera una nueva contraseña y desactiva la anterior.
4. Dado que doy de baja un usuario, cuando esa persona intenta iniciar sesión, el sistema le niega el acceso.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | Puede desarrollarse y probarse sin depender de otras historias funcionales del sistema (más allá del login propio del Administrador). |
| Negociable | Sí | |
| Valiosa | Sí | Sin esto no hay forma de dar de alta a nadie más en el sistema. |
| Estimable | Sí | |
| Pequeña | Sí | |
| Verificable | Sí | |

---

## HU-03 — Consultar solicitudes derivadas a la Dirección Técnica

| Campo | Detalle |
|-------|---------|
| Historia | Como Operador, quiero ver el listado de solicitudes derivadas a la Dirección Técnica de Arbolado, para saber qué casos tengo disponibles para dictaminar. |
| Módulo | Lectura de solicitudes del SUA |
| Requisitos relacionados | RF-09, RF-10, RF-11, RF-13 |

### Criterios de aceptación

1. Dado que inicié sesión, cuando accedo al listado de solicitudes, el sistema muestra únicamente las de tipo reclamo, subtipo "problema con el arbolado público", derivadas a Parques y Paseos – Dirección Técnica.
2. Cada solicitud del listado muestra su identificador (Número de SUA - Año) y su estado actual (Pendiente, Dictaminada o Pendiente-revisión).
3. Dado que soy Operador o Jefe, puedo ver todas las solicitudes pendientes y pendientes-revisión, sin que estén restringidas a un ingeniero en particular.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Parcial | Depende de que exista un login previo (HU-01). Es una dependencia estructural, no de negocio. |
| Negociable | Sí | |
| Valiosa | Sí | Es el punto de partida del trabajo diario del ingeniero. |
| Estimable | Sí | |
| Pequeña | Sí | |
| Verificable | Sí | |

---

## HU-04 — Generar ruta de trabajo diaria

| Campo | Detalle |
|-------|---------|
| Historia | Como Operador, quiero generar una ruta de trabajo con los criterios que yo elija, para organizar mi jornada de forma eficiente según cómo prefiero trabajar. |
| Módulo | Generación de rutas de trabajo |
| Requisitos relacionados | RF-14, RF-15, RF-16, RF-19 |

### Criterios de aceptación

1. Dado que tengo conexión a internet, cuando presiono "Generar ruta" definiendo una combinación de distrito, prioridad y/o una zona en el mapa, el sistema arma una ruta con las solicitudes pendientes que cumplen esos criterios.
2. Dado que una solicitud ya forma parte de la ruta activa de otro ingeniero, esa solicitud no aparece disponible para incluirse en mi nueva ruta.
3. Dado que no tengo conexión a internet, cuando intento generar una ruta, el sistema no permite la operación e informa que se necesita conexión.
4. Dado que elijo no filtrar por distrito, el sistema igual arma la ruta considerando el resto de los criterios seleccionados.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Parcial | Depende de HU-03 (ver solicitudes) y de HU-01 (login). Dependencia esperable dentro del flujo del sistema. |
| Negociable | Sí | El algoritmo de optimización puede ajustarse sin cambiar el valor de la historia. |
| Valiosa | Sí | |
| Estimable | Parcial | El algoritmo de armado de ruta puede requerir un análisis técnico previo antes de poder estimarse con precisión. |
| Pequeña | No del todo | Combina varios criterios de filtrado con selección manual en mapa, lo que la hace más grande que el resto de las historias. Podría dividirse por criterio si el equipo lo prefiere. |
| Verificable | Sí | |

---

## HU-05 — Restablecer ruta de trabajo

| Campo | Detalle |
|-------|---------|
| Historia | Como Operador, quiero poder descartar mi ruta actual y generar una nueva, para adaptarme si cambian las prioridades durante el día. |
| Módulo | Generación de rutas de trabajo |
| Requisitos relacionados | RF-17, RF-18 |

### Criterios de aceptación

1. Dado que tengo una ruta activa con solicitudes sin dictaminar, cuando presiono "Restablecer", el sistema descarta la ruta y libera esas solicitudes para que puedan incluirse en otras rutas.
2. Dado que ya dictaminé algunas solicitudes de mi ruta, esas no vuelven a aparecer como disponibles al restablecer, porque ya no están pendientes.
3. Dado que no presiono "Restablecer" ni dictamino todos los casos, cuando llegan las 18:00 hs, el sistema libera automáticamente las solicitudes no dictaminadas de mi ruta.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | No | Depende directamente de HU-04 (no existe ruta para restablecer si no se generó antes). Se podría fusionar con HU-04 si el equipo prefiere una sola historia de "gestión de ruta". |
| Negociable | Sí | |
| Valiosa | Sí | Evita que un caso quede bloqueado indefinidamente para los demás ingenieros. |
| Estimable | Sí | |
| Pequeña | Sí | |
| Verificable | Sí | |

---

## HU-06 — Completar y firmar un dictamen técnico

| Campo | Detalle |
|-------|---------|
| Historia | Como Operador, quiero completar y firmar digitalmente el dictamen técnico de una solicitud, para dejar registrada la intervención que corresponde sobre el ejemplar. |
| Módulo | Dictaminación |
| Requisitos relacionados | RF-20, RF-21, RF-22, RF-23, RF-24, RF-29 |

### Criterios de aceptación

1. Dado que abro una solicitud en estado Pendiente o Pendiente-revisión, cuando completo el formulario del dictamen y lo envío, el sistema lo firma digitalmente con hash, timestamp y mi usuario, y lo guarda en la base de datos propia.
2. Dado que otro ingeniero envía un dictamen para el mismo caso antes que yo, cuando intento enviar el mío, el sistema rechaza mi envío indicando que el caso ya fue dictaminado.
3. Dado que el dictamen se firmó correctamente, el sistema envía al SUA el subconjunto de campos correspondiente a los "datos complementarios de la solicitud".
4. El formulario del dictamen no muestra nombre ni datos de contacto del vecino, solo la información técnica necesaria (ubicación, descripción del reclamo).

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Parcial | Depende de HU-03/HU-04 (tener el caso disponible) y de HU-01 (login). Dependencia esperable del flujo. |
| Negociable | Sí | |
| Valiosa | Sí | Es la funcionalidad central del sistema. |
| Estimable | Sí | |
| Pequeña | No del todo | Agrupa firma digital, persistencia local y envío al SUA en una sola historia. Podría dividirse en "completar y firmar" vs. "enviar al SUA" si el equipo prefiere historias más chicas. |
| Verificable | Sí | |

---

## HU-07 — Descargar el dictamen como documento imprimible

| Campo | Detalle |
|-------|---------|
| Historia | Como Operador, quiero descargar el dictamen firmado en formato PDF, para poder imprimirlo como documento legal cuando sea necesario. |
| Módulo | Dictaminación |
| Requisitos relacionados | RF-26 |

### Criterios de aceptación

1. Dado que un dictamen ya fue firmado, cuando presiono "Descargar", el sistema genera un archivo PDF con el contenido completo del dictamen.
2. El PDF incluye los datos de la firma digital (hash, timestamp, usuario).
3. La descarga está disponible tanto para dictámenes recién firmados como para cualquiera del historial.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | Puede desarrollarse una vez que existe un dictamen firmado, sin acoplarse al resto de la lógica de negocio. |
| Negociable | Sí | |
| Valiosa | Sí | Resuelve una necesidad operativa concreta: el documento legal impreso. |
| Estimable | Sí | |
| Pequeña | Sí | |
| Verificable | Sí | |

---

## HU-08 — Consultar el dictamen anterior de un caso re-derivado

| Campo | Detalle |
|-------|---------|
| Historia | Como Operador, quiero poder consultar el dictamen anterior de un caso en estado Pendiente-revisión, para tener contexto de lo que se evaluó la vez pasada antes de completar el nuevo. |
| Módulo | Dictaminación |
| Requisitos relacionados | RF-27, RF-28 |

### Criterios de aceptación

1. Dado que abro un caso en estado Pendiente-revisión, el formulario del nuevo dictamen se presenta en blanco, sin datos precargados del anterior.
2. Dado que quiero ver qué se dictaminó antes, cuando presiono el botón "Ver dictamen anterior", el sistema me muestra su contenido completo y quién lo firmó.
3. El sistema conserva todos los dictámenes históricos de un mismo caso, sin sobrescribirlos entre sí.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Parcial | Depende de que exista al menos un dictamen previo del caso (HU-06). Es una dependencia de datos, no de desarrollo. |
| Negociable | Sí | |
| Valiosa | Sí | Da contexto al ingeniero y evita desconocer el historial del ejemplar. |
| Estimable | Sí | |
| Pequeña | Sí | |
| Verificable | Sí | |

---

## HU-09 — Dictaminar sin conexión a internet

| Campo | Detalle |
|-------|---------|
| Historia | Como Operador, quiero poder completar y firmar un dictamen aunque no tenga señal en el lugar donde estoy, para no depender de la conectividad del momento para hacer mi trabajo. |
| Módulo | Trabajo sin conexión y sincronización |
| Requisitos relacionados | RF-30, RF-31, RF-32 |

### Criterios de aceptación

1. Dado que tengo la app instalada como PWA en mi captor y ya generé mi ruta con conexión, cuando pierdo la señal, puedo seguir abriendo los casos de mi ruta y completar el dictamen normalmente.
2. Dado que firmo un dictamen sin conexión, el sistema lo guarda localmente en el dispositivo con estado "pendiente de sincronizar".
3. Dado que intento generar una ruta nueva sin conexión, el sistema no lo permite, ya que esa acción requiere conexión.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Parcial | Depende de HU-04 (ruta generada con conexión previa) y de HU-06 (lógica de completar/firmar dictamen). |
| Negociable | Sí | |
| Valiosa | Sí | Es una necesidad real del trabajo de campo, no una mejora accesoria. |
| Estimable | Parcial | Depende de decisiones técnicas de almacenamiento local (Service Worker, IndexedDB) que pueden requerir un análisis previo. |
| Pequeña | Sí | Dentro de lo que permite el alcance offline definido. |
| Verificable | Sí | |

---

## HU-10 — Sincronización automática de dictámenes pendientes

| Campo | Detalle |
|-------|---------|
| Historia | Como Operador, quiero que los dictámenes que quedaron pendientes de enviar se sincronicen solos apenas recupero señal, para no tener que acordarme de reenviarlos manualmente. |
| Módulo | Trabajo sin conexión y sincronización |
| Requisitos relacionados | RF-25, RF-33, RF-34 |

### Criterios de aceptación

1. Dado que tengo dictámenes en estado "pendiente de sincronizar", cuando el dispositivo recupera conexión, el sistema los envía automáticamente al SUA sin intervención manual.
2. Dado que el envío al SUA falla (por ejemplo, el SUA está caído), el sistema mantiene el dictamen en estado "pendiente de sincronizar" y reintenta más adelante.
3. En todo momento puedo ver un indicador que muestra si está todo sincronizado, cuántos dictámenes tengo pendientes, o si no tengo conexión.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Parcial | Depende de HU-09 (que existan dictámenes offline para sincronizar). Son dos caras del mismo flujo, separadas para que cada una sea más chica y verificable por separado. |
| Negociable | Sí | |
| Valiosa | Sí | Sin esto, el trabajo offline no se traduce nunca en datos reales en el SUA. |
| Estimable | Sí | |
| Pequeña | Sí | |
| Verificable | Sí | |

---

## HU-11 — Consultar métricas de dictaminación en el dashboard

| Campo | Detalle |
|-------|---------|
| Historia | Como Jefe, quiero ver un dashboard con la cantidad de solicitudes derivadas, dictaminadas y sin dictaminar, para hacer seguimiento del trabajo del equipo por período. |
| Módulo | Dashboard |
| Requisitos relacionados | RF-35, RF-36, RF-37 |

### Criterios de aceptación

1. Dado que accedo al dashboard, el sistema muestra la cantidad de solicitudes derivadas, dictaminadas y sin dictaminar.
2. Puedo filtrar esas métricas por mes y por año.
3. Dado que un caso fue re-derivado por vencimiento, ese evento se cuenta de forma independiente en las métricas del período en que ocurrió, aunque corresponda a un caso ya contado antes.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Parcial | Depende de que existan datos generados por los módulos de solicitudes y dictaminación. Es una dependencia de datos, no de desarrollo del propio módulo. |
| Negociable | Sí | |
| Valiosa | Sí | Es la herramienta de seguimiento para la Dirección. |
| Estimable | Sí | |
| Pequeña | Sí | |
| Verificable | Sí | |