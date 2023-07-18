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

.. warning::

  Each comment encapsulated in a warning box refers to an approximation used for non-regression purpose. Those mistakes will be removed later. 

.. _library:

"Library" object
**********************

Description
============

This class contains all the informations related to the chosen nuclear data library. It is created when calling the "Material" class, such as:

.. code-block:: Python

  materials = pydrag.Materials(NuclearData = 'https://github.com/IRSN/PyNjoy2016/releases/download/JEFF-3.x/drglibJEFF-3.1.1')

The "Library" class is able to identify internet adress (as long as it starts by "http") in order to download any library. For now, PyDrag can only download libraries from a Git repository. It is also possible to give the absolute path to this library (if it is located locally) or a symbolic link. In any case, PyDrag will create a new symbolic link named "MyLib", which is deleted at every restart (if it does exist). Moreover, PyDrag can handle different types of library, as long as they are APLIB2, APXSM or DRAGLIB.

Methods
==========

get_lib_type()
-------------------------

Get the library type, using the c2m procedure "GetLibType.c2m". It calls the LIB module while specificating a library type, looping through the three handlable types : "DRAGON", "APLIB2" and "APXSM". Obviously, if none of those type is right (or if the library is not readable), the calculation will stop.

get_isotope_list()
-------------------------

Recover the list of all the avaiable non-self-shielded isotopes' names in the chosen library.

get_energy_group()
-------------------------

Recover the energy group number considered in the chosen library, using the c2m procedures "GetEnergyGr.c2m" and "GetNRG.c2m". This information is used in order to define the self-shielding routine.

.. note::

  The "GetNRG" is calling LIB: module, and needs to know at least one isotope from the library. In consequence, this function can not be called before "get_isotope_list()".

get_isotope_name()
-------------------------

Check for any correspondance between a "user-defined" isotope's name and the library isotopes' names. For instance, it allows to link the user-defined isotope "Fe56" with its codename in the library (which can be "FE56_3" or anything else, depending on the library).

get_SS_isotope_list()
-------------------------

Recover the list of every available self-shielded isotopes in the chosen library.

.. note::

  This method is a bit different from the others. In fact, it is impossible (for now) to directly recover this list of names in a c2m procedure, as DRAGON only displays it in an output file (or in the terminal). This methods calls another dedicated python script (called "get_SS_isot.py") in order to execute the c2m procedure "GetSSIsot.c2m" and recovers the informations from the terminal.

It is mandatory to make a LIB: module call to get these informations : consequently, the name of at least one isotope from the library is required. This method can not be called before the "get_isotope_list()".

get_real_isot_name()
-------------------------

Check for any correspondance between a "user-defined" isotope's name and the library isotopes' names. **This function is used for specific isotopes (such as Mo95), for non-regression purpose.**

get_molar_mass()
-------------------------

Recover the molar masses of every available isotopes in chosen library. A correction is made in order to recover the right isotopic molar masses. In fact, the available libraries do not contain any direct mentions to the molar masses, and store the isotopic average weight ratio (AWR, which are the ratio of each isotope mass divided by the neutron mass). Moreover, as the isotopic neutron masses are not stored, the natural carbon mass (known, and set to 12.011 according to the NIST value) is used with the stored carbone AWR. The ratio of those two factors gives the corrective factor, applied on every AWRs.

.. _mix:

"Mix" object
******************

Description
============

The "Mix" objects are used to define the mixtures used for the calculation. By default, PyDrag is creating a list of pre-defined elements (based on common isotopic abundances), later used to create mixtures. The pre-defined mixtures have a fixed name, making them easier to handle by the code and directly callable in the input. However, it is possible for the user to create their own mixtures, duplicating existing mixtures, and such. 

Methods
==========

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

Description
============

This class contains all the inforations about every :ref:`mix` and :ref:`library`. These data are used to compute every isotopic concentrations, in order to define the used mixtures in assembly (through a LIB: module call to DRAGON). The user can modify each mix by interacting with each mixture's specific object, or can interact directly with the "Material" class (with the different methods).

Methods
==========

set_natural_abundance()
-------------------------

Set the isotopic abundances of the chosen natural element.

