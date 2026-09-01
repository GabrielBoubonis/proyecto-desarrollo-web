# Diseño de interfaz de usuario

> Pendiente de desarrollo. Esta sección se completará en la siguiente entrega con los
> wireframes/mockups de las pantallas principales (Login, Home/Dashboard, Reclamos sin
> dictaminar, Emitir dictamen técnico, Rutas eficientes, Urgencia por Tormenta y panel de
> Administrador).

## Lineamientos definidos hasta el momento

Derivados de los requerimientos no funcionales (ver [requisitos.md](./requisitos.md)):

- **RNF-01 — Responsive:** diseño mobile-first, ya que el uso principal es en campo desde
  celular o tablet, con adaptación a escritorio.
- **Heterogeneidad de datos de entrada:** la interfaz de los reclamos debe adaptarse a
  casos con y sin fotografía o descripción detallada.
- **RF-14 / RF-15:** los controles de tipo de intervención y de urgencia/complejidad deben
  reflejar visualmente las combinaciones bloqueadas (p. ej. deshabilitar opciones
  mutuamente excluyentes en lugar de solo validar al enviar).
- **RF-23 / RF-24:** el balanceador de carga requiere controles deslizables (sliders) por
  prioridad, con selección rápida de modos predefinidos.
- **RF-26:** la pantalla de resultado de ruta debe integrar un mapa (Google Maps/Leaflet),
  cronograma y métricas de eficiencia en una misma vista.

Los wireframes se agregarán en [`diagramas/wireframes/`](../diagramas/wireframes/).
