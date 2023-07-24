.. _exemple0:

################################
Simple Tihange exemple
################################

The data written in the file located at :file:`./data/Simplest.py` represent an light input designed to represent Tihange-1.
This exemple is a reduced version of :file:`./data/Tihange.py` (1.95% UO2 with water-filled tubes).
This chapter gives a description of this file content. To get started::

  materials = pydrag.Materials(NuclearData = 'https://github.com/IRSN/PyNjoy2016/releases/download/JEFF-3.x/drglibJEFF-3.1.1')

This last line allows the user to call a specific nuclear data library. In this case, the library will be downloaded from another Git repository. The Materials call will create a list of pre-defined mixtures.

::
  :caption: Description of UO2 fuel mixture (temperature, density and composition)

  materials.set_tfuel(286.1111,'C')
  materials.UO2.set_density(10.96*0.95)
  materials.UO2.set_enrichment('U234', 0.00018999999)
  materials.UO2.set_enrichment('U235', 0.0195)
  materials.UO2.set_enrichment('U236', 0.000120000004)

