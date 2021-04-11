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
SELECT "Elevation", ST_Union(geom) INTO contours_merged FROM contours GROUP BY "Elevation" ;
INSERT INTO contours_merged (SELECT "Elevation", ST_Union(geom) FROM "Contour_2" GROUP BY "Elevation");
INSERT INTO contours_merged (SELECT "Elevation", ST_Union(geom) FROM "Contour_3" GROUP BY "Elevation");
SELECT "Elevation", ST_Union(geom) INTO contours FROM contours_merged GROUP BY "Elevation" ;
DROP TABLE contours_merged;
DROP TABLE "Contour_1";
DROP TABLE "Contour_2";
DROP TABLE "Contour_3";
```