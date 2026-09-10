# Alcance del Proyecto

> **Nota:** este documento describe el alcance del **sistema real** que se propondría a la
> Dirección General de Parques y Paseos. La versión reducida que se presenta como demo
> para la materia Práctica Profesionalizante II se documenta por separado, en un apartado
> propio, para no mezclar "qué se propone" con "qué se muestra en la defensa".

---

## 1. Propósito del sistema

El sistema resuelve la etapa de **dictaminación técnica** de reclamos de arbolado público
una vez que estos ya fueron cargados y derivados a la Dirección Técnica de Arbolado dentro
del SUA (Sistema Único de Atención). Permite a los ingenieros visualizar los casos
asignados, organizarlos en una ruta de trabajo diaria, completar el dictamen técnico
correspondiente y sincronizar el resultado con el SUA. Además genera un dashboard de
seguimiento para la Dirección.

---

## 2. Alcance funcional

### 2.1 Autenticación y usuarios

- El login es en dos pasos:
  1. El usuario ingresa **usuario y contraseña propios del sistema**, validados contra la
     base de datos local.
  2. Si son válidos, el sistema llama a la API de Autenticación Institucional enviando el
     **usuario y una API key propia del sistema** (sin la contraseña). Si la Autenticación
     Institucional confirma que el usuario existe y está activo, devuelve un **token JWT**.
- El token se guarda para mantener la sesión activa. La sesión se cierra automáticamente
  luego de **30 minutos de inactividad**, mediante un mecanismo de refresh token.
- Al cerrarse la sesión por inactividad, la ruta de trabajo armada ese día **no se pierde**:
  queda persistida en la base de datos propia del sistema, no depende de la sesión.
- Los usuarios y sus roles (**Administrador, Jefe, Operador, Lector**) son propios del
  sistema y los gestiona el **CIL** desde un apartado de gestión de usuarios. Un usuario
  puede existir en la Autenticación Institucional y aun así no tener acceso a este sistema
  si el CIL no le creó una cuenta local. El detalle de permisos por rol se define en el
  documento de Requisitos.

### 2.2 Lectura de solicitudes del SUA

- El sistema consulta al SUA y obtiene las solicitudes de tipo **reclamo**, subtipo
  **problema con el arbolado público**, derivadas al área **Parques y Paseos – Dirección
  Técnica**. El filtro se aplica como parámetro de la consulta a la API que expone el SUA.
- Cada solicitud se identifica de forma única con el **Número de SUA - Año**, formato que
  se mantiene sin cambios durante toda la vida del caso (incluso ante una re-derivación
  años después).
- Un caso puede estar en uno de tres estados dentro del sistema:
  - **Pendiente**: derivado a la Dirección Técnica, sin dictaminar todavía.
  - **Dictaminada**: tiene un dictamen técnico vigente.
  - **Pendiente-revisión**: un caso que ya tuvo un dictamen, pero venció (ver 2.4) y fue
    re-derivado por Procesamiento de Datos con el mismo Número de SUA-Año.
- Cualquier ingeniero (rol Operador o Jefe) puede resolver cualquier solicitud asignada a
  la Dirección Técnica en cualquier momento, incluso si esa solicitud forma parte de la
  ruta de otro ingeniero (por ejemplo, ante un pedido urgente de un cargo superior). Esta
  posibilidad convive con la regla de exclusión entre rutas descrita en 2.5, que aplica
  solo al momento de *generar* una ruta nueva, no a quién puede dictaminar.

### 2.3 Dictaminación

- El ingeniero completa el dictamen técnico del ejemplar (especie, estado, intervención
  recomendada, etc.) para un caso pendiente o pendiente-revisión.
- **Solo puede existir un dictamen activo por ejemplar en un momento dado.** Si dos
  ingenieros intentan dictaminar el mismo caso, se acepta el primero que confirma el
  envío; el segundo intento es rechazado por el sistema.
