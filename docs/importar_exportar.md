<center><h1>Cap 2. IMPORTAR Y EXPORTAR DATOS</h1></center>

Los datos son el componente mas importante de un SIG ya que sin estos es imposible realizar cualquier análisis. Actualmente, existen muchas fuentes de datos disponibles que podemos incorporar como punto de partida en nuestra Base de Datos (previa evaluación de calidad). 

En este capítulo se mostrarán las principales herramientas y procesos para importar y exportar datos geoespaciales, de diferentes formatos, en nuestra base de datos.

## Ante de iniciar...

Crear una base de datos espacial tomando como plantilla la base de datos creada en el capitulo anterior (ver [aquí](https://chlopezgis.github.io/base_datos_espaciales/creacion))

_**NOTA: Los comandos se ejecutaran desde el servidor y utilizando el puerto por defecto, por lo que a lo largo del tutorial omitiremos estos parámetros**_

```
createdb -U <username> -T <tempalate> <dbname>
```

Ejecutando el comando

```
createdb -U postgres -T gis lore
```

Listar todas las bases existentes

```
psql -U postgres -l
```

![image](https://user-images.githubusercontent.com/88239150/178155564-ebb18b3f-6693-4d9c-b7c5-9f76facc4542.png)

Ahora, debemos modificar la ruta de búsqueda de esquemas (search_path). Esto evitará que tengamos que llamar a las funciones espaciales anteponiendo el nombre del esquema

```
psql -U postgres -d lore -c "ALTER DATABASE lore SET search_path = public,postgis,contrib"
```

![image](https://user-images.githubusercontent.com/88239150/178155726-d9384962-c703-417e-a1cd-8e4c6c0dd7ea.png)

Finalmente, verificar que PostGIS se instalo correctamente:

```
psql -U postgres -d lore -c "SELECT postgis_full_version()"
```

![image](https://user-images.githubusercontent.com/88239150/178155791-2d67fb92-4a44-4118-bf19-8986e0464adb.png)

## 1. Importar y exportar datos tabulares con el comando COPY

El comando **COPY** nos permite mover datos entre tablas de PostgreSQL y archivos de texto plano (CSV o TXT). 

**COPY FROM**
* Permite copiar los datos de un archivo de texto plano a una tabla en PostgreSQL. 
* Cada campo del archivo se inserta, en orden, en la columna especificada.
* Las columnas de la tabla no especificadas recibirán sus valores predeterminados.

**Sintaxis:**

```
COPY table_name [ ( column_name [, ...] ) ]
    FROM { 'filename' | PROGRAM 'command' | STDIN }
    [ [ WITH ] ( option [, ...] ) ]
    [ WHERE condition ]

Las opciones principales son:

    FORMAT format_name
    DELIMITER 'delimiter_character'
    HEADER [ boolean ]
    ENCODING 'encoding_name'
```

**COPY TO**
* Permite copiar el contenido de una tabla o los resultados de una consulta SELECT a un archivo de texto plano.
* Si se especifica una lista de columnas, copia solo los datos de las columnas especificadas en el archivo. 

**Sintaxis:**

```
COPY { table_name [ ( column_name [, ...] ) ] | ( query ) }
    TO { 'filename' | PROGRAM 'command' | STDOUT }
    [ [ WITH ] ( option [, ...] ) ]

Las opciones principales son:

    FORMAT format_name
    DELIMITER 'delimiter_character'
    HEADER [ boolean ]
    ENCODING 'encoding_name'
    
```

A continuación, se detalla el flujo de trabajo a seguir para importar/exportar archivos CSV utilizando el comando COPY:

**Paso 1.**  Con un editor de texto, inspeccionar la estructura del archivo que vamos a importar a PostgreSQL. Utilizaremos el archivo **comercios.csv** que contiene un listado de comercios levantados por COFOPRI.

![image](https://user-images.githubusercontent.com/88239150/178259319-4b5df6e8-54ec-4dfc-ab74-2100b49febc6.png)

Observamos que el archivo se encuentra delimitado por punto y coma (;) y tiene los siguiente campos:

* ID: Identificador de registro (Número entero)
* UBIGEO: Código de ubigeo (6 carácteres)
* COD_SECT: Código de sector (2 carácteres)
* COD_MZNA: Códio de manzana (3 carácteres)
* COD_LOTE: Código de lote (3 carácteres)
* COD_PISO: Código de piso (2 carácteres)
* COD_EDIFICACION: Código de edificación (2 carácteres)
* COD_USO: Código de uso comercial (5 carácteres)
* DESC_USO: Descripción del uso comercial (Hasta 150 carácteres)
* LON_X: Longitud (Númerico de 6 decimales)
* LAT_Y: Latitud (Númerico de 6 decimales)

**Paso 2.** Concetarse a la base de datos. A continuación, se muestra como conectarse con el cliente **psql** desde el simbolo del sistema:

```
    psql -U postgres lore
```

**Paso 3.** Crear un esquema de trabajo de nombre **data**

```
    CREATE SCHEMA data;
```

**Paso 4.** Crear la tabla con la estructura del archivo CSV respetando el orden de los campos. Tambien, agregar al final un campo de tipo geometría puntual con el sistema de referencia correspondiente.

```
    CREATE TABLE data.comercios(
            id integer PRIMARY KEY
            , ubigeo CHAR(6) NOT NULL
            , cod_sect CHAR(2) NOT NULL
            , cod_mzna CHAR(3) NOT NULL
            , cod_lote CHAR(3) NOT NULL
            , cod_piso CHAR(2) NOT NULL
            , cod_edificacion CHAR(2) NOT NULL
            , cod_uso VARCHAR(5)
            , desc_uso VARCHAR(150)
            , lon_x NUMERIC(10,6) NOT NULL
            , lat_y NUMERIC(10,6) NOT NULL
            , geom GEOMETRY(POINT, 4326)
    );
```

**Paso 4.** Ejecutar el comando **COPY FROM**

```
    COPY data.comercios(id, ubigeo, cod_sect, cod_mzna, cod_lote, cod_piso, cod_edificacion, cod_uso, desc_uso, lon_x, lat_y) 
    FROM 'D:\Charlie\05_Articulos\SpatialDB\data\cap02\comercios.csv' WITH CSV HEADER DELIMITER ';' ENCODING 'UTF-8';
```

**Paso 5.** Realizar una selección de la tabla para verificar que los registros se insertaron correctamente

```
    -- Conteo de registros
    **SELECT COUNT(*) AS cantidad FROM data.comercios;**
    
    -- Seleccionar los primeros 10 registros
    
```

## 2. Importar Shapefiles con el comando shp2pgsql

## 3. Importar otros formatos vectoriales con el comando GDAL/ogr

## 4. Importar ráster con el comando raster2pgsql

## 5. Exportar a Shapefiles con el comando pgsql2shp

## 6. Exportar a otros formatos vectoriales con el comando GDAL/ogr

## 7. Importar/Exportar datos con QGIS

## Referencias

https://www.postgresql.org/docs/current/sql-copy.html

