### COPY TO
* Permite copiar el contenido de una tabla o los resultados de una consulta SELECT a un archivo de texto plano.
* Si se especifica una lista de columnas, copia solo los datos de las columnas especificadas en el archivo. 

**Sintaxis:**

```
COPY { table_name [ ( column_name [, ...] ) ] | ( query ) }
    TO { 'filename' | PROGRAM 'command' | STDOUT }
    [ [ WITH ] ( option [, ...] ) ]
```

Las opciones principales son:

```
    FORMAT format_name
    DELIMITER 'delimiter_character'
    HEADER [ boolean ]
    ENCODING 'encoding_name'  
```

A continuación, exportaremos todos los Bazares en un archivo plano. Las coordenadas de salida deben estar re-proyectadas al sistema WGS 84/UTM zone 18S

**Paso 1.** Elaborar la consulta filtrando solo los BAZARES y considerando solo las columnas de: id, ubigeo, cod_uso, desc_uso y las coordenadas X,Y reproyectadas.

```
SELECT 
        id
        , ubigeo
        , cod_uso
        , desc_uso
        , ST_X(ST_Transform(geom, 32718)) AS x
        , ST_Y(ST_Transform(geom, 32718)) as y 
FROM data.comercios WHERE cod_uso = '34104' LIMIT 10;
```
![image](https://user-images.githubusercontent.com/88239150/179316969-bd7df4e8-986a-48f8-be40-2da4212e6816.png)

**Paso 2.** Ejecutar la consulta con el comando **COPY TO**.

```
COPY (SELECT 
            id
            , ubigeo
            , cod_uso
            , desc_uso
            , ST_X(ST_Transform(geom, 32718)) AS x
            , ST_Y(ST_Transform(geom, 32718)) as y 
FROM data.comercios WHERE cod_uso = '34104')
TO 'D:\salida\bazares_32718.csv' DELIMITER '|' CSV HEADER ENCODING 'UTF-8';
```

![image](https://user-images.githubusercontent.com/88239150/179317761-44a33630-1130-440b-ae4b-5b11fa488c65.png)

**Paso 3.** Finalmente, inspeccionar el archivo de salida con un editor de texto

![image](https://user-images.githubusercontent.com/88239150/179318205-1b3dd565-c1a6-43ec-a0b0-a4c2a43435ee.png)
