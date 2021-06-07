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

I found the tool crashes every time I try to run it against a large DEM (1.2GB) so I chopped up the DEM into 2000x2000 pixel tiles using the gdal retile processing tool:

![image](https://user-images.githubusercontent.com/178003/121039636-2ccd8900-c7a9-11eb-9b88-133ff1d02ea4.png)

![image](https://user-images.githubusercontent.com/178003/121039899-63a39f00-c7a9-11eb-83c6-cc82ce741caa.png)


If you prefer the command line:

```
gdal_retile.py -ps 2000 2000 -overlap 0 -levels 1 -r near -ot Float32 -targetDir /home/timlinux/Downloads /home/timlinux/Syncthing/StLucia/StLucia/st-lucia-dem-compressed-epsg2006.tif
```

For the rest of this document I will be using one of these tiles to demonstrate the workflow, but you will need to repeat the process across all of them and probably stitch back the results at the end if needed. I won't cover the latter workflows here. So this is what my working tile looks like:

![image](https://user-images.githubusercontent.com/178003/121042364-83d45d80-c7ab-11eb-8261-b050ee66f946.png)


Run the processing module against a DEM. Here I am using the default parameters:

![image](https://user-images.githubusercontent.com/178003/121007707-0b10d980-c78a-11eb-98dd-3d449ae4a983.png)

After processing the tile in Karika, it looks like this.

![image](https://user-images.githubusercontent.com/178003/121042132-4bcd1a80-c7ab-11eb-8074-0106c6aa95d2.png)

I know, it looks virtually identical to the input but Karika needs to be run to check the elevation model consistency before we pass it to Piotr.


## Piotr

The help file for Piotr looks like this:

```
  __/\\\\\\\\\\\\\__________________________________________________         
   _\/\\\/////////\\\________________________________________________        
    _\/\\\_______\/\\\__/\\\__________________/\\\____________________       
     _\/\\\\\\\\\\\\\/__\///______/\\\\\____/\\\\\\\\\\\__/\\/\\\\\\\__      
      _\/\\\/////////_____/\\\___/\\\///\\\_\////\\\////__\/\\\/////\\\_     
       _\/\\\_____________\/\\\__/\\\__\//\\\___\/\\\______\/\\\___\///__    
        _\/\\\_____________\/\\\_\//\\\__/\\\____\/\\\_/\\__\/\\\_________   
         _\/\\\_____________\/\\\__\///\\\\\/_____\//\\\\\___\/\\\_________  
          _\///______________\///_____\/////________\/////____\///__________ 
                                                                            
Usage: 

Enter    piotr.exe -h    for general help

Example: 
piotr.exe -l 15 -d /tmp/piotr_out -m ~/mask.asc ~/test.asc 

-l 15               generalization using LIC; integration length is 15
-d /tmp/piotr_out   put images and session file in this directory
-m ~/mask.asc       path to rock mask
~/test.asc          elevation model file path


For an in-depth description of the method, please refer to

Roman Geisthövel: Automatic Swiss style rock depiction. PhD thesis, ETH Zurich,
Diss. No. 24328, 2017
```

Nice and short and simple. One thing you will notice is that the input file needs to be in ascii grid format so we are going to convert our dem tile to that format:

![image](https://user-images.githubusercontent.com/178003/121043370-74a1df80-c7ac-11eb-8be7-4f7c44ecc23e.png)

In addition to the example shown in the image above, you also need to pass the creation option FORCE_CELLSIZE=YES to gdal_translate in order for Piotr to be able to read the ASCII grid file.

```
gdal_translate -a_srs EPSG:2006 -of AAIGrid -co FORCE_CELLSIZE=YES st-lucia-dem-compressed-epsg2006_05_10.tif dem.asc
```

We are also going to convert it to PNG format so that we can get a world file (.pngw) that we can use later to provide georederencing info to the Piotr output image.

```
gdal_translate -a_srs EPSG:2006 -of PNG st-lucia-dem-compressed-epsg2006_05_10.tif dem.png
```

![image](https://user-images.githubusercontent.com/178003/121094364-6ffa1d00-c7e6-11eb-80b2-57952f3286e3.png)

When Piotr finishes we will swap the .png file it creates over the one we pre-generated so that the .aux files will provide georeferencing data for the Piotr generated file.

Next we actually need to install Piotr. Note that for windows users there is also a windows build available at http://motlimot.net/software.html. macOS users will need to use the linux binary in a docker container (or try to build yourself from source).

```bash
wget http://motlimot.net/content/PiotrLinux64.zip
unzip PiotrLinux64.zip 
cp PiotrLinux64/piotr.exe .
./piotr.exe 
```
 
 Running last above cammand will print out the basic usage text to the console:
 
```bash
./piotr.exe 
usage: piotr.exe [-h] [-m MASK] [-d OUTDIR] [-a N] [-e N] -l N DEM
piotr.exe: error: the following arguments are required: -l/--lic, DEM
```


If you try to actually use the piotr command to run an analysis, you will get errors like this:

```
ERROR  load_extension_module: could not find module  /home/timlinux/Downloads/stlucia-dem-tiles/piotr/ext/lib/64/extagg*linux*
Traceback (most recent call last):
  File "main.py", line 67, in <module>
  File "<frozen importlib._bootstrap>", line 971, in _find_and_load
  File "<frozen importlib._bootstrap>", line 955, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 665, in _load_unlocked
  File "/usr/lib/python3.6/site-packages/PyInstaller/loader/pyimod03_importers.py", line 631, in exec_module
  File "piotr/piotr.py", line 31, in <module>
  File "<frozen importlib._bootstrap>", line 971, in _find_and_load
  File "<frozen importlib._bootstrap>", line 955, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 665, in _load_unlocked
  File "/usr/lib/python3.6/site-packages/PyInstaller/loader/pyimod03_importers.py", line 631, in exec_module
  File "piotr/agg.py", line 104, in <module>
  File "piotr/agg.py", line 88, in make_api
AttributeError: 'NoneType' object has no attribute 'draw_circle'
[45911] Failed to execute script main
```

To address this we can run the command inside a docker container. So now we can run it our our Karika-prepared DEM tile:

```
docker run -ti -v ${HOME}/Downloads/PiotrLinux64/:/piotr \
  -v ${HOME}/Downloads/:/tmp/ \
  -w /piotr frolvlad/alpine-glibc:latest \
  ./piotr.exe -l 15 -d piotr_out /tmp/dem.asc
```


