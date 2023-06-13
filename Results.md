# Results

FishNET was deployed for the first time in March, 2023.  The hardware captured around 21 hours of footage locally, containing several instances of fish visible within view.  All footage is stitched together and sped up to create a video available here:

https://drive.google.com/file/d/1nb95ZHyUV2Q3Kd5SMj38bWtMZCf6NVu6/view?usp=sharing

The camera battery lasted the entirety of the 7-day deployment.  The footage is usable, but the visibility distance varies wildly over time.  In the first few days, the bank is hardly within view despite being around 4 feet from the camera.  By the third day, the bank is clearly in view, as well as all of the rocks and branches in front of it.  Despite these variations in visibility, fish swimming in front of the camera are easy to pick out.

After confirming that fish were visible in the footage, we ran it through our AI model to isolate the frames containing fish.  The model ran successfully and greatly reduced the time needed to pick out images of fish from the massive amount of collected video.  The model's results are published in a repository that can be found here:

https://github.com/SVVSDICAI/FishNetStreamCapture/blob/master/convertcsv.csv

Each row in this data table shows a fish that was detected by the model.  Each row has a link to the corresponding image containing the fish.  Below are some of the best such images located by the model:

![fish1](fish1.png)

![fish2](fish2.png)

![fish3](fish3.png)

![fish4](fish4.png)

The model also generated a graph of the number of fish counted over the course of the deployment:

![fish1](fish_counts.png)

The counts are estimated values as the current iteration of this model does not include object tracking.  This means that no information about the fish is retained between frames.  In short, it has no way of knowing if the fish in one frame is the same as the fish in another frame.  We are working on a more advanced model that will have some of these capabilities.  To get around this restriction, when the current model sees a fish, it will only increment the counter if there was no fish in the previous frame.  This way, a fish that stays in view for several frames is only counted once.  With this in mind, the above counts are a good estimate for the number of fish on a given day, but not an exact count.

Each detected fish is also classified based on predicted species.  All fish from this deployment were classified as Plains Topminnow.  The visibility makes it difficult to verify the correctness of these classifications, but to us, many of the captured fish look more like Creek Chub.  Using the collected images as training data should help improve future accuracy.

We learned so much valuable information from this deployment, both about our hardware and software.  We will continue using the collected video to improve our AI models. 