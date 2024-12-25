# Script_Tienda
# Este script se ejecutara en un ambiente de SQLServer
# Nota: Ejecutar el script en el orden que se encuentra

-- ========================================
-- 1. Tabla DEPARTAMENTO
-- ========================================
CREATE TABLE departamento (
    id_departamento      INT             IDENTITY(1,1) NOT NULL,
    nombre_departamento  VARCHAR(50)     NOT NULL,
    descripcion          VARCHAR(200)    NULL,
    CONSTRAINT PK_DEPARTAMENTO PRIMARY KEY (id_departamento)
);
GO

-- ========================================
-- 2. Tabla CLIENTE
-- ========================================
CREATE TABLE cliente (
    id_cliente   INT             IDENTITY(1,1) NOT NULL,
    nombre       VARCHAR(100)    NOT NULL,
    telefono     VARCHAR(20)     NULL,
    direccion    VARCHAR(200)    NULL,
    -- Otros atributos que necesites...
    CONSTRAINT PK_CLIENTE PRIMARY KEY (id_cliente)
);
GO

-- ========================================
-- 3. Tabla PROMOCION
-- ========================================
CREATE TABLE promocion (
    id_promocion             INT             IDENTITY(1,1) NOT NULL,
    porcentaje_descuento      DECIMAL(5,2)   NOT NULL,  -- ej: 15.00 = 15%
    min_pedidos_requeridos    INT            NOT NULL,  -- ej: 2
    -- Otros atributos como fechas de vigencia, etc.
    CONSTRAINT PK_PROMOCION PRIMARY KEY (id_promocion)
);
GO

-- ========================================
-- 4. Tabla PRODUCTO
-- ========================================
CREATE TABLE producto (
    id_producto      INT              IDENTITY(1,1) NOT NULL,
    nombre_producto  VARCHAR(100)     NOT NULL,
    precio           DECIMAL(12,2)    NOT NULL,
    color            VARCHAR(30)      NOT NULL,
    talla            VARCHAR(10)      NOT NULL,
    es_outlet        BIT              NOT NULL,     -- 1 = es outlet
    stock            INT              NOT NULL,     -- Inventario disponible
    id_departamento  INT              NOT NULL,
    CONSTRAINT PK_PRODUCTO PRIMARY KEY (id_producto),
    CONSTRAINT FK_PRODUCTO_DEPARTAMENTO 
        FOREIGN KEY (id_departamento)
        REFERENCES DEPARTAMENTO (id_departamento)
);
GO

-- ========================================
-- 5. Tabla PEDIDO
-- ========================================
CREATE TABLE pedido (
    id_pedido     INT             IDENTITY(1,1) NOT NULL,
    fecha_pedido  DATE            NOT NULL,
    id_cliente    INT             NOT NULL,
    total_pedido  DECIMAL(12,2)   NOT NULL,
    -- Otros campos que necesites: estado, forma de pago, etc.
    CONSTRAINT PK_PEDIDO PRIMARY KEY (id_pedido),
    CONSTRAINT FK_PEDIDO_CLIENTE 
        FOREIGN KEY (id_cliente)
        REFERENCES CLIENTE (id_cliente)
);
GO

-- ========================================
-- 6. Tabla DETALLE_PEDIDO
-- ========================================
CREATE TABLE detalle_pedido (
    id_pedido       INT             NOT NULL,
    id_producto     INT             NOT NULL,
    cantidad        INT             NOT NULL,
    precio_unitario DECIMAL(12,2)   NOT NULL,
    CONSTRAINT PK_DETALLE_PEDIDO PRIMARY KEY (id_pedido, id_producto),
    CONSTRAINT FK_DETALLE_PEDIDO_PEDIDO 
        FOREIGN KEY (id_pedido)
        REFERENCES PEDIDO (id_pedido),
    CONSTRAINT FK_DETALLE_PEDIDO_PRODUCTO 
        FOREIGN KEY (id_producto)
        REFERENCES PRODUCTO (id_producto)
);
GO

-- ========================================
-- 7. Tabla PLAN_SEPARE
-- ========================================
CREATE TABLE plan_separe (
    id_plan_separe   INT             IDENTITY(1,1) NOT NULL,
    fecha_apartado   DATE            NOT NULL,
    id_cliente       INT             NOT NULL,
    total_apartado   DECIMAL(12,2)   NOT NULL, -- Suma de productos separados
    id_promocion     INT             NULL,      -- Puede ser NULL si no aplica
    CONSTRAINT PK_PLAN_SEPARE PRIMARY KEY (id_plan_separe),
    CONSTRAINT FK_PLAN_SEPARE_CLIENTE 
        FOREIGN KEY (id_cliente)
        REFERENCES CLIENTE (id_cliente),
    CONSTRAINT FK_PLAN_SEPARE_PROMOCION 
        FOREIGN KEY (id_promocion)
        REFERENCES PROMOCION (id_promocion)
);
GO

-- ========================================
-- 8. Tabla DETALLE_PLAN_SEPARE
-- ========================================
CREATE TABLE detalle_plan_separe (
    id_plan_separe INT             NOT NULL,
    id_producto    INT             NOT NULL,
    cantidad       INT             NOT NULL,
    subtotal       DECIMAL(12,2)   NOT NULL, -- Monto parcial de ese producto
    CONSTRAINT PK_DETALLE_PLAN_SEPARE PRIMARY KEY (id_plan_separe, id_producto),
    CONSTRAINT FK_DETALLE_PLAN_SEPARE_PLAN_SEPARE 
        FOREIGN KEY (id_plan_separe)
        REFERENCES PLAN_SEPARE (id_plan_separe),
    CONSTRAINT FK_DETALLE_PLAN_SEPARE_PRODUCTO 
        FOREIGN KEY (id_producto)
        REFERENCES PRODUCTO (id_producto)
);
GO
