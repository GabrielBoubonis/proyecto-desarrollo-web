# Diseño UI

_Presentar al menos un wireframe por pantalla o módulo relevante._
_Los wireframes en imagen o PDF van en `diagramas/wireframes/`; acá se documenta la
justificación de cada uno._

> **Nota general:** el sistema es una PWA instalable, pensada en primer lugar para el
> celular del ingeniero en el campo (captor). Todas las pantallas están diseñadas
> mobile-first, pero el diseño es responsive: en pantallas más anchas (desktop), las
> listas de tarjetas pasan a grilla de 2-3 columnas (patrón **Grid Layout**), la barra de
> navegación inferior se convierte en un menú lateral fijo, y los formularios paso a paso
> pueden mostrarse con más contexto visible a la vez sin perder la lógica de pasos. Esto es
> particularmente relevante para el Dashboard y la Gestión de usuarios, que probablemente
> se consulten también desde una computadora de oficina.

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
Navigation), Carga diferida (Lazy Loading).

**Justificación:** Se usa Card Layout en lugar de una tabla clásica porque en una pantalla
angosta una tabla con varias columnas (ID, estado, dirección, prioridad) obliga a scroll
horizontal, que es incómodo con una sola mano en el campo; la tarjeta permite apilar esa
misma información verticalmente y sigue siendo escaneable de un vistazo. Las pestañas
(Todas / Pendientes / En revisión) resuelven el filtro más usado sin abrir un menú
adicional, algo importante cuando el ingeniero necesita encontrar rápido su próximo caso.
Carga diferida evita traer de una vez el historial completo de solicitudes derivadas,
relevante porque el celular puede estar con datos móviles limitados (Alcance, restricción
de conectividad).

**Formulario (si aplica):** No aplica (es un listado, no un formulario).

---

## Pantalla / Módulo 3 — Generar ruta de trabajo

**Wireframe:** `diagramas/wireframes/03-generar-ruta.svg`

**Patrones de diseño utilizados:** Formulario en pasos (Step-by-Step Form), Modal.

**Justificación:** Armar una ruta combina varios criterios independientes (distrito,
prioridad, zona en el mapa). Mostrarlos todos juntos en una pantalla chica satura la
vista; separarlos en pasos reduce la carga cognitiva y evita que el ingeniero, parado en
la calle, tenga que hacer scroll largo para llegar al botón de confirmar. El Modal de
confirmación es intencional: generar una ruta reserva esas solicitudes y las saca de la
disponibilidad de otros ingenieros (RF-16), así que antes de ese efecto colateral el
sistema pide una confirmación explícita, en lugar de ejecutarlo directo al tocar
"Continuar".

**Formulario (si aplica):**
- Cantidad de campos: 3 criterios combinables (distrito, prioridad, zona en mapa), todos
  opcionales salvo que al menos uno esté definido.
- Flujo: por pasos (criterios → confirmación).
- Validaciones relevantes: si no hay solicitudes que cumplan los criterios elegidos, el
  sistema lo indica antes de dejar avanzar al paso de confirmación (ver CU-04, excepción
  E2).

---

## Pantalla / Módulo 4 — Formulario de dictamen técnico

**Wireframe:** `diagramas/wireframes/04-formulario-dictamen.svg`

**Patrones de diseño utilizados:** Formulario en pasos (Step-by-Step Form), indicador de
estado offline.

**Justificación:** Es el formulario más largo del sistema y el que tiene consecuencias
legales (firma digital), por lo que aplica directamente el punto 9 del material de
formularios: dividir en pasos con indicador de progreso. Separar "Ejemplar",
"Intervención" y "Firma" evita que el ingeniero pierda datos ya cargados si se distrae o
lo interrumpen en medio de una visita. El indicador de "SIN SEÑAL" en el encabezado no es
decorativo: comunica en todo momento si el dictamen que está por firmar se va a guardar
localmente en estado pendiente de sincronizar (RF-31, RF-32), algo que el ingeniero
necesita saber mientras trabaja, no recién al intentar enviar.

