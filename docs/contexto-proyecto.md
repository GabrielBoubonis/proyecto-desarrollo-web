# Contexto del proyecto — Documentación técnica (PP2)

_Documento de traspaso para continuar el trabajo en otra conversación sin repetir todo el
historial. Escuela Superior de Comercio N.º 49 — Diseño de Sistemas Web / Práctica
Profesional Profesionalizante II. Cátedra: Prof. Pablo Pedernera._

---

## 1. Qué es el sistema

Sistema que resuelve la etapa de **dictaminación técnica de reclamos de arbolado
público**, para la Dirección Técnica de Arbolado de la Dirección General de Parques y
Paseos (municipal). Actúa **después** de que un reclamo ya fue cargado y derivado por el
SUA (Sistema Único de Atención). Permite a los ingenieros ver los casos asignados, armar
una ruta de trabajo diaria, completar y firmar digitalmente el dictamen, y sincronizar el
resultado con el SUA. Incluye un dashboard de seguimiento y un protocolo especial para
emergencias climáticas.

**Doble naturaleza del entregable**: hay un **sistema real** (lo que se propondría a la
Municipalidad) y una **demo de PP2** (lo que se defiende en la materia). Se decidió
documentar primero el sistema real completo, dejando un apartado aparte (todavía no
armado) para las particularidades de la demo. La demo usa datos precargados en dos bases
simuladas (una como si fuera el SUA, otra como si fuera Autenticación Institucional), sin
llamadas HTTP reales.

## 2. Metodología de trabajo (importante para mantener el estilo)

- Antes de escribir cualquier documento, se explicitan **criterios de evaluación** propios
  para ese tipo de documento (ver cada sección abajo).
- Ante cualquier información nueva o ambigua, se **pregunta antes de escribir**, de a
  bloques, sin apurarse. El usuario valora mucho este enfoque — no conviene saltearlo.
- **Todo documento nuevo se revisa contra los documentos anteriores** para detectar
  inconsistencias (ej.: un cambio en Requisitos puede requerir tocar Alcance o
  Stakeholders). Se corrige en todos los lugares donde corresponda, no solo donde se pidió.
- Cada documento se entrega en **dos archivos**: uno `.md` (para el repo, con sintaxis
  markdown) y uno `_word.txt` (texto plano, sin sintaxis markdown, listo para pegar en
  Word).
- El profesor **no** dio plantilla para Stakeholders ni Alcance (se armaron con estructura
  propia). Para Requisitos, Historias de Usuario, Casos de Uso y Diseño UI **sí** dio
  plantillas exactas, que hay que respetar al pie de la letra.
- Se rechazó y descartó por completo un documento de un compañero (`T8-offline.md`,
  `06-offline-y-sincronizacion.md`) que traía reglas de negocio contradictorias (reservas
  indefinidas con "blindaje", alta de reclamos en la calle, baja de captores, protocolo de
  tormenta con otra lógica). De ese documento **solo se rescató**: que el sistema es una
  **PWA instalable** (no una web común, para tener almacenamiento persistente offline) y el
  patrón general de "guardar sin conexión y sincronizar al recuperar señal".
- El profesor tiene un sitio con material de la materia:
  **https://pablopedernera0.github.io/eidas-repaso** — con teoría y rúbricas de cada etapa
  (Casos de Uso, Diseño UI confirmado revisado; probablemente también Modelo ER y
  Trazabilidad, no revisados todavía). Conviene revisar ese sitio antes de armar cualquier
  etapa nueva, buscando la página específica (ej. `/diseno-ui.html`, patrón de URL a
  confirmar para otras secciones).

## 3. Archivos ya generados (en `/mnt/user-data/outputs/`)

| Archivo | Contenido |
|---|---|
| `stakeholders.md` / `_word.txt` | 10 stakeholders + tabla de roles del sistema al final |
| `alcance.md` / `_word.txt` | Alcance funcional completo, 2.1 a 2.7 |
| `requisitos.md` / `_word.txt` | RF-01 a RF-44 (7 módulos), RNF-01 a RNF-15 |
| `historias-de-usuario.md` / `_word.txt` | HU-01 a HU-15 |
| `casos_de_uso.md` / `_word.txt` | CU-01 a CU-12 |
| `diagramas/casos-de-uso.puml` | Diagrama PlantUML de casos de uso |
| `diseno_ui.md` / `_word.txt` | **En progreso, ver sección 8** |
| `diagramas/wireframes/01-login.svg` a `07-gestion-usuarios.svg` | Wireframes SVG, **algunos desactualizados, ver sección 8** |

## 4. Stakeholders — resumen (10 total)

