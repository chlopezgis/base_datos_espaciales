<center><h1>CAP 2. IMPORTAR Y EXPORTAR DATOS EN POSTGIS</h1></center>

Los datos son el componente mas importante de un SIG ya que sin estos es imposible realizar cualquier análisis. Actualmente, existen muchas fuentes de datos disponibles que podemos incorporar como punto de partida en nuestra Base de Datos (previa evaluación de calidad). 

En este capítulo, se mostrarán las principales herramientas y procesos para importar y exportar datos geoespaciales de diferentes formatos en nuestra base de datos.

## Ante de iniciar...

Crear una base de datos espacial tomando como plantilla la base de datos creada en el capitulo anterior (ver [aquí](https://chlopezgis.github.io/base_datos_espaciales/creacion))

```
createdb -U <username> -T <tempalate> <dbname>
```

_**NOTA: Los comandos se ejecutaran desde el servidor y el puerto por defecto, por lo cual omitiremos el ingresos de estos parámetros**_

## 1. Importar datos tabulares (CSV) con el comando COPY

## 2. Importar Shapefiles con el comando shp2pgsql

## 3. Importar otros formatos vectoriales con el comando GDAL/ogr

## 4. Importar ráster con el comando raster2pgsql

## 5. Exportar a Shapefiles con el comando pgsql2shp

## 6. Exportar a otros formatos vectoriales con el comando GDAL/ogr

## 7. Importar/Exportar datos con QGIS
