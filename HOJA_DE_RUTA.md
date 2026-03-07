# Mascoter - Hoja de ruta

## Fase 1: Fundacion y autenticacion

Proyecto base funcionando con autenticacion y configuracion.

- [ ] Inicializar proyecto backend (FastAPI + estructura)
- [ ] Configurar PostgreSQL y SQLAlchemy
- [ ] Configurar Alembic para migraciones
- [ ] Modelo de Usuario con roles (Admin, Vendedor)
- [ ] Endpoints de registro, login y autenticacion JWT
- [ ] Middleware de proteccion por roles
- [ ] Modelos de configuracion (Metodos_Pago,
  Cuentas_Transferencia, Terminales_Pago, Tipos_Tarjeta,
  Configuracion_Negocio)
- [ ] Endpoints CRUD de configuracion
- [ ] Seed de datos iniciales (admin, metodos de pago,
  tarjetas por defecto)
- [ ] Inicializar proyecto frontend (React + Vite + TS)
- [ ] Pantalla de login
- [ ] Contexto de autenticacion en React
- [ ] Rutas protegidas en frontend
- [ ] Layout principal con navegacion lateral
- [ ] Panel de configuracion (usuarios, metodos de pago,
  cuentas, terminales, tarjetas, datos del negocio)

## Fase 2: Gestion de productos

CRUD completo de productos con categorias, listas de
precios y movimientos de stock.

- [ ] Modelos: Producto, Categoria, Listas_Precios,
  Precios_Productos, Movimientos_Stock
- [ ] Esquemas Pydantic para productos, categorias,
  listas de precios y movimientos de stock
- [ ] Endpoints CRUD de categorias
- [ ] Endpoints CRUD de productos (con filtros y paginacion)
- [ ] Endpoints CRUD de listas de precios
- [ ] Endpoints de movimientos de stock (ingreso, egreso,
  historial, edicion)
- [ ] Endpoint de productos con stock bajo
- [ ] Pagina de listado de productos (tabla con busqueda)
- [ ] Formulario de crear/editar producto
- [ ] Pagina de categorias
- [ ] Pagina de listas de precios
- [ ] Pagina de movimientos de stock por producto
- [ ] Indicadores visuales de stock bajo

## Fase 3: Ventas (POS)

Punto de venta completo con carrito, descuentos, pago
mixto, calculo de vuelto y generacion de ticket.

- [ ] Modelos: Venta, Detalles_Venta, Pagos_Venta
- [ ] Esquemas Pydantic para ventas y pagos
- [ ] Endpoint de registrar venta (con descuento de stock
  automatico y registro en caja)
- [ ] Endpoint de listar ventas (con filtros por fecha,
  cliente, vendedor)
- [ ] Endpoint de detalle de venta
- [ ] Endpoint de anular venta (revertir stock y caja)
- [ ] Endpoint de generar ticket
- [ ] Pantalla POS con layout dividido (productos + carrito)
- [ ] Buscador de productos con tres modos (lista simple,
  lista compleja, cuadricula)
- [ ] Carrito con edicion de cantidad y precio
- [ ] Soporte para productos a granel (por unidad o monto)
- [ ] Descuentos (monto, porcentaje, al costo, por cliente)
- [ ] Selector de cliente (opcional)
- [ ] Modal de cobro con pago mixto
- [ ] Calculo de vuelto en efectivo
- [ ] Selector de cuenta transferencia, terminal, tarjeta
- [ ] Pantalla de ticket con botones imprimir/enviar
- [ ] Historial de ventas con filtros

## Fase 4: Clientes

Gestion de clientes con campos personalizables,
descuentos e historial.

- [ ] Modelos: Cliente, Campos_Custom_Clientes,
  Valores_Campos_Custom
- [ ] Esquemas Pydantic para clientes y campos custom
- [ ] Endpoints CRUD de clientes
- [ ] Endpoints CRUD de campos custom
- [ ] Endpoint de historial de compras por cliente
- [ ] Pagina de listado de clientes
- [ ] Formulario de crear/editar cliente (con campos
  custom y configuracion de descuento)
