


First follow steps in Contours.md then export the contours to a GPKG on their own. Then do:


Simplify the contours to 2.5m using Distance (Doglas-Puecker)

![image](https://user-images.githubusercontent.com/178003/120866157-44c5c280-c587-11eb-9498-6240727e2f56.png)

Then generate a mesh from the contours: 

![image](https://user-images.githubusercontent.com/178003/120866951-d4b83c00-c588-11eb-825f-13a3918e3adf.png)

Then generate a grid:

![image](https://user-images.githubusercontent.com/178003/120868141-34afe200-c58b-11eb-90d9-a9f5062ce78f.png)

Note the cell size set to 2m. If you make the grid too fine, QGIS will take forever (or never finish) to process the data.






# Untested alternative approach using gdal commandline tools.

```
![image](https://user-images.githubusercontent.com/178003/120867589-244b3780-c58a-11eb-88fc-47ae847a0bab.png)
gdal_grid -zfield elevation \
-outsize 89063 44552 -txe 499482.4000 1515163.7000 \
-tye 521758.0000 1559694.9000 \
contours.gpkg contours.tiff \
--config GDAL_NUM_THREADS ALL_CPUS
```

See https://gdal.org/programs/gdal_grid.html
