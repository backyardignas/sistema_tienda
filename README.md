# 🛒 Sistema de inventario para tienda — API REST

API REST para administrar los productos de una tienda y registrar sus **entradas y salidas de inventario**. Cada movimiento actualiza el stock automáticamente y el sistema impide vender más unidades de las que hay disponibles.

> Proyecto académico realizado en pareja en **CESDE**, como parte del programa de Desarrollo de Software.
> Portafolio: [backyardignas.github.io](https://backyardignas.github.io)

![Java](https://img.shields.io/badge/Java-21-orange?logo=openjdk)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-4-6DB33F?logo=springboot&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-8-4479A1?logo=mysql&logoColor=white)
![Swagger](https://img.shields.io/badge/Swagger-OpenAPI-85EA2D?logo=swagger&logoColor=black)

---

## ✨ Funcionalidades

- **Productos:** crear, listar, consultar, actualizar y eliminar.
- **Movimientos de inventario:** registrar entradas (`ENTRADA`) y salidas (`SALIDA`) de un producto.
- **Stock automático:** una entrada suma unidades y una salida las resta, todo dentro de una transacción.
- **Consistencia al editar o eliminar:** si se modifica o borra un movimiento, primero se revierte su efecto sobre el stock y luego se aplica el nuevo.
- **Validaciones:** nombre de producto único, precio y stock no negativos, cantidad mínima de 1 y stock suficiente para cada salida.
- **Errores claros:** un manejador global devuelve mensajes en español con el código HTTP adecuado.
- **Documentación interactiva** con Swagger UI para probar todos los endpoints desde el navegador.

## 🧰 Tecnologías

| Área | Herramientas |
|---|---|
| Lenguaje | Java 21 |
| Framework | Spring Boot 4 (Spring Web MVC, Spring Data JPA, Bean Validation) |
| Base de datos | MySQL (con XAMPP en local) |
| Documentación | springdoc-openapi (Swagger UI) |
| Utilidades | Lombok, Maven |

## 🏗️ Arquitectura

El proyecto está organizado por capas para separar responsabilidades:

```
src/main/java/co/edu/cesde/inventarionoche
├── domain/model              → Entidades: Producto, Movimiento y el enum TipoMovimiento
├── application/service       → Interfaces de los servicios
│   └── impl                  → Lógica de negocio (stock, validaciones, transacciones)
├── infrastructure
│   ├── repository            → Repositorios JPA
│   └── exception             → Excepciones propias y manejador global de errores
└── presentation/controller   → Controladores REST
```

## 📡 Endpoints

URL base: `http://localhost:8081`

### Productos — `/api/productos`

| Método | Ruta | Descripción |
|---|---|---|
| `GET` | `/api/productos` | Listar todos los productos |
| `GET` | `/api/productos/{id}` | Obtener un producto por id |
| `POST` | `/api/productos` | Crear un producto |
| `PUT` | `/api/productos` | Actualizar un producto (el `id` va en el cuerpo) |
| `DELETE` | `/api/productos/{id}` | Eliminar un producto |

### Movimientos — `/api/movimientos`

| Método | Ruta | Descripción |
|---|---|---|
| `GET` | `/api/movimientos` | Listar todos los movimientos |
| `GET` | `/api/movimientos/{id}` | Obtener un movimiento por id |
| `POST` | `/api/movimientos` | Registrar una entrada o salida |
| `PUT` | `/api/movimientos` | Actualizar un movimiento (el `id` va en el cuerpo) |
| `DELETE` | `/api/movimientos/{id}` | Eliminar un movimiento y revertir su efecto en el stock |

## 🧪 Ejemplos

**Crear un producto** — `POST /api/productos`

```json
{
  "nombre": "Arroz 1kg",
  "descripcion": "Bolsa de arroz blanco",
  "precio": 4500,
  "stock": 30
}
```

**Registrar una entrada** — `POST /api/movimientos`

```json
{
  "producto": { "id": 1 },
  "cantidad": 20,
  "tipo": "ENTRADA"
}
```

**Intentar una salida sin stock suficiente** — respuesta `400 Bad Request`

```json
{
  "mensaje": "Stock insuficiente para el producto 'Arroz 1kg'. Disponible: 50, solicitado: 80"
}
```

## ⚠️ Manejo de errores

| Situación | Código HTTP | Ejemplo de mensaje |
|---|---|---|
| El recurso no existe | `404 Not Found` | `No existe un producto con id 99` |
| Datos inválidos | `400 Bad Request` | `El nombre es obligatorio` |
| Stock insuficiente | `400 Bad Request` | `Stock insuficiente para el producto '…'` |
| Nombre de producto repetido | `409 Conflict` | `Ya existe un producto con el nombre '…'` |
| Eliminar un producto con movimientos | `409 Conflict` | `No se puede eliminar porque tiene movimientos asociados` |

## 🚀 Cómo ejecutarlo

### Requisitos

- Java 21
- Maven, o un IDE como IntelliJ IDEA que lo incluya
- MySQL corriendo en `localhost:3306` (por ejemplo, con XAMPP)

### Pasos

1. Clona el repositorio:

   ```bash
   git clone https://github.com/backyardignas/sistema_tienda.git
   cd sistema_tienda
   ```

2. Crea la base de datos en MySQL (las tablas se crean solas al iniciar):

   ```sql
   CREATE DATABASE inventario_tienda;
   ```

3. Revisa el usuario y la contraseña de MySQL en `src/main/resources/application.properties`. Por defecto usa el usuario `root` sin contraseña, como en XAMPP.

4. Inicia la aplicación:

   ```bash
   mvn spring-boot:run
   ```

   O ejecuta la clase `InventarioNocheApplication` desde tu IDE.

5. Abre la documentación interactiva en **http://localhost:8081/swagger-ui.html** y prueba los endpoints.

## 👥 Autores

Proyecto realizado en pareja en CESDE.

- **Santiago Arango Quintero** — Desarrollador frontend y estudiante de Desarrollo de Software y Análisis de Datos.
  [Portafolio](https://backyardignas.github.io) · [LinkedIn](https://www.linkedin.com/in/santi-arango-bb6820267/) · [GitHub](https://github.com/backyardignas)
- **Alex** — [GitHub](https://github.com/AlexMonroy69)