**Formulario (si aplica):**
- Cantidad de campos: variable según el paso (mostrados: especie, estado del ejemplar,
  ubicación, fotos — el paso de intervención y firma no se detallan en el wireframe, pero
  siguen la misma lógica).
- Flujo: por pasos (3: Ejemplar → Intervención → Firma).
- Validaciones relevantes: campos obligatorios marcados con asterisco y feedback inmediato
  si falta completar alguno (RF-20); verificación de que no exista ya un dictamen activo
  para el mismo ejemplar al momento de confirmar el envío (RF-21).

---

## Pantalla / Módulo 5 — Dashboard

**Wireframe:** `diagramas/wireframes/05-dashboard.svg`

**Patrones de diseño utilizados:** Tarjetas (Card Layout) para KPIs, Navegación en
pestañas (Tab Navigation), Grilla (Grid Layout) en la versión desktop.

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
de rutas normales (solo pide una cantidad, sin pasos), porque en un evento climático el
objetivo es que el ingeniero pueda actuar en el menor tiempo posible, sin la fricción de un
formulario multietapa que no tiene sentido cuando el único criterio es "las más antiguas
primero" (RF-41).

**Formulario (si aplica):**
- Cantidad de campos: 1 (cantidad de solicitudes a incluir en la ruta).
- Flujo: todo en una pantalla (un modal simple, sin pasos).
- Validaciones relevantes: si la cantidad pedida supera las solicitudes de tormenta
  disponibles, el sistema arma la ruta con todas las disponibles e informa la diferencia
  (ver CU-12, excepción E2).

---

## Pantalla / Módulo 7 — Gestión de usuarios (Administrador)

**Wireframe:** `diagramas/wireframes/07-gestion-usuarios.svg`

**Patrones de diseño utilizados:** Tarjetas/listado, Modal para alta de usuario.

**Justificación:** El listado con indicador de estado (activo/inactivo) le permite al
Administrador del CIL ver de un vistazo quién tiene acceso vigente, sin tener que abrir
cada usuario. El alta se resuelve en un Modal en lugar de una pantalla completa nueva
porque es una acción breve y acotada (tres campos), y mantenerla como superposición evita
que el Administrador pierda el listado de contexto mientras la completa. El campo de
usuario se muestra como de solo lectura con el formato ya definido (inicial + apellido +
número incremental), para que quede claro que no lo tipea manualmente, evitando el error
de usabilidad de pedir un dato que el propio sistema ya sabe generar.

**Formulario (si aplica):**
- Cantidad de campos: 3 (usuario autogenerado — solo lectura, contraseña, rol).
- Flujo: todo en una pantalla (modal).
- Validaciones relevantes: la contraseña se valida en tiempo real contra las reglas de
  complejidad (RNF-05) antes de habilitar el botón "Crear usuario", con el texto de ayuda
  siempre visible debajo del campo (no solo como mensaje de error posterior).

---

## Consideraciones de accesibilidad

- **Contraste para uso a la intemperie:** los ingenieros trabajan en la calle, a menudo
  bajo sol directo, donde el brillo ambiente reduce la legibilidad de la pantalla. Por eso
  los estados críticos (Pendiente, Dictaminada, Tormenta, Sin señal) se distinguen no solo
  por color sino también por texto y forma (badges con etiqueta escrita, no solo un punto
  de color), para que sigan siendo legibles aunque el contraste percibido baje por el
  reflejo del sol.
- **Tamaño de tap targets para uso con guantes o con una sola mano:** los botones
  principales (Ingresar, Continuar, Siguiente, Confirmar) ocupan todo el ancho disponible y
  tienen una altura mínima de 44-48 px, pensados para tocarse con precisión mientras el
  ingeniero sostiene una libreta, una vara de medición o está parado en una posición
  incómoda junto al árbol — no para un uso de escritorio con mouse.
- **Indicador de conectividad siempre visible, no solo textual:** dado que buena parte del
  trabajo ocurre sin señal, el estado de sincronización no se comunica únicamente con
  texto (que puede pasarse por alto en una pantalla chica), sino con un badge de color fijo
  en el encabezado, visible en todo momento sin necesidad de scrollear.