set_molar_mass()
-------------------------

Set the molar mass of the chosen natural element.

set_compounds()
-------------------------

Pre-define a list of chemical compounds.

.. note::

  The pre-defined compounds are B2O3, SiO2, Al2O3, Na2O, Gd2O3 and H2O.

add_compounds()
-------------------------

Add a user-defined chemical compound. 

add_element()
-------------------------

Add a user-defined natural element.

.. note::

  This method can be used to update the isotopic abundances of an existing element.

set_tfuel()
-------------------------

Set the fuel temperature. The available units are degree Celsius, Fahrenheit and Kelvin.

.. note::

  This method modifies the temperature of 'UO2', 'MOX' and 'Gd' mixtures.

duplicate_mix()
-------------------------

Duplicate the chosen mixture.

.. note::

  By default, the new mixture will be named after a combination of the original name and a digit.

.. note::

  It is recommanded to use this methode in order to create different MOX fuels, as it follows :

  1) Duplicate the existing "UO2" mixture (and name it "MOX")
  2) Duplicate the "MOX" fuel into as many mixtures as wanted. The identified MOX fuel names are "MOX_low", "MOX_medium" and "MOX_high", used to represent fuels with different plutonium enrichements.
  3) Manually set the isotopic enrichments (see :ref:`mix`).


set_natural_elements()
-------------------------

Pre-define a list of natural elements compositions. 
Each element is named after its chemical symbol, then followed by the locution "Nat".
For exemple :

==================  ====================
Element name        Name in PyDrag
==================  ====================
iron                  FeNat
chrome                CrNat
sulfur                SNat 
==================  ====================

.. warning::

  The isotope Gd152 is not taken into account in the natural gadolinium composition (and replaced by Gd154).
  Moreover, the natural aliminium composition is different when using an APXSM-formatted nuclear data library.


load_composition()
-------------------------

Load all the defined natural elements and mixtures' compositions into the object. This method follows these steps :

1) Recover the isotopes' molar masses from the library
2) Check for any missing isotopes by listing all the pre-defined ones. If an isotope is missing in the library, it is replaced by the N+2 isotope.
3) Compute the natural elements' molar masses (or recover these molar masses if they do exist in the library)
4) Compute the atomic densities of each mixtures

.. warning::

  The silicium molar mass is equal the Si28 molar mass. The molybdenum molar mass is equel to the Mo95 molar mass.

.. note::

  This method is used to update the mixtures/elements every time the user modifies the default caracteristics.

make_default_mix()
-------------------------

Pre-define the default mixtures' compositions.

.. note::

  Default mixtures' name are :

=====================  =======================
Common name              Pydrag mixture name
=====================  =======================
Stainless Steel 304      SS304
Inconel                  Inconel
Zircaloy-4               Zr4
Ag-In-Cd                 AIC
Air                      Air               
UO2+Gd fuel              Gd
MOX fuel                 MOX
UO2 fuel                 UO2
B4C                      B4C
Water                    water
Pyrex                    Pyrex
Void                     void
M5                       M5
Hafnium                  Hf
=====================  =======================

make_common_mix()
-------------------------

Compute the isotopic concetrations of the mixtures that are not made of U238. It includes all the "structure" materials, the "moderators", and the "absorbers".

.. warning::

  The molar mass of water is set to 18.01528 in the case of non-APLIB2 libraries.

add_grids()
-------------------------

Create different moderators to be used in different areas of the assembly. It is representing the diltued assembly grids. The created moderators are called "MODE" (for central fuel cells), "MODEL" (for lateral fuel cells), "MODEC" (for corner fuel cells) and "MODETE" (for tube cells).

.. note::

  The moderators compositions are calculated based on the grid description given in the :ref:`grids` and the "water" mixture.

make_mix()
-------------------------

Generate every mixtures' isotopic concentration based on all the given informations (default and user-defined).

add_combinated_mix()
-------------------------

Create a used-defined combinated mixture, which is a material described through other existing mixtures. It differs from other mixtures as it is desribed in DRAGON with the "COMB" keyword at the LIB: call. 

make_fuel_mix()
-------------------------

Compute the isotopic concetrations of the mixtures containing U238.

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