- El dictamen completo, firmado digitalmente (hash + timestamp + usuario), se guarda en la
  base de datos propia del sistema, ya que el SUA no tiene una entidad "dictamen" — solo
  campos de "datos complementarios de la solicitud".
- Una vez firmado, el sistema envía al SUA, vía la API que este expone, únicamente el
  subconjunto de campos que corresponde a esos datos complementarios. Si el envío falla, el
  dictamen queda guardado y firmado localmente con estado **pendiente de sincronizar**, y el
  sistema reintenta el envío automáticamente hasta lograrlo.
- El sistema permite descargar el dictamen como PDF (u otro formato imprimible), para su
  impresión como documento legal cuando sea necesario.
- Cada caso conserva un **historial de dictámenes**: si un caso vuelve a estar pendiente
  (por vencimiento), el dictamen anterior no se borra ni se sobreescribe, sino que queda
  disponible como consulta. Al dictaminar un caso en estado pendiente-revisión, el
  ingeniero arranca el formulario en blanco, pero cuenta con un botón para **ver el
  dictamen anterior** (contenido y autor).

### 2.4 Vigencia y re-derivación de casos

- Un dictamen tiene una **vigencia de 36 meses** desde su emisión. Si en ese plazo no se
  ejecuta la intervención especificada, el caso debe volver a dictaminarse.
- El vencimiento lo detecta y gestiona **Procesamiento de Datos** (fuera de este sistema),
  que vuelve a derivar el caso a la Dirección Técnica con el **mismo Número de SUA-Año**.
- **Supuesto a validar con el SUA:** se asume que esta re-derivación llegará identificada
  con algún indicador (por ejemplo, un estado de "revisión") que el sistema pueda leer para
  distinguirla de una solicitud nueva. Este campo todavía no está confirmado con el equipo
  que administra el SUA.
- Cada evento de re-derivación queda registrado por separado en un **historial de
  derivaciones** del caso (distinto del historial de dictámenes), para que el dashboard
  pueda contar cada evento de forma independiente aunque se trate del mismo caso.

### 2.5 Generación de ruta de trabajo

- Los roles **Operador** y **Jefe** pueden generar, mediante un botón "Generar ruta", una
  ruta de trabajo optimizada para su jornada laboral, a partir de los casos pendientes.
- El ingeniero define con qué criterios se arma la ruta, combinando de forma flexible:
  - Cantidad o porcentaje de casos por **distrito** (opcional: puede no filtrar por distrito).
  - Cantidad de casos por **nivel de prioridad**.
  - Selección manual de una **zona en el mapa**.
- Un botón **"Restablecer"** descarta la ruta completa y permite generar una nueva. Los
  casos que quedaron sin dictaminar en la ruta descartada vuelven a estar disponibles como
  pendientes para una nueva ruta.
- El rol **Administrador** no tiene acceso a esta funcionalidad; su función es
  exclusivamente la gestión de usuarios.
- **Exclusión de solicitudes entre rutas simultáneas:** una solicitud que ya forma parte
  de la ruta activa de un ingeniero no puede volver a ser incluida en la ruta que genere
  otro ingeniero (por ejemplo, si dos ingenieros trabajan la misma zona, un mismo domicilio
  no puede aparecer en ambas rutas). Esta exclusión aplica únicamente a la **generación**
  de rutas — no impide que otro ingeniero dictamine ese caso directamente si lo necesita
  (ver 2.2). Una solicitud queda reservada dentro de la ruta de un ingeniero hasta que
  ocurra alguna de estas condiciones:
  - Se dictamina (deja de estar pendiente).
  - El ingeniero presiona "Restablecer" y descarta su ruta completa.
  - Llegan las **18:00 hs** del día (fin de la jornada operativa, que va de lunes a sábado
    de 7:00 a 17:00 hs como máximo); a esa hora se liberan automáticamente todas las
    solicitudes que quedaron sin dictaminar en cualquier ruta activa.
  - La liberación es por solicitud individual: si un ingeniero dictamina 6 de los 10 casos
    de su ruta, esos 6 quedan libres de inmediato: los 4 restantes siguen reservados (no
    aparecen en rutas ajenas) hasta que él presione "Restablecer" o sean las 18:00 hs.

