.. _quickstart:

################################
Starting a PyDrag calculation
################################

It is possible to start a PyDrag calculation through two ways (using Tihange input as an exemple here) :

	- using the "pydrag" launching script by calling it with the name of chosen input (located in /data/ folder). It is possible to start the calculation on slurm by using the "-s" argument (which will start the calculation in the local ./tmp/ folder) :

	.. code-block:: sh

		cd ./data/
		../pydrag -s Tihange.py

	This command allows to store every PyGan-related informations into a txt file. However, it may be needed to manually delete the /tmp/ folder, as the slurm execution script can not delete the folder where it has been created.

  	- directly starting the chosen input with :
 	 
	.. code-block:: sh

		cd ./data/
		python -i Tihange.py


	The "-i" argument allows the user to keep the console open in order to interact with every objects such as results, LCM objets and classes.
