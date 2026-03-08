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
3. **Productos** - CRUD de productos, categorias, stock,
   variantes, precios por proveedor.
4. **Clientes** - Gestion de clientes y sus datos.
5. **Proveedores** - Gestion de proveedores y cuentas
   corrientes.
6. **Pedidos** - Generacion automatica de pedidos a
   proveedores segun stock y mejor precio.
7. **Caja** - Apertura, cierre y arqueo de caja diaria.
8. **Estadisticas** - Dashboard con metricas y reportes.
9. **Configuracion** - Usuarios, roles, metodos de pago,
   envios, datos del negocio.

---

## 3. Panel de ventas (POS)

El panel de ventas tiene dos pestañas principales:

- **POS** (por defecto): punto de venta para crear
  ventas nuevas.
- **Historial:** listado de ventas anteriores con
  busqueda, filtros y acceso rapido a editar,
  devolver o reimprimir ticket.

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

### 3.2 Ventas simultaneas

Se pueden tener multiples ventas abiertas al mismo
tiempo. Dos modos de visualizacion:

- **Pestañas (por defecto):** arriba del POS se muestran
  pestañas tipo navegador: "Venta 1", "Venta 2", "+".
  Cada pestaña es un POS completo e independiente. Se
  puede ir y venir entre pestañas sin perder el estado
  del carrito.
- **Pantalla dividida:** boton para dividir la pantalla
  en dos POS lado a lado. Util cuando se necesita ver
  ambas ventas al mismo tiempo.

```
Modo pestañas:
┌──────────────────────────────────────┐
│ [Venta 1] [Venta 2] [+]             │
│──────────────────────────────────────│
│  [Buscar..]        │  CARRITO        │
│  Productos         │  ...            │
│                    │  [COBRAR]       │
└──────────────────────────────────────┘

Modo dividido:
┌──────────────────┬───────────────────┐
│ VENTA 1          │ VENTA 2           │
│ [Buscar..]       │ [Buscar..]        │
│ Prods | Carrito  │ Prods | Carrito   │
│       | [COBRAR] │       | [COBRAR]  │
└──────────────────┴───────────────────┘
```

- Al cobrar una venta, la pestaña se cierra
  automaticamente.
- Se puede descartar una venta sin cobrar (con
  confirmacion).

### 3.3 Buscador de productos

- Barra de texto en la parte superior izquierda.
- Busca por nombre, codigo o categoria.
- Los resultados aparecen debajo a medida que se escribe.
- Tres modos de visualizacion:
  - **Lista simple:** nombre + precio en filas compactas.
  - **Lista compleja:** nombre + precio + foto + stock.
  - **Cuadricula:** tarjetas con foto, nombre y precio.

### 3.4 Orden de resultados

Los resultados del buscador se ordenan segun un criterio
configurable por el usuario:

- **Mas vendidos (por defecto):** productos con mas
  unidades vendidas en los ultimos X dias aparecen
  primero.
- **Mas recientes:** ultimos productos agregados primero.
- **Alfabetico:** A-Z o Z-A.
- **Por categoria:** agrupados por categoria.
- **Orden manual (favoritos):** el usuario puede "fijar"
  productos arriba, marcandolos como destacados. Los
  productos fijados siempre aparecen primero,
  independientemente del criterio elegido.
- El criterio se guarda por usuario (cada vendedor puede
  tener su propio orden preferido).

### 3.5 Agregar productos al carrito

- Un click en un producto lo agrega al carrito (1 unidad).
- Clicks sucesivos incrementan la cantidad.
- En el carrito se puede editar manualmente la cantidad.
- Se puede modificar el precio de venta de cada item en
  el carrito (precio personalizado para esa venta).
- **Insumo del cliente:** si un producto tiene insumo
  asociado (ej: envase), aparece un toggle "Trae
  insumo" en el item del carrito. Al activarlo, se
  descuenta el costo del insumo del precio de venta
  de ese item. Ej: Suavizante 1lt a $1.400 con envase
  de $200, si el cliente trae su envase → $1.200.

### 3.6 Productos a granel

- Los productos marcados como "granel" permiten dos modos
  de ingreso:
  - **Por unidades:** se ingresa la cantidad (ej: 2.5 kg).
  - **Por monto:** se ingresa el monto en pesos y el sistema
    calcula las unidades automaticamente.
    Ejemplo: producto a $1.000/kg, se ingresa $3.000,
    el sistema pone 3 unidades.

### 3.7 Descuentos

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

### 3.8 Seleccion de cliente

- Selector de cliente en la parte inferior izquierda.
- Opcional: se puede vender sin cliente (consumidor final).
- Al seleccionar un cliente, se aplican sus descuentos
  automaticamente.
- **Obligatorio si es envio:** cuando se marca la venta
  como envio, es obligatorio seleccionar un cliente. La
  direccion y telefono se cargan automaticamente desde la
  ficha del cliente.

### 3.9 Envio

Antes de cobrar, se puede marcar la venta como envio:

