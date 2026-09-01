# Modelo Entidad-Relación

Modelo entidad-relación del sistema de Dictaminado y Rutas Eficientes de la Dirección
Técnica de Arbolado. Incluye las entidades identificadas durante el análisis, sus atributos
principales con sus tipos de datos, y las relaciones que modelan de forma desacoplada la
lógica del sistema frente a la infraestructura municipal.

El diagrama fuente está en [`diagramas/er.puml`](../diagramas/er.puml) y puede
visualizarse en [plantuml.com](https://www.plantuml.com/plantuml/uml/).

## Descripción de entidades

| Entidad | Descripción | Relaciones clave |
| --- | --- | --- |
| **RECLAMO** | Registro de la solicitud del ciudadano extraído del SUA. Concentra la información de entrada del problema del arbolado público, su ubicación por distrito y la imagen cargada por el vecino. `estado_sua` varía dinámicamente según la fase de intervención. | Posee un único DICTAMEN_TECNICO. Se asigna a uno o muchos DETALLE_RUTA. Registra una o muchas ASIGNACION_INTERNA. |
| **DICTAMEN_TECNICO** | Documento resolutivo con validez legal emitido en formato digital por el ingeniero agrónomo. Almacena las variables fitosanitarias evaluadas en campo (copa, tronco, raíces, inclinación) y la acción definitiva a ejecutar. Formaliza el cierre del circuito técnico. | Pertenece a un único RECLAMO. Es generado por un USUARIO_AGENTE. |
| **USUARIO_AGENTE** | Personal municipal autenticado mediante la cuenta institucional corporativa. Su identificador único es el nombre de usuario de red, que arrastra legajo y rol para determinar permisos. | Genera uno o muchos DICTAMEN_TECNICO. Tiene asignada una o muchas RUTA_INSPECCION. |
| **RUTA_INSPECCION** | Registro de cabecera logístico que planifica el itinerario de un técnico de campo para una jornada laboral específica. Controla si el circuito diario está pendiente, en curso o finalizado. | Pertenece a un USUARIO_AGENTE. Contiene uno o muchos DETALLE_RUTA. |
| **DETALLE_RUTA** | Entidad intermedia que desglosa el orden secuencial de paradas de una ruta de inspección. Asocia la planificación con los reclamos geolocalizados del SUA y lleva la trazabilidad del estado de visita de cada árbol. | Forma parte de una RUTA_INSPECCION. Referencia a un RECLAMO específico del SUA. |
| **ASIGNACION_INTERNA** | Historial administrativo que documenta las derivaciones y pases del expediente del reclamo, realizados por el Área de Diagramación de Datos hacia las diferentes dependencias internas. | Es registrada por un RECLAMO. |

## Decisiones de diseño destacadas

- **Identificación por `usuario_agente` en lugar de IDs autonuméricos genéricos:**
  siguiendo el acoplamiento al sistema de autenticación unificado, se definió el usuario
  institucional como clave primaria de USUARIO_AGENTE, asociando de forma directa la
  autoría y firma digital del dictamen al legajo de red del agente, sin recrear estructuras
  de usuarios paralelas.

- **Entidad intermedia DETALLE_RUTA:** un reclamo del SUA puede planificarse en rutas de
  días distintos si surgen imprevistos operativos en campo. Para resolver la relación N:M
  resultante, se incorporó DETALLE_RUTA, que además encapsula atributos transitorios clave
  como `orden_visita` (calculado por el algoritmo de ruteo) y el booleano `visitado`.

- **Separación lógica de ASIGNACION_INTERNA:** el flujo del trámite en el SUA incluye
  pases de expedientes entre áreas. Mantener esta información en una tabla histórica
  vinculada a `nro_reclamo_sua` permite auditar los movimientos de un caso sin alterar los
  datos inmutables de la solicitud original.

- **Mantenimiento de `estado_sua` en RECLAMO:** actúa como disparador del ciclo de vida
  del trámite. Cuando un Operario guarda y firma un DICTAMEN_TECNICO, este campo se
  actualiza en paralelo, permitiendo que la capa de integración devuelva al ecosistema
  municipal el nuevo estado (p. ej. de "Asignado" a "Dictaminado") sin consultas pesadas.
