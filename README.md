# Example of multiple video playback using DirectVideo Android

This is a project I created to stress-test the [DirectVideo plugin](https://joemarshall.github.io/directvideo/). It plays a 2k (3840x2176x24fps) video on 8 different spheres using 8 different decoders and a different startup delay for each sphere, so the decoders are all reading the video and decoding it independently.

You need to [buy and install the plugin](https://www.fab.com/listings/3259a389-1214-4312-a6aa-14fc8012ce7b) to build and test this project. Precompiled apk files for quest are available in the releases for your testing.

What I wanted to know when doing this was a) How many videos one can play back at once on my Quest 3 without display frame rate dropping below 72fps, b) How the resolution affects that.

The answer I discover is that you can play back videos until you hit the fixed limits of the hardware decoder on the Snapdragon GPU. The most important limit I found was the maximum total per frame blocks. There is a limit on Quest 3 of 278528 16x16 pixel blocks. To calculate how many blocks you are using over all videos you are playing back at the same time:

1) For each video, divide the resolution by 16 on each axis and round up if not divisible by 16
2) Multiply the divided by 16 resolutions together for each video, this is the number of decoder blocks you are using.
3) Sum all the decoder block counts up to get your total per frame block size. If this is greater than 278528, you won't be able to run this configuration on Quest.

There appear to be some other limits in the Qualcomm code, but I couldn't hit them before I hit this limit, and I'm not quite sure how to query them otherwise. If you're interested in delving into the Qualcomm code, you can see it in the Linux kernel here: [msm_vidc_driver.h](https://github.com/HELLBOY017/kernel_nothing_sm8475/blob/1e9dd6b500785e043314bc71bcb4255d418fd68c/techpack/video/driver/vidc/inc/msm_vidc_driver.h). 