# Ejecución de pruebas

## Requisitos

- JDK 25 activo.
- Maven 3.9 o posterior.
- Docker y Docker Compose para integración MongoDB.
- Node.js y npm para Newman.

Comprobar el JDK efectivo:

```bash
java -version
mvn -version
printf '%s\n' "$JAVA_HOME"
```

## Pruebas unitarias

```bash
mvn clean test
```

Surefire ejecuta las pruebas `*Test` y excluye `*IntegrationTest`.

Las rutas de carrito, domicilios, compras y pedidos requieren un JWT cuyo `sub` coincida con el `usuarioId` de la URL. Un usuario autenticado sobre el recurso de otro usuario recibe HTTP 403.

## Pruebas de integración

Levantar MongoDB de pruebas:

```bash
docker compose up -d mongodb-test mongodb-test-init
export MONGODB_TEST_URI='mongodb://127.0.0.1:27018/metcoffee_test?replicaSet=rs0&directConnection=true'
mvn -Pintegration verify
```

La integración usa repositorios Mongo reales y la base `metcoffee_test`. Nunca hereda la URI de Atlas. `MongoIntegrationTest` contiene exactamente tres pruebas: lectura/escritura real, rollback de compra y autorización de pedido entre dos usuarios.

## Todas las pruebas y empaquetado

```bash
mvn clean test
mvn -Pintegration verify
mvn package -DskipTests
```

## API

Iniciar la aplicación de prueba:

```bash
mvn spring-boot:run -Dspring-boot.run.profiles=test
```

Después consultar la salud:

```bash
curl http://localhost:8080/actuator/health
```

Ejecutar Newman:

```bash
cd postman
npm install
npm run test:api:report
```

Los reportes quedan en `postman/reports/`.

El registro y login del cliente se ejecutan en orden. El login guarda automáticamente el JWT en `clienteToken` y el ID en `usuarioId`. El login administrativo utiliza `adminCorreo` y `adminPassword` locales, está omitido si están vacíos y guarda el JWT en `adminToken` cuando se habilita con credenciales de un administrador de pruebas.

## Limpieza

```bash
docker compose down -v
```

La limpieza elimina solo el almacenamiento Docker de pruebas.
