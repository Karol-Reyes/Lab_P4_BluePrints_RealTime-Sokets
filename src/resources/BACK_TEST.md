# Probando Funcionamiento del Back

## Iniciando

Para poder autenticarse toca:

1. Iniciar sesión 
    - usuario: `student`
    - contraseña: `student123`
2. Abrir el mismo blueprint en dos pestañas del navegador.
3. Dibujar un punto en una pestaña, y debe aparecer en la otra sin recargar.

Como se prueba con back solamente, lo haremos en postman probando los Update y Delete.

Al realizar estas pruebas tenemos los siguientes resultados.

**Put**
![](/src/resources/img/putB.png)
**Delete Funcional**
![](/src/resources/img/delete.png)
**Delete Fallido (Despupués del funcional)**
![](/src/resources/img/deleteF.png)

## Endpoints usados
### REST
| Método | Ruta | Scope requerido | Descripción |
|---|---|---|---|
| POST | /auth/login | — (público) | Login, devuelve JWT |
| GET | /api/blueprints | blueprints.read | Lista todos los planos |
| GET | /api/blueprints/{author} | blueprints.read | Planos de un autor |
| GET | /api/blueprints/{author}/{name} | blueprints.read | Un plano específico |
| POST | /api/blueprints | blueprints.write | Crea un plano |
| POST | /api/blueprints/{author}/{name}/points | blueprints.write | Agrega un punto |
| PUT | /api/blueprints/{author}/{name} | blueprints.write | Reemplaza todos los puntos |
| DELETE | /api/blueprints/{author}/{name} | blueprints.write | Elimina un plano |

### Tiempo real (STOMP)
- Endpoint de conexión: `ws://localhost:8080/ws-blueprints`
- Publicar un punto: `/app/draw` con body `{ author, name, point: {x, y} }`
- Suscribirse a un plano: `/topic/blueprints.{author}.{name}`

## Decisiones

- **STOMP sobre Socket.IO**: el backend es Spring Boot, y `spring-boot-starter-websocket` se integra sin levantar un servidor Node aparte.
- **Un tópico por plano** (`/topic/blueprints.{author}.{name}`), no un tópico global, cada cliente solo recibe actualizaciones del plano que tiene abierto.
- **El endpoint de WebSocket no exige JWT**: STOMP no manda `Authorization` en el handshake inicial de forma nativa. Se dejó abierto para el lab;
- **Sin lógica duplicada**: `DrawingController` reutiliza `BlueprintsServices.addPoint(...)`, el mismo método que usa el endpoint REST, un punto llegado por socket se persiste en Postgres igual que uno llegado por HTTP.
