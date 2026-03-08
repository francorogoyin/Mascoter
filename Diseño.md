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
2. **Envios** - Seguimiento de entregas pendientes.
3. **Productos** - CRUD de productos, categorias, stock.
4. **Clientes** - Gestion de clientes y sus datos.
5. **Proveedores** - Gestion de proveedores y cuentas
   corrientes.
6. **Caja** - Apertura, cierre y arqueo de caja diaria.
7. **Estadisticas** - Dashboard con metricas y reportes.
8. **Configuracion** - Usuarios, roles, metodos de pago,
   envios, datos del negocio.

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
- **Obligatorio si es envio:** cuando se marca la venta
  como envio, es obligatorio seleccionar un cliente. La
  direccion y telefono se cargan automaticamente desde la
  ficha del cliente.

### 3.7 Envio

Antes de cobrar, se puede marcar la venta como envio:

- **Toggle "Con envio"** en el carrito.
- Al activar el envio:
  - Se requiere seleccionar un cliente (obligatorio).
  - Se carga automaticamente la direccion del cliente.
  - Se puede ingresar una **direccion provisional** y un
    **telefono alternativo** (ej: "hoy llevenla a lo de
    mi cuñada").
  - Se agrega el costo de envio al total segun la
    configuracion (ver seccion 10.8).
  - El costo de envio se puede quitar o modificar
    manualmente en esa venta.
  - Se puede seleccionar **"Paga en la casa"**: la venta
    se registra pero el dinero NO ingresa a caja hasta
    que el envio se marque como entregado y pagado.

### 3.8 Cobro y cierre de venta

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

### 3.9 Facturacion AFIP (fase posterior)

- Configurable: definir que metodos de pago generan
  factura automaticamente (ej: solo tarjeta de credito).
- Integracion con AFIP para emision de facturas
  electronicas.
- Se implementa en una fase posterior al sistema base.

### 3.10 Ticket/comprobante

Al confirmar la venta:

- Se muestra un resumen en pantalla con todos los datos.
- Boton "Imprimir" para impresora termica (80mm) o A4.
- Boton "Enviar" con opciones:
  - Enviar PDF por WhatsApp.
  - Enviar PDF por email.

---

## 4. Panel de envios

### 4.1 Listado de envios pendientes

- Tabla con: numero de venta, cliente, direccion, monto
  total, estado de pago, fecha.
- Filtros por estado (Pendiente, Entregado, Cancelado).
- Ordenado por fecha (mas antiguos primero por defecto).

### 4.2 Estados del envio

- **Pendiente:** el envio fue registrado pero no se
  entrego todavia. Aparece en la lista de pendientes.
- **Entregado:** el repartidor entrego y se confirma
  en el sistema. Si tenia "Paga en la casa", al marcar
  como entregado se registra el pago y el dinero ingresa
  a caja.
- **Cancelado:** el envio se cancela. Si la venta tenia
  productos, se revierte el stock.

### 4.3 Paga en la casa

- Si la venta se marco como "Paga en la casa":
  - La venta queda registrada pero sin ingreso a caja.
  - Al marcar como "Entregado" se abre un modal de cobro
    (igual que el cobro normal: efectivo, transferencia,
    tarjeta, pago mixto).
  - Se puede registrar **pago parcial**: el monto pagado
    ingresa a caja, y el restante queda como pendiente
    en el envio (no se marca como entregado hasta que se
    pague todo, o se decide cancelar el faltante).
  - El envio muestra claramente cuanto se pago y cuanto
    falta.
- Si la venta ya fue pagada al momento de la compra (no
  es "Paga en la casa"), al marcar como entregado
  simplemente se cierra el envio.

### 4.4 Detalle del envio

- Datos del cliente (nombre, telefono).
- Direccion de entrega (del cliente o provisional).
- Telefono alternativo (si se ingreso uno).
- Lista de productos de la venta.
- Monto total.
- Estado de pago (pagado, pendiente, parcial).
- Notas adicionales.

---

## 5. Panel de productos

### 5.1 Listado de productos

- Tabla con columnas: nombre, categoria, precio venta,
  stock actual, estado.
- Buscador por nombre y filtros por categoria.
- Indicadores visuales de stock bajo (color rojo/amarillo).
- Paginacion.

### 5.2 Formulario de producto

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

### 5.3 Categorias

- CRUD de categorias con nombre y descripcion.
- Un producto pertenece a una categoria.
- Ejemplos: Alimentos, Accesorios, Higiene, Medicamentos.

### 5.4 Listas de precios

- Por defecto existe una lista "General" (precio de venta
  principal).
- Se pueden crear listas adicionales con nombre.
- Cada producto tiene un precio en cada lista.
- Al vender, se puede seleccionar que lista de precios
  usar.

### 5.5 Movimientos de stock

- **Ingreso:** compra a proveedor, devolucion de cliente,
  ajuste positivo.
- **Egreso:** venta (automatico), rotura, perdida, uso
  interno.
- Cada movimiento registra: fecha, tipo, cantidad,
  motivo, usuario que lo hizo.
- Historial completo de movimientos por producto.
- El historial es editable (corregir errores).

---

## 6. Panel de clientes

### 6.1 Listado de clientes

- Tabla con: nombre, telefono, email, ultima compra.
- Buscador por nombre, telefono o email.

### 6.2 Ficha de cliente

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

### 6.3 Descuento de cliente

- Cada cliente puede tener un descuento predeterminado:
  - Por porcentaje (ej: 10% en todas las compras).
  - Por monto fijo (ej: $500 de descuento siempre).
  - Al costo (compra todo a precio de costo).
- Se aplica automaticamente al seleccionarlo en una venta.
- Se puede anular o modificar en cada venta individual.

### 6.4 Historial de compras

- Lista de todas las ventas asociadas al cliente.
- Total gastado, cantidad de compras, promedio por compra.
- Ultima fecha de compra.

---

## 7. Panel de proveedores

### 7.1 Listado de proveedores

- Tabla con: nombre, telefono, saldo cuenta corriente.
- Buscador por nombre.

### 7.2 Ficha de proveedor

- **Nombre/razon social** (obligatorio).
- **Telefono.**
- **Email.**
- **Direccion.**
- **CUIT** (opcional).
- **Notas.**
- **Productos que provee:** asociacion con productos
  del catalogo.

### 7.3 Ordenes de compra

- Registrar compras al proveedor: fecha, productos,
  cantidades, precios, total.
- Al registrar una compra, se puede actualizar
  automaticamente el stock de los productos.
- Historial de compras por proveedor.
- Total gastado con cada proveedor.

### 7.4 Cuenta corriente

- Saldo actual con el proveedor (lo que se debe o lo
  que hay a favor).
- Registrar pagos al proveedor (parciales o totales).
- Historial de movimientos: compras, pagos, ajustes.
- Estados: al dia, con deuda, saldo a favor.

---

## 8. Panel de caja

### 8.1 Apertura de caja

- Boton "Abrir caja" con campo de monto inicial en
  efectivo.
- Registra quien abrio la caja y a que hora.
- Solo se puede vender con la caja abierta.

### 8.2 Durante la jornada

- Todas las ventas en efectivo se suman a la caja.
- Se pueden registrar egresos manuales (ej: pago a
  proveedor en efectivo, retiro de efectivo).
- Se pueden registrar ingresos manuales (ej: prestamo).

### 8.3 Cierre de caja

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

### 8.4 Historial de cajas

- Lista de todas las cajas con: fecha, usuario, monto
  inicial, monto final, diferencia.
- Detalle de cada caja con todos los movimientos.

---

## 9. Panel de estadisticas

### 9.1 Dashboard principal

Tarjetas de resumen rapido:

- Ventas del dia / semana / mes.
- Ingresos del dia / semana / mes.
- Cantidad de ventas realizadas.
- Margen de ganancia promedio.
- Productos con stock critico.

### 9.2 Graficos

- **Ventas por periodo:** grafico de lineas o barras
  (diario, semanal, mensual). Comparativa entre periodos.
- **Productos mas vendidos:** ranking con barras
  horizontales.
- **Categorias mas vendidas:** grafico de torta o barras.
- **Ingresos vs costos:** comparativa para ver margen.
- **Ventas por metodo de pago:** distribucion.

### 9.3 Metricas avanzadas

- **Rendimiento por vendedor:** ventas totales, cantidad
  de operaciones, ticket promedio por vendedor.
- **Rendimiento por proveedor:** gasto total, productos
  comprados, frecuencia.
- **Clientes frecuentes:** ranking por cantidad de
  compras o monto total.
- **Tendencias:** comparativa mes a mes, deteccion de
  crecimiento o caida.

### 9.4 Alertas

- Productos por debajo del stock minimo.
- Productos sin ventas en los ultimos X dias.
- Cuentas corrientes con deuda alta.

### 9.5 Exportacion

- Exportar cualquier reporte a CSV.
- Exportar a PDF.
- Filtros por rango de fechas en todos los reportes.

---

## 10. Panel de configuracion

### 10.1 Usuarios y roles

- CRUD de usuarios del sistema.
- Roles:
  - **Admin:** acceso total a todos los paneles.
  - **Vendedor:** acceso a ventas, productos (solo
    lectura), clientes. Sin acceso a configuracion ni
    estadisticas completas.
- Cada usuario tiene: nombre, email, contraseña, rol.

### 10.2 Metodos de pago

- CRUD de metodos de pago (efectivo, transferencia,
  tarjeta credito, tarjeta debito, etc.).
- Activar/desactivar metodos.

### 10.3 Cuentas de transferencia

- CRUD de cuentas destino para transferencias.
- Campos: nombre, entidad (Mercado Pago, banco, etc.),
  alias/CBU, titular.
- Ej: "Patricio (Mercado Pago)", "Sergio (Mercado Pago)".

### 10.4 Terminales de pago (posnet)

- CRUD de terminales.
- Campos: nombre/numero, ubicacion, proveedor.
- Ej: "Terminal 1", "Terminal 2".

### 10.5 Tipos de tarjeta

- CRUD de tipos de tarjeta.
- Por defecto: VISA, MasterCard, Naranja, American
  Express, Cabal.
- Se pueden agregar o desactivar.

### 10.6 Datos del negocio

- Nombre del negocio.
- Direccion.
- Telefono.
- Logo (para tickets).
- CUIT (para facturacion futura).

### 10.7 Configuracion de envios

- **Costo de envio:** configurable con varias modalidades:
  - **Exacto:** un monto fijo (ej: $1.500 siempre).
  - **Incremental:** monto base + adicional por distancia
    o zona (si se implementan zonas).
  - **Gratis a partir de monto:** no se cobra envio si
    el total de la venta supera un umbral configurable
    (ej: gratis a partir de $25.000).
  - **Gratis hasta monto:** se cobra envio solo si el
    total supera cierto monto.
- **Envio gratis por cliente:** en la ficha del cliente
  se puede marcar "envio gratis siempre". Esto tiene
  prioridad sobre las reglas de monto.
- El costo de envio calculado se puede quitar o modificar
  manualmente en cada venta individual.

### 10.8 Reglas de facturacion (fase posterior)

- Configurar que metodos de pago generan factura AFIP
  automaticamente.
- Tipo de factura por defecto (A, B, C).

---

## 11. Modelo de datos

### 11.1 Entidades principales

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
├── Envio_Gratis (booleano, default false)
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
├── Costo_Envio (default 0)
├── Total
├── Es_Envio (booleano, default false)
├── Paga_En_Casa (booleano, default false)
├── Estado (completada, anulada)
├── Id_Caja (FK)
├── Fecha_Creacion
└── Fecha_Actualizacion

Envios
├── Id
├── Id_Venta (FK)
├── Direccion_Entrega
├── Telefono_Alternativo (nullable)
├── Notas
├── Estado (pendiente, entregado, cancelado)
├── Monto_Pagado (default 0, para pagos parciales)
├── Monto_Pendiente
├── Fecha_Entrega (nullable)
├── Id_Usuario_Entrega (FK, nullable)
├── Fecha_Creacion
└── Fecha_Actualizacion

Configuracion_Envio
├── Id
├── Tipo_Costo (exacto, incremental, gratis_desde,
│   gratis_hasta)
├── Monto_Base
├── Monto_Umbral (nullable, para reglas de monto)
├── Monto_Adicional (nullable, para incremental)
├── Activo
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

## 12. API - Endpoints principales

### 12.1 Autenticacion

```
POST   /api/auth/login
POST   /api/auth/registro
GET    /api/auth/perfil
```

### 12.2 Usuarios

```
GET    /api/usuarios
GET    /api/usuarios/{id}
POST   /api/usuarios
PUT    /api/usuarios/{id}
DELETE /api/usuarios/{id}
```

### 12.3 Productos

```
GET    /api/productos
GET    /api/productos/{id}
POST   /api/productos
PUT    /api/productos/{id}
DELETE /api/productos/{id}
GET    /api/productos/{id}/movimientos-stock
GET    /api/productos/stock-bajo
```

### 12.4 Categorias

```
GET    /api/categorias
POST   /api/categorias
PUT    /api/categorias/{id}
DELETE /api/categorias/{id}
```

### 12.5 Listas de precios

```
GET    /api/listas-precios
POST   /api/listas-precios
PUT    /api/listas-precios/{id}
DELETE /api/listas-precios/{id}
PUT    /api/listas-precios/{id}/productos
```

### 12.6 Stock

```
POST   /api/stock/ingreso
POST   /api/stock/egreso
PUT    /api/stock/movimientos/{id}
GET    /api/stock/movimientos
```

### 12.7 Ventas

```
POST   /api/ventas
GET    /api/ventas
GET    /api/ventas/{id}
POST   /api/ventas/{id}/anular
GET    /api/ventas/{id}/ticket
```

### 12.8 Envios

```
GET    /api/envios
GET    /api/envios/{id}
PUT    /api/envios/{id}/estado
POST   /api/envios/{id}/pago
GET    /api/envios/pendientes
```

### 12.9 Clientes

```
GET    /api/clientes
GET    /api/clientes/{id}
POST   /api/clientes
PUT    /api/clientes/{id}
DELETE /api/clientes/{id}
GET    /api/clientes/{id}/historial
```

### 12.10 Campos custom de clientes

```
GET    /api/campos-custom
POST   /api/campos-custom
PUT    /api/campos-custom/{id}
DELETE /api/campos-custom/{id}
```

### 12.11 Proveedores

```
GET    /api/proveedores
GET    /api/proveedores/{id}
POST   /api/proveedores
PUT    /api/proveedores/{id}
DELETE /api/proveedores/{id}
GET    /api/proveedores/{id}/cuenta-corriente
```

### 12.12 Ordenes de compra

```
POST   /api/ordenes-compra
GET    /api/ordenes-compra
GET    /api/ordenes-compra/{id}
```

### 12.13 Pagos a proveedores

```
POST   /api/proveedores/{id}/pagos
GET    /api/proveedores/{id}/pagos
```

### 12.14 Caja

```
POST   /api/caja/abrir
POST   /api/caja/cerrar
GET    /api/caja/actual
POST   /api/caja/movimiento
GET    /api/caja/historial
GET    /api/caja/{id}
```

### 12.15 Estadisticas

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

### 12.16 Configuracion

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

## 13. Integraciones futuras (fases posteriores)

### 13.1 Mercado Pago

- Integracion con API de Mercado Pago para verificar
  transferencias entrantes.
- Al recibir una transferencia confirmada, habilitar
  automaticamente la venta.
- Webhook para notificaciones en tiempo real.

### 13.2 AFIP - Facturacion electronica

- Integracion con web services de AFIP.
- Emision de facturas A, B y C.
- Configurable: definir que metodos de pago disparan
  facturacion automatica.
- Almacenamiento de CAE y datos fiscales por venta.

---

## 14. Seguridad

- Autenticacion con JWT (access + refresh tokens).
- Contraseñas hasheadas con bcrypt.
- Proteccion de endpoints por rol.
- Validacion de datos con Pydantic en todos los
  endpoints.
- Variables de entorno para datos sensibles.
- CORS configurado para el dominio del frontend.

---

## 15. Consideraciones de UX

- Interfaz responsive (funciona en PC y tablet).
- Navegacion lateral colapsable.
- Atajos de teclado en el POS para agilizar ventas.
- Feedback visual en acciones (toasts de confirmacion
  y error).
- Carga lazy de datos pesados.
- Busqueda en tiempo real con debounce.
