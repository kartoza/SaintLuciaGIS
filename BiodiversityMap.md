Generate a hex grid with 1000m dimension.
Remove bins outside of the island
Use points in polygon alg to create a new layer with amount of occurrence records per hex bin


Use the field calculator to add a column called taxon_count on the hex layer using this expression:


![image](https://user-images.githubusercontent.com/178003/121389059-eceed900-c943-11eb-8785-c90262cad709.png)

```
-- Count the distict taxa in each hex cell
--geometry(@parent) refers to the current hex cell and
-- @geometry refers to the geometry of the biodiversity layer
aggregate(  'gbif_biodiversity_14dde011_d5a5_4034_8e74_8618ee27dae2',
  aggregate:='count_distinct',
  expression:="taxonKey",
  filter:=contains(geometry(@parent),$geometry ))
```

For the map layout I made three infographics using expressions:

For the label showing the total count of occurrence records I did:

```
[%aggregate( 
  'gbif_biodiversity_14dde011_d5a5_4034_8e74_8618ee27dae2',
  aggregate:='count',
  expression:="taxonKey")%]
```

![image](https://user-images.githubusercontent.com/178003/121598397-b1363b00-ca39-11eb-835c-31374d89314a.png)



For the label showing the number of unique taxa, I used this expression:

```
[%aggregate( 
  'gbif_biodiversity_14dde011_d5a5_4034_8e74_8618ee27dae2',
  aggregate:='count_distinct',
  expression:="taxonKey")%]
```

![image](https://user-images.githubusercontent.com/178003/121598842-24d84800-ca3a-11eb-88db-5e9b636ed3cd.png)


For the label showing the date range I used these expressions:

```
[% to_date(regexp_replace(
 aggregate( 
    'gbif_biodiversity_14dde011_d5a5_4034_8e74_8618ee27dae2',
    aggregate:='min',
    expression:="eventDate"),'"','')) %] 
to
[% to_date(regexp_replace(
 aggregate( 
    'gbif_biodiversity_14dde011_d5a5_4034_8e74_8618ee27dae2',
    aggregate:='max',
    expression:="eventDate"),'"','')) %]

```

![image](https://user-images.githubusercontent.com/178003/121599312-c495d600-ca3a-11eb-9afa-d52563265ed4.png)


Map Layout text:

This map shows the number of GBIF occurrence records per 1km hex grid square for the country of Saint Lucia. Data is sourced from https://gbif.org using the occurrences plugin in QGIS. The map is more of an indication of sampling effort than biodiversity since it does not indicate the number of unique species per cell. 

A summary of biodiversity data for Saint Lucia

Occurrence Records per 1km Hex Grid Cell
