# Arquitectura y diagramas del sistema propuesto

## Diagramas de secuencia

- **Login** — [`diagramas/secuencia-login.puml`](../diagramas/secuencia-login.puml)
  Flujo de autenticación contra `AuthAdapter` (implementación de prototipo con Supabase
  Auth). En producción se reemplaza por un adaptador contra el sistema institucional
  (RF-32), sin alterar el resto del flujo.

- **Emitir dictamen técnico** — [`diagramas/secuencia-dictamen.puml`](../diagramas/secuencia-dictamen.puml)
  Validación del N.° SUA + año contra `SUAAdapter`, carga de datos del ejemplar, firma
  digital con matrícula, y actualización de estado del reclamo. En producción,
  `SUAAdapter` consulta los endpoints reales del SUA municipal.

- **Planificar ruta eficiente** — [`diagramas/secuencia-ruta.puml`](../diagramas/secuencia-ruta.puml)
  El Backend obtiene los reclamos pendientes de la zona junto con el balanceador vigente,
  delega el cálculo al Motor de Ruteo y persiste `RUTA_INSPECCION` + `DETALLE_RUTA`.

## Diagrama de clases

[`diagramas/clases.puml`](../diagramas/clases.puml)

Modela las entidades de negocio (`Usuario`, `Reclamo`, `DictamenTecnico`,
`RutaInspeccion`, `DetalleRuta`, `AsignacionInterna`) y aplica el **patrón Adaptador**
(RNF-08) mediante las interfaces `IReclamoProvider` e `IAuthProvider`: el núcleo del
sistema depende únicamente de estas interfaces, de modo que pasar de la implementación de
prototipo (`SupabaseReclamoAdapter`, `SupabaseAuthAdapter`) a la de producción
(`SUAAdapter`, `AuthInstitucionalAdapter`) es solo reemplazar la clase concreta.

## Diagrama de despliegue

[`diagramas/despliegue.puml`](../diagramas/despliegue.puml)

- **Dispositivo de campo** (celular/tablet/PC): PWA/Web App.
- **Vercel**: hosting del frontend estático y del backend como Serverless Functions.
- **Supabase**: base PostgreSQL (reclamos simulados, dictámenes, rutas, usuarios), Auth y
  Storage (fotos/evidencias) — implementación vigente en el prototipo.
- **Infraestructura Municipal** (cloud, a integrar en producción): SUA y Autenticación
  Institucional, que reemplazarán a Supabase mediante los adaptadores `IReclamoProvider` e
  `IAuthProvider`.

## Diagrama de flujo de datos — Nivel 1

[`diagramas/dfd-nivel1.puml`](../diagramas/dfd-nivel1.puml)

Cinco procesos principales: Autenticar Usuario, Consultar Reclamos, Emitir Dictamen,
Planificar Rutas y Administrar Sistema, sobre cuatro almacenes de datos: Usuarios y Roles,
Reclamos (vía adaptador), Dictámenes y Rutas.
