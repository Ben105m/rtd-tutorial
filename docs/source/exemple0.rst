.. _exemple0:

################################
Simple Tihange exemple
################################

The data written in the file located at :file:`./data/Simplest.py` represent an light input designed to represent Tihange-1.
This exemple is a reduced version of :file:`./data/Tihange.py` (1.95% UO2 with water-filled tubes).
This chapter gives a description of this file content. To get started::

  materials = pydrag.Materials(NuclearData = 'https://github.com/IRSN/PyNjoy2016/releases/download/JEFF-3.x/drglibJEFF-3.1.1')

This last line allows the user to call a specific nuclear data library. In this case, the library will be downloaded (from another Git repository, in this exemple). The Materials call will create a list of pre-defined mixtures.

.. code-block:: python
  :caption: Description of UO2 fuel mixture (temperature, density and composition)

  materials.set_tfuel(286.1111,'C')
  materials.UO2.set_density(10.96*0.95)
  materials.UO2.set_enrichment('U234', 0.00018999999)
  materials.UO2.set_enrichment('U235', 0.0195)
  materials.UO2.set_enrichment('U236', 0.000120000004)

.. code-block:: python
  :caption: Description of water mixture (which is the moderator)

  materials.water.set_temperature(286.1111,'C')
  materials.water.set_density(0.716403)
  materials.water.set_boron(600)

.. code-block:: python
  :caption: Description of Tihange grids

  materials.grids.set_fraction(1.175,'Inconel','tube')
  materials.grids.set_fraction(2.223,'SS304','tube')
  materials.grids.set_fraction(0.5496,'Inconel','fuel')
  materials.grids.set_fraction(4.166,'Inconel','gap')

.. code-block:: python
  :caption: Geometry made of 2 different pins (fuel F and tube T)

  F = ['UO2', 0.464693,
       'void', 0.474218,
       'Zr4', 0.53594] 
  T = ['water', 0.65024,
       'Zr4', 0.69342] 

.. code-block:: python
  :caption: Geometry pin layout and assembly dimensions

  PinLayout = [[T, F, F, F, T, F, F, F],
                  [F, F, F, F, F, F, F],
                     [F, F, F, T, F, F],
                        [T, F, F, F, F],
                           [F, F, F, F],
                              [T, F, F],
                                 [F, F],
                                    [F]]
  geom = pydrag.Geometry(PinLayout,PinPitch = 1.4300201,AssemblyPitch = 21.50364,ActiveHeight = 365.76)

.. code-block:: python
  :caption: Assembly power density parameters

  powerDens = pydrag.Power(nbAssemblies = 157, corePower = 2652)

.. code-block:: python
  :caption: Tihange evolution calculation (with a Tihange-type grid dilution)

  burnup,kinf = pydrag.Deplete(materials, geom, powerDens,TypeDil = 'Tihange')