| Stakeholder | Tipo | Subtipo | Impacto |
|---|---|---|---|
| Vecino solicitante | Externo | Usuario indirecto | Medio |
| Director administrativo | Interno | Propietario del producto | Alto |
| Área de Diagramación de Datos | Interno | Usuario primario | Alto |
| Área de Procesamiento de Datos | Interno | Usuario indirecto (impactado) | Medio |
| Dirección Técnica de Arbolado | Interno | Usuario primario | Alto |
| Centro de Informática Local (CIL) | Interno | Administrador técnico | Alto |
| SUA | Sistema externo | — | Alto |
| Autenticación Institucional | Sistema externo | — | Alto |
| Empresas concesionarias | Externo | Usuario indirecto | Medio |
| Equipo de desarrollo | Interno | Desarrolladores y mantenedores | Alto |

**Roles del sistema** (distintos de los stakeholders, tabla al final de `stakeholders.md`):
**Lector, Operador, Jefe, Administrador**. El Administrador gestiona usuarios y, fuera de
eso, tiene el mismo acceso de solo lectura que el Lector (dashboard y protocolo por
tormenta en modo consulta). El CIL es quien crea/gestiona los usuarios y sus roles.

## 5. Alcance — puntos clave

- Login en dos pasos: 1) usuario/contraseña propios validados localmente; 2) el sistema
  llama a la Autenticación Institucional con usuario + API key propia (**sin mandar la
  contraseña institucional**) y recibe un JWT. Los roles del sistema **no** vienen de la
  Autenticación Institucional, son propios y los asigna el CIL.
- Sesión: JWT + refresh token, corte a los 30 min de inactividad. La ruta armada del día
  **no se pierde** al cerrarse la sesión (persiste en base, no en el cliente).
- El sistema lee del SUA solicitudes tipo reclamo, subtipo "problema con el arbolado
  público", derivadas a "Parques y Paseos – Dirección Técnica", filtro como parámetro de
  consulta.
- **ID de caso**: Número de SUA - Año. No cambia nunca, ni siquiera en re-derivaciones años
  después.
- **3 estados**: Pendiente, Dictaminada, Pendiente-revisión (re-derivación por vencimiento,
  detectada — según un indicador del SUA que **todavía no está confirmado, es un
  supuesto**).
- **Vigencia del dictamen: 36 meses.** Si vence sin ejecutarse la intervención,
  Procesamiento de Datos re-deriva el caso con el **mismo** Número de SUA-Año.
- Un caso puede tener **más de un dictamen a lo largo del tiempo** (historial, no se
  sobrescribe). Al dictaminar un caso pendiente-revisión, el formulario arranca en blanco,
  pero hay botón para consultar el dictamen anterior (sin precargarlo).
- **Reapertura por expediente en papel**: caso excepcional, sin proceso definido, **fuera
  de alcance**.
- **Concurrencia**: solo puede haber un dictamen activo por ejemplar. Gana el primero que
  envía; el segundo intento es rechazado.
- El SUA no tiene entidad "dictamen": el dictamen completo (firmado, hash+timestamp+
  usuario) vive en la base propia; al SUA se le manda solo un subconjunto de campos
  ("datos complementarios de la solicitud"), vía una API que **expone el SUA** (nosotros la
  consumimos). Si falla, queda "pendiente de sincronizar" con reintento automático.
- El identificador de firma es el **usuario** (no hay matrícula profesional — se sacó
  expresamente de toda la documentación): inicial del nombre + 6 letras del apellido +
  número incremental si se repite.
- **Generación de ruta**: criterios combinables — distrito (opcional), prioridad, zona
  dibujada en el mapa. **Ya no incluye "complejidad"** (se sacó a pedido del usuario).
  Exclusión entre rutas simultáneas: una solicitud en la ruta de un ingeniero no puede
  aparecer en la ruta de otro (pero cualquiera puede dictaminarla directamente si hace
  falta, ej. pedido urgente — eso no está bloqueado). Se libera al dictaminar, al presionar
  "Restablecer", o a las **18:00 hs** automáticamente.
- **Offline**: PWA instalable en los captores (celulares Android que da la organización).
  Generar/confirmar ruta **requiere conexión**; completar y firmar un dictamen **no** (se
  guarda localmente "pendiente de sincronizar" y se envía solo al recuperar señal).
- **Dashboard**: derivadas / dictaminadas / sin dictaminar, cortes por mes/año. Las
  re-derivaciones cuentan como **evento independiente**, no como caso único.
