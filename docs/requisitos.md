# Requerimientos funcionales y no funcionales

## Contexto y decisiones de diseño relevantes

La Dirección General de Parques y Paseos enfrenta un alto volumen de solicitudes
acumuladas en el SUA, con expedientes de más de tres años de antigüedad. El SUA es un
sistema macro de toda la Municipalidad, por lo que no cubre las particularidades operativas
de la gestión forestal. El presente proyecto no pretende ser una solución integral, sino una
herramienta técnica complementaria que ataca tres cuellos de botella:

- **Optimización de recursos y tiempos de campo:** rutas eficientes para reducir traslados.
- **Eliminación del cuello de botella administrativo:** validación digital que evita
  transcribir manualmente del papel al software.
- **Sustentabilidad y despapelización:** procesos virtuales sin archivos físicos.

### Decisiones de arquitectura y diseño técnico

- **Firma digital:** módulo de firma digital para los profesionales autorizados,
  reemplazando la firma hológrafa en papel.
- **Autenticación centralizada:** el acceso se acopla a la cuenta institucional existente,
  delegando la gestión de usuarios a la infraestructura actual.
- **Integración con SUA:** el sistema no define un modelo de datos propio para los reclamos;
  consume la base del SUA aplicando un filtro que extrae únicamente "Reclamo - Problemas
  con el arbolado público".
- **Heterogeneidad en la información de entrada:** la UI debe ser flexible ante reclamos con
  distinta cantidad y calidad de datos (fotos, descripciones).
- **Desacoplamiento y mantenibilidad:** la lógica de negocio se mantiene aislada de los
  proveedores tecnológicos del SUA y del sistema de autenticación, mediante adaptadores.
  Debido a la falta de acceso a endpoints y credenciales de producción, el prototipo usa
  Supabase como reemplazo temporal; el pase a producción solo requiere reconfigurar esos
  dos conectores.

## Requerimientos funcionales

### Autenticación y acceso

| ID | Descripción | Prioridad |
| --- | --- | --- |
| RF-01 | Autenticar al usuario con credenciales institucionales: validar usuario y contraseña, generar token de sesión (JWT), rechazar acceso sin token vigente y mostrar mensaje de error genérico ante credenciales incorrectas. | Alta |
| RF-02 | Gestionar sesión y permisos por rol (Lector, Operario, Administrador). Solo los Operarios con matrícula registrada pueden firmar dictámenes (RF-18). Permitir cerrar sesión invalidando el token. | Alta |

### Home / Dashboard

| ID | Descripción | Prioridad |
| --- | --- | --- |
| RF-03 | Mostrar tres métricas actualizadas: total de reclamos, sin dictaminar y dictaminados. | Alta |
| RF-04 | Mostrar gráfico de barras de pendientes por prioridad (verde/amarillo/naranja/rojo) y distribución por distrito/zona. | Media |
| RF-05 | Mostrar alertas operativas: dictámenes próximos a vencer (≤30 días de los 18 meses) y badge de casos "Protocolo Tormenta". | Media |
| RF-06 | Ofrecer accesos directos a Reclamos sin dictaminar, Realizar dictamen y Rutas eficientes. | Baja |

### Reclamos sin dictaminar

| ID | Descripción | Prioridad |
| --- | --- | --- |
| RF-07 | Listar solo los reclamos asignados a la Dirección Técnica en estado "sin dictaminar", ordenados por prioridad descendente. | Alta |
| RF-08 | Mostrar por reclamo: N.° SUA, año, dirección, descripción, prioridad, fecha de ingreso y foto (si existe). | Alta |
| RF-09 | Permitir filtrar por prioridad, zona/distrito, tipo de intervención y antigüedad, de forma combinable. | Media |
| RF-10 | Permitir iniciar el dictamen desde la tarjeta del reclamo ("Dictaminar este reclamo"). | Alta |
| RF-11 | Recalcular y elevar automáticamente la prioridad según escalamiento por tiempo (verde→amarillo→naranja→rojo), con umbrales configurables. | Media |

### Realizar dictamen técnico

| ID | Descripción | Prioridad |
| --- | --- | --- |
| RF-12 | Exigir como primer paso N.° de SUA + año, validar existencia y estado "sin dictaminar"; si no corresponde, impedir continuar mostrando el motivo. | Alta |
| RF-13 | Cargar datos del ejemplar: dirección exacta, especie, diámetro/perímetro, problemática y observaciones. | Alta |
| RF-14 | Seleccionar tipo de intervención (poda, extracción, corte de raíces u otras) bloqueando combinaciones mutuamente excluyentes (extracción deshabilita poda y corte de raíces, y viceversa). | Alta |
| RF-15 | Clasificar por urgencia (urgente/corto/mediano/largo plazo) y complejidad (baja/media/alta/máxima), un único valor por campo. El campo booleano "urgente" es independiente del plazo y puede coexistir con cualquiera de ellos. | Alta |
| RF-16 | Sugerir y registrar la época recomendada de intervención según estación del año y especie. | Media |
| RF-17 | Adjuntar fotografías y geolocalización (opcional). | Media |
| RF-18 | Exigir firma digital (captura táctil) + matrícula registrada para confirmar el dictamen; bloquear la firma a usuarios sin matrícula. | Alta |
| RF-19 | Registrar fecha de emisión, calcular vencimiento a 18 meses y dejar el dictamen firmado en solo lectura (inmutable), con sello de tiempo, matrícula y hash. | Alta |
| RF-20 | Actualizar el estado del reclamo de "sin dictaminar" a "dictaminado" al guardar el dictamen firmado. | Alta |

