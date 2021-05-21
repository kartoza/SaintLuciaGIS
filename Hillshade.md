

We are going to follow the steps in this tutorial to make a beautiful hillshade map:

https://somethingaboutmaps.wordpress.com/blender-relief-tutorial-getting-set-up/

For the first step of scaling the data to 0-65535, I used this formula in raster calculator:

```
("st-lucia-dem-compressed-epsg2006@1" -  ("st-lucia-dem-compressed-epsg2006@1" - 35.133 )) / ( 35.133 + 922.463)  * 65535
```

