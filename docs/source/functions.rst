.. _functions:

################################
Objetcs, functions and methods 
################################

PyDrag is manipulating different objects, each containing different kinds of informations :

  - :ref:`Library object <library>` : contains all the available informations for the chosen nuclear data library. The user does not need to directly interact with it, as the library type, the isotopes contained inside, and the nuclear data are automatically extracted with PyDrag ;

  -  :ref:`mix` : contains mixture's informations, such as temperature, linear expansion coefficient, compositions. These objects are pre-defined in PyDrag, and it is important to keep their name for the calculation ;

  - :ref:`grids` : contains the description of the assembly grids ;

  - :ref:`materials` : contains all the data related to mixtures. Among others, it includes :ref:`library`, :ref:`grids` and every existing :ref:`mix` (as sub-classes) ; 

  - :ref:`geometry` : describes the assembly geometry, such as the pin layout, the different dimensions, and the mixtures overlay of each pin ;

  - :ref:`power` : it is used to automatically computes the assembly power density (specifically used for evolution calculations) ;

PyDrag is calling the :ref:`calculation` in order to perform different actions on data (such as thermal expansion, dilutions, etc.) and make evolution calculations.

The next chapters detail the different objects handled by PyDrag, and describe the existing methods. The method's (current) limitations are clearly defined.

.. warning::

  Each comment encapsulated in a warning box refers to an approximation used for non-regression purpose. Those mistakes will be removed later. 

.. _calculation:

"calculation.py" module
*************************

Functions
==========

get_power_density()
-------------------------

Recover the assembly power density. If a non-zero density is already set in :ref:`power`, is it used. If not, the density is calculated.

get_water_density()
-------------------------

Get the water density from pre-tabulated DRAGON data, by using pressure and temperature stored in the water mixture object.

.. note::

	This method uses the c2m procedure "GetWaterDens.c2m".

count_pin()
-------------------------

Count the number of pins in assembly, according to their position (center, side and corner). It takes into the account the symmetry axis.

get_bu_list()
-------------------------

Get the pre-defined evolution burnup step. These values depend of the fuel type used (presence of Gd or not).

Tihange_model()
-------------------------

Process the calculation of relative elongation coefficient. The material temperature is taken into account.
The coefficient calculation routine comes from the publication "Qualification du système NEPTUNE" (by H.PANEK) at p.153.

.. note::

	This function is used when there is no defined thermal expansion coefficient, for each material.

PIN_model()
-------------------------

Process the calculation of distance dilatation.

.. note::

	This function is used when a non-zero thermal expansion coefficient is defined, for each material.

gap_dilution()
-------------------------

Process the gap dilution for each pin in geometry. The gaps are identified when PyDrag sees a "void" mixture in the cell, and will dilute this empty space with the closest clad.

.. note::

	In reality, the gap is made of helium, rather than void.

grid_dilution()
-------------------------

Process the dilution of the assembly grids into the water. It uses the :ref:`grids` data and water mixture data. Different dilution models are available:

- "Tihange": reference model for Tihange assembly. This model is based on the process described in the paper "Qualification du système NEPTUNE" (by H.PANEK), by using a unique temperature.
- "Tihange_dev": updated model for Tihange assembly grid dilution. Based on the same source than previous model, and making it temperature-dependant. More explanation in the "warning" box. This model uses the cold volumic frations description of the grids.
- "TOT": generic grid dilution process. This model uses the massic/volumic description of the grids.
- "NULL": no grid dilution.

.. warning::

	The "Tihange_dev" model is using the cold volumic fractions of each grid materials (in the different considered area to be diluted in) and the moderator temperature. These data allows PyDrag to compute the different hot assembly volumes, and then to get the hot volumic frations (which leads to the dilution ratio in the different areas). It allows to take into account the moderator temperature, which is always set to 286°C for "Tihange" model (which is a problem for branching calculations). As a side note, the "Tihange_dev" routine reproduces the results obtained in the reference document.

thermal_expans()
-------------------------

Process the thermal expansion calculation for every distances and densities. The thermal expansion either uses the user-defined linear elongation coefficients or computes them (based on pre-defined values and adapting them with temperatures).

verify_data()
-------------------------

Verify the content of the different PyDrag objects that may cause PyDrag calculation failures.

init_data()
-------------------------