- **Toggle "Con envio"** en el carrito.
- Al activar el envio:
  - Se requiere seleccionar un cliente (obligatorio).
  - Se carga automaticamente la direccion del cliente.
  - Se puede ingresar una **direccion provisional** y un
    **telefono alternativo** (ej: "Hoy llevenla a lo de
    mi cuñada").
  - Se agrega el costo de envio al total segun la
    configuracion (ver seccion 10.8).
  - El costo de envio se puede quitar o modificar
    manualmente en esa venta.
  - Se puede seleccionar **"Paga en la casa"**: la venta
    se registra pero el dinero NO ingresa a caja hasta
    que el envio se marque como entregado y pagado.

### 3.10 Cobro y cierre de venta

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
- **Redondeo del total:** si esta activado
  (ver seccion 11.10), el total de la venta se
  redondea hacia arriba (ceil) al multiplo configurado.
  Ej: si el multiplo es 50 y el total da $2.930, se
  redondea a $2.950.
  La diferencia ($20) se distribuye automaticamente
  entre los items de la venta (se ajusta el precio
  unitario de uno o mas productos para que el ticket
  cuadre). El vendedor puede desactivar el redondeo
  en esa venta puntual.

### 3.11 Facturacion AFIP (fase posterior)

- Configurable: definir que metodos de pago generan
  factura automaticamente (ej: solo tarjeta de credito).
- Integracion con AFIP para emision de facturas
  electronicas.
- Se implementa en una fase posterior al sistema base.

### 3.12 Ticket/comprobante

Al confirmar la venta:

- Se muestra un resumen en pantalla con todos los datos.
- Boton "Imprimir" para impresora termica (80mm) o A4.
- Boton "Enviar" con opciones:
  - Enviar PDF por WhatsApp.
  - Enviar PDF por email.

### 3.13 Editar venta

Una venta ya cerrada se puede editar:

- Desde el historial de ventas, boton "Editar".
- Se pueden quitar items, modificar cantidades o cambiar
  precios. Al guardar, se ajusta el stock y el total.
- Si la venta tenia un pago ya registrado, se recalcula
  la diferencia (a favor del cliente o del negocio).
- Se registra en el log de auditoria quien edito, que
  cambio y cuando.

### 3.14 Devoluciones y cambios

Accesible desde un boton "Devolucion" en el POS.
No requiere buscar la venta original.

**Devolucion:**

- Se seleccionan los productos a devolver y la
  cantidad (buscandolos por nombre, codigo o lector).
- Se indica el precio al que se devuelven (por defecto
  el precio de venta actual).
- Se elige la resolucion:
  - **Reembolso:** se devuelve el dinero al cliente
    (efectivo, transferencia, etc.). Se registra como
    egreso en la caja.
  - **Nota de credito:** se genera un saldo a favor
    del cliente que puede usar en futuras compras.
- El stock de los productos devueltos se reingresa
  automaticamente.

**Cambio:**

- El cliente trae uno o mas productos y se lleva otros.
- Flujo:
  1. Se seleccionan los productos que el cliente
     devuelve (por nombre, codigo o lector) y la
     cantidad.
  2. Se agregan los productos nuevos que se lleva.
  3. Se calcula la diferencia:
     - Si los productos nuevos cuestan mas, el cliente
       paga la diferencia (se abre modal de cobro
       normal).
     - Si los productos nuevos cuestan menos, la
       diferencia queda como nota de credito o se
       reembolsa.
     - Si es igual, se cierra sin cobro.
  4. Se ajusta el stock de ambos lados (ingreso de lo
     devuelto, egreso de lo nuevo).
  5. Se registra el movimiento en la caja (cobro o
     egreso segun corresponda).

### 3.15 Recargos por metodo de pago

- Cada metodo de pago puede tener un recargo
  configurable (ej: tarjeta de credito +10%).
- El recargo se configura en el panel de configuracion
  (ver seccion 11.2).
- Al seleccionar el metodo de pago en el modal de cobro,
  el recargo se aplica automaticamente al total.
- El vendedor puede quitar o modificar el recargo
  manualmente en esa venta especifica.
- Si el pago es mixto, cada metodo aplica su propio
  recargo sobre la porcion correspondiente.
- El ticket muestra el desglose: subtotal, recargo por
  metodo de pago, total final.

### 3.16 Lector de codigo de barras

- El lector de codigo de barras funciona como un
  teclado: escribe el codigo en el campo de busqueda.
- Al detectar un codigo completo (Enter del lector),
  el sistema busca el producto y lo agrega
  automaticamente al carrito (1 unidad).
- Si el producto tiene variantes, se agrega la variante
  que coincide con el codigo escaneado.
- Escaneos sucesivos del mismo codigo incrementan la
  cantidad en el carrito.
- Si el codigo no existe, se muestra un aviso y se
  ofrece crear el producto con ese codigo.

### 3.17 Historial de ventas

Pestaña "Historial" dentro del panel de ventas:

- **Tabla con columnas:** numero de venta, fecha/hora,
  cliente (o "Consumidor final"), cantidad de items,
  total, metodo de pago, estado, usuario que vendio.
- **Buscador:** por numero de venta, nombre de cliente
  o producto incluido en la venta.
- **Filtros:** por rango de fechas, por vendedor, por
  metodo de pago, por estado (completada, anulada,
  editada).
- **Ordenamiento:** por fecha (mas recientes primero
  por defecto), por monto, por cliente.
- **Acciones rapidas por venta:**
  - Ver detalle completo (modal o vista expandida).
  - Editar venta (abre el flujo de seccion 3.13).
  - Devolucion / cambio (acceso directo a 3.14).
  - Reimprimir ticket / enviar PDF.
  - Anular venta (con confirmacion).
- **Paginacion** con cantidad de resultados por pagina
  configurable.

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
- **Redondeo de precio de venta:** al calcular el precio
  de venta a partir del costo (ej: costo + margen), el
  resultado se redondea hacia arriba (ceil) al multiplo
  configurado por el usuario (ver seccion 11.10). Ej:
  si el multiplo es 100 y el calculo da $2.350, se
  redondea a $2.400.
- **Stock actual** (editable, aunque se modifica desde
  Movimientos de stock).
- **Stock minimo** (para alertas de stock bajo).
- **Stock objetivo** (opcional, para pedidos automaticos.
  Si no se define, se usa stock minimo x2 por defecto).
- **Politica de reposicion:** configurable por producto
  (hasta objetivo, hasta minimo x factor, manual).
- **Tipo de venta:** unidad o granel (kg, litro, etc.).
- **Empresa** (selector, opcional).
- **Marca** (selector filtrado por empresa, opcional).
- **Subcategoria** (selector filtrado por categoria,
  opcional).
- **Insumo asociado:** un producto puede tener un
  insumo cuyo costo se suma al precio de costo.
  - Toggle "Tiene insumo asociado" (ej: envase,
    jeringa, bolsa, etc.).
  - Campo para seleccionar el producto insumo (por
    codigo o nombre).
  - El costo total del producto se calcula como:
    costo propio + costo del insumo.
  - El precio de venta se calcula a partir del costo
    total (costo + insumo + margen).
  - Ej: Suavizante x 1lt cuesta $800, Envase 1lt
    cuesta $200. Costo total = $1.000. Con margen
    40% → precio venta = $1.400.
  - Si el insumo cambia de precio, el costo total
    se actualiza automaticamente.
- **Activo/Inactivo.**

### 5.3 Variantes de producto

Un producto puede tener variantes (distintas
presentaciones del mismo articulo):

- **Producto padre:** el articulo base (ej: "Rosco
  Carne"). No tiene stock ni precio propio.
- **Variantes:** las presentaciones concretas. Cada
  variante tiene su propio codigo, stock, precio y
  proveedores. Ej:
  - Rosco Carne 15kg - Codigo 1001 - $8.000
  - Rosco Carne 20kg - Codigo 1002 - $10.000
  - Rosco Carne Granel (x kg) - Codigo 1003

**Calculo de granel por paquete:**

- Si una variante es granel y otra es paquete, el
  sistema calcula automaticamente el precio por unidad
  fraccionada.
  Ej: Rosco 20kg a $10.000 = $500/kg automatico.
- **Comparativa de conveniencia:** el sistema muestra
  cual variante conviene mas para fraccionar:
  - Rosco 15kg: $8.000 / 15 = $533/kg
  - Rosco 20kg: $10.000 / 20 = $500/kg
  - Recomendacion: "Comprar bolsa de 20kg es mas
    conveniente para fraccionar."
- El precio de granel calculado se puede ajustar
  manualmente (ej: agregar margen).

**En el POS:**

- Al buscar un producto padre, se muestran sus variantes
  para elegir cual agregar al carrito.
- Si el producto es granel, se puede ingresar por
  unidades o por monto (igual que la seccion 3.6).

### 5.4 Precios por proveedor

Cada producto (o variante) puede tener multiples precios
segun el proveedor que lo suministra:

- **Precio por proveedor:** al asociar un producto a un
  proveedor, se registra el precio de compra de ese
  proveedor.
- **Ranking automatico:** el sistema ordena los
  proveedores de mas barato a mas caro:
  - Proveedor 1 (mas barato): Distribuidor X - $400
  - Proveedor 2: Mayorista Y - $450
  - Proveedor 3: Proveedor Z - $500
- El ranking se muestra en la ficha del producto y se
  usa en el modulo de Pedidos para elegir
  automaticamente al proveedor mas barato.
- Se puede subir/actualizar precios por proveedor
  mediante CSV (ver seccion 5.9).

### 5.5 Categorias y subcategorias

- CRUD de categorias con nombre y descripcion.
- Cada categoria puede tener **subcategorias**.
  Ej: Alimentos > Perros, Alimentos > Gatos.
- Un producto pertenece a una categoria y
  opcionalmente a una subcategoria.
- **Margen de ganancia por categoria:** cada categoria
  tiene margenes configurables por tipo de venta:
  - **Margen unidad** (ej: 30%) - para productos/
    variantes vendidos por unidad (bolsa, paquete).
  - **Margen granel** (ej: 50%) - para productos/
    variantes vendidos a granel (kg, litro).
  Al cargar un producto, el precio de venta se calcula
  automaticamente como costo + margen segun su tipo
  de venta.
- Si una subcategoria tiene sus propios margenes
  definidos, estos tienen prioridad sobre los de la
  categoria padre.
- **Margen por producto:** cada producto/variante
  puede sobreescribir el margen con un valor propio.
- **Jerarquia de prioridad:**
  1. Margen del producto/variante (si tiene).
  2. Margen de la subcategoria (si tiene).
  3. Margen de la categoria.

### 5.6 Empresas y marcas

- CRUD de **empresas** (fabricantes/distribuidores).
  Ej: Royal Canin, Eukanuba, Bayer.
- CRUD de **marcas** (lineas dentro de una empresa).
  Cada marca pertenece a una empresa.
  Ej: Empresa "Purina" → Marcas "Dog Chow",
  "Cat Chow", "Pro Plan".
- Cada producto puede asociarse a una empresa y una
  marca (ambos opcionales).
- Filtros por empresa y marca en el listado de
  productos.

### 5.7 Listas de precios

- Por defecto existe una lista "General" (precio de venta
  principal).
- Se pueden crear listas adicionales con nombre.
- Cada producto tiene un precio en cada lista.
- Al vender, se puede seleccionar que lista de precios
  usar.

### 5.8 Movimientos de stock

- **Ingreso:** compra a proveedor, devolucion de cliente,
  ajuste positivo.
- **Egreso:** venta (automatico), rotura, perdida, uso
  interno.
- Cada movimiento registra: fecha, tipo, cantidad,
  motivo, usuario que lo hizo.
- Historial completo de movimientos por producto.
- El historial es editable (corregir errores).

### 5.9 Codigos de producto

El sistema genera codigos automaticamente para productos
nuevos, siguiendo criterios configurables:

- **Criterio global por defecto:** se configura en
  Configuracion. Opciones:
  - **Secuencial numerico:** 0001, 0002, 0003...
  - **Prefijo de categoria + secuencial:** el prefijo
    se define por categoria (ej: Alimentos = 1xxx,
    Accesorios = 2xxx). Si el ultimo producto de
    Alimentos es 1234, el siguiente es 1235.
  - **Manual:** el usuario ingresa el codigo a mano.
- **Criterio por categoria:** cada categoria puede
  sobreescribir el criterio global con su propio formato.
  Ej: "Alimentos" usa prefijo "1" + secuencial de 3
  digitos, "Accesorios" usa prefijo "2", etc.
- Al crear un producto, el codigo se genera
  automaticamente segun el criterio. El usuario puede
  modificarlo antes de guardar.
- Los codigos deben ser unicos en todo el sistema.

### 5.10 Importacion y exportacion CSV

**Exportar productos a CSV:**

- Boton "Exportar CSV" en el listado de productos.
- Exporta todos los productos (o los filtrados) con sus
  columnas: codigo, nombre, categoria, precio costo,
  precio venta, stock, stock minimo, tipo venta, activo.

**Importar productos desde CSV:**

- Boton "Importar CSV" en el listado de productos.
- Flujo de importacion:
  1. Subir archivo CSV.
  2. **Pareo de columnas:** el sistema muestra las
     columnas del CSV y pide al usuario que asocie cada
     columna del CSV con un campo del sistema (codigo,
     nombre, precio, etc.). No se asume el orden.
  3. **Vista previa:** se muestran las primeras filas
     con los datos mapeados para verificacion.
  4. **Modo de importacion** (el usuario elige):
     - Solo actualizar existentes (por codigo).
     - Solo crear nuevos (codigos que no existen).
     - Ambos: actualizar existentes y crear nuevos.
  5. **Advertencia:** antes de confirmar, el sistema
     muestra un resumen: X productos a actualizar,
     Y productos a crear, Z filas ignoradas (errores).
     El usuario confirma o cancela.
  6. **Ejecucion:** se procesan los cambios. Se muestra
     un log con resultados (exitosos y errores).
- Para actualizar, se usa el **codigo de producto** como
  clave de match. Solo se sobreescriben los campos que
  vengan en el CSV.

**Importar precios por proveedor desde CSV:**

- En la ficha del proveedor, boton "Importar precios CSV".
- El CSV contiene: codigo producto, precio de compra.
- Pareo de columnas manual (igual que productos).
- Vista previa antes de confirmar.
- Actualiza los precios de ese proveedor para los
  productos indicados. Recalcula el ranking de
  proveedores automaticamente.

### 5.11 Aumentos masivos de precio

Permite aumentar el precio de costo de multiples
productos a la vez, filtrados por:

- **Categoria** (todos los productos de una categoria).
- **Subcategoria** (productos de una subcategoria).
- **Empresa** (productos de una empresa).
- **Marca** (productos de una marca).
- Se pueden combinar filtros (ej: categoria "Alimentos"
  + empresa "Royal Canin").

**Tipo de aumento:**

- **Por porcentaje:** aumentar el costo un X%
  (ej: +15%).
- **Por monto fijo:** sumar un monto al costo
  (ej: +$200).

**Flujo:**

1. Seleccionar filtros y tipo/valor de aumento.
2. **Vista previa:** se muestra una tabla con los
   productos afectados, costo actual, costo nuevo,
   y precio de venta recalculado (segun margen de
   la categoria).
3. Confirmar o cancelar.
4. Al confirmar, se actualizan los costos y se
   recalculan los precios de venta. Se registra en
   el historial de precios y en el log de auditoria.

### 5.12 Productos quimicos

Los productos quimicos son liquidos que se preparan
a partir de un concentrado diluido en agua, con una
relacion X:1 (X partes de agua por 1 de concentrado).

**Configuracion global de versiones de envase:**

En Configuracion se definen las versiones de envase
disponibles para quimicos (aplica a todo el sistema):

- Cada version tiene:
  - **Nombre** (ej: "250 cc", "500 cc", "1 lt",
    "5 lt").
  - **Capacidad en cc** (250, 500, 1000, 5000).
  - **Producto insumo asociado** (el envase/botella
    de esa capacidad, FK a Productos).
- Se pueden agregar, editar o desactivar versiones.

**Formulario de producto quimico:**

Al crear un producto se puede marcar como quimico.
Campos adicionales:

- **Costo del concentrado por litro** (obligatorio).
  Es el precio por litro del liquido puro/concentrado.
- **Relacion de dilucion** (X:1, obligatorio).
  Ej: 10:1 significa 10 partes agua + 1 concentrado.
- **Versiones habilitadas:** checkboxes con las
  versiones de envase disponibles. Se tilda cuales
  aplican a este quimico (no todos tienen todas).
  Ej: Quita Sarro → 500 cc, 1 lt, 5 lt.
  Fluido → solo 1 lt.

**Calculo automatico de costos:**

El sistema calcula automaticamente para cada version:

- **Costo del liquido por version:**
  Concentrado_por_cc = Costo_Concentrado_Litro / 1000
  Concentrado_necesario = Capacidad_cc / (Relacion + 1)
  Costo_liquido = Concentrado_necesario
                  * Concentrado_por_cc
  Ej: Quita Sarro, concentrado $10.000/lt, relacion
  10:1, version 1 lt (1000 cc):
  - Concentrado necesario = 1000 / 11 = 90,9 cc
  - Costo liquido = 90,9 * ($10.000/1000) = $909
- **Costo total = costo liquido + costo del envase**
  (el envase es el insumo de esa version).
  Ej: Envase 1 lt cuesta $200.
  Costo total = $909 + $200 = $1.109.
- **Precio de venta:** costo total + margen de la
  categoria (o manual), con redondeo si aplica.

Cada version habilitada genera una **variante** del
producto (igual que seccion 5.3), con su propio
codigo, stock, precio y costo calculado.

**Venta a medida (envase del cliente):**

A veces el cliente trae su propio envase con un
volumen arbitrario (ej: 4,5 litros).

- En el POS, al seleccionar un producto quimico
  aparece la opcion "Venta a medida".
- Se ingresa la cantidad en cc o litros.
- El sistema calcula el precio en base al costo por
  cc del liquido diluido * volumen + margen.
- No se suma costo de envase (lo trae el cliente).
- **Indicaciones de preparacion:** el POS muestra
  cuanto concentrado y cuanta agua hay que poner.
  Ej: para 4,5 lt de Quita Sarro (relacion 10:1):
  - Concentrado: 4500 / 11 = 409 cc
  - Agua: 4500 - 409 = 4091 cc (~4,1 lt)
- El stock de concentrado se descuenta
  proporcionalmente.

**Actualizacion de costos:**

- Si cambia el costo del concentrado, se recalculan
  automaticamente los costos y precios de todas las
  versiones de ese quimico.
- Si cambia el costo de un envase (producto insumo),
  se recalculan todos los quimicos que usen esa
  version.

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

## 8. Panel de pedidos

El panel tiene cuatro pestañas:

- **Nuevo pedido (+):** crear un pedido manual o
  automatico.
- **Stock critico:** productos con stock bajo o sin
  stock, ordenados por relevancia.
- **Pendientes:** pedidos enviados que aun no llegaron.
- **Historial:** pedidos completados o cancelados.

### 8.1 Crear pedido manual

Boton "+" para armar un pedido desde cero:

1. **Seleccionar proveedor:** se elige el proveedor
   al que se le va a pedir.
2. **Agregar productos:** buscador de productos. Al
   agregar un producto se muestra:
   - Nombre y codigo del producto.
   - Precio de compra con ese proveedor.
   - Stock actual y stock minimo.
   - **Indicador de ranking:** si ese proveedor NO es
     el Proveedor 1 (mas barato) del producto, se
     muestra en rojo la diferencia de precio.
     Ej: "$450 (+$50 vs Distribuidor X)" indicando
     que el Proveedor 1 lo vende $50 mas barato.
   - Campo de cantidad a pedir.
3. **Total estimado:** se va sumando el total a medida
   que se agregan productos.
4. **Enviar pedido:** boton para generar el pedido como
   PDF o texto y enviarlo al proveedor por WhatsApp
   o email, sin salir del sistema.
5. **Guardar pedido:** se guarda con estado "Pendiente"
   y queda en la pestaña de pendientes.

### 8.2 Generacion automatica de pedidos

Boton "Generar automatico" que analiza el stock:

- Analiza todos los productos que estan por debajo de
  su stock minimo o que necesitan reposicion segun su
  politica configurada.
- Para cada producto, selecciona automaticamente al
  **proveedor mas barato** (Proveedor 1 del ranking).
- Agrupa los productos por proveedor, generando un
  pedido sugerido por proveedor.
- Calcula la cantidad a pedir segun la politica de
  reposicion del producto:
  - **Hasta objetivo:** pide la diferencia entre stock
    actual y stock objetivo.
  - **Hasta minimo x factor:** pide la diferencia
    entre stock actual y (stock minimo x factor).
  - **Manual:** no sugiere cantidad, el usuario la
    ingresa.
- Se muestra el pedido sugerido con los mismos datos
  que el pedido manual (precios, ranking, totales).
- El usuario puede editar todo antes de confirmar:
  modificar cantidades, quitar productos, agregar
  otros, o cambiar el proveedor de un producto.
- Al confirmar, se guarda como pedido "Pendiente".

### 8.3 Stock critico

Pestaña que muestra productos que necesitan reposicion:

- **Productos sin stock (stock 0):** resaltados en
  rojo, ordenados por cantidad de ventas (los mas
  vendidos primero, ya que son los mas urgentes).
- **Productos bajo stock minimo:** resaltados en
  amarillo, ordenados por proximidad a cero.
- Para cada producto se muestra: nombre, stock actual,
  stock minimo, ventas ultimos 30 dias, proveedor
  mas barato y su precio.
- **Accion rapida:** boton "Agregar a pedido" que
  agrega el producto directamente a un pedido nuevo
  o existente (en borrador).
- Filtros por categoria, subcategoria, empresa, marca.

### 8.4 Pedidos pendientes

Lista de pedidos que fueron enviados al proveedor
pero aun no llegaron:

- Tabla con: numero de pedido, proveedor, cantidad
  de productos, total estimado, fecha, dias pendiente.
- Al seleccionar un pedido pendiente se ve el detalle
  completo: productos, cantidades pedidas, precios
  estimados.

### 8.5 Recepcion de pedido

Cuando el pedido llega, se selecciona de la lista de
pendientes y se presiona "Registrar recepcion":

1. Se abre el pedido original con todos los productos
   y cantidades que se pidieron.
2. **Para cada producto, el usuario completa:**
   - **Cantidad recibida:** puede ser igual, menor o
     mayor a la pedida. Si es distinta, se resalta
     la diferencia.
   - **Precio real:** si el proveedor cambio el precio,
     se ingresa el nuevo. Se muestra la diferencia
     con el estimado (ej: "Estimado: $400, Real:
     $450, Diferencia: +$50" en rojo).
   - **No recibido:** se puede marcar un producto como
     no entregado (cantidad recibida = 0).
3. **Resumen de recepcion:** antes de confirmar se
   muestra:
   - Productos recibidos completos.
   - Productos con diferencia de cantidad.
   - Productos no recibidos.
   - Total estimado vs total real.
   - Diferencia total.
4. **Confirmar recepcion:**
   - El stock se actualiza automaticamente con las
     cantidades realmente recibidas.
   - Los precios del proveedor se actualizan si
     cambiaron (se recalcula el ranking).
   - Se registra en el historial de precios.
   - El pedido pasa a estado "Recibido" y se mueve
     al historial.
   - El pedido guarda tanto la informacion original
     (lo que se pidio) como la recepcion (lo que
     realmente llego).

**Recepcion parcial:**

- Si el proveedor envia parte del pedido hoy y el
  resto despues, se puede registrar una recepcion
  parcial.
- Al registrar, solo se completan los productos que
  llegaron. Los que no llegaron quedan pendientes.
- El pedido pasa a estado "Recibido parcial" y sigue
  apareciendo en pendientes.
- Se pueden registrar multiples recepciones parciales
  hasta completar todo el pedido.
- Cada recepcion parcial actualiza el stock de los
  productos recibidos en ese momento.
- Al recibir todo, el pedido pasa a "Recibido" y se
  mueve al historial.

### 8.6 Historial de pedidos

- Lista de todos los pedidos con fecha, proveedor,
  total estimado, total real, estado.
- Estados: pendiente, recibido parcial, recibido,
  cancelado.
- Al ver el detalle de un pedido recibido se muestra
  la comparativa lado a lado: pedido vs recepcion
  (cantidades y precios estimados vs reales).
- Filtros por proveedor, por rango de fechas, por
  estado.

### 8.7 Comparativa de proveedores

- Vista general donde se ven todos los productos con
  sus proveedores y precios.
- Tabla comparativa: Producto | Proveedor 1 (precio) |
  Proveedor 2 (precio) | Proveedor 3 (precio).
- Filtros por categoria, por proveedor, por diferencia
  de precio.
- Util para negociar precios o decidir cambios de
  proveedor.

---

## 9. Panel de caja

### 9.1 Apertura de caja

- Boton "Abrir caja" con campo de monto inicial en
  efectivo.
- Registra quien abrio la caja y a que hora.
- Solo se puede vender con la caja abierta.

### 9.2 Durante la jornada

- Todas las ventas en efectivo se suman a la caja.
- Se pueden registrar egresos manuales (ej: pago a
  proveedor en efectivo, retiro de efectivo).
- Se pueden registrar ingresos manuales (ej: prestamo).

### 9.3 Apertura y cierre automatico

- Se puede configurar un horario de apertura automatica
  de caja (ej: todos los dias a las 8:00). El sistema
  abre la caja con el monto inicial configurado.
- Se puede configurar un horario de cierre automatico
  (ej: todos los dias a las 21:00). El sistema cierra
  la caja y registra el monto esperado. El arqueo
  queda pendiente para que el usuario lo complete.
- Si la caja ya esta abierta/cerrada al momento del
  horario automatico, no se duplica la accion.
- Los horarios se configuran en el panel de
  configuracion.

### 9.4 Cierre de caja

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

### 9.5 Historial de cajas

- Lista de todas las cajas con: fecha, usuario, monto
  inicial, monto final, diferencia.
- Detalle de cada caja con todos los movimientos.

---

## 10. Panel de estadisticas

### 10.1 Dashboard principal

Tarjetas de resumen rapido:

- Ventas del dia / semana / mes.
- Ingresos del dia / semana / mes.
- Cantidad de ventas realizadas.
- Margen de ganancia promedio.
- Productos con stock critico.

### 10.2 Graficos

- **Ventas por periodo:** grafico de lineas o barras
  (diario, semanal, mensual). Comparativa entre periodos.
- **Productos mas vendidos:** ranking con barras
  horizontales.
- **Categorias mas vendidas:** grafico de torta o barras.
- **Ingresos vs costos:** comparativa para ver margen.
- **Ventas por metodo de pago:** distribucion.

### 10.3 Metricas avanzadas

- **Rendimiento por vendedor:** ventas totales, cantidad
  de operaciones, ticket promedio por vendedor.
- **Rendimiento por proveedor:** gasto total, productos
  comprados, frecuencia.
- **Clientes frecuentes:** ranking por cantidad de
  compras o monto total.
- **Tendencias:** comparativa mes a mes, deteccion de
  crecimiento o caida.

### 10.4 Alertas

- Productos por debajo del stock minimo.
- Productos sin ventas en los ultimos X dias.
- Cuentas corrientes con deuda alta.

### 10.5 Exportacion

- Exportar cualquier reporte a CSV.
- Exportar a PDF.
- Filtros por rango de fechas en todos los reportes.

---

## 11. Panel de configuracion

### 11.1 Usuarios y roles

- CRUD de usuarios del sistema.
- Roles:
  - **Admin:** acceso total a todos los paneles.
  - **Vendedor:** acceso a ventas, productos (solo
    lectura), clientes. Sin acceso a configuracion ni
    estadisticas completas.
- Cada usuario tiene: nombre, email, contraseña, rol.

### 11.2 Metodos de pago

- CRUD de metodos de pago (efectivo, transferencia,
  tarjeta credito, tarjeta debito, etc.).
- Activar/desactivar metodos.
- **Recargo por metodo:** cada metodo puede tener un
  recargo configurable:
  - Por porcentaje (ej: +10% tarjeta credito).
  - Por monto fijo (ej: +$500).
  - Sin recargo (por defecto).
- El recargo se aplica automaticamente al cobrar con
  ese metodo, pero el vendedor puede quitarlo o
  modificarlo en cada venta individual.

### 11.3 Cuentas de transferencia

- CRUD de cuentas destino para transferencias.
- Campos: nombre, entidad (Mercado Pago, banco, etc.),
  alias/CBU, titular.
- Ej: "Patricio (Mercado Pago)", "Sergio (Mercado Pago)".

### 11.4 Terminales de pago (posnet)

- CRUD de terminales.
- Campos: nombre/numero, ubicacion, proveedor.
- Ej: "Terminal 1", "Terminal 2".

### 11.5 Tipos de tarjeta

- CRUD de tipos de tarjeta.
- Por defecto: VISA, MasterCard, Naranja, American
  Express, Cabal.
- Se pueden agregar o desactivar.

### 11.6 Datos del negocio

- Nombre del negocio.
- Direccion.
- Telefono.
- Logo (para tickets).
- CUIT (para facturacion futura).

### 11.7 Configuracion de envios

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

### 11.8 Configuracion de codigos de producto

- **Criterio global por defecto:**
  - Secuencial numerico (0001, 0002...).
  - Prefijo de categoria + secuencial.
  - Manual (el usuario lo ingresa).
- **Criterio por categoria:** cada categoria puede
  definir su propio prefijo y formato, sobreescribiendo
  el criterio global.
- **Longitud del codigo:** configurable (ej: 4 digitos,
  6 digitos).

### 11.9 Configuracion de caja automatica

- **Hora de apertura automatica:** horario en que el
  sistema abre la caja automaticamente (ej: 08:00).
- **Hora de cierre automatico:** horario en que el
  sistema cierra la caja automaticamente (ej: 21:00).
  El arqueo queda pendiente para completar despues.
- **Monto inicial automatico:** monto con el que se
  abre la caja automaticamente (default 0).
- Se puede activar/desactivar la funcion.
- Si la caja ya esta abierta/cerrada al momento del
  horario, no se duplica la accion.

### 11.10 Configuracion de redondeo

- **Redondeo de precio de venta (producto):**
  - Multiplo de redondeo para precios de venta
    calculados a partir del costo (ej: 10, 50, 100).
  - Se aplica al calcular precio costo + margen.
  - Ej: multiplo 100, calculo da $2.350 → $2.400.
  - Se puede desactivar (sin redondeo).
- **Redondeo del total de venta:**
  - Multiplo de redondeo para el total de la venta
    (ej: 10, 50, 100).
  - Ej: multiplo 50, total $2.930 → $2.950.
  - La diferencia se reparte entre los items de la
    venta para que el ticket cuadre. Se ajusta el
    precio unitario del producto de mayor valor (o
    se distribuye proporcionalmente).
  - Activar/desactivar. El vendedor puede anularlo
    por venta individual.
- **Direccion del redondeo:** siempre hacia arriba
  (ceil al multiplo mas cercano).

### 11.11 Versiones de envase para quimicos

CRUD de las versiones de envase disponibles para
productos quimicos:

- **Nombre** (ej: "250 cc", "500 cc", "1 lt", "5 lt").
- **Capacidad en cc** (valor numerico).
- **Producto insumo** (selector de producto existente
  que representa el envase/botella de esa capacidad).
- Activar/desactivar versiones.
- Al agregar o modificar una version, se recalculan
  los costos de todos los quimicos que la usen.

### 11.12 Reglas de facturacion (fase posterior)

- Configurar que metodos de pago generan factura AFIP
  automaticamente.
- Tipo de factura por defecto (A, B, C).

---

## 12. Modelo de datos

### 12.1 Entidades principales

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
├── Margen_Unidad (nullable, ej: 30.0)
├── Margen_Granel (nullable, ej: 50.0)
├── Prefijo_Codigo (nullable, para codigos por categoria)
├── Ultimo_Secuencial (default 0)
├── Fecha_Creacion
└── Fecha_Actualizacion

Subcategorias
├── Id
├── Id_Categoria (FK)
├── Nombre
├── Descripcion
├── Margen_Unidad (nullable, sobreescribe categoria)
├── Margen_Granel (nullable, sobreescribe categoria)
├── Fecha_Creacion
└── Fecha_Actualizacion

Empresas
├── Id
├── Nombre
├── Descripcion
├── Activo
├── Fecha_Creacion
└── Fecha_Actualizacion

Marcas
├── Id
├── Id_Empresa (FK)
├── Nombre
├── Descripcion
├── Activo
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
├── Stock_Objetivo (nullable)
├── Politica_Reposicion (objetivo, minimo_x_factor,
│   manual)
├── Factor_Reposicion (default 2)
├── Tipo_Venta (unidad, granel)
├── Unidad_Medida (kg, litro, unidad)
├── Es_Padre (booleano, default false)
├── Id_Producto_Padre (FK, nullable)
├── Cantidad_Por_Paquete (nullable, para calculo granel)
├── Orden_Favorito (nullable, para fijar en POS)
├── Tiene_Insumo (booleano, default false)
├── Id_Producto_Insumo (FK, nullable)
├── Activo
├── Id_Categoria (FK)
├── Id_Subcategoria (FK, nullable)
├── Id_Empresa (FK, nullable)
├── Id_Marca (FK, nullable)
├── Margen_Ganancia (nullable, sobreescribe jerarquia)
├── Es_Quimico (booleano, default false)
├── Costo_Concentrado_Litro (nullable, solo quimicos)
├── Relacion_Dilucion (nullable, el X de X:1)
├── Fecha_Creacion
└── Fecha_Actualizacion

Precios_Proveedor
├── Id
├── Id_Producto (FK)
├── Id_Proveedor (FK)
├── Precio_Compra
├── Ranking (calculado: 1 = mas barato)
├── Fecha_Actualizacion_Precio
├── Fecha_Creacion
└── Fecha_Actualizacion

Pedidos
├── Id
├── Id_Proveedor (FK)
├── Estado (pendiente, recibido_parcial, recibido,
│   cancelado)
├── Total_Estimado
├── Total_Real (nullable, al recibir)
├── Notas
├── Id_Usuario (FK)
├── Fecha_Recepcion (nullable)
├── Id_Usuario_Recepcion (FK, nullable)
├── Fecha_Creacion
└── Fecha_Actualizacion

Detalles_Pedido
├── Id
├── Id_Pedido (FK)
├── Id_Producto (FK)
├── Cantidad_Pedida
├── Cantidad_Recibida (nullable, al recibir)
├── Precio_Unitario_Estimado
├── Precio_Unitario_Real (nullable, al recibir)
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
├── Tipo_Recargo (ninguno, porcentaje, monto)
├── Valor_Recargo (default 0)
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
├── Id_Lista_Precio (FK, nullable)
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
├── Trae_Insumo (booleano, default false)
├── Descuento_Insumo (default 0, monto descontado)
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
├── Tipo_Recargo (ninguno, porcentaje, monto)
├── Valor_Recargo (default 0)
├── Monto_Recargo (monto calculado del recargo)
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

Configuracion_Codigos
├── Id
├── Criterio_Global (secuencial, prefijo_categoria,
│   manual)
├── Longitud_Codigo (default 4)
├── Ultimo_Secuencial_Global (default 0)
├── Fecha_Creacion
└── Fecha_Actualizacion

Devoluciones
├── Id
├── Tipo (devolucion, cambio)
├── Resolucion (reembolso, nota_credito, cambio)
├── Monto_Devuelto
├── Monto_Diferencia (nullable, para cambios)
├── Id_Cliente (FK, nullable)
├── Notas
├── Id_Usuario (FK)
├── Fecha_Creacion
└── Fecha_Actualizacion

Detalles_Devolucion
├── Id
├── Id_Devolucion (FK)
├── Id_Producto (FK)
├── Cantidad
├── Precio_Unitario
├── Subtotal
├── Fecha_Creacion
└── Fecha_Actualizacion

Notas_Credito
├── Id
├── Id_Cliente (FK)
├── Id_Devolucion (FK)
├── Monto_Original
├── Monto_Usado (default 0)
├── Monto_Restante
├── Activa (booleano, default true)
├── Fecha_Creacion
└── Fecha_Actualizacion

Historial_Precios
├── Id
├── Id_Producto (FK)
├── Tipo_Precio (costo, venta, lista, proveedor)
├── Id_Lista_Precio (FK, nullable)
├── Id_Proveedor (FK, nullable)
├── Precio_Anterior
├── Precio_Nuevo
├── Id_Usuario (FK)
├── Fecha_Creacion
└── Fecha_Actualizacion

Log_Auditoria
├── Id
├── Id_Usuario (FK)
├── Accion (crear, editar, eliminar, anular, etc.)
├── Entidad (producto, venta, cliente, stock, etc.)
├── Id_Entidad
├── Datos_Anteriores (JSON, nullable)
├── Datos_Nuevos (JSON, nullable)
├── Ip (nullable)
├── Fecha_Creacion
└── Fecha_Actualizacion

Configuracion_Caja
├── Id
├── Hora_Apertura_Auto (nullable, ej: "08:00")
├── Hora_Cierre_Auto (nullable, ej: "21:00")
├── Monto_Inicial_Auto (default 0)
├── Activo (booleano, default false)
├── Fecha_Creacion
└── Fecha_Actualizacion

Versiones_Envase_Quimico
├── Id
├── Nombre (ej: "250 cc", "500 cc", "1 lt")
├── Capacidad_Cc (entero, ej: 250, 500, 1000)
├── Id_Producto_Insumo (FK a Productos, el envase)
├── Activo (booleano, default true)
├── Fecha_Creacion
└── Fecha_Actualizacion

Quimicos_Versiones_Habilitadas
├── Id
├── Id_Producto (FK, el producto quimico)
├── Id_Version_Envase (FK a Versiones_Envase_Quimico)
├── Id_Variante_Generada (FK a Productos, la variante)
├── Fecha_Creacion
└── Fecha_Actualizacion

Configuracion_Redondeo
├── Id
├── Multiplo_Precio_Venta (nullable, ej: 100)
├── Redondeo_Precio_Activo (booleano, default false)
├── Multiplo_Total_Venta (nullable, ej: 50)
├── Redondeo_Total_Activo (booleano, default false)
├── Fecha_Creacion
└── Fecha_Actualizacion
```

---

## 13. API - Endpoints principales

### 13.1 Autenticacion

```
POST   /api/auth/login
POST   /api/auth/registro
GET    /api/auth/perfil
```

### 13.2 Usuarios

```
GET    /api/usuarios
GET    /api/usuarios/{id}
POST   /api/usuarios
PUT    /api/usuarios/{id}
DELETE /api/usuarios/{id}
```

### 13.3 Productos

```
GET    /api/productos
GET    /api/productos/{id}
POST   /api/productos
PUT    /api/productos/{id}
DELETE /api/productos/{id}
GET    /api/productos/{id}/movimientos-stock
GET    /api/productos/{id}/variantes
POST   /api/productos/{id}/variantes
GET    /api/productos/{id}/proveedores-precios
GET    /api/productos/{id}/comparativa-granel
GET    /api/productos/stock-bajo
GET    /api/productos/exportar-csv
POST   /api/productos/importar-csv
POST   /api/productos/importar-csv/preview
POST   /api/productos/{id}/quimico/versiones
GET    /api/productos/{id}/quimico/costos
POST   /api/productos/{id}/quimico/venta-medida
```

### 13.4 Variantes

```
GET    /api/variantes/{id}
PUT    /api/variantes/{id}
DELETE /api/variantes/{id}
```

### 13.5 Precios por proveedor

```
GET    /api/precios-proveedor
POST   /api/precios-proveedor
PUT    /api/precios-proveedor/{id}
POST   /api/precios-proveedor/importar-csv
POST   /api/precios-proveedor/importar-csv/preview
GET    /api/precios-proveedor/comparativa
```

### 13.6 Categorias y subcategorias

```
GET    /api/categorias
POST   /api/categorias
PUT    /api/categorias/{id}
DELETE /api/categorias/{id}
GET    /api/categorias/{id}/subcategorias
POST   /api/subcategorias
PUT    /api/subcategorias/{id}
DELETE /api/subcategorias/{id}
```

### 13.7 Empresas y marcas

```
GET    /api/empresas
POST   /api/empresas
PUT    /api/empresas/{id}
DELETE /api/empresas/{id}
GET    /api/empresas/{id}/marcas
POST   /api/marcas
PUT    /api/marcas/{id}
DELETE /api/marcas/{id}
```

### 13.8 Aumentos masivos de precio

```
POST   /api/aumentos/preview
POST   /api/aumentos/aplicar
```

Permiten aumentar el precio de costo de productos
filtrados por categoria, subcategoria, empresa o
marca. El aumento puede ser por porcentaje o por
monto fijo. El endpoint de preview muestra los
productos afectados y los nuevos precios antes de
confirmar. Al aplicar, se recalculan los precios de
venta segun el margen de la categoria.

### 13.9 Listas de precios

```
GET    /api/listas-precios
POST   /api/listas-precios
PUT    /api/listas-precios/{id}
DELETE /api/listas-precios/{id}
PUT    /api/listas-precios/{id}/productos
```

### 13.10 Stock

```
POST   /api/stock/ingreso
POST   /api/stock/egreso
PUT    /api/stock/movimientos/{id}
GET    /api/stock/movimientos
```

### 13.11 Ventas

```
POST   /api/ventas
GET    /api/ventas
GET    /api/ventas/{id}
PUT    /api/ventas/{id}
POST   /api/ventas/{id}/anular
GET    /api/ventas/{id}/ticket
```

### 13.12 Devoluciones

```
POST   /api/devoluciones
GET    /api/devoluciones
GET    /api/devoluciones/{id}
POST   /api/devoluciones/cambios
```

### 13.13 Notas de credito

```
GET    /api/notas-credito
GET    /api/notas-credito/{id}
GET    /api/clientes/{id}/notas-credito
POST   /api/notas-credito/{id}/usar
```

### 13.14 Envios

```
GET    /api/envios
GET    /api/envios/{id}
PUT    /api/envios/{id}/estado
POST   /api/envios/{id}/pago
GET    /api/envios/pendientes
```

### 13.15 Clientes

```
GET    /api/clientes
GET    /api/clientes/{id}
POST   /api/clientes
PUT    /api/clientes/{id}
DELETE /api/clientes/{id}
GET    /api/clientes/{id}/historial
```

### 13.16 Campos custom de clientes

```
GET    /api/campos-custom
POST   /api/campos-custom
PUT    /api/campos-custom/{id}
DELETE /api/campos-custom/{id}
```

### 13.17 Proveedores

```
GET    /api/proveedores
GET    /api/proveedores/{id}
POST   /api/proveedores
PUT    /api/proveedores/{id}
DELETE /api/proveedores/{id}
GET    /api/proveedores/{id}/cuenta-corriente
POST   /api/proveedores/{id}/importar-precios-csv
```

### 13.18 Ordenes de compra

```
POST   /api/ordenes-compra
GET    /api/ordenes-compra
GET    /api/ordenes-compra/{id}
```

### 13.19 Pagos a proveedores

```
POST   /api/proveedores/{id}/pagos
GET    /api/proveedores/{id}/pagos
```

### 13.20 Pedidos

```
POST   /api/pedidos
POST   /api/pedidos/generar
GET    /api/pedidos
GET    /api/pedidos/{id}
PUT    /api/pedidos/{id}
POST   /api/pedidos/{id}/recepcion
POST   /api/pedidos/{id}/enviar-proveedor
PUT    /api/pedidos/{id}/cancelar
GET    /api/pedidos/stock-critico
GET    /api/pedidos/comparativa-proveedores
```

### 13.21 Caja

```
POST   /api/caja/abrir
POST   /api/caja/cerrar
GET    /api/caja/actual
POST   /api/caja/movimiento
GET    /api/caja/historial
GET    /api/caja/{id}
```

### 13.22 Estadisticas

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

### 13.23 Configuracion

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
GET    /api/configuracion/caja
PUT    /api/configuracion/caja
GET    /api/configuracion/redondeo
PUT    /api/configuracion/redondeo
GET    /api/configuracion/versiones-envase
POST   /api/configuracion/versiones-envase
PUT    /api/configuracion/versiones-envase/{id}
DELETE /api/configuracion/versiones-envase/{id}
```

### 13.24 Historial de precios

```
GET    /api/historial-precios
GET    /api/historial-precios/producto/{id}
```

### 13.25 Auditoria

```
GET    /api/auditoria
GET    /api/auditoria/{entidad}/{id}
```

---

## 14. Integraciones futuras (fases posteriores)

### 14.1 Mercado Pago

- Integracion con API de Mercado Pago para verificar
  transferencias entrantes.
- Al recibir una transferencia confirmada, habilitar
  automaticamente la venta.
- Webhook para notificaciones en tiempo real.

### 14.2 AFIP - Facturacion electronica

- Integracion con web services de AFIP.
- Emision de facturas A, B y C.
- Configurable: definir que metodos de pago disparan
  facturacion automatica.
- Almacenamiento de CAE y datos fiscales por venta.

---

## 15. Seguridad

- Autenticacion con JWT (access + refresh tokens).
- Contraseñas hasheadas con bcrypt.
- Proteccion de endpoints por rol.
- Validacion de datos con Pydantic en todos los
  endpoints.
- Variables de entorno para datos sensibles.
- CORS configurado para el dominio del frontend.

---

## 16. Consideraciones de UX

- Interfaz responsive (funciona en PC y tablet).
- Navegacion lateral colapsable.
- Atajos de teclado en el POS para agilizar ventas.
- Feedback visual en acciones (toasts de confirmacion
  y error).
- Carga lazy de datos pesados.
- Busqueda en tiempo real con debounce.
- **Modo offline / resiliencia:** si se cae la conexion
  al backend, el POS guarda las ventas en una cola
  local (localStorage/IndexedDB) y las sincroniza
  automaticamente cuando se restablece la conexion.
  Se muestra un indicador visual de estado offline.
- **Notificaciones en tiempo real:** WebSockets para
  alertas de stock bajo, envios pendientes, caja sin
  cerrar, y cambios realizados por otros usuarios.
  Util cuando hay varios vendedores simultaneos.
