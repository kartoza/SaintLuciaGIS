First follow steps in Contours.md then export the contours to a GPKG on their own. Then do:


```
gdal_grid -zfield elevation \
-outsize 89063 44552 -txe 499482.4000 1515163.7000 \
-tye 521758.0000 1559694.9000 \
contours.gpkg contours.tiff \
--config GDAL_NUM_THREADS ALL_CPUS
```