Initialize some calculation informations, based on the user-defined data in input. This functions :

- adds some moderator in the surrounding area of each pin (in order to make rectangular cells) ;
- creates the circular rings for the fuel pins ; 
- computes the average clad temperature ;
- processes the thermal expansion, gap dilutions and grid dilution ;
- regroups every identical clads for the different pins ;
- creates the circular rings for AIC/B4C/Hafnium for control rods ;
- saves the mixtures/pins used for the assembly (in order to not modelize unused pre-defined mixtures) ;
- gives a generic name for each identical pins of the plan ;
- identifies the isotopes that should be self-shielded ;

.. warning::

	The AIC aborber does not contain any ring for 15x15 pins assembly.

.. warning::

	The silicium isotopes are ignored for the description of the Pyrex absoring rods (even if they are used to compute the isotopic concentrations and densities). 

get_SS_IDs()
-------------------------

Get the INRS number of self-shielded isotopes (INRS is the index of resonant region associated with each isotope). These IDs come from the DRAGON user guide.

LIB()
-------------------------

Process the LIB: module call, by desribing every used mixtures. There are two leakage model that can be used ("APOL" and "OLDW").

.. note::

	The self-shielding method is automatically adapted to the number of energy groups in the library :
	subgroup self-shielding with physical probability tables ("SUBG") if there are less than 295 groupes
	mathematical probability ("PT") tables otherwise

GEO()
-------------------------

Process the GEO: module call, in order to describe to assembly and to associate each geometrical areas to a mixture.


USS()
-------------------------

Process the USS: module call, in order to create/update self-shielded library. The USS: calls are performed for every self-shielding burnup steps (that are pre-defined).

FLU()
-------------------------

Process the ASM: and FLU: modules call. There are three different available leakage models :

- "APOL" : APOLLO-equivalent leakage model
- "CASM" : CASMO-equivalent leakage model
- "NLKG" : no leakage model

T0()
-------------------------

Process the T0 calculation, wihch initialize every LCM objects (by calling init_data(), LIB(), GEO(), USS() and FLU()) .

EVO()
-------------------------

Perform the EVO: module call between two time (in days) / burnup (in MWd/t or GWd/t) steps.

Deplete()
-------------------------

Perform the evolution calculation for the given burnup step list, grid dilution type and leakage model.

.. note::

	It is possible to make a depletion calculation for a burnup step list equals to [0], in order to make a T0 calculation.

.. _library:

"Library" object
**********************

Description
============

This class contains all the informations related to the chosen nuclear data library. It is created when calling the "Material" class, such as:

.. code-block:: python
  :caption: Library specification exemple in a PyDrag input (through the Materials class)

  materials = pydrag.Materials(NuclearData = 'https://github.com/IRSN/PyNjoy2016/releases/download/JEFF-3.x/drglibJEFF-3.1.1')

The "Library" class is able to identify internet adress (as long as it starts by "http") in order to download any library. For now, PyDrag can only download libraries from a Git repository. It is also possible to give the absolute path to this library (if it is located locally) or a symbolic link. In any case, PyDrag will create a new symbolic link named "MyLib", which is deleted at every restart (if it does exist). Moreover, PyDrag can handle different types of library, as long as they are APLIB2, APXSM or DRAGLIB.

.. warning:

	For now, it is impossible to use different libraries for the calculations if the files have the **same name** in different repositories. In fact, cloning a file with :file:`git clone` will not copy a new library if it has the same name as the previous one (as PyDrag will identify them as the exact same ones, based on their names).


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

Recover the molar masses of every available isotopes in chosen library. A correction is made in order to recover the right isotopic molar masses. In fact, the available libraries do not contain any direct mentions to the molar masses, and store the isotopic average weight ratio (AWR, which are the ratio of each isotope mass divided by the neutron mass). Moreover, as the isotopic neutron masses are not stored, the natural carbon mass (known, and set to 12.011 according to the natural carbon NIST value) is used with the stored carbone AWR. The ratio of those two factors gives the corrective factor, applied on every AWRs.

.. _mix:

"Mix" object
******************

Description
============

The "Mix" objects are used to define the mixtures used for the calculation. By default, PyDrag is creating a list of pre-defined elements (based on common isotopic abundances), later used to create mixtures. The pre-defined mixtures have a fixed name, making them easier to handle by the code and directly callable in the input. However, it is possible for the user to create their own mixtures, duplicating existing mixtures, and such. 

