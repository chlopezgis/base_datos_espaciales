# CREACIÓN DE UNA BASE DE DATOS ESPACIAL
<p>Tutorial que explica como crear una Base de Datos Espacial con PostGIS</p>

## ¿Qué es una base de datos?
<p> Una base de datos es una colección organizada y estructurada de datos relacionados entre sí, cuyo objetivo es facilitar el uso y acceso a la información.</p>

## ¿Qué es una base de datos espacial?
<p>Es una base de datos optimizada que permite almacenar y manipular objetos espaciales. Existen tres aspectos que asocian los datos espaciales con una base de datos: tipos de datos, índices y funciones.</p>

* **Tipo de datos espaciales**: Tipo de datos que permiten almacenar y representar carácteristicas geográficas.
* **Índices espaciales**: Tipo de índice extendido que permite indexar una columna espacial. Se utilizan para mejorar el rendimiento de consultas espaciales
* **Funciones espaciales**: Funciones que permiten analizar componentes geométricos, determinar relaciones espaciales y manipular geometrías.

## ¿Qué es PostGIS?
<p>Es una extensión que convierte el sistema de administración de bases de datos PostgreSQL en una base de datos espacial.</p>

## ¿Cómo crear una base de datos espacial con PostGIS?
<p>A continuación, se explicará como crear una base de datos espacial utilizando PostGIS 2.0 en un sistema operativo windows 10.</p>

### I. Añadir variable de Entorno.
<p>Para el desarrollo de este tutorial utilizaremos los comando de utilidad que provee PostgreSQL y que se ejecutan desde la consola. Los comandos mas utilizados son:</p>

| Comando de utilidad | Descripción |
|-------------------|-------------|
| **psql**   | Cliente de línea de comandos que permite comunicarnos con el servidor de PostgreSQL mediante sentencias SQL. Además, proporciona una serie de metacomandos y varias funciones similares a las de un shell para facilitar la escritura de scripts y la automatización de una amplia variedad de tareas.    |
| **createdb** | Crear una nueva base de datos.       |
| **dropdb** | Borrar una base de datos existente. |
| **pg_dump** | Realizar copias de seguridad de una base de datos |
| **pg_restore** | Restaurar una base de datos PostgreSQL desde un archivo creado por pg_dump |

<p>Para utilizar las utilidades de PostgreSQL en sistemas operativos windows, es necesario configurar las variables de entorno del sistema.</p>

1. Como primer paso, debemos identificar y copiar la ruta del directorio "**bin**" que es donde se almacenan los comandos de utilidad. Este directorio depende de la instalación y la versión de postgreSQL, por lo general debe ser una ruta similar a la siguiente:

```
C:\Program Files\PostgreSQL\14\bin
```

2. Abrir las variables de entorno del sistema 

![image](https://user-images.githubusercontent.com/88239150/174875175-37d190d2-83f8-44c8-9a62-9c085a0964a9.png)

3. En las **variables del sistema** seleccionar la variable **path** y luego dar clic en **Editar**.

![image](https://user-images.githubusercontent.com/88239150/174875654-4564bbb0-d290-4530-b057-2405a7f985ba.png)

4. En la ventana de Edición de Variables de Entorno, dar clic en el botón **Nuevo** y copiar la ruta en la **nueva línea**. Finalmente, dar clic en **Aceptar** todo.

![image](https://user-images.githubusercontent.com/88239150/174876489-572e65db-63eb-466a-b216-e7e5693340db.png)

### II. Crear una Base de Datos.

<p>Ahora procederemos a crear una base de datos.</p>

1. Abrir la consola de comandos de Windows.
2. Ejecutar el comando **createdb**

```
createdb -h <hostname> -p <port> -U <username> <dbname>
```

Si se ejecuta desde el servidor podemos omitir el nombre del host y el puerto:

```
createdb -U <username> <dbname>
```
Entonces, crearemos la base de datos de nombre gis utilizando un usuario con permisos de creación de base de datos, para este ejemplo utilizaremos el superusuario *postgres*

```
createdb -U postgres gis
```

### III. Configurar Base de Datos.

Vamos a configurar la base de datos para instalar todas las funciones espaciales en un nuevo esquema. De esta manera, tendremos separada las funciones espaciales de otras funciones que se puedan crear en el esquema por **public**. Antes de iniciar, es necesario conocer la sintaxis para ejecutar consultas SQL con psql.

```
psql -h <hostname> -U <username> -p <port> -d <dbname> -c "<QUERY>"
```
1. Como primer paso, crearemos el esquema **postgis** que almacenará todas las funciones espaciales.

```
psql -U postgres -d gis -c "CREATE SCHEMA postgis"
```

2. Luego, procederemos a dar acceso a todos los usuarios al esquema **postgis**
```
psql -U postgres -d gis -c "GRANT USAGE ON SCHEMA postgis TO public"
```

3. Añadiremos el esquema **postgis** a la ruta de búsqueda de esquemas (**search_path**)
```
psql -U postgres -d gis -c "ALTER DATABASE gis SET search_path = public,postgis,contrib"
```

<p>Para verificar que el esquema se agrego a la ruta de búsqueda ejecutamos los siguiente</p>

```
psql -U postgres -d gis -c "SHOW search_path"
```

### III. Crear extensiones espaciales

Una vez configurada la base de datos crearemos las extensiones espaciales, en el esquema **postgis**, que permitiran almacenar, manipular y consultar la información geográfica. La extensiones son las siguientes:

* **postgis**: Administrar objetos espaciales como puntos, lineas y polígonos.

* **postgis_raster**: Administrar datos ráster.

* **postgis_topology**: Administrar objetos topológicos como nodos, bordes, caras y sus relaciones.

```
psql -U postgres -d gis -c "CREATE EXTENSION postgis SCHEMA postgis"

psql -U postgres -d gis -c "CREATE EXTENSION postgis_raster SCHEMA postgis"

psql -U postgres -d gis -c "CREATE EXTENSION postgis_topology"
```
#### IV. Verificar la instalación

Primero listaremos las base de datos existentes en el servidor. Debe aparecer la base de datos creada.

```
psql -h <hostname> -p <port> -U <username> -l
```
