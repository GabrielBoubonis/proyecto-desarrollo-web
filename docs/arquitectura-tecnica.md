# Arquitectura técnica

_Este documento no forma parte de las plantillas que exige la cátedra (Requisitos,
Historias de Usuario, Casos de Uso, DoR, Slicing, Modelo ER, Diseño UI): es un complemento
para que cualquier persona o agente de IA que tenga que **construir** el sistema a partir
de esta documentación cuente con las decisiones técnicas concretas que los documentos de
cátedra solo mencionan de forma funcional. Los `.md` existentes llevan una nota breve que
apunta acá en los puntos donde esto aplica; el detalle completo vive únicamente en este
archivo, para no romper el formato exigido en los demás._

_Todas las piezas elegidas son gratuitas / de código abierto, sin costo de licencia ni de
uso en el volumen de esta demo (4 a 7 ingenieros, ~140 dictámenes/día)._

---

## 1. Stack tecnológico

| Capa | Elección | Motivo |
|---|---|---|
| Backend | Node.js + TypeScript + Express | Gratuito, mismo lenguaje que el frontend (menor curva para el equipo), tipado fuerte reduce errores de contrato entre módulos. |
| Base de datos | PostgreSQL | Gratuita/OSS, soporta bien los tipos del Modelo ER (claves compuestas de `Solicitud`, JSON para campos opcionales), con opciones de hosting gratuito para la demo (Neon, Supabase, Railway — capa free). |
| ORM | Prisma | Gratuito, genera tipos TypeScript desde el esquema, simplifica traducir `er-modelo.md` a migraciones SQL. |
| Frontend | React + Vite | Gratuito, ecosistema grande, Vite da un build rápido y soporte nativo para PWA vía plugin. |
| PWA / offline | Workbox (Service Worker) + Dexie.js (IndexedDB) | Ambos gratuitos/OSS. Workbox cachea los assets de la app; Dexie maneja la cola de dictámenes/fotos "pendiente de sincronizar" (RF-31 a RF-34) con una API más simple que IndexedDB nativo. |
| Autenticación | JWT (librería `jsonwebtoken`) + bcrypt + WebAuthn (`@simplewebauthn/server` y `/browser`) | Las tres son gratuitas/OSS. WebAuthn resuelve RF-51 usando el sensor biométrico que ya tienen los captores Android, sin depender de SMS/email. |
| Mapas y ruteo | OpenRouteService (API gratuita, límite ~2000 requests/día en el tier free) + Leaflet (mapa interactivo, OSS) | Cubre RNF-14: cálculo de distancia/tiempo a pie y en vehículo, y dibujo de zona en el mapa (RF-15) sin costo. |
| Rate limiting | `express-rate-limit` | Gratuito/OSS, cubre RNF-19. |
| Testing | Jest (backend) + Vitest (frontend) | Ambos gratuitos/OSS, estándar en el ecosistema Node/React. |
| Hosting de la demo (opcional) | Render / Railway (backend, capa free) + Vercel o Netlify (frontend, capa free) + Neon/Supabase (Postgres, capa free) | Ninguno tiene costo para el volumen de una demo académica; para producción real, el CIL define el hosting institucional (fuera de este alcance, ver `alcance.md` §3). |

**Fuera de esta elección (y por qué):** no se propone GraphQL (REST alcanza para la
cantidad de recursos del sistema, y es más simple de documentar con OpenAPI) ni un
framework de backend más pesado (NestJS, etc. — Express es suficiente para el tamaño de
este proyecto y reduce la curva de entrada).

---

## 2. Contrato de API interno (resumen)

API REST, JSON, autenticación por JWT en el header `Authorization: Bearer <token>` salvo en
`/auth/login`. Agrupado por módulo de `requisitos.md`:

