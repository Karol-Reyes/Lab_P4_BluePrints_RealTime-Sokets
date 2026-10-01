# Probando Funcionamiento del Front

## Preparación

Antes de realizar las pruebas, iniciar PostgreSQL y el backend siguiendo las instrucciones de [SETUP.md](../../SETUP.md). Después, iniciar el Front y abrir `http://localhost:5173`.

El backend debe estar disponible en `http://localhost:8080`. El Front usa la API REST para autenticación y operaciones CRUD, y STOMP para sincronizar los cambios en tiempo real.

**NOTA:** toda la evidencia se encuentra en el video al final de este archivo.

## Autenticación

1. Abrir la opción **Login**.
2. Ingresar las credenciales de prueba:
	- Usuario: `student`
	- Contraseña: `student123`
3. Confirmar que el inicio de sesión es exitoso antes de consultar o modificar blueprints.


## Pruebas funcionales

### Crear y consultar un blueprint

1. Crear un blueprint indicando autor, nombre y puntos iniciales.
2. Consultar los blueprints de ese autor con **Get blueprints**.
3. Confirmar que aparece en la tabla con su cantidad de puntos y que el total del autor se actualiza.
4. Abrir el blueprint y confirmar que sus puntos se muestran en el canvas.


### Dibujo local

Con el modo RT en **None**, hacer clic en el canvas y confirmar que el punto se agrega localmente. Usar **Save / Update** para persistir los puntos en el backend.


### Colaboración en tiempo real

1. Abrir el mismo blueprint, con el mismo autor y nombre, en dos pestañas.
2. Seleccionar **STOMP** en ambas y esperar a que la conexión indique `connected`.
3. Dibujar un punto en una pestaña y confirmar que aparece en la otra sin recargar.
4. Guardar o actualizar el blueprint y confirmar que ambas pestañas muestran la colección de puntos actualizada.
5. Eliminar el blueprint desde una pestaña y confirmar que también desaparece de la selección y la lista de la otra.

Los mensajes recibidos por el tópico `/topic/blueprints.{author}.{name}` pueden ser:

- Evento de dibujo: `{ "author": "w", "name": "z", "point": { "x": 1, "y": 1 } }`.
- Actualización: `{ "type": "UPDATED", "points": [{ "x": 1, "y": 1 }, { "x": 2, "y": 2 }] }`.
- Eliminación: `{ "type": "DELETED", "points": [] }`.

El Front identifica el evento por `type`: agrega el punto para el evento de dibujo, reemplaza todos los puntos en `UPDATED` y quita el blueprint en `DELETED`.


### Actualizar y eliminar

- **Save / Update** envía los puntos actuales del canvas mediante `PUT /api/blueprints/{author}/{name}`.
- **Delete** elimina el blueprint mediante `DELETE /api/blueprints/{author}/{name}`.
- Después de cada operación, verificar que la tabla y el total del autor reflejen el estado actualizado.

## Resultado

Las pruebas del Front permiten verificar la autenticación, el CRUD, el conteo de puntos y la colaboración STOMP entre clientes. La guía para iniciar los servicios y la configuración del entorno está en [SETUP.md](../../SETUP.md); las pruebas del backend están en [BACK_TEST.md](BACK_TEST.md).

### Video:




https://github.com/user-attachments/assets/a3a3db97-826f-4061-82bb-ec207c7718fa




