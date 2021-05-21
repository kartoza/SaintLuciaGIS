

We are going to follow the steps in this tutorial to make a beautiful hillshade map:

https://somethingaboutmaps.wordpress.com/blender-relief-tutorial-getting-set-up/

For the first step of scaling the data to 0-65535, I used this formula in gdal_calc.py:

```
gdal_calc.py -A st-lucia-dem-compressed-epsg2006.tif \
  --calc="((A + 35.133014678955)/(35.133014678955+922.46307373047)) * 65535" --outfile /tmp/stlucia-heightfield.tifraster
```

I had to flip the logic around a  bit because in the article the lowest value in the DEM was a positive value wheras for StLucia the lowest is negative.

```
gdal_translate -of GTiff -ot UInt16 /tmp/stlucia-heightfield.tif /tmp/stlucia-heightfield-u16.tif
```

