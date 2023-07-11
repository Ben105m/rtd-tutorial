.. _functions:

================================
Objetcs, functions and methods 
================================

PyDrag is manipulating different objects, each containing different kinds of informations :

  -  :ref:`mix` : contains mixture's informations, such as temperature, linear expansion coefficient, compositions. These objects are pre-defined in PyDrag, and it is important to keep their name for the calculation ;

  -  :ref:`grids` : contains the description of the assembly grids ;

  - :ref:`materials` : contains all the data related to mixtures. Among others, it includes "Library", :ref:`grids` and every existing :ref:`mix` (as sub-classes) ; 

  - "Geometry" object : describes the assembly geometry, such as the pin layout, the different dimensions, and the mixtures overlay of each pin ;

  - "Library" object : contains all the available informations for the chosen nuclear data library. The user does not need to directly interact with it, as the library type, the isotopes contained inside, and the nuclear data are automatically extracted with PyDrag ;

  - "Power" object : it is used to automatically computes the assembly power density (specifically used for evolution calculations) ;


.. _mix:

"Mix" object
----------------------------

The "Mix" objects are used to define the mixtures used for the calculation. By default, PyDrag is creating a list of pre-defined elements (based on common isotopic abundances), later used to create mixtures. The pre-defined mixtures have a fixed name, making them easier to handle by the code and directly callable in the input. However, it is possible for the user to create their own mixtures, duplicating existing mixtures, and such. The pre-definde mixtures are :  SS304, Inconel, Zr4, M5, AIC, B4C, Pyrex, Hf, water, Air, void, Gd, UO2, MOX.

.. _grids:

"Grids" object
----------------------------

TO BE COMPLETED

.. materials:

"Material" object
----------------------------

TO BE COMPLETED
