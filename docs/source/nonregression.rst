.. _nonregression:

######################################
Non-regression protocole
######################################

Each input ends with a non-regression section. These sections contain reference kinf value, which are compared to the nominal evolution kinf obtained with the calculation scheme. 

The references come from calculations made with the first commit of PyDrag, with Version5 distribution 5.0.9 (ev2958) and using nuclear data library JEFF3.1.1 (172gr) located at `PyNjoy2016 GitHub repository <https://github.com/IRSN/PyNjoy2016/releases/download/JEFF-3.x/drglibJEFF-3.1.1>`_.

.. note:

  Before the public release, the non-regression tests were led with PyDrag and its reference calculation scheme, using different nuclear data library.

Reference results were made for the different available calculation parameters (control rods type, fuel type, enrichment value, ...). These kinf are then called through pydrag.non_regression function, that displays the results for each burnup step in addtion with the kinf difference (in pcm). A warning message is displayed if the difference is higher than a chosen threshold (default:0.5 pcm). It is possible to create a txt file in the chosen location that stores those results.

Obviously, it is possible to comment/delete these sections if they are not required.
