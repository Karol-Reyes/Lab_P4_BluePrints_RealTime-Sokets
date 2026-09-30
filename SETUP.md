# Setup de BluePrints — Parte 4

En este repositorio, tenemos organizaso el entregable de documentación y evidencia del equipo. El código vive en dos repos separados:

- **Backend**: https://github.com/Karol-Reyes/Back_Blueprint
- **Frontend**: https://github.com/Karol-Reyes/Front_Blueprint

## Orden de inicialización

### 1. Base de datos (PostgreSQL)

```bash
git clone https://github.com/Karol-Reyes/Back_Blueprint.git
cd Back_Blueprint
docker-compose up -d
```

Verificamos con `docker ps` que el contenedor de Postgres quedó `Up`.

---

### 2. Backend

Desde la misma carpeta `Back_Blueprint`:

```bash
mvn clean install
mvn spring-boot:run
```

Debe arrancar en `http://localhost:8080`. 

Confirmamos que en los logs que conecta a Postgres (`HikariPool-1 - Added connection`) y que expone el endpoint de WebSocket en `/ws-blueprints`.

---

### 3. Frontend

```bash
git clone https://github.com/Karol-Reyes/Front_Blueprint.git
cd Front_Blueprint
npm install
npm run dev
```
Una vez que aparezca corriendo, entrar al link `http://localhost:5173`.