# Mascoter - Sistema de gestion para pet shop

## Descripcion del proyecto

Mascoter es un sistema web de gestion integral para pet shops.
Permite administrar productos, registrar ventas, gestionar
clientes y visualizar estadisticas del negocio.

## Stack tecnologico

- **Backend:** Python 3.12+ con FastAPI
- **Frontend:** React 18+ con Vite y TypeScript
- **Base de datos:** PostgreSQL 16+
- **ORM:** SQLAlchemy 2.0+ con Alembic para migraciones
- **Autenticacion:** JWT (JSON Web Tokens)
- **Deploy:** Railway (backend + frontend + base de datos)

## Estructura del proyecto

```
Mascoter/
  Backend/
    App/
      Api/              # Endpoints organizados por modulo.
      Modelos/          # Modelos SQLAlchemy.
      Esquemas/         # Esquemas Pydantic.
      Servicios/        # Logica de negocio.
      Nucleo/           # Configuracion, seguridad, dependencias.
    Migraciones/        # Migraciones de Alembic.
    Pruebas/            # Tests con pytest.
    requirements.txt
  Frontend/
    src/
      Componentes/      # Componentes reutilizables de React.
      Paginas/          # Vistas principales.
      Servicios/        # Llamadas a la API.
      Contextos/        # Context API para estado global.
      Hooks/            # Custom hooks.
      Tipos/            # Tipos TypeScript.
    public/
    package.json
```

## Convenciones de codigo

### General

- Todas las carpetas y nombres de archivos deben usar
  Pascal_Snake_Case (ej: Servicios/, Crear_Producto.py,
  Tabla_Ventas.tsx).

### Python (Backend)

- Usar Pascal_Snake_Case en variables, funciones, clases,
  modulos y archivos.
- Nombres descriptivos, sin abreviaciones.
- Todo el codigo en espanol (variables, funciones, comentarios).
- Comentarios descriptivos, siempre finalizados con punto.
- Lineas de maximo 70 caracteres.
- Signo = con espacio antes y despues.
- Funciones con responsabilidad unica, nombres con verbo.
- Docstrings exhaustivos con type hints en cada funcion y clase.
- Formato de docstring con lineas vacias entre parametros,
  contenido y comillas de cierre.

### TypeScript/React (Frontend)

- Usar Pascal_Snake_Case en variables, funciones, componentes,
  archivos y carpetas.
- Nombres descriptivos, sin abreviaciones.
- Todo el codigo en espanol.
- Comentarios descriptivos finalizados con punto.
- Lineas de maximo 70 caracteres.
- Componentes funcionales con hooks.
- Tipado estricto con TypeScript, evitar `any`.

### Base de datos

- Nombres de tablas en Pascal_Snake_Case y en plural
  (Productos, Ventas, Clientes).
- Nombres de columnas en Pascal_Snake_Case
  (Nombre_Producto, Precio_Unitario).
- Toda tabla tiene Id como clave primaria autoincremental.
- Timestamps: Fecha_Creacion y Fecha_Actualizacion en toda
  tabla.

## Modulos del sistema

1. **Autenticacion y Usuarios:** Login, roles (Admin, Vendedor),
   gestion de usuarios.
2. **Gestion de Productos:** CRUD de productos, categorias,
   control de stock, precios.
3. **Ventas y Facturacion:** Registro de ventas, tickets,
   historial, metodos de pago.
4. **Estadisticas y Reportes:** Dashboards, graficos de ventas,
   productos mas vendidos, tendencias.
5. **Gestion de Clientes:** Base de clientes, historial de
   compras.

## Reglas de desarrollo

- No agregar funcionalidades no solicitadas.
- Validar datos en el backend con Pydantic.
- Manejar errores con excepciones HTTP apropiadas.
- Proteger endpoints segun rol de usuario.
- Usar variables de entorno para configuracion sensible.
- Escribir tests para la logica de negocio critica.
- El frontend debe ser responsive.

## Comandos utiles

```bash
# Backend
cd backend
pip install -r Requirements.txt
uvicorn app.principal:Aplicacion --reload

# Frontend
cd frontend
npm install
npm run dev

# Migraciones
cd backend
alembic upgrade head
alembic revision --autogenerate -m "descripcion"
```
