# Setup de BluePrints — Parte 4

Este repositorio reúne la documentación y evidencia del equipo. El código vive en dos repositorios separados:

- **Backend**: https://github.com/Karol-Reyes/Back_Blueprint
- **Frontend**: https://github.com/Karol-Reyes/Front_Blueprint

## Documentación del equipo

- [README.md](README.md): resumen del laboratorio y rutas de la documentación.
- [FRONT_TEST.md](src/resources/FRONT_TEST.md): pruebas funcionales y evidencias del Front.
- [BACK_TEST.md](src/resources/BACK_TEST.md): pruebas, endpoints y decisiones del Back.

## Orden de inicialización

Requisitos: Docker Desktop, Java 21, Maven y Node.js/npm. Inicia los servicios en el orden indicado y deja abiertas las terminales del Back y del Front mientras realizas las pruebas.

### 1. Base de datos (PostgreSQL)

```bash
git clone https://github.com/Karol-Reyes/Back_Blueprint.git
cd Back_Blueprint
docker compose up -d db
```

Verifica con `docker compose ps` que `postgres-dev` aparece como `Up` y publica el puerto `5432`.

---

### 2. Backend

Desde otra terminal, en la carpeta `Back_Blueprint`:

```bash
mvn clean install
mvn spring-boot:run
```

El Back debe escuchar en `http://localhost:8080`. En los logs confirma la conexión a PostgreSQL (`HikariPool-1 - Added connection`) y el inicio del broker STOMP. El endpoint WebSocket es `/ws-blueprints`.

---

### 3. Frontend

```bash
git clone https://github.com/Karol-Reyes/Front_Blueprint.git
cd Front_Blueprint
npm install
cp .env.example .env
npm run dev
```
Las variables de `.env.example` apuntan REST y STOMP a `http://localhost:8080` y desactivan el mock. Vite debe mostrar `http://localhost:5173`.

## Verificación rápida

1. Abre `http://localhost:5173` e inicia sesión con el usuario de prueba `student` y la contraseña `student123`.
2. Crea o consulta un plano y ábrelo.
3. Para colaboración, abre el mismo plano en dos pestañas, selecciona `STOMP` en ambas y comprueba que un clic se replica.
4. Prueba `Save / Update` y `Delete`; consulta [FRONT_TEST.md](src/resources/FRONT_TEST.md) y [BACK_TEST.md](src/resources/BACK_TEST.md) para los casos detallados.

## Detener servicios

- Detén el Front y el Back con `Ctrl+C` en sus respectivas terminales.
- Detén PostgreSQL desde `Back_Blueprint` con `docker compose down`.
- No uses `docker compose down -v` si quieres conservar el volumen de datos.