.. _nonregression:

==========================
Non-regression protocole
==========================

Each input ends with a non-regression section. These sections contain reference kinf value, which are compared to the nominal evolution kinf obtained with the calculation scheme. The references come from calculations made with DRAGOR-V1.3, with Version5 distribution 5.0.7 (ev2068) and using nuclear data library CEA93.V7 with 172 energy groups.
Reference results were made with the different available calculation parameters (control rods type, fuel type, enrichment value, ...). These kinf are then called through pydrag.non_regression function, that displays the results for each burnup step in addtion with the kinf difference (in pcm). A warning message is displayed if the difference is higher than a chosen threshold (default:0.5 pcm). It is possible to create a txt file in the chosen location that stores those results.

Obviously, it is possible to comment/delete these sections if they are not required.