### 2.6 Dashboard

- Disponible para los roles **Jefe, Administrador y Lector**.
- Muestra, como mínimo:
  - Cantidad de solicitudes derivadas a la Dirección Técnica.
  - Cantidad de solicitudes dictaminadas.
  - Cantidad de solicitudes sin dictaminar.
  - Cortes por mes y por año.
- Las solicitudes re-derivadas (por vencimiento) se cuentan como un **evento de derivación
  independiente**, no como un caso único: si un mismo caso ingresó en 2026 y volvió a
  derivarse en 2027, ambos años deben reflejar ese ingreso por separado.
- El sistema real probablemente requiera métricas adicionales a definir más adelante.

---

## 3. Fuera de alcance

- **Circuito de ingreso del reclamo**: Munibot, oficinas/distritos de atención presencial y
  el proceso de carga y clasificación inicial del reclamo en el SUA. El sistema solo actúa
  desde que el caso ya está derivado a la Dirección Técnica.
- **Derivación de trabajo a empresas concesionarias**: si el dictamen deriva en una
  intervención que termina asignándose a una concesionaria, ese circuito ocurre fuera de
  este sistema, vía SUA.
- **Reapertura de un caso por expediente en papel**: es un caso excepcional, sin proceso
  definido dentro de la organización, que se resuelve de forma manual y queda fuera del
  sistema.
- **Implementación de la integración real** con el SUA y la Autenticación Institucional:
  el sistema se entrega funcional y configurable, pero la conexión final (endpoints, APIs y
  credenciales de producción) la configura el **CIL**, no el equipo de desarrollo.
- **Definición de permisos detallados por rol**: se nombran los cuatro roles, pero el
  detalle de qué puede hacer cada uno se especifica en el documento de Requisitos.

---

## 4. Actores y sistemas externos involucrados

Coherente con el documento de Stakeholders: todo actor mencionado en este Alcance
corresponde a uno ya identificado allí.

| Actor / Sistema | Interacción con el sistema |
|---|---|
| Dirección Técnica de Arbolado | Usuaria directa: visualiza casos, arma rutas, dictamina |
| Centro de Informática Local (CIL) | Configura endpoints/credenciales reales, administra usuarios y roles |
| Director administrativo | Consume el dashboard |
| Área de Procesamiento de Datos | Gestiona re-derivaciones por vencimiento (fuera del sistema) |
| Empresas concesionarias | Reciben trabajo derivado del dictamen, fuera del sistema |
| SUA | Fuente de solicitudes y destino de los datos complementarios del dictamen |
| Autenticación Institucional | Valida usuario + API key y emite el token JWT |

---

## 5. Restricciones y supuestos

- El acceso real a las APIs del SUA y de la Autenticación Institucional es limitado (pocas
  credenciales, documentación acotada). El **esquema de campos usado en este documento es
  una aproximación**, sujeto a validación posterior con el CIL y el equipo del SUA.
- Se asume que la re-derivación de un caso vencido llega identificable mediante algún
  indicador (supuesto, no confirmado — ver 2.4).
- El sistema no almacena contraseñas de la Autenticación Institucional; solo mantiene su
  propia contraseña local para el primer paso del login.
- No se contempla asignación exclusiva de casos por ingeniero: cualquier Operador o Jefe
  puede tomar cualquier caso pendiente.

---

## 6. Identificación de entidades

Cada solicitud/caso se identifica de forma única con el formato **Número de SUA - Año**.
Este identificador no cambia durante todo el ciclo de vida del caso, incluso si se
re-deriva años después de su creación original.