| Recurso | Endpoints principales | RF relacionados |
|---|---|---|
| `/auth` | `POST /auth/login` (paso 1 y 2, RF-01 a RF-03), `POST /auth/refresh`, `POST /auth/logout`, `POST /auth/webauthn/verify` (segundo factor, RF-51) | RF-01 a RF-05, RF-50, RF-51 |
| `/usuarios` | `GET /usuarios`, `POST /usuarios`, `PATCH /usuarios/:id`, `POST /usuarios/:id/reset-password`, `POST /usuarios/:id/revocar-captor` | RF-06 a RF-08, RF-52 |
| `/solicitudes` | `GET /solicitudes` (filtros de estado/distrito), `GET /solicitudes/:numeroSua/:anio` | RF-09 a RF-13 |
| `/rutas` | `POST /rutas` (generar, RF-14 a RF-19, RF-47 a RF-49), `GET /rutas/activa`, `POST /rutas/:id/restablecer` | RF-14 a RF-19, RF-47 a RF-49 |
| `/dictamenes` | `POST /dictamenes` (completar y firmar, dispara WebAuthn), `GET /dictamenes/:numeroSua/:anio/historial`, `GET /dictamenes/:id/pdf` | RF-20 a RF-29, RF-45, RF-46, RF-51 |
| `/sync` | `POST /sync/dictamenes` (cola offline → servidor, recalcula hash, RF-53) | RF-30 a RF-34, RF-53 |
| `/dashboard` | `GET /dashboard?mes=&anio=` | RF-35 a RF-37, RF-44 |
| `/tormenta` | `GET /tormenta`, `POST /tormenta/rutas` | RF-38 a RF-43 |
| `/auditoria` | `GET /auditoria?usuario=&accion=&desde=&hasta=` | RF-54, RF-55 |

Cada endpoint de escritura que modifica usuarios o configuración de rutas dispara
internamente un registro en `/auditoria` (RF-54) — no es un endpoint que el cliente llame
por separado.

---

## 3. Esquemas simulados para la demo (SUA y Autenticación Institucional)

