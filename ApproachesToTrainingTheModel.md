# Approaches to Training the Model

FishNET V1 used [Teachable Machine](https://teachablemachine.withgoogle.com/) to train the back-end AI model.  This process uses transfer learning to fine-tune an image classification model on our dataset of fish images.

This approach does not allow for customizing training parameters.

## Possible Improvements
- FishNET V2 will implement [YOLO V8](https://docs.ultralytics.com/) which will give the system image segmentation capabilities, and object tracking. 