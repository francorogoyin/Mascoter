# Mascoter - Documento de diseño

## 1. Vision general

Sistema web de gestion integral para pet shop. Permite
administrar ventas, productos, stock, clientes, proveedores,
caja diaria y estadisticas del negocio. Multiusuario con
roles (Admin y Vendedor).

---

## 2. Paneles del sistema

El sistema se organiza en paneles accesibles desde una barra
de navegacion lateral:

1. **Ventas (POS)** - Punto de venta principal.
2. **Productos** - CRUD de productos, categorias, stock.
3. **Clientes** - Gestion de clientes y sus datos.
4. **Proveedores** - Gestion de proveedores y cuentas
   corrientes.
5. **Caja** - Apertura, cierre y arqueo de caja diaria.
6. **Estadisticas** - Dashboard con metricas y reportes.
7. **Configuracion** - Usuarios, roles, metodos de pago,
   datos del negocio.

---

## 3. Panel de ventas (POS)

### 3.1 Layout

Pantalla dividida en dos secciones:

```
┌─────────────────────────┬──────────────────┐
│  [🔍 Buscar producto..] │  CARRITO          │
│  [Lista|Detalle|Grilla] │                  │
│                          │  Arroz x3kg     │
│  ┌──────┐ ┌──────┐      │         $3.000   │
│  │Prod A│ │Prod B│      │  Prod A x2       │
│  │ $500 │ │ $300 │      │         $1.000   │
│  └──────┘ └──────┘      │  Prod B x1       │
│  ┌──────┐ ┌──────┐      │           $300   │
│  │Prod C│ │Prod D│      │                  │
│  │ $150 │ │ $800 │      │  Descuento: -10% │
│  └──────┘ └──────┘      │──────────────────│
│                          │  TOTAL: $3.870   │
│  [Cliente: Sin cliente▾]│  [COBRAR]        │
└─────────────────────────┴──────────────────┘
```

### 3.2 Buscador de productos

- Barra de texto en la parte superior izquierda.
- Busca por nombre, codigo o categoria.
- Los resultados aparecen debajo a medida que se escribe.
- Tres modos de visualizacion:
  - **Lista simple:** nombre + precio en filas compactas.
  - **Lista compleja:** nombre + precio + foto + stock.
  - **Cuadricula:** tarjetas con foto, nombre y precio.

### 3.3 Agregar productos al carrito

- Un click en un producto lo agrega al carrito (1 unidad).
- Clicks sucesivos incrementan la cantidad.
- En el carrito se puede editar manualmente la cantidad.
- Se puede modificar el precio de venta de cada item en
  el carrito (precio personalizado para esa venta).

### 3.4 Productos a granel

- Los productos marcados como "granel" permiten dos modos
  de ingreso:
  - **Por unidades:** se ingresa la cantidad (ej: 2.5 kg).
  - **Por monto:** se ingresa el monto en pesos y el sistema
    calcula las unidades automaticamente.
    Ejemplo: producto a $1.000/kg, se ingresa $3.000,
    el sistema pone 3 unidades.

### 3.5 Descuentos

Se pueden aplicar descuentos de varias formas:

- **Por monto fijo:** restar un monto al total o a un item
  (ej: -$200).
- **Por porcentaje:** aplicar un porcentaje de descuento
  (ej: -15%).
- **Al costo:** dejar el precio de venta igual al precio
  de costo.
- **Por cliente:** descuento automatico configurado en la
  ficha del cliente. Al seleccionar el cliente en la venta,
  se aplica su descuento predeterminado.

### 3.6 Seleccion de cliente

- Selector de cliente en la parte inferior izquierda.
- Opcional: se puede vender sin cliente (consumidor final).
- Al seleccionar un cliente, se aplican sus descuentos
  automaticamente.

### 3.7 Cobro y cierre de venta

Al presionar "Cobrar" se abre un modal con:

- **Total a cobrar.**
- **Metodos de pago disponibles** (configurables):
  - Efectivo.
  - Transferencia (con selector de cuenta destino, ej:
    "Patricio (Mercado Pago)", "Sergio (Mercado Pago)").
  - Tarjeta de credito (con selector de terminal/posnet
    y tipo de tarjeta: VISA, MasterCard, Naranja, etc.).
  - Tarjeta de debito (misma logica de terminal y tarjeta).
