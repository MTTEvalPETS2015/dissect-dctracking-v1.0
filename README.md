# dissect-dctracking-v1.0
Original version of DCO for  CVPR 2012 paper

### Breakdown steps
(1) The additional packages
> - GCO
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
