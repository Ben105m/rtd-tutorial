.. _exemple1:

################################
Fessenheim-1 exemple
################################

The data written in the file located at :file:`./data/Fessenheim-Bugey-Almaraz.py` represent the first start-ups of Almaraz-2, Bugey-2 and Fessenheim-1 and 2.
This chapter gives a description of this file content. 

The references for this input are :

- [1] `"In-core fuel management code package validation for PWRs" <https://inis.iaea.org/collection/NCLCollectionStore/_Public/26/077/26077395.pdf>`_ -- IAEA TECHOD-815 (1995)
- [2] `"Incertitudes et ajustements de données nucléaires au moyen de méthodes déterministes, probabilistes et de mesures effectuées sur des réacteurs à eau sous pression" <https://publications.polymtl.ca/10545/1/2022_VivianSalino.pdf>`_ -- V.SALINO Ph.D. thesis, Ecole Polytechnique de Montreal (2022)
- [3] `"Contribution à l’élaboration et à la qualification d’un schéma de calcul pour la gestion des réacteurs PWR, à l’aide du système Neptune - suivi du réacteur Fessenheim 2 <https://inis.iaea.org/collection/NCLCollectionStore/_Public/18/076/18076909.pdf>"`_ -- E.KAMHA Ph.D. thesis, Université Paris-Sud (1981)
- [4] `"Westinghouse technology advanced manual" <https://www.nrc.gov/docs/ML0230/ML023030318.pdf>`_ -- Section 4.1 OCFR50.46 Bases

To get started::

  for enri, Rods in [[3.10/100, 'None'],
                     [3.10/100, 'AIC']]:

This is the list of some assembly configurations, described by the fuel enrichment and the control rod types. Then::

  materials = pydrag.Materials(NuclearData= 'https://github.com/IRSN/PyNjoy2016/releases/download/JEFF-3.x/drglibJEFF-3.3_295')

This last line allows the user to call a specific nuclear data library. In this case, the library will be downloaded from another Git repository. The Materials call will create a list of pre-defined mixtures.

.. note::

  If the library does already exist in the :file:`./libraries/` folder (created / updated at each calculation), the library is not be downloaded again.

::

  materials.Pyrex.composition={'B2O3':12.5/100,
                               'SiO2':64.8/100,
                               'Al2O3':7.6/100,
                               'K2O':3.2/100,
                               'Na2O':3.1/100}
  materials.AIC.set_density(10.1564) 
  materials.UO2.set_enrichment('U235', enri)
  CoefPoro = 0.95
  CoefVoid = 0.9883
  materials.UO2.set_density(10.96*CoefPoro*CoefVoid)

These lines update different materials' property : Pyrex composition, AIC density, UO2 fuel density and composition.

.. note::

  In the case of fuel compositions, it is not required to declare the U238 ratio (as it is automatically calculated from the other isotopes).

::

  materials.set_tfuel([547, 'F'])
  materials.water.set_temperature([547, 'F'])
  materials.water.set_boron(600) 

Then, the fuel temperature (547°F), moderator temperature (547°F) and boron concentration (600 pcm) are defined. Moreover, the core power is given (in order to be used later during evolution calculation).

::

  materials.grids.set_mass(7*611.4,'Inconel')

The grids is declared through its mass (in this case, 7 grids of 611.4 g of Inconel).

::

  F = ['UO2', 0.4096,
       'void', 0.4179,
       'Zr4', 0.4750]

An exemple of fue pin description is given : 3 circles of UO2 fuel, "void" (used for the gap dilution, that is Helium in reality) and a Zircaloy-4 clad.

::

  if Rods == 'None':
    PinLayout = [[I, F, F, O, F, F, O, F, F],
                    [F, F, F, F, F, F, F, F],
                       [F, F, F, F, F, F, F],
                          [O, F, F, O, F, F],
                             [F, F, F, F, F],
                                [O, F, F, F],
                                   [F, F, F],
                                      [F, F],
                                         [F]]
  elif Rods == 'AIC':
    PinLayout = [[I, F, F, C, F, F, C, F, F],
                    [F, F, F, F, F, F, F, F],
                       [F, F, F, F, F, F, F],
                          [C, F, F, C, F, F],
                             [F, F, F, F, F],
                                [C, F, F, F],
                                   [F, F, F],
                                      [F, F],
                                         [F]]

The assembly pin layout is given as a list of list, representing each line of pins. These pins are described before (as it is done for the previously given fuel pin).

::

  geom = pydrag.Geometry(PinLayout, symmetry = '1/8',
                         PinPitch = 0.496*2.54,
                         AssemblyPitch = 8.466*2.54,
                         ActiveHeight = 144*2.54)

This line gives to PyDrag the pin layout and different assembly dimensions in order to represent the assembly geometry.

::

  powerDens = pydrag.Power(nbAssemblies = 157, corePower = 2686)

The assembly power density is automatically computed, based on the number of assemblies, the core power, and the different fuel types density.

::

  burnup,kinf = pydrag.Deplete(materials, geom, powerDens, ThermalExpans = True)

This last line asks PyDrag to make a depletion calculation (between 0 and 72 Gwd/t, using pre-defined burnup steps), by giving it every different informations related to materials, geometry and assembly power.
