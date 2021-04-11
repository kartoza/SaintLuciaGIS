# Processing contours

Data received in Geopackage consisting of 4 layers

Contours_1 - South
Contours_2 - Middle
Contours_3 - North
Contours_Merged

The contours merged data was broken as the contour 
segements for a chopped up line had different Elevation values.

Drag and dropped the 1-3 datasets into Postgres then and then did:

```
SELECT "Elevation" as elevation, ST_Union(geom) as geom INTO contours_merged FROM "Contour_1" GROUP BY "Elevation" ;
INSERT INTO contours_merged (SELECT "Elevation" as elevation, ST_Union(geom) as geom FROM "Contour_2" GROUP BY "Elevation");
INSERT INTO contours_merged (SELECT "Elevation" as elevation, ST_Union(geom) as geom FROM "Contour_3" GROUP BY "Elevation");
SELECT "elevation", ST_Union(geom) INTO contours FROM contours_merged GROUP BY elevation ;
DROP TABLE contours_merged;
DROP TABLE "Contour_1";
DROP TABLE "Contour_2";
DROP TABLE "Contour_3";
```

The number of inserts should more or less correspond to the number of contour intervals on the island.

```
SELECT 279
INSERT 0 381
INSERT 0 312
```
