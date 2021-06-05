


First follow steps in Contours.md then export the contours to a GPKG on their own. Then do:


Simplify the contours to 2.5m using Distance (Doglas-Puecker)

![image](https://user-images.githubusercontent.com/178003/120866157-44c5c280-c587-11eb-9498-6240727e2f56.png)

Then generate a mesh from the contours: 

![image](https://user-images.githubusercontent.com/178003/120866951-d4b83c00-c588-11eb-825f-13a3918e3adf.png)

Then generate a grid:

![image](https://user-images.githubusercontent.com/178003/120868141-34afe200-c58b-11eb-90d9-a9f5062ce78f.png)

Note the cell size set to 2m. If you make the grid too fine, QGIS will take forever (or never finish) to process the data.

For the resulting DEM layer, set the render options to hillshade, and crank up the brightness so that we mainly see terrain highlights e.g.

![image](https://user-images.githubusercontent.com/178003/120868344-c881ae00-c58b-11eb-8f60-5f9e92c22529.png)

This layer won't render too quickly. We could generate pyramids but it will still be a bit slow. To fix that we will export the layer as rendered:

![image](https://user-images.githubusercontent.com/178003/120868502-1a2a3880-c58c-11eb-85c1-4e762deb8c42.png)

Make sure to chose renderered image as the output mode:

![image](https://user-images.githubusercontent.com/178003/120868677-79884880-c58c-11eb-9ef3-dac4bac88322.png)

The next step is to convert from an RGB to single band grayscale image:

![image](https://user-images.githubusercontent.com/178003/120869560-9faee800-c58e-11eb-9772-529538fb6340.png)


Example formula (taken from https://lists.osgeo.org/pipermail/gdal-dev/2015-April/041600.html):

```
"shade@1"*0.2989 + "shade@2"*0.5870 + "shade@3" * 0.1140
```

![image](https://user-images.githubusercontent.com/178003/120869649-d08f1d00-c58e-11eb-9b59-aa724e3abe4c.png)


The last step is to compose the rendered image output as an 8bit single band tif to make it as small as possible:

![image](https://user-images.githubusercontent.com/178003/120869676-e1d82980-c58e-11eb-978a-bd8ed3467978.png)


![image](https://user-images.githubusercontent.com/178003/120869933-83f81180-c58f-11eb-8456-65355bb5ce67.png)

Resulting output layer is a singleband 8bit image:

![image](https://user-images.githubusercontent.com/178003/120870118-fbc63c00-c58f-11eb-8f88-48b0b5f4e7db.png)


We can see the comparitive file sizes too:

![image](https://user-images.githubusercontent.com/178003/120870175-231d0900-c590-11eb-904c-714177d1bc2a.png)

The final product we created is ony 28mb!

Now the final step is to use it as an underlay in the topographic map:

Now you can add other layers over the top and let the DEM 'shine' through using multiply blending mode:

![image](https://user-images.githubusercontent.com/178003/120870400-c0783d00-c590-11eb-8a9e-955b1b16f5df.png)


# Trying permutations

You can experiemnt with the brightness levels at the start of the work flow to see how to achieve different levels of subtleness of the shading.Here are some more examples of settings and the results you can get following the same workflow:


Setting | Value
--------| ------
Global Opacity| 28%
Z-factor| 2
Multidirectional| Check
Brightness| 22
Gamma| 3,9
Grayscale| By lightness
Resample| Cubic, Cubic

![image](https://user-images.githubusercontent.com/178003/120882385-5a5eda80-c5cf-11eb-9479-b5cad7a3004d.png)


Result:

Topo map

![image](https://user-images.githubusercontent.com/178003/120883122-5df46080-c5d3-11eb-96b6-1c211dc06e55.png)

Relief map

![image](https://user-images.githubusercontent.com/178003/120883178-9dbb4800-c5d3-11eb-9455-af316d762baf.png)





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
