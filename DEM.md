


First follow steps in Contours.md then export the contours to a GPKG on their own. Then do:



![image](https://user-images.githubusercontent.com/178003/120864087-9409f400-c583-11eb-8b6a-f25158a41951.png)

Note the cell size set to 10m. If you make the grid too fine, QGIS will take forever (or never finish) to process the data.




# Untested alternative approach using gdal commandline tools.

```
gdal_grid -zfield elevation \
-outsize 89063 44552 -txe 499482.4000 1515163.7000 \
-tye 521758.0000 1559694.9000 \
contours.gpkg contours.tiff \
--config GDAL_NUM_THREADS ALL_CPUS
```

See https://gdal.org/programs/gdal_grid.html
