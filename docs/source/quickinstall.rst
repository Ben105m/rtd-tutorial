.. _quickinstall:

######################################
PyDrag installation guide
######################################

This is the documentation about PyDrag project installation with all required dependencies. Some additional guidelines are also given.
This guide has been written for `Version5 <http://merlin.polymtl.ca/development.htm>`_ revisions 5.0.7 and 5.0.8.

***********************************
Setting environnement variables
***********************************

First, you need to set some variables used by `Version5 <http://merlin.polymtl.ca/development.htm>`_ and PyGan, by adding or modifying them in your .profile.perso file :

.. code-block:: sh

	export FORTRANPATH=/soft/gcc/7.3.0/lib64/ #path to your gcc compiler library
	export HDF5_API=/usr/lib64/               #path to your HDF5 library
	module load gcc/7.3.0


This exemple works for FARUX users, but may be adapted to your own system architecture if it differs. Once it is done, re-source your .profile.perso file.

***********************************
Cloning PyDrag project
***********************************

You can either clone the project through a direct HTTP protocole or by downloading the project as an archive.
If you want to use the HTTP protocole:

1. Clone the PyDrag project from GitLab into your local machine with the HTTP protocole :

.. code-block:: sh

	git clone https://gitlab.extra.irsn.fr/PyDrag/PyDrag.git .

.. note::

	GitLab.extra will ask you for your passwords. If it is not working, you may create a PAT (Personal Access Token) in order to access the repository.

2. It may be required to change branch, as the calculation scheme may be developped on different branches :

.. code-block:: sh
	
	git checkout DRAGOR-V2.1

Otherwise, if you want to download the archive :

1. Download the PyDrag project from GitLab as a TAR (or TGZ) archive (be careful with the branch of the project you want to download!)

2. Place the archive in the folder of your choice

3. Unzip it with :

.. code-block:: sh

	tar -xvzf PyDrag-main.tgz


**********************************************************************
Manage the Python virtual environnement
**********************************************************************

You will need Python3 to use PyDrag, as well as the built-in modules "os", "math", "sys" and the module "numpy". To ensure these requirements, you have to create a virtual environnement (unless you already have one).

1. Create and enable a virtual environnement with virtualenv (see `Virtual environnement configuration <http://peanuts:8080/bin/view/Informatique/Python/>`_ )

2. Install all the required official Python packages (numpy, os, math, sys)

.. code-block:: sh

	pip install -r requirements.txt


Now, you should have the PyDrag project cloned into your system and all necessary official Python modules.

**********************************************************************
Installing and compiling Version5
**********************************************************************

PyDrag is based on PyGan libraries from Version5 (currently, distribution number v5bev2958). You will need DRAGON-related codes and sources. There are two different situations :

 - you want to work with the original Version5 distribution. Then, you can use the ./src/Version5/ folder, containing every sources from this distribution (that is no longer available online). Use the following commands :

	.. code-block:: sh

		cd ./src/Version5/Donjon/
		make
		make clean
		cd ../Pygan/
		make
		make clean


 - you want to work on your own distribution : you need to download the chosen archive from `Version5 <http://merlin.polymtl.ca/development.htm>`_ and compile it
 
	1. Download the latest TAR archive of Version5 and place it wherever you need

	2. Use the next commands (detailled here for the revision ev2761 and must be adapted to your revision number) :
	
		.. code-block:: sh

			tar -xvzf Version5.0.8_ev2761.tgz
			cd Version5.0.8_ev2761/Donjon/
			make
			make clean
			cd ../Pygan/
			make
			make clean


By default, using the 'make' command in Donjon folder allows your system to compile Donjon code and all its depedencies. If there is any problem, you can try to make this process for each codes (in this exact order) : Utilib, Ganlib, Dragon, Donjon, PyGan.

***********************************
Sourcing PyGan libraries
***********************************

Source the PyGan library path in your .profile.perso file by adding the path to PyGan python libraries :

.. code-block:: sh

	export PYTHONPATH=[my_personnal_folder]/src/Version5/PyGan/lib/Linux_x86_64/python/

where '[my_personnal_folder]' is the path to the folder containing PyDrag project.
At this step, you should have cloned PyDrag project, installed all Python dependencies and compiled/sourced PyGan.
