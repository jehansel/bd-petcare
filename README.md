# Base de Datos - Pet Care

## Diagrama Entidad-Relación:

#### Diagrama

![My Image](./diagramaER.svg)

#### Entidades

- Usuarios
- Mascotas
- Reservas
- Establecimientos
- Servicios
- Pedidos
- Detalle_Pedido

#### Relaciones

- Un `Usuario` _tiene_ multiples `Mascotas`.
- Un `Usuario` _realiza_ multiples `Pedidos`.
- Un `Usuario` _realiza_ multiples `Reservas`.
- Una `Mascota` _incluye_ varias `Reservas`.
- Un `Establecimiento` _alberga_ varias `Reservas`.
- Un `Pedido` _contiene_ multiples `Servicios`.
- Un `Servicio` es _incluido_ en multiples `Pedidos`.

## Esquema en Mysql

![My Image](./mysql_schema.png)

## Script SQL

```sql

-- Creación de base de datos
DROP DATABASE IF EXISTS pet_care;
CREATE DATABASE IF NOT EXISTS pet_care;
USE pet_care;

-- Creación de tablas
DROP TABLE IF EXISTS usuario;
CREATE TABLE IF NOT EXISTS usuario (
	id_usuario SMALLINT UNSIGNED AUTO_INCREMENT,
    nombre VARCHAR(255) NOT NULL,
    apellido VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    contrasenia VARCHAR(255) NOT NULL,
    rol ENUM('admin', 'cliente') NOT NULL,
    telefono VARCHAR(15),
    fecha_registro TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    fecha_actualizacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    es_borrado BOOLEAN DEFAULT FALSE,
    fecha_borrado TIMESTAMP,
    CONSTRAINT pk_usuario
		PRIMARY KEY(id_usuario)
);

DROP TABLE IF EXISTS mascota;
CREATE TABLE IF NOT EXISTS mascota (
    id_mascota SMALLINT UNSIGNED AUTO_INCREMENT,
    id_usuario SMALLINT UNSIGNED NOT NULL,
    nombre VARCHAR(255) NOT NULL,
    tipo ENUM('perro', 'gato', 'otro') NOT NULL,
    raza VARCHAR(255),
    edad INT,
    peso DECIMAL(5, 2),
    notas_adicionales TEXT,
    fecha_registro TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    fecha_actualizacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    es_borrado BOOLEAN DEFAULT FALSE,
    fecha_borrado TIMESTAMP,
    CONSTRAINT pk_mascota
		PRIMARY KEY(id_mascota),
	CONSTRAINT fk_mascota_usuario
		FOREIGN KEY (id_usuario) REFERENCES usuario(id_usuario)
);

DROP TABLE IF EXISTS establecimiento;
CREATE TABLE IF NOT EXISTS establecimiento (
    id_establecimiento SMALLINT UNSIGNED AUTO_INCREMENT,
    nombre VARCHAR(255) NOT NULL,
    direccion VARCHAR(255) NOT NULL,
    telefono VARCHAR(15) NOT NULL,
    capacidad_maxima INT,
    descripcion TEXT,
    imagen_url JSON,
    es_borrado BOOLEAN DEFAULT FALSE,
    fecha_borrado TIMESTAMP,
    CONSTRAINT pk_establecimiento
		PRIMARY KEY(id_establecimiento)
);

DROP TABLE IF EXISTS reserva;
CREATE TABLE IF NOT EXISTS reserva (
    id_reserva SMALLINT UNSIGNED AUTO_INCREMENT,
    id_usuario SMALLINT UNSIGNED NOT NULL,
    id_mascota SMALLINT UNSIGNED NOT NULL,
    id_establecimiento SMALLINT UNSIGNED NOT NULL,
    fecha_inicio DATE NOT NULL,
    fecha_fin DATE NOT NULL,
    estado ENUM('pendiente', 'confirmada', 'cancelada') NOT NULL,
    fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    fecha_actualizacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    es_borrado BOOLEAN DEFAULT FALSE,
    fecha_borrado TIMESTAMP,
    CONSTRAINT pk_reserva
		PRIMARY KEY(id_reserva),
    CONSTRAINT fk_reserva_usuario
		FOREIGN KEY (id_usuario) REFERENCES usuario(id_usuario),
	CONSTRAINT fk_reserva_mascota
		FOREIGN KEY (id_mascota) REFERENCES mascota(id_mascota),
	CONSTRAINT fk_reserva_establecimiento
		FOREIGN KEY (id_establecimiento) REFERENCES establecimiento(id_establecimiento)
);

DROP TABLE IF EXISTS servicio;
CREATE TABLE IF NOT EXISTS servicio (
    id_servicio SMALLINT UNSIGNED AUTO_INCREMENT,
    nombre VARCHAR(255) NOT NULL,
    descripcion TEXT,
    precio DECIMAL(6, 2) NOT NULL,
    imagen_url JSON,
    disponibilidad ENUM('disponible', 'no_disponible') NOT NULL,
    es_borrado BOOLEAN DEFAULT FALSE,
    fecha_borrado TIMESTAMP,
    CONSTRAINT pk_servicio
		PRIMARY KEY(id_servicio)
);

DROP TABLE IF EXISTS pedido;
CREATE TABLE IF NOT EXISTS pedido (
    id_pedido SMALLINT UNSIGNED AUTO_INCREMENT,
    id_usuario SMALLINT UNSIGNED NOT NULL,
    fecha_pedido TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    estado ENUM('pendiente', 'completado', 'cancelado') NOT NULL,
    es_borrado BOOLEAN DEFAULT FALSE,
    fecha_borrado TIMESTAMP,
    CONSTRAINT pk_pedido
		PRIMARY KEY(id_pedido),
	CONSTRAINT fk_pedido_usuario
		FOREIGN KEY (id_usuario) REFERENCES usuario(id_usuario)
);

DROP TABLE IF EXISTS detalle_pedido;
CREATE TABLE IF NOT EXISTS detalle_pedido (
    id_detalle_pedido SMALLINT UNSIGNED AUTO_INCREMENT,
    id_pedido SMALLINT UNSIGNED NOT NULL,
    id_servicio SMALLINT UNSIGNED NOT NULL,
    cantidad SMALLINT NOT NULL,
    precio_unitario DECIMAL(6, 2) NOT NULL,
    es_borrado BOOLEAN DEFAULT FALSE,
    fecha_borrado TIMESTAMP,
    CONSTRAINT pk_detalle_pedido
		PRIMARY KEY(id_detalle_pedido),
	CONSTRAINT fk_detalle_pedido_pedido
		FOREIGN KEY (id_pedido) REFERENCES pedido(id_pedido),
	CONSTRAINT fk_detalle_pedido_servicio
    FOREIGN KEY (id_servicio) REFERENCES servicio(id_servicio)
);

```
