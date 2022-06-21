# CREACIÓN DE UNA BASE DE DATOS ESPACIAL
<p>Tutorial que explica como crear una Base de Datos Espacial con PostGIS</p>

### ¿Qué es una base de datos?
<p> Una base de datos es una colección organizada y estructurada de datos relacionados entre sí, cuyo objetivo es facilitar el uso y acceso a la información</p>

### ¿Qué es una base de datos espacial?
<p>Es una base de datos optimizada que permite almacenar y manipular objetos espaciales. Existen tres aspectos que asocian los datos espaciales con una base de datos: tipos de datos, índices y funciones.</p>

* **Tipo de datos espaciales**: Tipo de datos que permiten almacenar y representar carácteristicas geográficas.
* **Índices espaciales**: Tipo de índice extendido que permite indexar una columna espacial. Se utilizan para mejorar el rendimiento de consultas espaciales
* **Funciones espaciales**: Funciones que permiten analizar componentes geométricos, determinar relaciones espaciales y manipular geometrías.

### ¿Qué es PostGIS?
<p>Es una extensión que convierte el sistema de administración de bases de datos PostgreSQL en una base de datos espacial.</p>

### ¿Cómo crear una base de datos espacial con PostGIS?
<p>A continuación, se explicará como crear una base de datos espacial utilizando PostGIS 2.0 en un sistema operativo windows 10.</p>

#### 1. Establecer variable de Entorno.
<p>Para el desarrollo de este tutorial utilizaremos las utilidades de línea de comando que provee PostgreSQL:</p>

* **psql**: Cliente de línea de comando que permite escribir consultas de forma interactiva, enviarlas a PostgreSQL y ver los resultados de la consulta. Además, proporciona una serie de metacomandos y varias funciones similares a las de un shell para facilitar la escritura de scripts y la automatización de una amplia variedad de tareas.

* **createdb**: Es una utilidad de línea de comandos que nos permite crear una base de datos.

<p></p>
