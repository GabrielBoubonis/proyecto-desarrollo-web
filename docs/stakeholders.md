# Identificación de stakeholders

Partes interesadas relevantes para el sistema de Dictaminado y Rutas Eficientes. Para
cada una se describe su rol y se justifica su importancia estratégica para el proyecto.

## Vecino solicitante

Ciudadano que reporta una necesidad de servicio o intervención sobre un ejemplar del
arbolado público de Rosario (en la vereda de su domicilio o en cualquier otro espacio de
la ciudad). Detecta la problemática e inicia el circuito al ingresar la solicitud al sistema
general (SUA).

**¿Por qué es clave?** Es el usuario externo que justifica la existencia de la repartición.
Si el sistema optimiza los tiempos de dictaminación internos, el impacto directo se
reflejará en una solución más rápida para el ciudadano.

## Director administrativo

Autoridad máxima encargada de supervisar el circuito administrativo, los tiempos de
resolución y la optimización de recursos de la Dirección. Monitorea periódicamente el
estado de los trámites y analiza indicadores de rendimiento del área.

**¿Por qué es clave?** Su validación es indispensable para la aprobación e implementación
institucional del proyecto. Es el principal interesado en contar con un dashboard que
centralice métricas para la toma de decisiones estratégicas.

## Área de Diagramación de Datos

Oficina interna responsable de la recepción, clasificación, derivación y seguimiento
administrativo de los reclamos. Actualmente actúa como nexo manual crítico: recibe los
dictámenes técnicos en papel, busca cada solicitud en el SUA y transcribe manualmente la
información para actualizar y cerrar las actuaciones, apoyándose en planillas de cálculo
paralelas por falta de herramientas específicas.

**¿Por qué es clave?** Sufre directamente la ineficiencia del traspaso "papel a digital" y
la duplicación de tareas. El nuevo sistema automatiza la digitalización desde el origen,
eliminando la carga manual y el riesgo de errores de transcripción.

## Dirección Técnica de Arbolado

Área técnica especializada y principal actor afectado positivamente por el nuevo sistema.
Su participación activa (entrevistas, mesas de trabajo, análisis del histórico de
dictámenes) fue la base para el diseño de la aplicación. Es un eslabón crítico en el ciclo
de vida del reclamo: según la Ordenanza N.º 5.118, la normativa de arbolado urbano, la Ley
Provincial del Árbol N.º 13.836 y la Ley Provincial N.º 9.004, ninguna cuadrilla operativa
puede intervenir un ejemplar sin el aval y la firma de un dictamen técnico previo.

**¿Por qué es clave?** Es el stakeholder con mayor interés estratégico. El sistema le
provee una herramienta para reordenar prioridades, planificar rutas eficientes y optimizar
la distribución de recursos técnicos y humanos en el territorio.

## Centro de Informática Local (CIL)

Área tecnológica interna a cargo del soporte, la infraestructura y la seguridad de los
sistemas de la organización. Su intervención en este proyecto se acota a la fase de
despliegue, integración y mantenimiento.

**¿Por qué es clave?** Será responsable de configurar los conectores/adaptadores para
vincular la aplicación con las bases de datos institucionales y de desplegarla en los
dispositivos móviles corporativos del personal de campo, así como de su gobernanza,
mantenimiento y soporte técnico.

## Modelo de roles y permisos

Se definen tres roles de sistema:

| Rol | Descripción | Acceso |
| --- | --- | --- |
| **Lector** | Consulta pasiva, sin edición | Solo Home / Dashboard (RF-03, RF-04, RF-05) |
| **Operario** | Rol operativo pleno | Reclamos sin dictaminar, Emitir dictamen, Rutas eficientes, Urgencia por Tormenta, Dashboard. Dentro de este rol, solo quienes tengan matrícula profesional registrada en su perfil pueden firmar dictámenes (RF-18) |
| **Administrador** | Personal del CIL | Gestión de usuarios y roles, configuración de conectores/adaptadores externos, parámetros del sistema (umbrales, tiempos, perfiles de distribución), placeholder de certificación de firma digital |
