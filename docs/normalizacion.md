# Normalización de la Base de Datos
### Proyecto: E-commerce Tecnológico — Innovaciber

---

## 1. Objetivo

El objetivo de este diseño es construir una base de datos relacional eficiente, escalable y libre de redundancias para la empresa **Innovaciber**, asegurando la integridad de los datos y el cumplimiento de la **Tercera Forma Normal (3NF)**.

---

## 2. Modelo de Datos

El sistema está compuesto por las siguientes entidades:

- `customers`
- `orders`
- `order_items`
- `products`
- `categories`
- `payments`
- `shipments`
- `reviews`

Estas tablas representan los principales procesos del negocio: gestión de clientes, catálogo de productos, ventas, pagos, envíos y reseñas.

---

## 3. Reglas de Negocio

El diseño se basa en las siguientes reglas:

- Un cliente puede realizar múltiples pedidos **(1:N)**
- Un pedido pertenece a un único cliente
- Un pedido contiene uno o varios productos
- La relación entre pedidos y productos se resuelve mediante la tabla `order_items`
- Un producto pertenece a una sola categoría
- Un cliente puede dejar múltiples reseñas
- Una reseña está asociada a un único producto
- Cada pedido tiene un único pago asociado
- Cada pedido tiene un único envío asociado

---

## 4. Proceso de Normalización

### 4.1 Primera Forma Normal (1NF)

Se cumple porque:

- Cada campo contiene valores atómicos (no divisibles)
- No existen atributos multivaluados
- Cada registro es único e identificado mediante una clave primaria

> **Ejemplo:** En la tabla `customers`, cada campo como `first_name` o `email` contiene un único valor por fila.

---

### 4.2 Segunda Forma Normal (2NF)

Se cumple porque:

- Todas las tablas tienen clave primaria definida
- No existen dependencias parciales
- Todos los atributos dependen completamente de su clave primaria

> **Ejemplo:** En la tabla `order_items`, los atributos `quantity` y `unit_price` dependen completamente de `order_item_id`.

---

### 4.3 Tercera Forma Normal (3NF)

Se cumple porque:

- No existen dependencias transitivas
- Los atributos dependen únicamente de la clave primaria

> **Ejemplos:**
> - En la tabla `products`, la categoría no se repite, sino que se referencia mediante `category_id`
> - La información del cliente no se duplica en `orders`, sino que se relaciona mediante `customer_id`
> - Los datos de pago y envío se separan en las tablas `payments` y `shipments`

---

## 5. Decisiones de Diseño

### 5.1 Separación de `order_items`

Se crea la tabla `order_items` para resolver la relación **muchos a muchos** entre pedidos y productos. Esto permite que un pedido contenga múltiples productos y que un producto esté presente en múltiples pedidos.

### 5.2 Almacenamiento de `unit_price`

El precio unitario se almacena en `order_items` para mantener un **histórico de precios** y evitar inconsistencias en caso de cambios en el precio del producto.

### 5.3 Separación de `categories`

Se evita la redundancia de datos agrupando las categorías en una tabla independiente, referenciada desde `products`.

### 5.4 Modularidad del sistema

Las tablas `payments` y `shipments` están separadas de `orders` para permitir una mayor **flexibilidad y escalabilidad** en la gestión de pagos y logística.

### 5.5 Independencia de entidades

Cada tabla representa un concepto específico del negocio, lo que facilita el **mantenimiento, la escalabilidad y la comprensión** del modelo.

---

## 6. Conclusión

El modelo de datos diseñado para **Innovaciber** cumple con los principios de la **Tercera Forma Normal (3NF)**, garantizando la reducción de redundancia, la integridad de los datos y la escalabilidad del sistema. Este diseño permite soportar de manera eficiente las operaciones de un e-commerce tecnológico.