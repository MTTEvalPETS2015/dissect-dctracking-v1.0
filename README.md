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

(5) detection format for dctracking-v1.0 (namely DCO), dctracking (namely DCO_X), contracking (namely CEM)
> - refer to `dctracking-v1.0/utils/parseDetections.m`, `dctracking-v1.0/utils/displayDetectionBBoxes.m`
> - once load, detections are loaded into a 1xN struct array with 11 fields, where N is the total number of sequence frames.
> - the length of each field equals to the number of detections at current frame
>   - bx, by: coordinates of top-left corner of the bounding box
>   - ht, wd: width, height of the bounding box
>   - xi, yi: coordinates of the median point of the bottom of bounding box, i.e. foot posiiton
>   - xp, yp: redundant fields, equals to (xi,yi) in 2d case, or (xw,yw) in 3d case
>   - sc: detection score
>   - xw, yw: world coordinates of the detected target (NOT SURE WHICH POINT EXACTLY BUT MOST LIKELY THE FOOT POSITION)
>   - **xc, yc**: centroid of the bounding box, read from XML file, not included in MATLAB struct arrary

(6) detection format for tracking_cvpr11_release_v1.0 (namely DP_MCF)
> - refer to `detect_objects.m`, `bboxes2dres.m`, `dres2bboxes.m` under `tracking_cvpr11_release_v1.0`
> - detections are saved in 1xN struct array with 1 field, where N is the total number of sequence frames.
>   - each item has 1 field 'bbox', which is in turn a Dx5 

(7) Errors before success
> - **sceneInfo.targetSize** is missing
```matlab
Reference to non-existent field 'targetSize'
Error in getSplineProposals (line 24)
speedThreshold = sceneInfo.targetSize;
```
> - **sceneInfo.trackingArea** is missing
```matlab
Reference to non-existent field 'trackingArea'.
Error in getSplineGoodness (line 22)
areaLimits=sceneInfo.trackingArea;
Error in getSplineProposals (line 111)
    [sg, ~]=getSplineGoodness(cubicspline,1,alldpoints,T);
Error in dcTrackerDemo1 (line 122)
```
> - solution, copy the following from `dctracking-v1.0/getSceneInfoDCDemo.m`

```matlab
%% target size
sceneInfo.targetSize=20;                % target 'radius'
sceneInfo.targetSize=sceneInfo.imgWidth/30;

%% tracking area
% if we are tracking on the ground plane
% we need to explicitly secify the tracking area
% otherwise image = tracking area
if opt.track3d
     sceneInfo.trackingArea=[-14069.6, 4981.3, -14274.0, 1733.5];
else
     sceneInfo.trackingArea=[1 sceneInfo.imgWidth 1 sceneInfo.imgHeight];   % tracking area
end

%% check
if opt.track3d
    if ~isfield(sceneInfo,'trackingArea')
        error('tracking area [minx maxx miny maxy] required for 3d tracking');
    elseif ~isfield(sceneInfo,'camFile')
        error('camera parameters required for 3d tracking');
    end
end
```


