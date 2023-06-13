# Camera
The camera is located inside the underwater enclosure, it is the most important part of the system.  FishNET uses the raspberry pi camera 2 (pictured below).

![pi cam connected](pi-camera-attached.jpg)

The camera can be seen situated in the [underwater box](enclosure.md) in the following image.

![camera in box](camera_in_box.jpg)

## Types of camera
The raspberry pi can be used with several camera models, for our original prototype we used the [high quality camera](https://www.raspberrypi.com/products/raspberry-pi-high-quality-camera). Any other model could be used, though each will have distinct advantages ad disadvantages.
### infrared cameras
The raspberry pi [camera module 3](https://www.raspberrypi.com/products/camera-module-3/) can be bought in a full spectrum "noIR" (no infrared filter) version. Infrared light is blocked by water much more effectively than the visible spectrum so the scene would need to be lit artificially.

## Possible Improvements
- Infrared camera with artificial lighting as mentioned in [[#Camera#Types of camera#infrared cameras]]
- Camera Multiplexing to create panoramic video