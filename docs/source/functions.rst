.. _functions:

================================
Objetcs, functions and methods 
================================

PyDrag is manipulating different objects, each containing different kinds of informations :

  -  "Mix" objects : contains mixture's informations, such as temperature, linear expansion coefficient, compositions. These objects are pre-defined in PyDrag, and it is important to keep their name for the calculation ;

  -  "Grids" object : contains the description of the assembly grids ;

  - "Material" object : contains all the data related to mixtures. Among others, it includes "Library", "Grids" and every existing "Mix" objects (as sub-classes) ; 

  - "Geometry" object : describes the assembly geometry, such as the pin layout, the different dimensions, and the mixtures overlay of each pin ;

  - "Library" object : contains all the available informations for the chosen nuclear data library. The user does not need to directly interact with it, as the library type, the isotopes contained inside, and the nuclear data are automatically extracted with PyDrag ;

  - "Power" object : it is used to automatically computes the assembly power density (specifically used for evolution calculations) ;


------------------
"Mix" object
------------------

The "Mix" objects are used to define the mixtures used for the calculation. By default, PyDrag is creating a list of pre-defined elements (based on common isotopic abundances), later used to create mixtures. The pre-defined mixtures have a fixed name, making them easier to handle by the code and directly callable in the input. However, it is possible for the user to create their own mixtures, duplicating existing mixtures, and such. The pre-definde mixtures are :  SS304, Inconel, Zr4, M5, AIC, B4C, Pyrex, Hf, water, Air, void, Gd, UO2, MOX.
As a side-note : "void" mixture's name can not be used by itself. In fact, this mix is identified (in the function "gap_dilution") as a surface to be diluted with the closest clad surface (during the thermal expansion process). 

A mixture is defined by its name, its temperature, density and composition. Additional informations can be given, such as the self-shielding, the pressure and boron concentration (explicitely for "water" and other moderators), or its ID (used in DRAGON to identify each mixture).