- **Protocolo por tormenta** (sección 2.7 de Alcance): apartado separado del listado
  general (las solicitudes de tormenta NO aparecen ahí). Acceso: Operador/Jefe gestionan
  (ven, generan ruta, dictaminan); Lector/Administrador solo consultan. La ruta de tormenta
  se genera indicando solo **cantidad**, priorizando las **más antiguas primero**. Mismas
  reglas de exclusión/liberación que rutas normales. Objetivo operativo: no más de 100
  solicitudes de tormenta sin dictaminar cada 48 horas hábiles. Se suma al dashboard
  general y también tiene desglose propio.
- Datos del vecino: **limitados** a lo técnico (ubicación, descripción del reclamo), sin
  nombre ni contacto.
- Backup: conservación **indefinida** de dictámenes (valor legal). Sin SLA de disponibilidad
  institucional definido — se propuso 24/7 con ventanas de mantenimiento fuera de la
  jornada operativa (lunes a sábado, 7 a 17 hs).

## 6. Requisitos — estructura

**7 módulos de RF** (37 + 3 agregados = hasta RF-44, con RF-44 dentro del módulo Dashboard
pero numerado al final por orden de creación):

1. Autenticación y gestión de usuarios (RF-01 a RF-08)
2. Lectura de solicitudes del SUA (RF-09 a RF-13)
3. Generación de rutas de trabajo (RF-14 a RF-19)
4. Dictaminación (RF-20 a RF-29)
5. Trabajo sin conexión y sincronización (RF-30 a RF-34)
6. Dashboard (RF-35, RF-36, RF-37, **RF-44**)
7. Protocolo por tormenta (RF-38 a RF-43)

**RNF** en 4 categorías: Rendimiento y disponibilidad (RNF-01 a 04, + **RNF-15** capacidad
de tormenta), Seguridad y usabilidad (RNF-05 a 09), Almacenamiento y continuidad (RNF-10,
11), Plataforma (RNF-12 a 14).

Volumen de referencia: 4 a 7 ingenieros simultáneos, ~20 dictámenes/día por ingeniero
(~140/día total).

## 7. Historias de Usuario y Casos de Uso

- **15 HU** (HU-01 a HU-15), una o dos por módulo, con INVEST evaluado con
  honestidad (varias marcadas "Parcial" en Independiente/Pequeña, con la razón explicada,
  no todo tildado "Sí" de relleno).
- **12 CU** (CU-01 a CU-12), con diagrama PlantUML. Relaciones: `<<include>>` CU-06→CU-07
  (dictaminar siempre sincroniza), CU-04→CU-03 (generar ruta siempre consulta
  solicitudes), CU-12→CU-04 (tormenta reutiliza generación de ruta); `<<extend>>`
  CU-09→CU-06 (consultar dictamen anterior, solo si pendiente-revisión), CU-10→CU-06
  (dictaminar offline, solo si no hay conexión).
- Actores principales: Operador, Jefe, Administrador, Lector. Secundarios: SUA,
  Autenticación Institucional.

## 8. Diseño UI — ESTADO ACTUAL (lo más importante para continuar)

**Esto está a mitad de una ronda de correcciones, no está cerrado.**

### Rúbrica del profesor (de eidas-repaso/diseno-ui.html)

- Al menos un wireframe por pantalla/módulo relevante (imagen o PDF, en
  `diagramas/wireframes/`).
- Patrones **justificados en función del caso y los usuarios reales**, no solo nombrados.
- 4 patrones nombrados explícitamente por la cátedra: **Card** (listar ítems comparables),
  **Modal** (acción puntual sin cambiar de pantalla), **Step-by-step** (formulario largo),
  **Tabla con paginación** (listados largos a revisar/buscar). No hace falta limitarse a
  estos 4, pero son los que la cátedra reconoce de entrada.
- Formularios: cantidad de campos, si van juntos o por pasos y **por qué**, validaciones.
- Al menos una consideración de **accesibilidad concreta** (atada a un usuario y pantalla
  reales del caso, no "cumple WCAG").
- Rúbrica de 7 puntos: Muy bueno (6-7) exige TODO lo anterior bien resuelto.
- Hay una página adicional "El molde reciclable"
  (`eidas-repaso/molde-reciclable.html`) con un caso resuelto de punta a punta
  (Cosmeticos SA) que sirve de referencia de formato y de profundidad esperada.

### 7 pantallas definidas

1. Login
2. Listado de solicitudes
3. Generar ruta de trabajo
4. Formulario de dictamen técnico
5. Dashboard
6. Protocolo por tormenta
7. Gestión de usuarios (Administrador)

Diseño **mobile-first** (viewport 375×812, es una PWA en el celular del ingeniero), con
nota de que es responsive y en desktop cambia de disposición (grillas en vez de columna
única, tabla en vez de tarjetas donde aplique, menú lateral en vez de bottom nav).

### Decisiones de patrones YA CONFIRMADAS con el usuario (no reabrir)

