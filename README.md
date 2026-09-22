# [Nombre del Sistema] — Grupo [N]

> Materia: Diseño de Sistemas Web — Analista Funcional de Sistemas  
> Institución: Terciario Urquiza — Rosario  
> Docente: Pedernera Pablo  
> Cuatrimestre: 2.° 2026

## Integrantes

Ver [integrantes.md](integrantes.md)

## Descripción del proyecto

El sistema resuelve la etapa de dictaminación técnica de reclamos de arbolado público, una vez que estos ya fueron derivados a la Dirección Técnica de Arbolado dentro del SUA (Sistema Único de Atención). Permite a los ingenieros agrónomos consultar las solicitudes asignadas, armar una ruta de trabajo diaria, completar y firmar digitalmente el dictamen técnico correspondiente, y sincronizar el resultado con el SUA. También genera un dashboard de seguimiento para la Dirección. Opera como una aplicación web progresiva (PWA) instalable en los dispositivos móviles (captores) que la organización provee a los ingenieros, para poder trabajar sin conexión en el campo.

## Caso de estudio

**Organismo comitente:** Dirección General de Parques y Paseos — Municipalidad de Rosario.

El proyecto surge de un relevamiento realizado durante 2025 (materia Práctica Profesionalizante I), en el cual se identificaron problemáticas y oportunidades de mejora en los procesos de la repartición. A partir de ese relevamiento, y con autorización de los directivos, se decidió desarrollar una herramienta complementaria — no una solución integral — orientada a atacar tres cuellos de botella concretos: los tiempos de campo de los ingenieros agrónomos, la carga manual "papel a digital" que realiza el Área de Procesamiento de Datos, y la despapelización institucional.

El sistema no reemplaza al SUA (plataforma transversal a toda la Municipalidad); se integra con él como capa complementaria, filtrando únicamente los reclamos con el subtipo "Problema con el arbolado público", y queda desacoplado de los sistemas de autenticación institucional y de la base de reclamos del SUA mediante adaptadores, dado que en esta etapa de prototipo no se cuenta con acceso a los endpoints de producción.

## Entregas

| Entrega | Descripción | Fecha | Estado |
|---------|-------------|-------|--------|
| EP-01 | Presentación preliminar | | |
| EP-02 | | | |
| Final | Versión definitiva | | |

## Estructura del repositorio

```
/
├── README.md
├── integrantes.md
├── RECURSOS.md         ← leer antes de empezar: prerrequisitos, cheatsheet de git, recursos
├── docs/
│   ├── requisitos.md
│   ├── historias-de-usuario.md
│   ├── casos-de-uso.md
│   ├── er-modelo.md
│   ├── diseño-ui.md
│   └── stakeholders.md
├── diagramas/
│   ├── casos-de-uso.puml
│   ├── er.puml
│   └── wireframes/
└── cuestionario/
```

## Instrucciones operativas

- Un integrante del grupo es responsable de subir los cambios al repositorio.
- Completar `integrantes.md` antes de la primera entrega.
- Mantener los archivos en la carpeta correspondiente según la estructura indicada.
- Los diagramas deben entregarse en formato PlantUML (`.puml`). Se pueden visualizar en [plantuml.com](https://www.plantuml.com/plantuml/uml/).
