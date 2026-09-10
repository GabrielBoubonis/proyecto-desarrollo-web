# Requisitos del sistema

## Descripción del sistema

El sistema resuelve la etapa de dictaminación técnica de reclamos de arbolado público,
una vez que estos ya fueron derivados a la Dirección Técnica de Arbolado dentro del SUA
(Sistema Único de Atención). Permite a los ingenieros consultar las solicitudes asignadas,
armar una ruta de trabajo diaria, completar y firmar digitalmente el dictamen técnico
correspondiente, y sincronizar el resultado con el SUA. También genera un dashboard de
seguimiento para la Dirección. Opera como una aplicación web progresiva (PWA) instalable
en los dispositivos móviles (captores) que la organización provee a los ingenieros, para
poder trabajar sin conexión en el campo.

## Requisitos funcionales

_Agrupados por módulo o área funcional._

### Módulo 1 — Autenticación y gestión de usuarios

| ID | Requisito |
|----|-----------|
| RF-01 | El sistema debe permitir el inicio de sesión mediante usuario y contraseña propios, validados contra la base de datos local del sistema. |
| RF-02 | Si las credenciales locales son válidas, el sistema debe validar el usuario contra la Autenticación Institucional (usuario + API key propia del sistema) y obtener un token JWT. |
| RF-03 | El sistema debe rechazar el inicio de sesión si el usuario no existe en la base local, aunque exista en la Autenticación Institucional. |
| RF-04 | El sistema debe cerrar la sesión automáticamente luego de 30 minutos sin interacción del usuario. |
| RF-05 | El sistema debe renovar la sesión mediante refresh token mientras haya actividad, sin requerir reingresar credenciales. |
| RF-06 | El sistema debe permitir a un usuario con rol Administrador crear, modificar y dar de baja usuarios, y asignarles un rol (Administrador, Jefe, Operador o Lector). |
| RF-07 | El sistema debe exigir que la contraseña local cumpla: mínimo 8 caracteres, al menos una mayúscula, una minúscula, un número y un carácter especial. |
| RF-08 | El sistema debe permitir que un Administrador restablezca (blanquee) la contraseña de un usuario. |

### Módulo 2 — Lectura de solicitudes del SUA

| ID | Requisito |
|----|-----------|
| RF-09 | El sistema debe consultar al SUA las solicitudes de tipo "reclamo", subtipo "problema con el arbolado público", derivadas al área "Parques y Paseos – Dirección Técnica", aplicando ese filtro como parámetro de la consulta. |
| RF-10 | El sistema debe identificar cada solicitud de forma única mediante el Número de SUA - Año. |
| RF-11 | El sistema debe mostrar cada solicitud en uno de tres estados: Pendiente, Dictaminada o Pendiente-revisión. |
| RF-12 | El sistema debe marcar como Pendiente-revisión a una solicitud que ya tuvo un dictamen y fue re-derivada por Procesamiento de Datos con el mismo Número de SUA-Año. |
| RF-13 | El sistema debe permitir a cualquier usuario con rol Operador o Jefe visualizar el listado completo de solicitudes pendientes o pendientes-revisión, sin restricción de asignación exclusiva. |

### Módulo 3 — Generación de rutas de trabajo

| ID | Requisito |
|----|-----------|
| RF-14 | El sistema debe permitir a un usuario con rol Operador o Jefe generar una ruta de trabajo diaria a partir de las solicitudes pendientes. |
| RF-15 | El sistema debe permitir combinar los siguientes criterios para armar la ruta: cantidad/porcentaje por distrito (opcional), cantidad por nivel de prioridad, selección manual de una zona en el mapa. |
| RF-16 | El sistema debe excluir de una ruta nueva las solicitudes que ya forman parte de la ruta activa de otro ingeniero. |
| RF-17 | El sistema debe liberar automáticamente una solicitud reservada en una ruta cuando: se dictamina, el ingeniero presiona "Restablecer", o son las 18:00 hs del día. |
| RF-18 | El sistema debe permitir a un ingeniero descartar su ruta completa mediante el botón "Restablecer", liberando las solicitudes no dictaminadas. |
| RF-19 | El sistema debe requerir conexión a internet para generar o confirmar una ruta (no disponible en modo offline). |

### Módulo 4 — Dictaminación

