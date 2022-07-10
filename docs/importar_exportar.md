<center><h1>CAP 2. IMPORTAR Y EXPORTAR DATOS EN POSTGIS</h1></center>

Los datos son el componente mas importante de un SIG ya que sin estos es imposible realizar cualquier análisis. Actualmente, existen muchas fuentes de datos disponibles que podemos incorporar como punto de partida en nuestra Base de Datos (previa evaluación de calidad). 

En este capítulo, se mostrarán las principales herramientas y procesos para importar y exportar datos geoespaciales de diferentes formatos en nuestra base de datos.

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

## 1. Importar datos tabulares (CSV) con el comando COPY

## 2. Importar Shapefiles con el comando shp2pgsql

## 3. Importar otros formatos vectoriales con el comando GDAL/ogr

## 4. Importar ráster con el comando raster2pgsql

## 5. Exportar a Shapefiles con el comando pgsql2shp

## 6. Exportar a otros formatos vectoriales con el comando GDAL/ogr

## 7. Importar/Exportar datos con QGIS