- **Formulario de dictamen (pantalla 4): SE QUEDA en step-by-step** (3 pasos: Ejemplar →
  Intervención → Firma). Justificación confirmada: es un formulario realmente largo y es
  más cómodo completarlo por partes. **No cambiar esto.**
- **Generar ruta (pantalla 3): CAMBIA a secciones en una sola pantalla** (no step-by-step),
  porque no es un formulario largo (solo 3 criterios). Mantiene el Modal de confirmación al
  final. **Ya se rehizo el archivo SVG con este layout nuevo** (secciones: Distrito,
  Prioridad, Zona en el mapa, resumen, botón "Generar ruta", modal de confirmación) —
  pendiente de guardar/confirmar el reemplazo del archivo (el intento de sobrescribir
  `03-generar-ruta.svg` con `create_file` falló porque el archivo ya existía; hay que
  usar `str_replace` o borrar y recrear, o usar `bash_tool` con heredoc para sobrescribirlo).
- **Listado de solicitudes (pantalla 2): mantiene tarjetas** (Card), justificado por uso
  mobile (evita scroll horizontal). **Pero cambia la mecánica de listado**: se saca el
  scroll infinito / lazy loading que se había puesto, y se reemplaza por **paginación
  explícita de 30 solicitudes por página** (con indicador tipo "1-30 de 84 · página 1 de
  3"), más un **filtro por año** además del filtro por estado (tabs) que ya existía.
  **Todavía no se aplicó este cambio al SVG ni al .md.**

### Pendiente de aplicar (acordado pero no ejecutado todavía)

1. Reescribir `03-generar-ruta.svg` con el layout de secciones (contenido ya diseñado, ver
   arriba — solo falta guardarlo correctamente).
2. Editar `02-listado-solicitudes.svg`: agregar filtro por año, cambiar el texto de "scroll
   para ver más / carga diferida" por una paginación explícita de 30 por página.
3. Agregar en `01-login.svg` o en el `.md` una aclaración explícita de que el logo/nombre
   de marca son **placeholders intencionales**, porque el sistema no tiene una marca
   definida y la rúbrica no lo exige.
4. Agregar en el `.md` una justificación explícita del **ícono de sincronización** que
   aparece en varios headers (qué comunica: estado de sync offline, ligado a RF-33/RF-34).
5. Evaluar agregar una **pantalla "Perfil"** (cerrar sesión, ver usuario propio) — el
   usuario quería basarla en una imagen de referencia que **nunca llegó adjunta** en la
   conversación (se mencionó dos veces, no se subió ningún archivo de imagen para esto).
   Hay que volver a pedirla o decidir un diseño simple sin ella. No es un módulo que surja
   de ningún RF, así que no necesita el mismo nivel de detalle que el resto según la
   rúbrica.
6. Actualizar `diseno_ui.md` (y su `_word.txt`) con:
   - Los cambios de patrón de las pantallas 2 y 3 (arriba).
   - La nota aclaratoria de placeholders (punto 3).
   - La justificación del ícono de sync (punto 4).
   - Mantener intacta la justificación de la pantalla 4 (dictamen), que no cambia.
   - En Gestión de usuarios (pantalla 7), sumar una línea conectando explícitamente con
     el ejemplo del profesor (Usuarios/Roles → Tabla + Modal), aclarando que en mobile se
     usan tarjetas por el mismo motivo que en el listado de solicitudes, y que en desktop
     se vería como tabla con paginación.

### Pantallas que NO cambian (ya están bien, no tocar)

- 01-login.svg (salvo la aclaración del punto 3, que es textual/nota, no un rediseño)
- 04-formulario-dictamen.svg (confirmado step-by-step, sin cambios)
- 05-dashboard.svg
- 06-protocolo-tormenta.svg
- 07-gestion-usuarios.svg (salvo la línea nueva de justificación en el `.md`, sin cambios
  en el SVG)

## 9. Preguntas que quedaron resueltas sin necesidad de preguntarle al profesor

Gracias al sitio del profesor, esto ya NO hace falta consultarlo:
- No hace falta Figma; wireframes de baja fidelidad tipo boceto alcanzan.
- SVG cuenta como "imagen" válida.
- No hace falta un wireframe por cada paso de un formulario step-by-step, alcanza con
  documentar el flujo en el campo correspondiente.

## 10. Cosas que probablemente sigan después de Diseño UI

Mencionadas en el sitio del profesor pero **todavía no confirmadas ni empezadas**: parece
haber también una sección de **Trazabilidad** (RF/RNF → HU → CU) y posiblemente un
**Modelo de Entidad-Relación**. Conviene revisar
`https://pablopedernera0.github.io/eidas-repaso` para confirmar el listado completo de
etapas antes de asumir que Diseño UI es la última.