`contexto_proyecto.md` establece que la demo usa dos bases precargadas, simuladas, sin
llamadas HTTP reales. Estos son los esquemas mínimos propuestos para esas dos bases —
**son una aproximación para la demo, no el contrato real del SUA** (ver `alcance.md` §5,
"el esquema de campos usado en este documento es una aproximación, sujeto a validación
posterior con el CIL y el equipo del SUA").

**Mock SUA — tabla `solicitudes_sua`:**

```
numero_sua (string), anio (int), tipo (string, fijo "reclamo"),
subtipo (string, fijo "problema con el arbolado público"),
area_derivada (string, fijo "Parques y Paseos - Dirección Técnica"),
distrito (string), prioridad (string), direccion (string),
descripcion_reclamo (string), es_tormenta (boolean),
es_rederivacion (boolean), fecha_derivacion (date)
```

**Mock Autenticación Institucional — tabla `usuarios_institucionales`:**

```
nombre_usuario (string), vigente (boolean), nombre_completo (string)
```

El conector propio del sistema (`IReclamoProvider`, `IAuthProvider` — ver
`stakeholders.md`) se implementa contra estas tablas mock en la demo, y se reemplaza por
las llamadas HTTP reales sin tocar el resto del sistema cuando el CIL confirme el esquema y
las credenciales de producción.

---

## 4. Mecanismos de seguridad (detalle de implementación)

| Mecanismo | Elección concreta | RF/RNF que resuelve |
|---|---|---|
| Hash de firma del dictamen | SHA-256 sobre el JSON canónico del dictamen (campos ordenados, sin espacios) | RF-22, RF-53 |
| Hashing de contraseñas | bcrypt, costo 12 | RNF-05 |
| Segundo factor al firmar | WebAuthn (biometría del dispositivo); ver wireframe `04b-segundo-factor.svg` | RF-51 |
| Cifrado at-rest en el captor | AES-GCM (Web Crypto API), clave derivada de la sesión activa | RNF-16 |
| Borrado post-sincronización | al confirmarse la sincronización (RF-33), se eliminan de IndexedDB la fotografía y el dictamen ya enviados | RNF-16 |
| Rate limiting server-side | 10 intentos / 15 min por combinación IP + usuario, backoff exponencial | RNF-19 |
| Sesión | JWT corto (30 min) + refresh token con tope absoluto de 7 días | RF-04, RF-05, RNF-20 |
| Cabeceras HTTP | CSP `default-src 'self'`, HSTS, `X-Frame-Options: DENY` en todas las respuestas | RNF-21 |

---

## 5. Algoritmo de selección y ruteo (RF-47, RF-48, RF-49)

1. **Filtrado:** se filtran las solicitudes pendientes según distrito (si se indicó),
   prioridad y/o zona dibujada en el mapa (polígono, verificado con un chequeo
   punto-en-polígono sobre la coordenada de la solicitud).
2. **Selección cuando sobran candidatas (RF-47):**
   - Se agrupan las solicitudes filtradas en **franjas de antigüedad de 2 días corridos**
     (todas las solicitudes derivadas dentro de la misma franja se consideran "igual de
     antiguas" a efectos prácticos).
   - Se recorren las franjas de la más antigua a la más nueva. Dentro de cada franja, se
     ordenan por **distancia estimada en metros** (ver punto 3) respecto del punto ya
     seleccionado más cercano (heurística de vecino más próximo, acumulativa).
   - Se completa el cupo pedido por el ingeniero siguiendo ese orden.
3. **Cálculo de distancia/tiempo (RNF-14):** cada solicitud ya tiene `latitud`/`longitud`
   cacheadas (geocodificadas una sola vez al momento de la derivación, vía la API de
   Geocoding de OpenRouteService — ver `er-modelo.md`, Decisión 9), así que generar una
   ruta no geocodifica de nuevo. Se consulta la API de Matrix de OpenRouteService (perfil
   `foot-walking` o `driving-car` según el modo elegido, RF-49) para obtener la matriz de
   distancias entre esas coordenadas y la sede de la Dirección General de Parques y Paseos
   (punto de partida y cierre del cálculo, ver `alcance.md` §2.5). Las solicitudes sin
   coordenadas (geocodificación fallida) quedan fuera del cálculo hasta corregirse.
4. **Orden de visita (RF-48):** una vez seleccionadas las solicitudes, se resuelve el orden
   de recorrido con la misma heurística de vecino más próximo sobre la matriz de
   distancias ya calculada, partiendo de la sede.
5. **Si OpenRouteService no responde:** la selección continúa usando solo el criterio de
   antigüedad (sin el desempate por distancia), y el orden de visita queda sin optimizar
   hasta que el servicio vuelva a responder — ver `Slicing.md`, Parte B, segunda fila de la
   tabla de excepciones.

---

## 6. Almacenamiento offline (HU-09, HU-10)

- **Service Worker (Workbox):** cachea los assets estáticos de la PWA para que la app
  cargue sin conexión.
- **IndexedDB (Dexie.js):** guarda la cola de dictámenes y fotografías firmados sin
  conexión, en estado `pendiente_sincronizar`, cifrados con AES-GCM (ver sección 4).
- **Sincronización:** un listener de `online`/`offline` del navegador dispara el envío de
  la cola apenas se detecta conexión (RF-33), sin intervención del usuario.

---

## 7. Vacíos cerrados por este documento

| Vacío identificado en la revisión | Resuelto en |
|---|---|
| Stack tecnológico no definido | Sección 1 |
| Sin contrato de API interno | Sección 2 |
| Esquemas de las bases simuladas de la demo | Sección 3 |
| Algoritmo/hash de firma y de contraseñas sin especificar | Sección 4 |
| Mecanismo concreto del segundo factor (RF-51) | Sección 4 |
| Servicio de mapas sin nombrar (RNF-14) | Secciones 1 y 5 |
| Métrica de "eficiencia" sin definir (RF-47, pendiente del DoR) | Sección 5 |
| Tecnología de almacenamiento offline sin decidir (HU-09) | Sección 6 |
| Rate-limiting server-side, tope de sesión, cabeceras HTTP, borrado post-sync | Sección 4 |

**Lo que sigue sin poder cerrarse acá** (depende de terceros, ver reporte de vacíos
anterior en la conversación): el esquema real de campos del SUA y de la Autenticación
Institucional, y la confirmación del indicador de re-derivación — ambos requieren
validación directa con el CIL y el equipo del SUA, no son una decisión de este equipo.
