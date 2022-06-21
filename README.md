# CREACIÓN DE UNA BASE DE DATOS ESPACIAL
<p>Tutorial que explica como crear una Base de Datos Espacial con PostGIS</p>

## ¿Qué es una base de datos?
<p> Una base de datos es una colección organizada y estructurada de datos relacionados entre sí, cuyo objetivo es facilitar el uso y acceso a la información</p>

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
<p>Para el desarrollo de este tutorial utilizaremos las utilidades de línea de comando que provee PostgreSQL:</p>

* **psql**: Cliente de línea de comandos que permite comunicarnos con el servidor de PostgreSQL mediante sentencias SQL. Además, proporciona una serie de metacomandos y varias funciones similares a las de un shell para facilitar la escritura de scripts y la automatización de una amplia variedad de tareas.

* **createdb**: Es una utilidad de línea de comandos que permite crear una base de datos.

<p>Para utilizar las utilidades de PostgreSQL en sistemas operativos windows, es necesario configurar las variables de entorno del sistema.</p>

1. Como primer paso, debemos identificar y copiar la ruta del directorio donde se almacenan estas utilidades (binarios y/o ejecutables). Este directorio depende de la instalación y la versión de postgreSQL, por lo general debe ser una ruta similar a la siguiente:

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
![image](https://user-images.githubusercontent.com/88239150/174878240-48f68995-28a9-4c7a-b190-46c97bc16855.png)

### III. Configurar Base de Datos.
<p>Vamos a configurar la base de datos para instalar todas las funciones espaciales en un nuevo esquema. De esta manera, tendremos separada las funciones espaciales de otras funciones que se puedan crear en el esquema por defecto (Esquema **public**). Antes de iniciar, es necesario conocer la sintaxis para ejecutar consultas SQL con psql</p>

```
psql -h <hostname> -U <username> -p <port> -d <dbname> -c "<QUERY>"
```
1. Como primer paso, crearemos el esquema **postgis** que almacenará todas las funciones espaciales.

```
psql -U postgres -d gis -c "CREATE SCHEMA postgis"
```
![image](https://user-images.githubusercontent.com/88239150/174879918-70ce1909-a96a-4d76-8559-3a261597f834.png)

2. Luego, procederemos a dar acceso a todos los usuarios al esquema **postgis**
```
psql -U postgres -d gis -c "GRANT USAGE ON SCHEMA postgis TO public"
```
![image](https://user-images.githubusercontent.com/88239150/174880441-13965065-2ffa-43fa-a7bd-a3e0d6d2a224.png)

3. Añadiremos el esquema **postgis** a la ruta de búsqueda de esquemas (**search_path**)
```
psql -U postgres -d gis -c "ALTER DATABASE gis SET search_path = public,postgis,contrib"
```
![image](https://user-images.githubusercontent.com/88239150/174881104-809c70df-af0e-44e6-ae60-ea30b894dcb7.png)

<p>Para verificar que el esquema se agrego a la ruta de búsqueda ejecutamos los siguiente</p>

```
psql -U postgres -d gis -c "SHOW search_path"
```

![image](https://user-images.githubusercontent.com/88239150/174881513-55e4f04c-9ece-443d-97ad-089e4b8f6441.png)

