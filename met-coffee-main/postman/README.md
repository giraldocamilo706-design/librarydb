# Pruebas de API

Estas pruebas deben ejecutarse únicamente contra una instancia local de Met Coffee configurada con la base MongoDB de pruebas. No uses esta colección contra MongoDB Atlas.

## Preparación

Desde la raíz del proyecto:

```bash
docker compose up -d mongodb-test mongodb-test-init
export MONGODB_TEST_URI='mongodb://127.0.0.1:27018/metcoffee_test?replicaSet=rs0&directConnection=true'
```

Inicia la aplicación con el perfil de pruebas:

```bash
mvn spring-boot:run -Dspring-boot.run.profiles=test
```

Importa en Postman:

- `MetCoffee.postman_collection.json`
- `MetCoffee.postman_environment.json`

La colección crea un usuario cliente y guarda su token e identificadores en variables. Las solicitudes administrativas requieren que `adminToken` contenga un JWT de un usuario de pruebas con rol `ADMINISTRADOR`.

El login del cliente guarda automáticamente el JWT en `clienteToken` y el identificador en `usuarioId`. Las rutas de carrito, domicilios, compras y pedidos usan ese token sin copiarlo manualmente.

La solicitud `Iniciar sesión administrador (requiere variables locales)` está deshabilitada por defecto. Para usarla, define localmente `adminCorreo` y `adminPassword`, habilítala en Postman y ejecútala; su respuesta guarda el JWT en `adminToken`. El usuario administrador debe prepararse previamente en la base de pruebas mediante un mecanismo administrativo controlado. No existe un registro público de administradores.

## Newman

```bash
cd postman
npm install
npm run test:api
npm run test:api:report
```

Los reportes se generan en `postman/reports/`. El flujo de compra necesita un producto creado y disponible; las solicitudes administrativas están incluidas para preparar esos datos cuando exista un `adminToken` válido. Ejecuta las solicitudes en orden: Cuenta, Catálogo, Administración para crear producto, Bolsa/carrito, Domicilios y compras, y Pedidos.

## Limpieza

```bash
docker compose down -v
```

El comando elimina únicamente el volumen `metcoffee-mongodb-test-data` del entorno local de pruebas.