- **Pago mixto:** se puede dividir el pago entre multiples
  metodos. Ej: $2.000 efectivo + $1.500 transferencia.
- **Calculo de vuelto:** si se paga en efectivo, campo
  "monto recibido" y calculo automatico del vuelto.
- Los metodos de pago, cuentas de transferencia, terminales
  y tipos de tarjeta son todos configurables desde el
  panel de configuracion.

### 3.8 Facturacion AFIP (fase posterior)

- Configurable: definir que metodos de pago generan
  factura automaticamente (ej: solo tarjeta de credito).
- Integracion con AFIP para emision de facturas
  electronicas.
- Se implementa en una fase posterior al sistema base.

### 3.9 Ticket/comprobante

Al confirmar la venta:

- Se muestra un resumen en pantalla con todos los datos.
- Boton "Imprimir" para impresora termica (80mm) o A4.
- Boton "Enviar" con opciones:
  - Enviar PDF por WhatsApp.
  - Enviar PDF por email.

---

## 4. Panel de productos

### 4.1 Listado de productos

- Tabla con columnas: nombre, categoria, precio venta,
  stock actual, estado.
- Buscador por nombre y filtros por categoria.
- Indicadores visuales de stock bajo (color rojo/amarillo).
- Paginacion.

### 4.2 Formulario de producto

Campos del producto:

- **Nombre** (obligatorio).
- **Codigo** (opcional, codigo de barras o interno).
- **Categoria** (selector, obligatorio).
- **Descripcion** (opcional).
- **Imagen** (opcional).
- **Precio de costo** (obligatorio).
- **Listas de precios:** por defecto una sola lista de
  precio de venta. Se pueden crear listas adicionales
  (ej: "Mayorista", "Empleados").
- **Stock actual** (solo lectura, se modifica desde
  movimientos de stock).
- **Stock minimo** (para alertas de stock bajo).
- **Tipo de venta:** unidad o granel (kg, litro, etc.).
- **Activo/Inactivo.**

### 4.3 Categorias

- CRUD de categorias con nombre y descripcion.
- Un producto pertenece a una categoria.
- Ejemplos: Alimentos, Accesorios, Higiene, Medicamentos.

### 4.4 Listas de precios

- Por defecto existe una lista "General" (precio de venta
  principal).
- Se pueden crear listas adicionales con nombre.
- Cada producto tiene un precio en cada lista.
- Al vender, se puede seleccionar que lista de precios
  usar.

### 4.5 Movimientos de stock

- **Ingreso:** compra a proveedor, devolucion de cliente,
  ajuste positivo.
- **Egreso:** venta (automatico), rotura, perdida, uso
  interno.
- Cada movimiento registra: fecha, tipo, cantidad,
  motivo, usuario que lo hizo.
- Historial completo de movimientos por producto.
- El historial es editable (corregir errores).

---

## 5. Panel de clientes

### 5.1 Listado de clientes

- Tabla con: nombre, telefono, email, ultima compra.
- Buscador por nombre, telefono o email.

### 5.2 Ficha de cliente

Campos fijos:

- **Nombre completo** (obligatorio).
- **Telefono.**
- **Email.**
- **Direccion.**
- **Notas** (campo libre).

Campos personalizables:

