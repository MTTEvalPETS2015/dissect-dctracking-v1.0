# dissect-dctracking-v1.0
Original version of DCO for  CVPR 2012 paper

### Breakdown steps
(1) The additional packages
> - GCO
>   - A bunch of GCO_*.m files are missing from the `dctracking-v1.0/gco-v3.0/matlab` folder, which are crucial to bridge Matlab and GCO wrappers
>   - the MEX files of gco-v3.0 included in *dctracking-v1.0* are out of dated for my laptop (matlab 2015b etc.)
>   - DCO have to use compiled GCO folder from GCO_X* (i.e. `dctracking/external/GCO/{matlab, bin}`) by addpath() when running `dcTrackerDemo.m`

> - splinefit

(2) fill options struct
> - opt=getDCOptionsDemo;
> - NO config file, all parameters are hard coded in getDCOptionsDemo.m file

(3) fill scene info with `sceneInfo=getSceneInfoDCDemo;`
> - SceneInfo layout is **IDENTICAL** to contracking/getSceneInfoConDemo.m, with different values apparently
> - required field
>   -   detfile         detections file (.idl or .xml)
>   -   frameNums       frame numbers (eg. frameNums=1:107)
>   -   imgFolder       image folder
>   -   imgFileFormat   format for images (eg. frame_%04d.jpg)
>   -   targetSize      approx. size of targets (default: 5 on image, 350 in 3d)
> - Required for 3D Tracking only
>   -   trackingArea    tracking area
>   -   camFile         camera calibration file (.xml PETS format)
> - Optional:
>   -   gtFile          file with ground truth bounding boxes (.xml CVML)
>   -   initSolFile     initial solution (.xml or .mat)
>   -   targetAR        aspect ratio of targets on image
>   -   bgMask          mask to bleach out the background

(4) To facilitate the batch process of many sequences in future, the main function `dcTrackerDemo1.m` is modified to read options and `SceneInfo` from configuration files. 
> - exactly the same way as **dctracking** 
>    - opt=readDCOptions(optfile);
>    - sceneInfo=getSceneInfo(scenario,opti);

(5) detection format
> - refer to `dctracking-v1.0/utils/parseDetections.m`, `dctracking-v1.0/utils/displayDetectionBBoxes.m`
> - once load, detections are loaded into a 1xN struct array with 11 fields
>   - bx, by: coordinates of top-left corner of the bounding box
>   - ht, wd: width, height of the bounding box
>   - xi, yi: coordinates of the median point of the bottom of bounding box, i.e. foot posiiton
>   - xp, yp: redundant fields, equals to (xi,yi) in 2d case, or (xw,yw) in 3d case
>   - sc: detection score
>   - xw, yw: world coordinates of the detected target (NOT SURE WHICH POINT EXACTLY BUT MOST LIKELY THE FOOT POSITION)
>   - **xc, yc**: centroid of the bounding box, read from XML file, not included in MATLAB struct arrary

