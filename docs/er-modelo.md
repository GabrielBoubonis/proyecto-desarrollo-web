# Modelo Entidad-Relación

## Diagrama

_El código PlantUML se encuentra en `diagramas/er.puml`._
_Visualizar en [plantuml.com](https://www.plantuml.com/plantuml/uml/)._

## Entidades

| Entidad | Descripción | Relaciones clave |
|---------|-------------|-----------------|
| Usuario | Persona con acceso al sistema (rol Lector, Operador, Jefe o Administrador), creada y gestionada por el CIL. | Firma muchos Dictamen; genera muchas Ruta. |
| Solicitud | Copia local de un reclamo derivado por el SUA a la Dirección Técnica. Identificada con el mismo par (Número de SUA, Año) que usa el SUA, para no duplicar identificadores. | Tiene historial de Dictamen; registra eventos en HistorialDerivacion; pertenece a una o varias RutaSolicitud a lo largo del tiempo. |
| HistorialDerivacion | Un registro por cada vez que el SUA deriva una Solicitud a la Dirección Técnica (la original y las re-derivaciones por vencimiento). | Pertenece a una Solicitud. |
| Dictamen | El dictamen técnico completo firmado por un ingeniero para una Solicitud, con todos los campos del formulario físico digitalizados. | Pertenece a una Solicitud y a un Usuario firmante; incluye Fotografia, TrabajoAereo y TrabajoSubterraneo. |
| Fotografia | Una imagen del ejemplar adjunta a un Dictamen. Obligatoria: un Dictamen no puede firmarse sin al menos una. | Pertenece a un Dictamen. |
| TrabajoAereo | Un ítem seleccionado del catálogo de trabajos sobre la copa/ramas (Bloque 4 del formulario), de selección múltiple. | Pertenece a un Dictamen. |
| TrabajoSubterraneo | Un ítem seleccionado del catálogo de trabajos sobre raíces (Bloque 3 del formulario), de selección múltiple. | Pertenece a un Dictamen. |
| Ruta | Conjunto de solicitudes que un ingeniero decide atender en una jornada (normal o de tormenta). | Generada por un Usuario; agrupa Solicitudes a través de RutaSolicitud. |
| RutaSolicitud | Tabla intermedia que resuelve la relación N a M entre Ruta y Solicitud, con la fecha de reserva y si ya fue liberada. | Conecta una Ruta con una Solicitud. |

## Descripción de atributos principales

### Usuario

- `nombre_usuario` (PK): identificador único del usuario. Formato: inicial del nombre + 6
  letras del apellido + número incremental si se repite (definido en Alcance/Requisitos,
  no es un dato que el usuario elija).
- `contraseña_hash`: hash de la contraseña propia del sistema (nunca se guarda en texto
  plano; tampoco se guarda ninguna contraseña de la Autenticación Institucional).
- `rol`: uno de Lector, Operador, Jefe, Administrador. Ver Decisión 1 sobre por qué es un
  atributo y no una entidad aparte.
- `activo`: booleano. Permite dar de baja el acceso sin borrar el historial de dictámenes
  que ese usuario firmó (no se puede eliminar un Usuario que ya firmó algo).
- `fecha_alta`: cuándo el CIL creó la cuenta.

### Solicitud

- `numero_sua` (PK, compuesta con `anio`): número que asigna el SUA al reclamo.
- `anio` (PK, compuesta con `numero_sua`): año de creación **original** de la solicitud en
  el SUA. No cambia aunque el caso se re-derive años después (ver Alcance, sección 2.4).
- `distrito`, `prioridad`: usados para el armado de rutas (RF-15). `prioridad` es la
  prioridad de **triage** al momento de crear el reclamo — no confundir con
  `Dictamen.nivel_prioridad`, que es la urgencia de ejecución que define el ingeniero
  después de dictaminar (ver Decisión 6).
- `direccion`: domicilio de la solicitud tal como lo reporta el SUA (puede no coincidir con
  la ubicación exacta del ejemplar; ver `domicilio_ejemplar` en Dictamen).
- `descripcion_reclamo`: texto técnico del problema reportado, sin datos personales del
  vecino (RF-29).
- `estado`: Pendiente, Dictaminada o Pendiente-revisión.
- `es_tormenta`: booleano que determina si la solicitud aparece en el listado general o
  exclusivamente en el Protocolo por tormenta (RF-39).
- `fecha_ultima_derivacion`: se usa para ordenar el Protocolo por tormenta por antigüedad
  (RF-41) y para calcular el objetivo de 48 horas hábiles.

### HistorialDerivacion

- `id_derivacion` (PK): identificador propio del sistema (no viene del SUA).
- `numero_sua`, `anio` (FK): a qué Solicitud pertenece este evento.
- `fecha_derivacion`: cuándo ocurrió esa derivación puntual.
- `motivo`: "Nueva" (primera vez) o "Re-derivación por vencimiento". Este registro es lo
  que le permite al Dashboard contar cada derivación como un evento independiente
  (RF-37), aunque corresponda a la misma Solicitud.

### Dictamen

- `id_dictamen` (PK): identificador propio, ya que puede haber más de un dictamen por
  Solicitud a lo largo del tiempo (historial, RF-27).
- `numero_sua`, `anio` (FK): a qué Solicitud pertenece.
- `nombre_usuario` (FK): quién lo firmó.
- `nota_numero`, `expediente_numero`: datos del trámite administrativo municipal.
  **Opcionales** (no todo dictamen tiene expediente asociado al momento de firmarse).
- `fecha_emision`: se guarda junto con el hash como parte de la firma digital (RF-22).
- `domicilio_ejemplar`: dirección real donde está el árbol, determinada por el ingeniero en
  el lugar. **Obligatorio.** Se guarda acá y no solo en Solicitud — ver Decisión 3.
- `calle_esquina`, `numero_esquina`, `referencia_ubicacion`, `distancia_medianera_referencia`:
  datos de ubicación complementarios. **Opcionales**, se completan solo cuando el domicilio
  no tiene numeración exacta.
- `especie`: especie del ejemplar. **Obligatorio.**
- `cantidad_frente`: cantidad de ejemplares sobre el frente del domicilio. **Obligatorio**
  (con valor por defecto 1).
- `nivel_dano_vereda`: Alto/Medio/Bajo. **Opcional** (no todos los casos tienen daño en
  vereda).
- `es_extraccion` (booleano), `perimetro_tronco`, `motivo_extraccion`: datos del Bloque 2.
  `perimetro_tronco` y `motivo_extraccion` son **obligatorios solo si** `es_extraccion` es
  verdadero.
- `es_sin_trabajo` (booleano), `motivo_sin_trabajo`: datos del Bloque 5. `motivo_sin_trabajo`
  es **obligatorio solo si** `es_sin_trabajo` es verdadero.
- `complejidad`: Baja/Media/Alta/Máxima. **Obligatorio.** Es la complejidad de la
  **intervención a realizar sobre el ejemplar**, un dato propio del dictamen. No debe
  confundirse con ningún criterio de generación de rutas (RF-15): son dos conceptos
  independientes, uno se define al armar la ruta (antes de dictaminar) y el otro al
  completar el dictamen (durante o después de la visita).
- `urgente`, `arbol_frente_garage`, `media_tension`, `de_oficio`: booleanos. **Opcionales**
  por naturaleza (su ausencia equivale a "no", no bloquean la firma). `de_oficio` describe
  que el reclamo se originó por iniciativa de la Dirección y no por un vecino — esto es
  independiente de la funcionalidad de "alta de reclamo desde la calle" que se descartó del
  alcance: acá el dictamen ya parte de una Solicitud existente derivada por el SUA, este
  campo solo documenta el origen para el registro legal.
- `plantar`: Cazuela / Construir cazuela / Vereda jardín / ninguna. **Opcional.**
- `nivel_prioridad`: Alta/Media/Baja. **Obligatorio.** Es la urgencia con la que hay que
  **ejecutar** la intervención ya dictaminada (por ejemplo, una poda de riesgo eléctrico
  puede necesitar resolverse antes que una de mantenimiento estético), definida por el
  ingeniero al cerrar el dictamen. No debe confundirse con `Solicitud.prioridad`, que es la
  prioridad de **triage** asignada por quien recibe el reclamo y se usa para armar rutas
  (RF-15) — son dos momentos y dos criterios distintos. Se ubica al final del formulario,
  junto a `observaciones`, como el último dato que se completa antes de firmar. Ver
  Decisión 6.
- `observaciones`: texto libre. **Opcional.**
- `hash_firma`, `estado_sincronizacion`: generados por el sistema al firmar, no los completa
  el usuario (RF-22, RF-25).
- **Obligatorio y bloqueante para firmar** (no es un atributo de Dictamen, es una regla de
  validación): debe existir al menos una Fotografia asociada (RF-45).
- **Obligatorio y bloqueante para firmar** (regla de validación, no un atributo): si
  `es_extraccion` es verdadero, no puede existir ninguna fila asociada en TrabajoAereo ni en
  TrabajoSubterraneo para este Dictamen (RF-46). Ver Decisión 5.

### Fotografia

- `id_foto` (PK).
- `id_dictamen` (FK): a qué dictamen pertenece.
- `archivo_referencia`: referencia/ruta del archivo de imagen.
- `orden`: para mostrarlas siempre en el mismo orden en el documento final.

### TrabajoAereo

- `id_trabajo_aereo` (PK).
- `id_dictamen` (FK).
- `tipo_trabajo`: uno de los 12 ítems del catálogo del Bloque 4 (poda de formación,
  liberación de conductores eléctricos, etc.). Cada fila representa **un** ítem marcado;
  un Dictamen puede tener varias filas (selección múltiple confirmada). **No puede tener
  filas si el Dictamen tiene `es_extraccion = true`** (ver Decisión 5).

### TrabajoSubterraneo

- `id_trabajo_subterraneo` (PK).
- `id_dictamen` (FK).
- `tipo_trabajo`: Corte vertical de raíces / Corte horizontal de raíces / Agrandamiento de
  cazuela.
- `distancia_borde`: **obligatorio solo si** `tipo_trabajo` es "Corte vertical de raíces"
  (es el único de los tres que registra una distancia en el formulario original).
  **No puede tener filas si el Dictamen tiene `es_extraccion = true`** (ver Decisión 5).

### Ruta

- `id_ruta` (PK).
- `nombre_usuario` (FK): quién la generó.
- `fecha`: día de la jornada a la que corresponde.
- `tipo`: Normal o Tormenta.
- `estado`: Activa / Restablecida.

### RutaSolicitud

- `id_ruta` (PK, FK): la ruta.
- `numero_sua`, `anio` (PK, FK): la solicitud incluida.
- `fecha_incorporacion`: cuándo se agregó esa solicitud a la ruta.
- `liberada` (booleano), `fecha_liberacion`: si la solicitud dejó de estar reservada (por
  dictaminarse, por "Restablecer" o por el corte automático de las 18:00 hs) y cuándo.

## Decisiones de diseño

### Decisión 1 — Rol como atributo de Usuario, no como entidad aparte

Se consideró modelar `Rol` como una entidad independiente (con su propia tabla de
catálogo), pero se descartó: los cuatro roles del sistema (Lector, Operador, Jefe,
Administrador) son un conjunto fijo, no tienen atributos propios más allá del nombre, y los
permisos asociados a cada uno están definidos en Requisitos (qué RF puede ejecutar cada
rol), no en datos que vivan en la base. Crear una entidad aparte solo para poder hacer un
JOIN a una tabla de cuatro filas invariantes agrega complejidad sin ningún beneficio real.
Si en el futuro los roles necesitaran atributos propios (por ejemplo, permisos
configurables dinámicamente), ahí sí se justificaría separarlo.

### Decisión 2 — Clave primaria compuesta en Solicitud, no un ID autogenerado

Se consideró usar un `id_solicitud` autogenerado como clave primaria, con
`numero_sua`/`anio` como un campo único aparte. Se descartó porque el negocio ya definió
explícitamente ese identificador compuesto como el ID real del caso (Alcance, sección 6):
es lo que usan el SUA, los ingenieros y el dashboard para referirse a un caso. Agregar un
ID interno distinto solo introduciría una capa de indirección sin ningún beneficio, ya que
ese par de valores nunca cambia durante la vida del caso (ni siquiera en una re-derivación
años después).

### Decisión 3 — El domicilio del ejemplar se guarda en Dictamen, no solo en Solicitud

Aunque `Solicitud.direccion` ya guarda una dirección, se decidió agregar
`Dictamen.domicilio_ejemplar` como un campo propio en vez de simplemente leer el de
Solicitud. Motivo: `Solicitud` es una copia local de datos que **vienen del SUA** y podrían
en teoría actualizarse; pero un Dictamen ya firmado es un **documento legal** que debe
quedar autocontenido y no depender de que un dato externo cacheado no cambie después de la
firma. Además, el propio formulario en papel distingue "domicilio de solicitud" de
"domicilio del ejemplar" porque pueden no coincidir (el reclamo se origina en una dirección
y el árbol a intervenir está en otra cercana). Guardarlo aparte refleja esa realidad del
dominio y no solo una necesidad técnica.

### Decisión 4 — Trabajos aéreos y subterráneos como entidades hijas, no columnas booleanas

Se consideró modelar cada ítem de los catálogos de los Bloques 3 y 4 como una columna
booleana en `Dictamen` (por ejemplo, `trabajo_poda_formacion`, `trabajo_liberacion_electrica`,
etc.: 12 columnas solo para el Bloque 4). Se descartó por dos motivos: primero, como se
confirmó que son de selección múltiple, un Dictamen puede tener entre 0 y 12 trabajos
aéreos marcados, y tener 15 columnas booleanas mayormente en `false` es un esquema poco
prolijo y difícil de mantener si el catálogo cambia. Segundo, modelarlos como una entidad
hija con una fila por ítem seleccionado permite consultas más simples (por ejemplo,
"¿cuántos dictámenes incluyeron liberación de conductores eléctricos este mes?") sin tener
que revisar columna por columna.

### Decisión 5 — Exclusión entre extracción y trabajos aéreos/subterráneos como regla de validación, no como restricción de base de datos

Si en el Bloque 2 del dictamen se marca **Extracción** (`es_extraccion = true`), no tiene
sentido de negocio que el mismo dictamen incluya trabajos del Bloque 3 (subterráneos) o del
Bloque 4 (aéreos): sería incongruente indicar que se va a podar la copa o intervenir las
raíces de un ejemplar que, en la misma intervención, se está dictaminando para extraer. Se
evaluaron dos formas de resolverlo:

- **Restricción a nivel de base de datos** (por ejemplo, un `CHECK` o un trigger que impida
  insertar filas en TrabajoAereo/TrabajoSubterraneo cuando el Dictamen relacionado tiene
  `es_extraccion = true`). Se descartó como única defensa: no todos los motores de base de
  datos soportan `CHECK` entre tablas distintas de forma simple, y el error resultante sería
  poco claro para mostrárselo al ingeniero en el momento (aparecería recién al guardar, no
  mientras completa el formulario).
- **Regla de validación de la aplicación** (la elegida): el formulario deshabilita
  directamente los campos de trabajos aéreos y subterráneos apenas se marca Extracción —el
  ingeniero ve por qué no puede seleccionarlos, en el momento en que ocurre, sin esperar a
  un error de guardado— y el backend repite la misma validación antes de firmar, para
  cubrir el caso de que el dato llegue igualmente manipulado (por ejemplo, un dictamen que
  se firmó sin conexión y se sincroniza después). Esta doble verificación (interfaz +
  backend) es la misma lógica que ya se usa para otros campos condicionales del formulario
  (por ejemplo, `motivo_extraccion` solo es obligatorio si `es_extraccion` es verdadero).

Esta regla queda documentada como requisito funcional en `requisitos.md` (RF-46) y
representada como nota sobre la entidad Dictamen en el diagrama (`diagramas/er.puml`), ya
que el modelo entidad-relación en sí no tiene una forma nativa de expresar "estas dos
relaciones son mutuamente excluyentes según el valor de un atributo del lado uno".

### Decisión 6 — Dos campos de prioridad, con significados distintos, en dos entidades distintas

`Solicitud.prioridad` y `Dictamen.nivel_prioridad` podrían leerse como un campo duplicado,
así que vale la pena dejar explícita la diferencia entre ambos:

- **`Solicitud.prioridad`** es una prioridad de **triage**: la asigna quien recibe el
  reclamo, **antes** de que cualquier ingeniero haya visto el ejemplar, y su único uso es
  ayudar a armar rutas de trabajo (RF-15) — responde a la pregunta "¿qué casos conviene
  visitar primero?".
- **`Dictamen.nivel_prioridad`** es una prioridad de **ejecución operativa**: la asigna el
  ingeniero recién al cerrar el dictamen, **después** de haber inspeccionado el ejemplar en
  persona, y responde a una pregunta distinta: "ya que se determinó qué intervención
  corresponde, ¿con qué urgencia hay que efectivamente hacerla?" (por ejemplo, una
  liberación de conductores eléctricos puede requerir resolverse antes que una poda de
  formación, aunque ambas solicitudes hayan entrado con la misma prioridad de triage).

Se decidió no unificarlos en un solo campo porque representan decisiones tomadas por
personas distintas, en momentos distintos, con información distinta disponible (antes y
después de la inspección en el lugar), y ambos quedan documentados: el de triage en el
historial de la Solicitud, el de ejecución en el documento legal firmado. Por eso
`nivel_prioridad` se ubica al final del formulario del dictamen, junto a `observaciones`,
como el último criterio que el ingeniero define justo antes de firmar — es una instancia
distinta a la de generación de rutas y así se lo tiene que percibir en la interfaz.