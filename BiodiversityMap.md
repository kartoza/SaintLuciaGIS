Generate a hex grid
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

