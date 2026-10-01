# Lab P4 — BluePrints en Tiempo Real (Sockets & STOMP)

## Integrantes

- Karol XImena Rodriguez Reyes.
- Juan David Moreno D'Aleman.

---

## Documentación del equipo

Toda la información para configurar e iniciar los servicios está centralizada en [SETUP.md](SETUP.md). Los documentos de pruebas y evidencias son:

- [SETUP.md](SETUP.md): instrucciones de instalación, configuración y ejecución.
- [FRONT_TEST.md](src/resources/FRONT_TEST.md): pruebas funcionales y de colaboración del Front.
- [BACK_TEST.md](src/resources/BACK_TEST.md): pruebas y endpoints del Back.

---

> **Repositorio:** `DECSIS-ECI/Lab_P4_BluePrints_RealTime-Sokets`
>
> **Front:** React + Vite (Canvas, REST CRUD y selector RT)
> **Implementación del equipo:** Back Spring Boot + PostgreSQL y tiempo real con STOMP. Socket.IO queda como alternativa de referencia y no está conectado a esta aplicación.

## 🎯 Objetivo del laboratorio
Implementar **colaboración en tiempo real** para el caso de BluePrints. El Front consume la API CRUD de la Parte 3 y usa **STOMP** para que múltiples clientes dibujen el mismo plano de forma simultánea.

Al finalizar, el equipo debe:
1. Integrar el Front con su **API CRUD** (listar/crear/actualizar/eliminar planos, y total de puntos por autor).
2. Conectar el Front al backend de tiempo real mediante STOMP.
3. Demostrar **colaboración en vivo** (dos pestañas navegando el mismo plano).

---

## 🧩 Alcance y criterios funcionales
- **CRUD** (REST):
  - `GET /api/blueprints` → lista todos los planos.
  - `GET /api/blueprints/{author}` → lista los planos de un autor.
  - `GET /api/blueprints/{author}/{name}` → puntos del plano.
  - `POST /api/blueprints` → crear.
  - `PUT /api/blueprints/{author}/{name}` → actualizar.
  - `DELETE /api/blueprints/{author}/{name}` → eliminar.
- **Tiempo real (RT, implementado por el equipo):**
  - **STOMP** (topics): `/app/draw` → broadcast a `/topic/blueprints.{author}.{name}`.
- **UI**:
  - Canvas con **dibujo por clic** (incremental).
  - Panel del autor: **tabla** de planos y **total de puntos** (`reduce`).
  - Barra de acciones: **Create / Save/Update / Delete** y selector RT (`None` / `STOMP`).
- **DX/Calidad**: código limpio, manejo de errores, README de equipo.

---

## 🏗️ Arquitectura (visión rápida)

```
React (Vite)
 ├─ HTTP (REST CRUD + estado inicial) ───────────────> Tu API (P3 / propia)
 └─ STOMP: /app/draw -> /topic/blueprints.* ────────> Spring WebSocket/STOMP
```

**Convenciones recomendadas**  
- **Plano como canal/sala**: `blueprints.{author}.{name}`  
- **Payload de punto**: `{ x, y }`

---

## 📦 Referencias de tiempo real
- **STOMP (Spring Boot, usado por este equipo):** https://github.com/DECSIS-ECI/example-backend-stopm/tree/main
  - El cliente publica en `/app/draw` y se suscribe al tópico `/topic/blueprints.{author}.{name}`.
- **Socket.IO (alternativa de referencia, no integrada):** https://github.com/DECSIS-ECI/example-backend-socketio-node-/blob/main/README.md

---

---

## 🚀 Puesta en marcha

Consulta [SETUP.md](SETUP.md) para los pasos completos de instalación, configuración y ejecución del laboratorio.

---

## 🔌 Protocolos de Tiempo Real (detalle mínimo)

### A) Socket.IO (alternativa de referencia; no conectada en este proyecto)
- **Unirse a sala**
  ```js
  socket.emit('join-room', `blueprints.${author}.${name}`)
  ```
- **Enviar punto**
  ```js
  socket.emit('draw-event', { room, author, name, point: { x, y } })
  ```
- **Recibir actualización**
  ```js
  socket.on('blueprint-update', (upd) => { /* append points y repintar */ })
  ```

### B) STOMP
- **Publicar punto**
  ```js
  client.publish({ destination: '/app/draw', body: JSON.stringify({ author, name, point }) })
  ```
- **Suscribirse a tópico**
  ```js
  client.subscribe(`/topic/blueprints.${author}.${name}`, (msg) => { /* actualizar estado según evento */ })
  ```

El tópico puede publicar el evento de dibujo `{ author, name, point }` o los eventos `UPDATED` y `DELETED`, que incluyen la lista `points`.

---

## 🧪 Casos de prueba mínimos
- **Estado inicial**: al seleccionar plano, el canvas carga puntos (`GET /api/blueprints/{author}/{name}`).
- **Dibujo local**: clic en canvas agrega puntos y redibuja.  
- **RT multi-pestaña**: con 2 pestañas, los puntos se **replican** casi en tiempo real.  
- **CRUD**: Create/Save/Delete funcionan y refrescan la lista y el **Total** del autor.

---

## 📊 Entregables del equipo
1. Código del Front integrado con **CRUD** y **RT** (Socket.IO o STOMP).  
2. **Video corto** (≤ 90s) mostrando colaboración en vivo y operaciones CRUD.  
3. **README del equipo**: setup, endpoints usados, decisiones (rooms/tópicos), y (opcional) breve comparativa Socket.IO vs STOMP.

Este equipo implementa STOMP; la decisión y los destinos `/app/draw` y `/topic/blueprints.{author}.{name}` están documentados en [BACK_TEST.md](src/resources/BACK_TEST.md). Socket.IO es una alternativa de referencia y no forma parte de la aplicación entregada.

---

## 🧮 Rúbrica sugerida
- **Funcionalidad (40%)**: RT estable (join/broadcast), aislamiento por plano, CRUD operativo.  
- **Calidad técnica (30%)**: estructura limpia, manejo de errores, documentación clara.  
- **Observabilidad/DX (15%)**: logs útiles (conexión, eventos), health checks básicos.  
- **Análisis (15%)**: hallazgos (latencia/reconexión) y, si aplica, pros/cons Socket.IO vs STOMP.

---

## 🩺 Troubleshooting
- **Pantalla en blanco (Front)**: revisa consola; confirma `@vitejs/plugin-react` instalado y que `AppP4.jsx` esté en `src/`.  
- **No hay broadcast**: ambas pestañas deben abrir el mismo plano y suscribirse al tópico STOMP `/topic/blueprints.{author}.{name}`.
- **CORS**: en dev permite `http://localhost:5173`; en prod, **restringe orígenes**.  
- **STOMP no recibe**: verifica `brokerURL`/`webSocketFactory` y los prefijos `/app` y `/topic` en Spring.

---

## 🔐 Seguridad (mínimos)
- Validación de payloads (p. ej., zod/joi).  
- Restricción de orígenes en prod.  
- Opcional: **JWT** + autorización por plano/sala.

---

## 📄 Licencia
MIT (o la definida por el curso/equipo).
