# 🍽️ BASE DE DATOS – SUMAK MIKUY

**Sistema de Gestión de Restaurante**

---

## 📑 TABLA DE CONTENIDOS

1. [Inicialización](#1-inicialización)
2. [Tablas Maestras](#2-tablas-maestras)
3. [Tablas Transaccionales](#3-tablas-transaccionales)
4. [Relaciones](#4-relaciones)

---

## 1. INICIALIZACIÓN

   ```sql
   DROP DATABASE IF EXISTS sumak_mikuy;
   CREATE DATABASE sumak_mikuy CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
   USE sumak_mikuy;
   ```

   **Nota:** Se utiliza UTF-8mb4 para soportar caracteres especiales y emojis.

---

## 2. TABLAS MAESTRAS

   Datos estáticos de referencia del sistema

---

### 2.1 TABLA `usuarios`

   **Descripción:** Gestión de usuarios (clientes, admin, mesero, cocina)

   ```sql
   CREATE TABLE usuarios (
       id INT AUTO_INCREMENT PRIMARY KEY,
       nombres VARCHAR(100) NOT NULL,
       apellidos VARCHAR(100) NOT NULL,
       email VARCHAR(100) NOT NULL UNIQUE,
       password VARCHAR(255) NOT NULL,
       direccion VARCHAR(255) NOT NULL,
       celular VARCHAR(20) NOT NULL,
       provider ENUM('local', 'google', 'facebook') DEFAULT 'local',
       fecha_registro TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
       ultima_sesion TIMESTAMP NULL,
       status ENUM('activo', 'inactivo', 'suspendido') DEFAULT 'activo',
       rol ENUM('cliente', 'admin', 'mesero', 'cocina') DEFAULT 'cliente' NOT NULL,
       INDEX idx_email(email),
       INDEX idx_status(status),
       INDEX idx_rol(rol)
   ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
   ```

   **Insertar datos:**

   ```sql
   INSERT INTO usuarios (nombres, apellidos, email, password, direccion, celular, provider, status, rol) VALUES
   ('Juan', 'Pérez', 'juan.perez@gmail.com', 'hashed_pass', 'Av. Los Incas 123', '987654321', 'local', 'activo', 'cliente'),
   ('María', 'González', 'maria.gonzalez@hotmail.com', 'hashed_pass', 'Jr. Miraflores 456', '912345678', 'google', 'activo', 'admin');
   ```

---

### 2.2 TABLA `mesas`

   **Descripción:** Información de mesas del restaurante

   ```sql
   CREATE TABLE mesas (
       id INT AUTO_INCREMENT PRIMARY KEY,
       numero_mesa INT NOT NULL UNIQUE,
       capacidad INT NOT NULL,
       ubicacion ENUM('interior','exterior','terraza','vip') DEFAULT 'interior',
       descripcion VARCHAR(255),
       status ENUM('disponible','ocupada','reservada','mantenimiento') DEFAULT 'disponible',
       INDEX idx_status(status),
       INDEX idx_numero(numero_mesa)
   ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
   ```

   **Insertar datos:**

   ```sql
   INSERT INTO mesas (numero_mesa, capacidad, ubicacion, descripcion, status) VALUES
   (1, 4, 'interior', 'Mesa cerca de ventana', 'disponible'),
   (2, 2, 'interior', 'Mesa íntima para parejas', 'disponible');
   ```

---

### 2.3 TABLA `categorias_menu`

   **Descripción:** Categorías de platos en el menú

   ```sql
   CREATE TABLE categorias_menu (
       id INT AUTO_INCREMENT PRIMARY KEY,
       nombre VARCHAR(100) NOT NULL,
       descripcion TEXT,
       orden INT DEFAULT 0,
       status ENUM('activo','inactivo') DEFAULT 'activo',
       INDEX idx_orden(orden),
       INDEX idx_status(status)
   ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
   ```

   **Insertar datos:**

   ```sql
   INSERT INTO categorias_menu (nombre, descripcion, orden, status) VALUES
   ('Entradas', 'Entradas andinas', 1, 'activo'),
   ('Platos Principales', 'Platos típicos peruanos', 2, 'activo');
   ```

---

### 2.4 TABLA `platos`

   **Descripción:** Catálogo de platos del menú

   ```sql
   CREATE TABLE platos (
       id INT AUTO_INCREMENT PRIMARY KEY,
       categoria_id INT NOT NULL,
       nombre VARCHAR(150) NOT NULL,
       descripcion TEXT NOT NULL,
       precio DECIMAL(10,2) NOT NULL,
       tiempo_preparacion INT,
       ingredientes TEXT,
       es_vegetariano BOOLEAN DEFAULT FALSE,
       es_picante BOOLEAN DEFAULT FALSE,
       status ENUM('disponible','agotado','descontinuado') DEFAULT 'disponible',
       FOREIGN KEY (categoria_id) REFERENCES categorias_menu(id) ON DELETE RESTRICT,
       INDEX idx_categoria(categoria_id),
       INDEX idx_status(status)
   ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
   ```

   **Insertar datos:**

   ```sql
   INSERT INTO platos (categoria_id, nombre, descripcion, precio, tiempo_preparacion, ingredientes, es_vegetariano, es_picante, status) VALUES
   (1, 'Anticuchos', 'Corazón de res marinado', 25.00, 30, 'Corazón de res, ají panca, papa', FALSE, TRUE, 'disponible'),
   (2, 'Lomo saltado', 'Carne de res, cebolla y papas fritas', 35.00, 25, 'Carne de res, cebolla, tomate, papas, arroz', FALSE, FALSE, 'disponible');
   ```

---

## 3. TABLAS TRANSACCIONALES

   Registros de operaciones y movimientos del sistema

---

### 3.1 TABLA `reservas`

   **Descripción:** Registro de reservas de mesas

   ```sql
   CREATE TABLE reservas (
       id INT AUTO_INCREMENT PRIMARY KEY,
       usuario_id INT,
       mesa_id INT NOT NULL,
       numero_personas INT NOT NULL,
       fecha_reserva DATE NOT NULL,
       hora_reserva TIME NOT NULL,
       fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
       notas TEXT,
       status ENUM('pendiente','confirmada','completada','cancelada','no_asistio') DEFAULT 'pendiente',
       FOREIGN KEY (usuario_id) REFERENCES usuarios(id) ON DELETE SET NULL,
       FOREIGN KEY (mesa_id) REFERENCES mesas(id) ON DELETE RESTRICT,
       INDEX idx_fecha(fecha_reserva),
       INDEX idx_status(status)
   ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
   ```

   **Insertar datos:**

   ```sql
   INSERT INTO reservas (usuario_id, mesa_id, numero_personas, fecha_reserva, hora_reserva, notas, status) VALUES
   (1, 3, 6, '2025-10-10', '19:00:00', 'Cumpleaños', 'confirmada');
   ```

---

### 3.2 TABLA `pedidos`

   **Descripción:** Registro de pedidos realizados

   ```sql
   CREATE TABLE pedidos (
       id INT AUTO_INCREMENT PRIMARY KEY,
       usuario_id INT,
       mesa_id INT,
       reserva_id INT,
       fecha_pedido TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
       total DECIMAL(10,2) NOT NULL,
       metodo_pago ENUM('efectivo','tarjeta','yape','plin','transferencia') DEFAULT 'efectivo',
       notas_especiales TEXT,
       status ENUM('pendiente','en_preparacion','listo','entregado','cancelado') DEFAULT 'pendiente',
       FOREIGN KEY (usuario_id) REFERENCES usuarios(id) ON DELETE SET NULL,
       FOREIGN KEY (mesa_id) REFERENCES mesas(id) ON DELETE SET NULL,
       FOREIGN KEY (reserva_id) REFERENCES reservas(id) ON DELETE SET NULL,
       INDEX idx_fecha(fecha_pedido),
       INDEX idx_status(status)
   ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
   ```

   **Insertar datos:**

   ```sql
   INSERT INTO pedidos (usuario_id, mesa_id, reserva_id, total, metodo_pago, notas_especiales, status) VALUES
   (1, 3, 1, 180.00, 'tarjeta', 'Sin cebolla', 'entregado');
   ```

---

### 3.3 TABLA `detalle_pedidos`

   **Descripción:** Detalles de items en cada pedido

   ```sql
   CREATE TABLE detalle_pedidos (
       id INT AUTO_INCREMENT PRIMARY KEY,
       pedido_id INT NOT NULL,
       plato_id INT NOT NULL,
       cantidad INT NOT NULL DEFAULT 1,
       precio_unitario DECIMAL(10,2) NOT NULL,
       subtotal DECIMAL(10,2) AS (cantidad * precio_unitario) STORED,
       notas VARCHAR(255),
       status ENUM('pendiente','preparando','listo','entregado') DEFAULT 'pendiente',
       FOREIGN KEY (pedido_id) REFERENCES pedidos(id) ON DELETE CASCADE,
       FOREIGN KEY (plato_id) REFERENCES platos(id) ON DELETE RESTRICT,
       INDEX idx_pedido(pedido_id),
       INDEX idx_plato(plato_id)
   ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
   ```

   **Insertar datos:**

   ```sql
   INSERT INTO detalle_pedidos (pedido_id, plato_id, cantidad, precio_unitario, notas, status) VALUES
   (1, 1, 2, 25.00, 'Término medio', 'entregado');
   ```

---

## 4. RELACIONES

   | Relación | Tipo | Descripción |
   |----------|------|-------------|
   | usuarios → reservas | 1:N | Un usuario tiene múltiples reservas |
   | usuarios → pedidos | 1:N | Un usuario realiza múltiples pedidos |
   | mesas → reservas | 1:N | Una mesa tiene múltiples reservas |
   | mesas → pedidos | 1:N | Una mesa tiene múltiples pedidos |
   | categorias_menu → platos | 1:N | Una categoría contiene múltiples platos |
   | platos → detalle_pedidos | 1:N | Un plato aparece en múltiples pedidos |
   | pedidos → detalle_pedidos | 1:N | Un pedido contiene múltiples items |

---

**Última actualización:** 24 de Noviembre, 2025