- El usuario puede agregar campos custom por cliente o
  de forma global (ej: "Veterinario", "Alergias de
  mascota", "Fecha de cumpleaños").

### 5.3 Descuento de cliente

- Cada cliente puede tener un descuento predeterminado:
  - Por porcentaje (ej: 10% en todas las compras).
  - Por monto fijo (ej: $500 de descuento siempre).
  - Al costo (compra todo a precio de costo).
- Se aplica automaticamente al seleccionarlo en una venta.
- Se puede anular o modificar en cada venta individual.

### 5.4 Historial de compras

- Lista de todas las ventas asociadas al cliente.
- Total gastado, cantidad de compras, promedio por compra.
- Ultima fecha de compra.

---

## 6. Panel de proveedores

### 6.1 Listado de proveedores

- Tabla con: nombre, telefono, saldo cuenta corriente.
- Buscador por nombre.

### 6.2 Ficha de proveedor

- **Nombre/razon social** (obligatorio).
- **Telefono.**
- **Email.**
- **Direccion.**
- **CUIT** (opcional).
- **Notas.**
- **Productos que provee:** asociacion con productos
  del catalogo.

### 6.3 Ordenes de compra

- Registrar compras al proveedor: fecha, productos,
  cantidades, precios, total.
- Al registrar una compra, se puede actualizar
  automaticamente el stock de los productos.
- Historial de compras por proveedor.
- Total gastado con cada proveedor.

### 6.4 Cuenta corriente

- Saldo actual con el proveedor (lo que se debe o lo
  que hay a favor).
- Registrar pagos al proveedor (parciales o totales).
- Historial de movimientos: compras, pagos, ajustes.
- Estados: al dia, con deuda, saldo a favor.

---

## 7. Panel de caja

### 7.1 Apertura de caja

- Boton "Abrir caja" con campo de monto inicial en
  efectivo.
- Registra quien abrio la caja y a que hora.
- Solo se puede vender con la caja abierta.

### 7.2 Durante la jornada

- Todas las ventas en efectivo se suman a la caja.
- Se pueden registrar egresos manuales (ej: pago a
  proveedor en efectivo, retiro de efectivo).
- Se pueden registrar ingresos manuales (ej: prestamo).

### 7.3 Cierre de caja

- Boton "Cerrar caja."
- Se muestra:
  - Monto inicial.
  - Total de ventas en efectivo.
  - Ingresos manuales.
  - Egresos manuales.
  - **Monto esperado en caja.**
  - Campo "monto real contado" para el arqueo.
  - **Diferencia** (sobrante o faltante).
- Se guarda el cierre con todos los datos y la
  diferencia.

### 7.4 Historial de cajas

- Lista de todas las cajas con: fecha, usuario, monto
  inicial, monto final, diferencia.
- Detalle de cada caja con todos los movimientos.

---

## 8. Panel de estadisticas

### 8.1 Dashboard principal

Tarjetas de resumen rapido:

- Ventas del dia / semana / mes.
- Ingresos del dia / semana / mes.
- Cantidad de ventas realizadas.
- Margen de ganancia promedio.
- Productos con stock critico.

### 8.2 Graficos

- **Ventas por periodo:** grafico de lineas o barras
  (diario, semanal, mensual). Comparativa entre periodos.
- **Productos mas vendidos:** ranking con barras
  horizontales.
- **Categorias mas vendidas:** grafico de torta o barras.
- **Ingresos vs costos:** comparativa para ver margen.
- **Ventas por metodo de pago:** distribucion.

### 8.3 Metricas avanzadas

- **Rendimiento por vendedor:** ventas totales, cantidad
  de operaciones, ticket promedio por vendedor.
- **Rendimiento por proveedor:** gasto total, productos
  comprados, frecuencia.
- **Clientes frecuentes:** ranking por cantidad de
  compras o monto total.
- **Tendencias:** comparativa mes a mes, deteccion de
  crecimiento o caida.

### 8.4 Alertas

- Productos por debajo del stock minimo.
- Productos sin ventas en los ultimos X dias.
- Cuentas corrientes con deuda alta.

### 8.5 Exportacion

- Exportar cualquier reporte a CSV.
- Exportar a PDF.
- Filtros por rango de fechas en todos los reportes.

---

## 9. Panel de configuracion

### 9.1 Usuarios y roles

- CRUD de usuarios del sistema.
- Roles:
  - **Admin:** acceso total a todos los paneles.
  - **Vendedor:** acceso a ventas, productos (solo
    lectura), clientes. Sin acceso a configuracion ni
    estadisticas completas.
- Cada usuario tiene: nombre, email, contraseña, rol.

### 9.2 Metodos de pago

- CRUD de metodos de pago (efectivo, transferencia,
  tarjeta credito, tarjeta debito, etc.).
- Activar/desactivar metodos.

### 9.3 Cuentas de transferencia

- CRUD de cuentas destino para transferencias.
- Campos: nombre, entidad (Mercado Pago, banco, etc.),
  alias/CBU, titular.
- Ej: "Patricio (Mercado Pago)", "Sergio (Mercado Pago)".

### 9.4 Terminales de pago (posnet)

- CRUD de terminales.
- Campos: nombre/numero, ubicacion, proveedor.
- Ej: "Terminal 1", "Terminal 2".

### 9.5 Tipos de tarjeta

- CRUD de tipos de tarjeta.
- Por defecto: VISA, MasterCard, Naranja, American
  Express, Cabal.
- Se pueden agregar o desactivar.

### 9.6 Datos del negocio

- Nombre del negocio.
- Direccion.
- Telefono.
- Logo (para tickets).
- CUIT (para facturacion futura).

### 9.7 Reglas de facturacion (fase posterior)

- Configurar que metodos de pago generan factura AFIP
  automaticamente.
- Tipo de factura por defecto (A, B, C).

---

## 10. Modelo de datos

### 10.1 Entidades principales

```
Usuarios
├── Id
├── Nombre
├── Email
├── Contraseña_Hash
├── Rol (Admin, Vendedor)
├── Activo
├── Fecha_Creacion
└── Fecha_Actualizacion

Categorias
├── Id
├── Nombre
├── Descripcion
├── Fecha_Creacion
└── Fecha_Actualizacion

Productos
├── Id
├── Nombre
├── Codigo
├── Descripcion
├── Imagen_Url
├── Precio_Costo
├── Stock_Actual
├── Stock_Minimo
├── Tipo_Venta (unidad, granel)
├── Unidad_Medida (kg, litro, unidad)
├── Activo
├── Id_Categoria (FK)
├── Fecha_Creacion
└── Fecha_Actualizacion

Listas_Precios
├── Id
├── Nombre
├── Es_Principal
├── Fecha_Creacion
└── Fecha_Actualizacion

Precios_Productos
├── Id
├── Id_Producto (FK)
├── Id_Lista_Precio (FK)
├── Precio
├── Fecha_Creacion
└── Fecha_Actualizacion

Clientes
├── Id
├── Nombre_Completo
├── Telefono
├── Email
├── Direccion
├── Notas
├── Tipo_Descuento (ninguno, porcentaje, monto, al_costo)
├── Valor_Descuento
├── Fecha_Creacion
└── Fecha_Actualizacion

Campos_Custom_Clientes
├── Id
├── Nombre_Campo
├── Tipo_Campo (texto, numero, fecha, booleano)
├── Fecha_Creacion
└── Fecha_Actualizacion

Valores_Campos_Custom
├── Id
├── Id_Cliente (FK)
├── Id_Campo_Custom (FK)
├── Valor
├── Fecha_Creacion
└── Fecha_Actualizacion

Proveedores
├── Id
├── Nombre
├── Telefono
├── Email
├── Direccion
├── Cuit
├── Notas
├── Saldo_Cuenta_Corriente
├── Fecha_Creacion
└── Fecha_Actualizacion

Productos_Proveedores
├── Id
├── Id_Producto (FK)
├── Id_Proveedor (FK)
├── Fecha_Creacion
└── Fecha_Actualizacion

Ordenes_Compra
├── Id
├── Id_Proveedor (FK)
├── Fecha
├── Total
├── Notas
├── Id_Usuario (FK)
├── Fecha_Creacion
└── Fecha_Actualizacion

Detalles_Orden_Compra
├── Id
├── Id_Orden_Compra (FK)
├── Id_Producto (FK)
├── Cantidad
├── Precio_Unitario
├── Subtotal
├── Fecha_Creacion
└── Fecha_Actualizacion

Movimientos_Cuenta_Corriente
├── Id
├── Id_Proveedor (FK)
├── Tipo (compra, pago, ajuste)
├── Monto
├── Descripcion
├── Fecha
├── Id_Usuario (FK)
├── Fecha_Creacion
└── Fecha_Actualizacion

Metodos_Pago
├── Id
├── Nombre
├── Activo
├── Fecha_Creacion
└── Fecha_Actualizacion

Cuentas_Transferencia
├── Id
├── Nombre
├── Entidad
├── Alias_Cbu
├── Titular
├── Activo
├── Fecha_Creacion
└── Fecha_Actualizacion

Terminales_Pago
├── Id
├── Nombre
├── Ubicacion
├── Activo
├── Fecha_Creacion
└── Fecha_Actualizacion

Tipos_Tarjeta
├── Id
├── Nombre
├── Activo
├── Fecha_Creacion
└── Fecha_Actualizacion

Ventas
├── Id
├── Fecha
├── Id_Cliente (FK, nullable)
├── Id_Usuario (FK)
├── Subtotal
├── Tipo_Descuento (ninguno, porcentaje, monto, al_costo)
├── Valor_Descuento
├── Total
├── Estado (completada, anulada)
├── Id_Caja (FK)
├── Fecha_Creacion
└── Fecha_Actualizacion

Detalles_Venta
├── Id
├── Id_Venta (FK)
├── Id_Producto (FK)
├── Cantidad
├── Precio_Unitario
├── Precio_Original
├── Tipo_Descuento_Item
├── Valor_Descuento_Item
├── Subtotal
├── Fecha_Creacion
└── Fecha_Actualizacion

Pagos_Venta
├── Id
├── Id_Venta (FK)
├── Id_Metodo_Pago (FK)
├── Monto
├── Id_Cuenta_Transferencia (FK, nullable)
├── Id_Terminal_Pago (FK, nullable)
├── Id_Tipo_Tarjeta (FK, nullable)
├── Monto_Recibido (nullable, para efectivo)
├── Vuelto (nullable, para efectivo)
├── Fecha_Creacion
└── Fecha_Actualizacion

Movimientos_Stock
├── Id
├── Id_Producto (FK)
├── Tipo (ingreso, egreso)
├── Motivo (compra, venta, rotura, perdida, uso, ajuste)
├── Cantidad
├── Stock_Anterior
├── Stock_Posterior
├── Notas
├── Id_Usuario (FK)
├── Id_Orden_Compra (FK, nullable)
├── Id_Venta (FK, nullable)
├── Fecha_Creacion
└── Fecha_Actualizacion

Cajas
├── Id
├── Fecha_Apertura
├── Fecha_Cierre (nullable)
├── Monto_Inicial
├── Monto_Esperado (nullable)
├── Monto_Real (nullable)
├── Diferencia (nullable)
├── Estado (abierta, cerrada)
├── Id_Usuario_Apertura (FK)
├── Id_Usuario_Cierre (FK, nullable)
├── Fecha_Creacion
└── Fecha_Actualizacion

Movimientos_Caja
├── Id
├── Id_Caja (FK)
├── Tipo (ingreso, egreso)
├── Motivo (venta, retiro, ingreso_manual, pago_proveedor)
├── Monto
├── Descripcion
├── Id_Venta (FK, nullable)
├── Id_Usuario (FK)
├── Fecha_Creacion
└── Fecha_Actualizacion

Configuracion_Negocio
├── Id
├── Nombre_Negocio
├── Direccion
├── Telefono
├── Logo_Url
├── Cuit
├── Fecha_Creacion
└── Fecha_Actualizacion
```

---

## 11. API - Endpoints principales

### 11.1 Autenticacion

```
POST   /api/auth/login
POST   /api/auth/registro
GET    /api/auth/perfil
```

### 11.2 Usuarios

```
GET    /api/usuarios
GET    /api/usuarios/{id}
POST   /api/usuarios
PUT    /api/usuarios/{id}
DELETE /api/usuarios/{id}
```

### 11.3 Productos

```
GET    /api/productos
GET    /api/productos/{id}
POST   /api/productos
PUT    /api/productos/{id}
DELETE /api/productos/{id}
GET    /api/productos/{id}/movimientos-stock
GET    /api/productos/stock-bajo
```

### 11.4 Categorias

```
GET    /api/categorias
POST   /api/categorias
PUT    /api/categorias/{id}
DELETE /api/categorias/{id}
```

### 11.5 Listas de precios

```
GET    /api/listas-precios
POST   /api/listas-precios
PUT    /api/listas-precios/{id}
DELETE /api/listas-precios/{id}
PUT    /api/listas-precios/{id}/productos
```

### 11.6 Stock

```
POST   /api/stock/ingreso
POST   /api/stock/egreso
PUT    /api/stock/movimientos/{id}
GET    /api/stock/movimientos
```

### 11.7 Ventas

```
POST   /api/ventas
GET    /api/ventas
GET    /api/ventas/{id}
POST   /api/ventas/{id}/anular
GET    /api/ventas/{id}/ticket
```

### 11.8 Clientes

```
GET    /api/clientes
GET    /api/clientes/{id}
POST   /api/clientes
PUT    /api/clientes/{id}
DELETE /api/clientes/{id}
GET    /api/clientes/{id}/historial
```

### 11.9 Campos custom de clientes

```
GET    /api/campos-custom
POST   /api/campos-custom
PUT    /api/campos-custom/{id}
DELETE /api/campos-custom/{id}
```

### 11.10 Proveedores

```
GET    /api/proveedores
GET    /api/proveedores/{id}
POST   /api/proveedores
PUT    /api/proveedores/{id}
DELETE /api/proveedores/{id}
GET    /api/proveedores/{id}/cuenta-corriente
```

### 11.11 Ordenes de compra

```
POST   /api/ordenes-compra
GET    /api/ordenes-compra
GET    /api/ordenes-compra/{id}
```

### 11.12 Pagos a proveedores

```
POST   /api/proveedores/{id}/pagos
GET    /api/proveedores/{id}/pagos
```

### 11.13 Caja

```
POST   /api/caja/abrir
POST   /api/caja/cerrar
GET    /api/caja/actual
POST   /api/caja/movimiento
GET    /api/caja/historial
GET    /api/caja/{id}
```

### 11.14 Estadisticas

```
GET    /api/estadisticas/ventas
GET    /api/estadisticas/productos-mas-vendidos
GET    /api/estadisticas/categorias
GET    /api/estadisticas/ingresos
GET    /api/estadisticas/margenes
GET    /api/estadisticas/rendimiento-vendedores
GET    /api/estadisticas/rendimiento-proveedores
GET    /api/estadisticas/clientes-frecuentes
GET    /api/estadisticas/alertas
GET    /api/estadisticas/exportar
```

### 11.15 Configuracion

```
GET    /api/configuracion/negocio
PUT    /api/configuracion/negocio
GET    /api/configuracion/metodos-pago
POST   /api/configuracion/metodos-pago
PUT    /api/configuracion/metodos-pago/{id}
GET    /api/configuracion/cuentas-transferencia
POST   /api/configuracion/cuentas-transferencia
PUT    /api/configuracion/cuentas-transferencia/{id}
GET    /api/configuracion/terminales
POST   /api/configuracion/terminales
PUT    /api/configuracion/terminales/{id}
GET    /api/configuracion/tipos-tarjeta
POST   /api/configuracion/tipos-tarjeta
PUT    /api/configuracion/tipos-tarjeta/{id}
```

---

## 12. Integraciones futuras (fases posteriores)

### 12.1 Mercado Pago

- Integracion con API de Mercado Pago para verificar
  transferencias entrantes.
- Al recibir una transferencia confirmada, habilitar
  automaticamente la venta.
- Webhook para notificaciones en tiempo real.

### 12.2 AFIP - Facturacion electronica

- Integracion con web services de AFIP.
- Emision de facturas A, B y C.
- Configurable: definir que metodos de pago disparan
  facturacion automatica.
- Almacenamiento de CAE y datos fiscales por venta.

---

## 13. Seguridad

- Autenticacion con JWT (access + refresh tokens).
- Contraseñas hasheadas con bcrypt.
- Proteccion de endpoints por rol.
- Validacion de datos con Pydantic en todos los
  endpoints.
- Variables de entorno para datos sensibles.
- CORS configurado para el dominio del frontend.

---

## 14. Consideraciones de UX

- Interfaz responsive (funciona en PC y tablet).
- Navegacion lateral colapsable.
- Atajos de teclado en el POS para agilizar ventas.
- Feedback visual en acciones (toasts de confirmacion
  y error).
- Carga lazy de datos pesados.
- Busqueda en tiempo real con debounce.