| ID | Requisito |
|----|-----------|
| RF-20 | El sistema debe permitir a un usuario con rol Operador o Jefe completar un dictamen técnico para una solicitud en estado Pendiente o Pendiente-revisión. |
| RF-21 | El sistema debe impedir que exista más de un dictamen activo para el mismo ejemplar: ante dos envíos simultáneos para el mismo caso, debe aceptar el primero y rechazar el segundo. |
| RF-22 | El sistema debe firmar digitalmente cada dictamen con hash, timestamp y el usuario que lo generó (identificador: inicial del nombre + 6 letras del apellido + número incremental). |
| RF-23 | El sistema debe almacenar el dictamen completo y firmado en la base de datos propia del sistema. |
| RF-24 | El sistema debe enviar al SUA, mediante la API que este expone, el subconjunto de campos correspondiente a los "datos complementarios" de la solicitud. |
| RF-25 | Si el envío al SUA falla, el sistema debe guardar el dictamen localmente con estado "pendiente de sincronizar" y reintentar el envío automáticamente. |
| RF-26 | El sistema debe permitir descargar el dictamen firmado en formato PDF (u otro formato imprimible) para su impresión como documento legal. |
| RF-27 | El sistema debe conservar un historial de todos los dictámenes emitidos para un mismo caso (Número de SUA-Año), sin sobrescribir los anteriores. |
| RF-28 | El sistema debe permitir a un ingeniero que dictamina un caso en estado Pendiente-revisión consultar el dictamen anterior (contenido y autor), sin precargarlo en el formulario nuevo. |
| RF-29 | El sistema debe limitar los datos del vecino visibles a lo estrictamente necesario para el trabajo técnico (ubicación y descripción del reclamo), excluyendo nombre y datos de contacto. |

### Módulo 5 — Trabajo sin conexión y sincronización

| ID | Requisito |
|----|-----------|
| RF-30 | El sistema debe estar disponible como PWA instalable en los captores (dispositivos Android provistos por la organización). |
| RF-31 | El sistema debe permitir completar y firmar un dictamen sin conexión a internet, siempre que la ruta correspondiente ya haya sido generada con conexión. |
| RF-32 | El sistema debe guardar localmente en el dispositivo cualquier dictamen firmado sin conexión, en estado "pendiente de sincronizar". |
| RF-33 | El sistema debe reintentar automáticamente el envío de los dictámenes pendientes de sincronizar en cuanto detecte conexión, sin intervención manual del usuario. |
| RF-34 | El sistema debe mostrar al ingeniero un indicador permanente del estado de sincronización (todo sincronizado / cantidad de dictámenes pendientes / sin conexión). |

### Módulo 6 — Dashboard

| ID | Requisito |
|----|-----------|
| RF-35 | El sistema debe mostrar, para los roles Jefe, Administrador y Lector, la cantidad de solicitudes derivadas a la Dirección Técnica, dictaminadas y sin dictaminar. |
| RF-36 | El sistema debe permitir filtrar las métricas del dashboard por mes y por año. |
| RF-37 | El sistema debe contabilizar cada re-derivación de una solicitud (por vencimiento) como un evento independiente en las métricas, aunque corresponda al mismo Número de SUA-Año. |

## Requisitos no funcionales

### Rendimiento y disponibilidad

| ID | Requisito |
|----|-----------|
| RNF-01 | El sistema debe estar disponible 24/7, salvo ventanas de mantenimiento programadas fuera de la jornada operativa (lunes a sábado, 7 a 17 hs). |
| RNF-02 | El sistema debe soportar el uso simultáneo de entre 4 y 7 ingenieros sin degradación perceptible del tiempo de respuesta. |
| RNF-03 | El sistema debe soportar un volumen de al menos 140 dictámenes diarios (aprox. 20 por ingeniero) sin degradación del rendimiento. |
| RNF-04 | En modo offline, cada solicitud HTTP debe tener un tiempo límite de espera corto (del orden de segundos) para no bloquear la interfaz ante señal intermitente. |

### Seguridad y usabilidad

| ID | Requisito |
|----|-----------|
| RNF-05 | La contraseña local debe exigir un mínimo de 8 caracteres, con al menos una mayúscula, una minúscula, un número y un carácter especial. |
| RNF-06 | El sistema no debe almacenar ni transmitir la contraseña institucional del usuario; la validación institucional se realiza solo con usuario + API key propia. |
| RNF-07 | El sistema debe usar HTTPS/TLS en todas las comunicaciones con el SUA, la Autenticación Institucional y entre cliente y servidor. |
| RNF-08 | El sistema debe limitar los datos personales del vecino que consulta y almacena a los estrictamente necesarios para el trabajo técnico. |
| RNF-09 | La interfaz debe ser utilizable en pantallas de celular (diseño responsive), dado que el ingeniero trabaja desde un captor en el campo. |

### Almacenamiento y continuidad

| ID | Requisito |
|----|-----------|
| RNF-10 | Los dictámenes deben conservarse de forma indefinida en la base de datos del sistema, dado su valor de documento legal. |
| RNF-11 | El sistema debe contar con copias de backup periódicas de la base de datos de dictámenes. |

### Plataforma

| ID | Requisito |
|----|-----------|
| RNF-12 | El sistema debe funcionar como PWA instalable en dispositivos Android (captores provistos por la organización). |
| RNF-13 | El sistema no requiere soporte para iOS, dado que los dispositivos de campo son exclusivamente Android. |
| RNF-14 | El sistema debe utilizar un servicio de mapas/geolocalización de uso gratuito para la generación de rutas. |