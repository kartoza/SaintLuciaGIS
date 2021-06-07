# Rock Hachures

## Introduction

Rock hachures are a cartographic technique used for rendering rocky areas:

![image](https://user-images.githubusercontent.com/178003/121005919-3692c480-c788-11eb-9260-91d8b14472d9.png)

To do this we use two tools:

* https://plugins.qgis.org/plugins/karika/
* Piotr http://motlimot.net/software.html

Please also refer to this (open access) paper by Roman Geisthövel & Lorenz Hurni Published online: 01 Mar 2019 for more details about how the technique was done originally and how it is done procedurally with Piotr.

https://www.tandfonline.com/doi/abs/10.1080/00087041.2018.1551955?journalCode=ycaj20

Download citation https://doi.org/10.1080/00087041.2018.1551955 CrossMark Logo CrossMark 

## Workflow

First install the Karika plugin:

![image](https://user-images.githubusercontent.com/178003/121007144-9dfd4400-c789-11eb-9431-4788eebbf4d4.png)

It will add a new tool to the processing toolbox:

![image](https://user-images.githubusercontent.com/178003/121007263-bd946c80-c789-11eb-88de-eb77d9afa2b7.png)


Run the processing module against a DEM. Here I am using the default parameters:

![image](https://user-images.githubusercontent.com/178003/121007707-0b10d980-c78a-11eb-98dd-3d449ae4a983.png)


