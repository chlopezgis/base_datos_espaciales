<center><h1>Importar y Exportar datos en PostGIS</h1></center>

Los datos son el componente mas importante de un SIG ya que sin estos es imposible realizar cualquier análisis. Actualmente, existen muchas fuentes de datos geoespaciales disponibles que podemos incorporar dentro de nuestra base de datos. Por este motivo, en este tutorial se mostrarán las principales herramientas y procesos para importar datos de diferentes formatos a PostGIS.

## Antes de iniciar...

1. Crear una base de datos espacial (Ver capitulo [Crear base de datos espacial ](https://chlopezgis.github.io/base_datos_espaciales/creacion)).
2. Descargar los datos de la práctica (Aquí).

## 1. Importar datos tabulares con el comando COPY

El comando **COPY** nos permite mover datos entre tablas de PostgreSQL y archivos de texto plano (CSV o TXT). 

### COPY FROM

* Permite copiar los datos de un archivo de texto plano a una tabla en PostgreSQL. 
* Cada campo del archivo se inserta, en orden, en la columna especificada.
* Las columnas de la tabla no especificadas recibirán sus valores predeterminados.

**Sintaxis:**

```
COPY table_name [ ( column_name [, ...] ) ]
    FROM { 'filename' | PROGRAM 'command' | STDIN }
    [ [ WITH ] ( option [, ...] ) ]
    [ WHERE condition ]
```

Las opciones principales son:

```
    FORMAT format_name
    DELIMITER 'delimiter_character'
    HEADER [ boolean ]
    ENCODING 'encoding_name'
```

A continuación, se detalla el flujo de trabajo a seguir para importar archivos CSV utilizando el comando COPY:

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

**Paso 2.** Concetarse a la base de datos espacial. A continuación, se muestra como conectarse con el cliente **psql** desde el simbolo del sistema:

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

**Paso 5.** Ejecutar el comando **COPY FROM**

```
COPY data.comercios(id
                    , ubigeo
                    , cod_sect
                    , cod_mzna
                    , cod_lote
                    , cod_piso
                    , cod_edificacion
                    , cod_uso
                    , desc_uso
                    , lon_x
                    , lat_y) 
FROM 'D:\Charlie\05_Articulos\SpatialDB\data\cap02\comercios.csv' 
WITH CSV HEADER DELIMITER ';' ENCODING 'UTF-8';
```

Nota: Si no es superusuario de la base de datos debe anteponer la barra invertida: **\COPY**

**Paso 6.** Realizar una selección de la tabla para verificar que los registros se insertaron correctamente

Contar la cantidad de registros:

```
SELECT COUNT(*) AS cantidad FROM data.comercios;
``` 
![image](https://user-images.githubusercontent.com/88239150/178388439-59ef9675-9ed2-4d3b-943b-16694f414d69.png)

Mostrar los primeros 10 registros:

```
SELECT id, ubigeo, cod_uso, desc_uso, lon_x, lat_y FROM data.comercios LIMIT 10;
```

![image](https://user-images.githubusercontent.com/88239150/178388694-5f50e1b0-afe5-4721-beb7-1909ebf48616.png)

**Paso 7.** Ahora vamos a calcular la geometría a partir de las coordenadas de los comercios

```
UPDATE data.comercios SET geom = ST_GeomFromText('POINT('||lon_x||' '||lat_y||')', 4326);
```

Verificar que el proceso se ejecuto correctamente:

```
SELECT id, cod_uso, desc_uso, ST_AsText(geom) AS geom_text FROM data.comercios LIMIT 10;
```
![image](https://user-images.githubusercontent.com/88239150/178389562-3c9d0474-8568-4266-b668-17208902fab6.png)

## 2. Importar Shapefiles con el comando shp2pgsql

La utilidad shp2pgsql es una herramienta de linea de comandos que permite convertir archivos Shapefiles a SQL preparado para la inserción en una base de datos espacial.

**Sintaxis**

```
shp2pgsql [<options>] <shapefile> [[<schema>.]<table>]
 ```
Opciones:

* **-s \[\<from\>:\]\<srid\>**: Establece el Sistema de Coordenadas. El valor predeterminado es 0. Opcionalmente reproyecta desde un SRID dado.
* **(-d\|a\|c\|p)**: Estas son opciones mutuamente excluyentes:
    * **-d**: Elimina la tabla, luego la vuelve a crear y la completa con los datos del Shapefile actual.
    * **-a**: Agrega el Shapefile a la tabla actual. Debe ser exactamente el mismo esquema de tabla.
    * **-c**: Crea una nueva tabla y la llena con los datos. Este es el valor predeterminado.
    * **-p**: Modo preparar, solo crea la tabla.
* **-g \<geocolumn\>**: Especifica el nombre de la columna geometría/geografía (principalmente útil en el modo de adición "-a").
* **-D:** Usar el formato Dump de postgresql.
* **-e:** Ejecuta cada declaración individualmente, no usa una transacción. No compatible con -D.
* **-G**: Usar tipo geografía (requiere datos de longitud/latitud o -s para reproyectar).
* **-k**: Mantener mayúsculas y minúsculas en los identificadores de postgresql
* **-i**: Usa int4 para todos los campos enteros del dbf
* **-I**: Crea un índice spacial en la columna de la geometría
* **-S**: Genera geometrías simples en vez de geometrías MULTI
* **-t \<dimensionality\>**: Fuerza a la geometría a ser una de '2D', '3DZ', '3DM' o '4D'
* **-w**: Salida WKT en lugar de WKB. Tenga en cuenta que esto puede resultar en una desviación de coordenadas.
* **-W \<encoding\>**: Especifica la codificación de los caracteres (predeterminado: "UTF-8").
* **-N \<policy\>**: Política de manejo de geometrías NULL (insert*, skip, abort).
* **-n**: Solo importa el archivo DBF.
* **-T \<tablespace\>**: Especifique el tablespace para la nueva tabla. Tenga en cuenta que los índices seguirán utilizando el espacio de tabla predeterminado a menos que también se utilice el indicador -X.
* **-X \<tablespace\>**: Especifique el tablespace para los índices de la tabla. Esto se aplica a la clave principal y al índice espacial si se utiliza el indicador -I.
* **-Z**: Evita que se analicen las tablas.
* **-?**: Muestra la ayuda

Para este ejemplo, importaremos una capa de polígonos de Habilitaciones Urbanas (hab_urbanas.shp):

**Paso 1.** Convertir el archivo Shapefile a SQL

```
shp2pgsql -s 4326 -I -g geom "data/cap02/hab_urbanas.shp" data.hab_urbanas > "data/cap02/hab_urbanas.sql"
```
![image](https://user-images.githubusercontent.com/88239150/179329727-2093f0e3-21a5-4a63-8679-53588e82f4e8.png)

__Consideraciones__:
* **-s**: Especifica el sistema de referencia WGS84 (EPSG 4326)
* **-I**: Crea un índice espacial
* **-g**: Especifica el nombre de la geocolumna como "geom"
* **"cap02/hab_urbanas.shp"**: Shapefile de entrada (Incluye la ruta)
* **data.hab_urbanas**: Nombre de la tabla SQL que incluye el esquema.
* **\>**: Redirige la salida del comando a un archivo
* **"data/cap02/hab_urbanas.sql"**: Archivo en formato SQL que almacena la salida del comando shp2pgsql (Incluye la ruta)

**Paso 2.** Con un editor de texto inspeccionar el archivo "hab_urbanas.sql".

![image](https://user-images.githubusercontent.com/88239150/179329973-786c0c68-a1ef-40a8-8f21-069631fb83db.png)

Como se observa, el archivo tiene las sentencias SQL para crear la tabla e insertar los registros en esta.

**Paso 3.** Con el comando psql ejecutaremos el archivo SQL en la base de datos

```
psql -U postgres -d lore -f "data/cap02/hab_urbanas.sql"
```
![image](https://user-images.githubusercontent.com/88239150/179330968-87dad381-5fe5-4f11-b37d-af5dfcd58122.png)

**Paso 4.** Conectarse con PgAdmin para verificar graficamente que el shapefile se cargó correctamente

![image](https://user-images.githubusercontent.com/88239150/179332178-48f8bf0c-a25d-4715-b776-fd7440bd04ea.png)


## 3. Importar otros formatos vectoriales con el comando GDAL/ogr

## 4. Importar ráster con el comando raster2pgsql

## 5. Exportar a Shapefiles con el comando pgsql2shp

## 6. Exportar a otros formatos vectoriales con el comando GDAL/ogr

## 7. Importar/Exportar datos con QGIS

## Referencias

https://www.postgresql.org/docs/current/sql-copy.html
