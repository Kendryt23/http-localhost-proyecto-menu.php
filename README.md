# Sistema de Login por Roles - ElectroTech

Proyecto final de Administración de Bases de Datos. Sistema de gestión para una tienda de productos electrónicos con control de accesos por roles.

## Integrantes del Grupo

- Diseñador de datos: [Nombre]
- Administrador de BD: [Nombre]
- Desarrollador: [Nombre]
- Coordinador y tester: [Nombre]

## Descripción del Sistema

ElectroTech es una tienda de productos electrónicos que necesita un sistema para:
- Gestionar inventario de productos
- Registrar ventas de forma segura
- Controlar accesos según el rol del usuario
- Generar informes de ventas

## Roles del Sistema

### Admin
- Crea y gestiona usuarios del sistema
- Revisa auditoría de accesos
- Tiene acceso completo a la información

### Dueño
- Gestiona productos, precios y clientes
- Registra ventas
- Ve informes y estadísticas
- No puede crear usuarios

### Empleado
- Registra ventas diarias
- Consulta productos disponibles
- Ve solo su historial de ventas
- No puede ver datos de otros empleados

## Estructura del Proyecto

```
Proyecto final/
├── 1_analisis.md           # Análisis del problema y requisitos
├── 2_diseno.md             # Diseño de la base de datos (diagrama E-R)
├── 00_partida.sql          # Script de partida (base de datos y tablas de login)
├── 01_estructura.sql       # Estructura de tablas del negocio
├── 02_datos.sql            # Datos de prueba
├── 03_seguridad.sql        # Usuarios MySQL, permisos, vistas, procedimientos, triggers
├── 04_consultas.sql        # Consultas de análisis
├── pruebas.md              # Tabla de pruebas y respaldo
└── README.md               # Este archivo

C:\xampp\htdocs\proyecto/
├── conexion.php            # Conexión a la base de datos
├── login.php               # Formulario de login
├── menu.php                # Menú principal según rol
├── logout.php              # Cerrar sesión
├── admin_usuarios.php      # Gestión de usuarios (admin)
├── admin_auditoria.php     # Auditoría de accesos (admin)
├── gestionar_productos.php # Gestión de productos (dueño)
├── registrar_venta.php     # Registrar venta (dueño/empleado)
├── ver_productos.php       # Ver productos (todos)
├── ver_ventas.php          # Ver todas las ventas (admin/dueño)
├── informe_ventas.php      # Informe de ventas (dueño)
├── ver_clientes.php        # Ver clientes (dueño)
└── mis_ventas.php          # Mis ventas (empleado)
```

## Instalación

### Requisitos
- MySQL 8 con MySQL Workbench
- XAMPP (Apache + PHP + MySQL)
- Navegador web

### Pasos

1. **Copiar archivos PHP**
   - Crear carpeta `C:\xampp\htdocs\proyecto`
   - Copiar todos los archivos `.php` a esa carpeta

2. **Ejecutar scripts SQL en orden**
   - Abrir MySQL Workbench
   - Conectarse como `root`
   - Ejecutar `00_partida.sql`
   - Ejecutar `01_estructura.sql`
   - Ejecutar `02_datos.sql`
   - Ejecutar `03_seguridad_simple.sql` (versión para XAMPP/MariaDB)
   - **Nota**: Si usas MySQL 8 puro, usa `03_seguridad.sql` en su lugar

3. **Verificar instalación**
   ```sql
   USE proyecto_roles;
   SHOW TABLES;
   SELECT * FROM rol;
   SELECT * FROM usuario;
   ```

4. **Acceder a la aplicación**
   - Abrir navegador en `http://localhost/proyecto/login.php`

## Usuarios de Prueba

| Usuario  | Contraseña | Rol      |
|----------|------------|----------|
| admin    | password   | Admin    |
| duenio   | password   | Dueño    |
| empleado1| password   | Empleado |
| empleado2| password   | Empleado |
| empleado3| password   | Empleado |

**Nota**: La contraseña en la base de datos está cifrada. La palabra "password" es el texto plano original.

## Generar Contraseñas Cifradas

Para generar nuevos hashes de contraseñas, abrir Shell de XAMPP y ejecutar:

```bash
php -r "echo password_hash('TuContraseña', PASSWORD_DEFAULT);"
```

Copiar el resultado (empieza con `$2y$`) y usarlo en la columna `password_hash` de la tabla `usuario`.

## Permisos MySQL

El sistema crea tres usuarios de MySQL:

- `app_admin`@`localhost` - Contraseña: `Admin!2026#Secure`
- `app_duenio`@`localhost` - Contraseña: `Duenio!2026#Secure`
- `app_empleado`@`localhost` - Contraseña: `Emp!2026#Secure`

### Verificar permisos

```sql
SHOW GRANTS FOR 'app_admin'@'localhost';
SHOW GRANTS FOR 'app_duenio'@'localhost';
SHOW GRANTS FOR 'app_empleado'@'localhost';
```

### Probar permisos

Conectarse como cada usuario y probar:

```bash
# Como app_empleado
mysql -u app_empleado -p proyecto_roles
SELECT * FROM producto;  -- Debe funcionar
DELETE FROM producto WHERE id_producto = 1;  -- Debe fallar
```

## Respaldo y Restauración

### Hacer copia

```bash
mysqldump -u root -p --routines --triggers proyecto_roles > respaldo.sql
```

### Restaurar

```bash
mysql -u root -p -e "DROP DATABASE proyecto_roles; CREATE DATABASE proyecto_roles;"
mysql -u root -p proyecto_roles < respaldo.sql
```

## Consultas de Análisis

Las consultas requeridas están en `04_consultas.sql`:

1. Ventas totales por día
2. Producto más vendido
3. Empleado con más ventas
4. Productos con stock bajo
5. Intentos de login fallidos por usuario

## Características de Seguridad

- Contraseñas cifradas con `password_hash()` y verificadas con `password_verify()`
- Auditoría de todos los intentos de login
- Permisos granulares por rol en MySQL
- Consultas SQL preparadas para prevenir inyección SQL
- Validación de roles en cada página PHP
- Restricciones de integridad (PK, FK, UNIQUE, CHECK)

## Pruebas

Ver archivo `pruebas.md` para la tabla completa de pruebas por rol.

## Notas para la Presentación

1. Explicar el problema: gestión manual de inventario y ventas
2. Mostrar el diagrama E-R
3. Demostrar login con los 3 roles
4. Mostrar las diferentes vistas según el rol
5. Demostrar el procedimiento almacenado de registro de venta
6. Mostrar el trigger de historial de precios
7. Probar los permisos de MySQL (mostrar error de permiso denegado)
8. Realizar la restauración de la copia de seguridad
