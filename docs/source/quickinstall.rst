.. _quickinstall:

This is the documentation about PyDrag project installation with all required dependencies. Some additional guidelines are also given.
This guide has been written for Version5 revisions 5.0.7 and 5.0.8.
PyDrag is developped on FARUX (CentOS 7.8.2003) with Python3.6.8.

==========================
Informations and sources
==========================

---------------------------
Sources
---------------------------

Following documents have been used to write this README :

- `Version5 distributions <http://merlin.polymtl.ca/development.htm>`_

- `PyGan guide <http://merlin.polymtl.ca/downloads/IGE332.pdf>`_

- `Virtual environnement configuration <http://peanuts:8080/bin/view/Informatique/Python/>`_


---------------------------
Contacts
---------------------------

If you have problems, questions, or improvements for PyDrag, you may contact following users :

- `SALINO Vivian <mailto:vivian.salino@irsn.fr>`_

- `TAFOREAU Julien <mailto:julien.taforeau@irsn.fr>`_

- `BENEDET Benjamin <mailto:benjamin.benedet@uranus-tech.net>`_

---------------------------
Developpement environnement
---------------------------

PyDrag is developped under the following environnement:

- CentOS Linux release 7.8.2003 (Core)

- GCC version 7.3.0

- anaconda3 base environnement + Python 3.6.8 through virtualenv 20.17.0

==========================
Installation of PyDrag
==========================

---------------------------
Setting your environnement variables
---------------------------

First, you need to set some variables used by Version5 and PyGan, by adding or modifying them in your .profile.perso file :
```bash
export FORTRANPATH=/soft/gcc/7.3.0/lib64/ #path to your gcc compiler library
export HDF5_API=/usr/lib64/               #path to your HDF5 library
module load gcc/7.3.0
```

This exemple works for FARUX users, but may be adapted to your own system architecture if it differs. Once it is done, re-source your .profile.perso file.

---------------------------
Cloning PyDrag project
---------------------------

You can either clone the project through a direct HTTP protocole (if you are on FARUX) or by downloading the project as an archive.

---------------------------
Cloning through HTTP protocole
---------------------------

1. Clone the PyDrag project from PICOCS into your local machine with the HTTP protocole :
```bash
git clone http://picocs.neutron.intra.irsn.fr/orion/dragor.git .
```
	
2. It may be required to change branch from "master" (DRAGOR-V1.3) to "DRAGOR-V2.1" (the one under developpement) :
```bash
git checkout DRAGOR-V2.1
```

---------------------------
Cloning through an archive
---------------------------

1. Download the PyDrag project from PICOCS as a TAR (or TGZ) archive (be careful with the branch of the project you want to download!)

2. Place the archive in the folder of your choice

3. Unzip it with :
```bash
tar -xvzf dragor-DRAGOR-V2.1.tgz
```

---------------------------
Manage the Python virtual environnement
---------------------------

You will need Python3 to use PyDrag, as well as the built-in modules "os", "math", "sys" and the module "numpy". To ensure these requirements, you have to create a virtual environnement (unless you already have one).
1. Create and enable a virtual environnement with virtualenv (see [Virtual environnement configuration](http://peanuts:8080/bin/view/Informatique/Python/) )

2. Install all the required official Python packages (numpy, os, math, sys)
```bash
pip install -r requirements.txt
```

Now, you should have the PyDrag project cloned into your system and all necessary official Python modules.

---------------------------
Installing and compiling Version5
---------------------------

PyDrag is based on PyGan libraries from Version5 (currently, distribution number v5bev2761). You will need DRAGON-related codes and sources. There are two different situations :
 - you want to work with the original Version5 distribution. Then, you can use the ./src/Version5/ folder, containing every sources from this distribution (that is no longer available online). Use the following commands :
	```bash
	cd ./src/Version5/Donjon/
	make
	make clean
	cd ../Pygan/
	make
	make clean
	```

 - you want to work on your own distribution : you need to download the chosen archive from [Version5](http://merlin.polymtl.ca/development.htm) and compile it
 
	1. Download the latest TAR archive of Version5 and place it wherever you need
	2. Use the next commands (detailled here for the revision ev2761 and must be adapted to your revision number) :
	```bash
	tar -xvzf Version5.0.8_ev2761.tgz
	cd Version5.0.8_ev2761/Donjon/
	make
	make clean
	cd ../Pygan/
	make
	make clean
	```
	By default, using the 'make' command in Donjon folder allows your system to compile Donjon code and all its depedencies. If there is any problem, you can try to make this process for each codes (in this exact order) : Utilib, Ganlib, Dragon, Donjon, PyGan.

---------------------------
Sourcing PyGan libraries
---------------------------

Source the PyGan library path in your .profile.perso file by adding the path to PyGan python libraries :
```bash
export PYTHONPATH=[my_personnal_folder]/src/Version5/PyGan/lib/Linux_x86_64/python/
```
where 'my_personnal_folder' is the path to the folder containing PyDrag project.
At this step, you should have cloned PyDrag project, installed all Python dependencies and compiled/sourced PyGan.

==========================
Utility guidelines
==========================

PyDrag is using PyGan to create a communication between DRAGON and Python. This allows python code to access/store some memory-located LCM objects, usually manipulated by DRAGON/DONJON. It grants (more or less direct) access to them in a Python script. 

It means that developping PyDrag may require a significant amount of RAM (for exemple, if you do not delete memory-located LCM variables) : therefore, it is highly recommanded to test and develop scripts on a Slurm node using "qrsh" for nominal evolution calculations.
However, as SAPHYB/MULTICOMPO calculations are usually requiring thousands of branches (e.q thousands of calculation points), it is expected for the calculation (performed through a qrsh connection) to crash due to the limited allocated memory.

==========================
Start a PyDrag calculation
==========================

It is possible to start a PyDrag calculation through two ways (using Tihange input as an exemple) :

  - using the "pydrag" launching script by calling it with the name of chosen input (located in /data/ folder). It is possible to start the calculation on slurm by using the "-s" argument (which will start the calculation in the local ./tmp/ folder) :
  ```bash
./pydrag -s Tihange.py
```
This command allows to store every PyGan-related informations into a txt file. However, it may be needed to manually delete the /tmp/ folder, as the slurm execution script can not delete the folder where it has been created.

  - directly starting the chosen input with 
  ```bash
cd ./data/
python -i Tihange.py
```
The "-i" argument allows the user to keep the console open in order to interact with every objects such as results, LCM objets and classes.

==========================
Non-regression protocole
==========================

Each input ends with a non-regression section. These sections contain reference kinf value, which are compared to the nominal evolution kinf obtained with the calculation scheme. The references come from calculations made with DRAGOR-V1.3, with Version5 distribution 5.0.7 (ev2068) and using nuclear data library CEA93.V7 with 172 energy groups.
Reference results were made with the different available calculation parameters (control rods type, fuel type, enrichment value, ...). These kinf are then called through pydrag.non_regression function, that displays the results for each burnup step in addtion with the kinf difference (in pcm). A warning message is displayed if the difference is higher than a chosen threshold (default:0.5 pcm). It is possible to create a txt file in the chosen location that stores those results.

Obviously, it is possible to comment/delete these sections if they are not required.
