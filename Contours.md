# Processing contours

Data received in Geopackage consisting of 4 layers

1. Contours_1 - South
1. Contours_2 - Middle
1. Contours_3 - North
1. Contours_Merged

The contours merged data was broken as the contour 
segements for a chopped up line had different Elevation values.

Drag and dropped the 1-3 datasets into Postgres then and then did:

```
SELECT "Elevation" as elevation, ST_Union(geom) as geom INTO contours_merged FROM "Contour_1" GROUP BY "Elevation" ;
INSERT INTO contours_merged (SELECT "Elevation" as elevation, ST_Union(geom) as geom FROM "Contour_2" GROUP BY "Elevation");
INSERT INTO contours_merged (SELECT "Elevation" as elevation, ST_Union(geom) as geom FROM "Contour_3" GROUP BY "Elevation");
SELECT "elevation", ST_Union(geom) as geom INTO contours FROM contours_merged GROUP BY elevation ;
DROP TABLE contours_merged;
DROP TABLE "Contour_1";
DROP TABLE "Contour_2";
DROP TABLE "Contour_3";
ALTER TABLE contours ADD COLUMN id SERIAL PRIMARY KEY;
CREATE INDEX contours_geom_idx
  ON contours
  USING GIST (geom);
-- 2006 is the EPSG code for the projection used in St Lucia
ALTER TABLE contours ALTER COLUMN geom TYPE geometry(MultiLinestringZ,2006) USING ST_FORCE3DZ(ST_MULTI(geom));
-- The multilines are still made up of little chopped up segments
-- we want to stitch any contiguous pieces together
-- hard coded 2 in collect extract is for linestring
-- https://postgis.net/docs/ST_CollectionExtract.html
SELECT id, elevation, ST_CollectionExtract(ST_Collect(ST_LineMerge(geom)),2) AS geom INTO contours2 FROM contours GROUP BY id;

The number of inserts should more or less correspond to the number of contour intervals on the island.

```
SELECT 279
INSERT 0 381
INSERT 0 312
SELECT 397
```

-- Convert those merged geometries back into single parts again
SELECT elevation, (ST_Dump(geom)).geom AS geom  INTO contours3 FROM contours;
ALTER TABLE contours ADD COLUMN id SERIAL NOT NULL PRIMARY KEY;

-- Get rid of the intermediate working tables again

DROP TABLE contours;
DROP TABLE contours2;
ALTER TABLE contours3 RENAME TO contours;

-- now re-create the spatial index

CREATE INDEX contours_geom_idx
  ON contours
  USING GIST (geom);

-- Snapping the contours to grid should remove any mismatches between start and end node locations
-- we will snap to nearest 19cm

UPDATE contours SET geom = ST_SnapToGrid(geom, 0.1);



SELECT Populate_Geometry_Columns('public.contours'::regclass);
SELECT UpdateGeometrySRID('contours','geom',2006);
```




