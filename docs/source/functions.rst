.. _functions:

################################
Objetcs, functions and methods 
################################

PyDrag is manipulating different objects, each containing different kinds of informations :

  - :ref:`library` : contains all the available informations for the chosen nuclear data library. The user does not need to directly interact with it, as the library type, the isotopes contained inside, and the nuclear data are automatically extracted with PyDrag ;

  -  :ref:`mix` : contains mixture's informations, such as temperature, linear expansion coefficient, compositions. These objects are pre-defined in PyDrag, and it is important to keep their name for the calculation ;

  - :ref:`grids` : contains the description of the assembly grids ;

  - :ref:`materials` : contains all the data related to mixtures. Among others, it includes :ref:`library`, :ref:`grids` and every existing :ref:`mix` (as sub-classes) ; 

  - :ref:`geometry` : describes the assembly geometry, such as the pin layout, the different dimensions, and the mixtures overlay of each pin ;

  - :ref:`power` : it is used to automatically computes the assembly power density (specifically used for evolution calculations) ;

The next chapters detail the different objects handled by PyDrag, and describe the existing methods. The method's (current) limitations are clearly defined.

.. _library:

"Library" object
**********************

TO BE COMPLETED

Description
============

Methods
==========

.. _mix:

"Mix" object
******************

Description
============

The "Mix" objects are used to define the mixtures used for the calculation. By default, PyDrag is creating a list of pre-defined elements (based on common isotopic abundances), later used to create mixtures. The pre-defined mixtures have a fixed name, making them easier to handle by the code and directly callable in the input. However, it is possible for the user to create their own mixtures, duplicating existing mixtures, and such. The pre-definde mixtures are :  SS304, Inconel, Zr4, M5, AIC, B4C, Pyrex, Hf, water, Air, void, Gd, UO2, MOX.

Methods
==========

set_natural_elements()
-------------------------

Set a pre-defined list of natural elements compositions. 

.. note:: 

	Each element is named after its chemical symbol, then followed by the locution "Nat".
	For exemple :
	+------------------+-------------------------------------------------------+
	|**Element name**  |**Name in PyDrag**                                     |
	+------------------+-------------------------------------------------------+
	| iron             | FeNat                                                 |
	+------------------+-------------------------------------------------------+
	| chrome           | CrNat                                                 |
	+------------------+-------------------------------------------------------+
	| sulfur           | SNat                                                  |
	+------------------+-------------------------------------------------------+


set_natural_abundance()
-------------------------

set_molar_mass()
-------------------------

set_compounds()
-------------------------

add_compounds()
-------------------------

add_element()
-------------------------

load_composition()
-------------------------

set_tfuel()
-------------------------

make_default_mix()
-------------------------

make_common_mix()
-------------------------

add_grids()
-------------------------

make_mix()
-------------------------

add_combinated_mix()
-------------------------

duplicate_mix()
-------------------------

make_fuel_mix()
-------------------------


.. _grids:

"Grids" object
*****************

TO BE COMPLETED

Description
============

Methods
==========

.. _materials:

"Material" object
***********************

TO BE COMPLETED

Description
============

Methods
==========

.. _geometry:

"Geometry" object
*********************

TO BE COMPLETED

Description
============

Methods
==========

.. _power:

"Power" object
*********************

TO BE COMPLETED

Description
============

Methods
==========