- [ ] Vista de detalle de cliente con historial de compras

## Fase 5: Proveedores

Gestion de proveedores con ordenes de compra y cuentas
corrientes.

- [ ] Modelos: Proveedor, Productos_Proveedores,
  Ordenes_Compra, Detalles_Orden_Compra,
  Movimientos_Cuenta_Corriente
- [ ] Esquemas Pydantic para proveedores, ordenes y
  cuenta corriente
- [ ] Endpoints CRUD de proveedores
- [ ] Endpoints de ordenes de compra (con actualizacion
  automatica de stock)
- [ ] Endpoints de pagos y cuenta corriente
- [ ] Pagina de listado de proveedores
- [ ] Formulario de crear/editar proveedor
- [ ] Vista de detalle con productos, ordenes y cuenta
  corriente
- [ ] Formulario de orden de compra
- [ ] Registro de pagos a proveedor

## Fase 6: Caja

Control de caja diaria con apertura, cierre y arqueo.

- [ ] Modelos: Cajas, Movimientos_Caja
- [ ] Esquemas Pydantic para caja y movimientos
- [ ] Endpoint de abrir caja
- [ ] Endpoint de cerrar caja (con calculo de esperado
  y diferencia)
- [ ] Endpoint de registrar movimiento manual
- [ ] Endpoint de caja actual
- [ ] Endpoint de historial de cajas
- [ ] Bloqueo de ventas si no hay caja abierta
- [ ] Pantalla de caja actual (estado, movimientos)
- [ ] Modal de apertura de caja
- [ ] Modal de cierre con arqueo
- [ ] Historial de cajas con detalle

## Fase 7: Estadisticas y reportes

Dashboard con metricas, graficos, alertas y exportacion.

- [ ] Endpoints de estadisticas (ventas por periodo,
  productos mas vendidos, categorias, ingresos vs costos,
  metodos de pago, rendimiento vendedores, rendimiento
  proveedores, clientes frecuentes)
- [ ] Endpoint de alertas (stock bajo, productos sin
  ventas, deudas proveedores)
- [ ] Endpoint de exportacion (CSV, PDF)
- [ ] Dashboard principal con tarjetas de resumen
- [ ] Grafico de ventas por periodo (lineas/barras)
- [ ] Grafico de productos/categorias mas vendidos
- [ ] Grafico de ingresos vs costos
- [ ] Grafico de metodos de pago
- [ ] Ranking de vendedores
- [ ] Ranking de clientes frecuentes
- [ ] Panel de alertas
- [ ] Exportacion CSV y PDF con filtros de fecha

## Fase 8: Deploy y produccion

Sistema funcionando en la nube con Railway.

- [ ] Configurar Dockerfile para backend
- [ ] Configurar build de frontend para produccion
- [ ] Crear proyecto en Railway
- [ ] Configurar PostgreSQL en Railway
- [ ] Variables de entorno en Railway
- [ ] Deploy de backend
- [ ] Deploy de frontend
- [ ] Configurar dominio (opcional)
- [ ] Pruebas en produccion

## Fase 9: Integracion Mercado Pago (futura)

Verificacion automatica de transferencias entrantes.

- [ ] Integracion con API de Mercado Pago
- [ ] Webhook para notificaciones de pago
- [ ] Verificacion automatica al cobrar con transferencia
- [ ] Pantalla de configuracion de credenciales MP

## Fase 10: Integracion AFIP (futura)

Facturacion electronica automatica.

- [ ] Integracion con web services de AFIP
- [ ] Emision de facturas A, B y C
- [ ] Configuracion de reglas de facturacion por metodo
  de pago
- [ ] Almacenamiento de CAE por venta

## Orden de ejecucion

Fase 1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7 -> 8 -> 9 -> 10

Cada fase se completa antes de iniciar la siguiente.
Dentro de cada fase, primero se desarrolla el backend
y luego el frontend correspondiente.

Las fases 9 y 10 son opcionales y se implementan una
vez que el sistema base este estable en produccion.