Methods
==========

info()
-------------------------

Displays general informations about the mixture class.

set_density()
-------------------------

Set the mixture's density (in g/cm3).

set_temperature()
-------------------------

Set the mixture's temperature (in °C, °K or °F). The temperature is converted and stored as °C.

set_enrichment()
-------------------------

Set the isotopic enrichment/ratio (works for one isotope/element at a time).

.. note::

  This method is only used to define fuel mixtures.

set_pressure()
-------------------------

Set the mixture's pressure (in bar, psi or Pa). The pressure is converted and stored as bar.

.. note::

  This methods is only used to set moderator's pressure.

set_compo()
-------------------------

Set the mixture's composition. This methods can take as many isotopes/elements as needed in one call.

set_boron()
-------------------------

Set the mixture's boron concentration (in pcm).

.. note::

  This methods is only used to set moderator's boron concentration.

set_fraction()
-------------------------

Set the mixture's MOX fraction in the different assembly areas (corner, side and center pins).

.. note::

  This methods is only used for MOX-type fuels.

set_thermal_coef()
-------------------------

Set the mixture's linear expansion coefficient. By default, this coefficient is always set to 0. If the user defines its own coefficients, they will be used accordingly. However, if there is no coefficient (or if some are missing), PyDrag automatically check for pre-defined expansion coefficients (as a function of the mixture's temperature).

.. _grids:

"Grids" object
*****************

Description
============

This class describes the assembly grids. PyDrag identifies the grids in two different categories : "Crate" ones (default grid) and "Sleeve" ones. This model comes from the BEAVRS grids representation. Those two different grid types represent (in the same order) the grids that will be diluted in the assembly moderator, and the grids to be diluted in the surrounding water gap. It is possible to describe the grids by giving different types of information, described in the following sub-chapter.

Methods
==========

set_fraction()
-------------------------

Set the cold volumic fraction of chosen material in different moderator areas ("tube", "fuel" and "gap" areas).

.. note::

	This method is specifically designed for Tihange-1 assembly description, where there is no data about grid mass/volume.

set_mass()
-------------------------

Set the chosen material's total mass in the grids (in g).

set_volume()
-------------------------

Set the chosen material's total volume in the grids (in cm3).

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

.. note::

	It is not necessary to declare the proportion of U238 in fuel mixtures, as it is automatically calculated based on the other declared ratio.

add_combinated_mix()
-------------------------

Create a used-defined combinated mixture, which is a material described through other existing mixtures. It differs from other mixtures as it is desribed in DRAGON with the "COMB" keyword at the LIB: call. 

make_fuel_mix()
-------------------------

Compute the isotopic concetrations of the mixtures containing U238.

.. _geometry:

"Geometry" object
*********************

Description
============

This class describes the assembly geometry to be modelized. It is mainly represented by a list of lists, containing the assiociation of pin's dimensions and the different materials' names inside each described areas. A pin is represented as a rectangular cell composed of centered circular regions. An assembly can be fully represented, or using 4th symmetry or 8th symmetry. For now, it is mandatory to describe it by its "South East" side (for 4th symmetry) or "East South-East" side (for 8th symmetry), as PyDrag has pre-defined symmetry axis for the GEO: module call.

Methods
==========

set_mesh()
-------------------------

Set the pin's mesh for each pin type, based on the type of moderator used in the cell.

make_rings()
-------------------------

Create a list of rings for the chosen material.

.. note::

	This functions is used for the spatial discretization of fuel (and absorber) rings.
	
set_pin_type()
-------------------------

Create a copy of the assembly plan attributes and replace every identical pins by a generic name (in order to make them easier to identify).

get_position()
-------------------------

Identify the position (corner/side/center) of each pin of the plan. This method helps to identify which kind of moderator will be used for the pins.

.. _power:

"Power" object
*********************

Description
============

This class contains some data required to compute the assembly power density (this calculation is done with a function from calculation.py, as it requires some informations about fuels).

Methods
==========

set_power_density()
-------------------------

Set the power density of assembly. By default, the value is set to 0, which leads to an automatic calculation of the density in calculation.py. A non-zero value will be directly used for evolution calculation.