### Rutas eficientes

| ID | Descripción | Prioridad |
| --- | --- | --- |
| RF-21 | Seleccionar zona/área y planificar por horas de trabajo o cantidad de casos, con 10 min/dictamen configurable. | Alta |
| RF-22 | Elegir modo de traslado (auto, a pie, bicicleta) y generar la ruta más eficiente por tiempo total, partiendo y volviendo a Parques y Paseos. | Alta |
| RF-23 | Balanceador de carga por sesión: ajustar distribución por prioridad con controles deslizables (modos: urgentes primero, por porcentaje, automático equilibrado). | Alta |
| RF-24 | Configurar y guardar perfiles/presets de distribución (p. ej. 100% urgentes) para directivas superiores. | Alta |
| RF-25 | Redistribuir automáticamente el cupo cuando una prioridad no tenga stock suficiente, considerando además la época recomendada (RF-16). | Media |
| RF-26 | Mostrar la ruta en mapa (Google Maps / Leaflet) con pin por prioridad, cronograma estimado, desglose de tiempos (traslado vs. dictaminación) y % de eficiencia. | Alta |
| RF-27 | Consultar N.° SUA y año de cada reclamo asignado desde la página de rutas. | Media |

### Urgencia por Tormenta

| ID | Descripción | Prioridad |
| --- | --- | --- |
| RF-28 | Mostrar la sección solo si hay reclamos con etiqueta de tormenta; listar casos de los últimos 3 días con filtro por fecha; badge con la cantidad de pendientes. | Alta |
| RF-29 | Tratar todos los casos de tormenta con la misma prioridad, sin distribución por niveles ni balanceador. | Alta |
| RF-30 | Calcular la ruta de emergencia priorizando la mínima distancia/tiempo total. | Alta |

### Rol Administrador

| ID | Descripción | Prioridad |
| --- | --- | --- |
| RF-31 | Vincular una cuenta institucional existente a un rol (Lector/Operario/Administrador) y cargar matrícula para Operarios. El sistema no gestiona altas de credenciales ni contraseñas (dependen del mecanismo institucional). Desactivar un usuario revoca su acceso sin afectar la cuenta institucional.<br>*Nota de alcance — prototipo académico:* dado que el equipo no cuenta con permisos para los endpoints reales, la demo usa Supabase, donde el Administrador sí puede dar de alta cuentas de prueba con contraseña propia, solo a fines demostrativos. En producción el flujo pasa a ser exclusivamente de vinculación de rol. | Alta |
| RF-32 | Configurar los adaptadores de conexión externa (SUA, autenticación institucional) sin modificar código fuente, dejando preparado —no operativo— el módulo de certificación de firma digital (placeholder académico). | Alta |

## Requerimientos no funcionales

| ID | Categoría | Descripción | Prioridad |
| --- | --- | --- | --- |
| RNF-01 | Usabilidad / Responsive | Totalmente funcional en celular y tablet (uso principal en campo), adaptable a escritorio. | Alta |
| RNF-02 | Accesibilidad de despliegue | Accesible desde una URL pública fija sin instalación ni configuración previa. | Alta |
| RNF-03 | Conectividad en campo | Debe operar mediante datos móviles, sin depender del proxy de la red municipal interna. | Alta |
| RNF-04 | Rendimiento | Responder a login, listado, guardado de dictamen y cálculo de ruta en tiempo aceptable bajo conectividad variable. | Media |
| RNF-05 | Seguridad de acceso | Protección por autenticación y control de sesión por token; sin recursos internos accesibles sin autenticación. | Alta |
| RNF-06 | Integridad y trazabilidad | Dictámenes firmados como registros inmutables con sello de tiempo, matrícula y hash. | Alta |
| RNF-07 | Consistencia de datos | Integridad referencial entre reclamos, dictámenes, ingenieros y rutas; sin dictámenes huérfanos o duplicados. | Alta |
| RNF-08 | Mantenibilidad / Desacoplamiento | Lógica de negocio aislada de proveedores externos (SUA, autenticación municipal); pase a producción solo reconfigura conectores. | Alta |
| RNF-09 | Configurabilidad | Parámetros de negocio (tiempo por dictamen, umbrales, perfiles, criterios estacionales) configurables sin tocar código. | Alta |
| RNF-10 | Escalabilidad | La arquitectura permite incorporar nuevos módulos o reglas sin rediseñar el sistema. | Media |
| RNF-11 | Despliegue continuo | Despliegue automático desde el repositorio ante cada actualización de la rama principal. | Media |
| RNF-12 | Compatibilidad con el SUA | Preparado para consumir reclamos del SUA en producción reemplazando los endpoints de lectura, sin alterar el resto de la app. | Alta |
| RNF-13 | Despapelización | Elimina la necesidad de soporte físico para la emisión y carga del dictamen técnico. | Media |