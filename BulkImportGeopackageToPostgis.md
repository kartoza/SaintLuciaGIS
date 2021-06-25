# Bulk importing GeoPackage data to PostgreSQL

In this document we describe how to load your GeoPackage data into PostGIS

```
cd Syncthing/StLucia/StLucia
ogr2ogr -f PostgreSQL "PG:service=stlucia" saintluciagis.gpkg
```


After import we can validate it in PostGIS using QGIS browser:

![image](https://user-images.githubusercontent.com/178003/123412928-1184bb00-d5aa-11eb-9a7f-6a823e2fe06d.png)
