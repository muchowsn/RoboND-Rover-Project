## Project: Search and Sample Return
### Writeup Template: You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---


**The goals / steps of this project are the following:**  

**Training / Calibration**  

* Download the simulator and take data in "Training Mode"
* Test out the functions in the Jupyter Notebook provided
* Add functions to detect obstacles and samples of interest (golden rocks)
* Fill in the `process_image()` function with the appropriate image processing steps (perspective transform, color threshold etc.) to get from raw images to a map.  The `output_image` you create in this step should demonstrate that your mapping pipeline works.
* Use `moviepy` to process the images in your saved dataset with the `process_image()` function.  Include the video you produce as part of your submission.

**Autonomous Navigation / Mapping**

* Fill in the `perception_step()` function within the `perception.py` script with the appropriate image processing functions to create a map and update `Rover()` data (similar to what you did with `process_image()` in the notebook). 
* Fill in the `decision_step()` function within the `decision.py` script with conditional statements that take into consideration the outputs of the `perception_step()` in deciding how to issue throttle, brake and steering commands. 
* Iterate on your perception and decision function until your rover does a reasonable (need to define metric) job of navigating and mapping.  

[//]: # (Image References)

[image1]: ./misc/rover_image.jpg
[image2]: ./calibration_images/example_grid1.jpg
[image3]: ./calibration_images/example_rock1.jpg 

## [Rubric](https://review.udacity.com/#!/rubrics/916/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it!

### Notebook Analysis
#### 1. Run the functions provided in the notebook on test images (first with the test data provided, next on data you have recorded). Add/modify functions to allow for color selection of obstacles and rock samples.

[please refer to test_dataset/recorded_img for recorded data](test_dataset/recorded_img)

* The functon for finding the color of obstacles was given in the color_thresh function. This fnction takes in a image and a RGB values to and returns a black and white image of everything above and below the set thresh hold. i experimented with the RGB values by looking at the rover img and looking at the navigable terrian and the mountain formation then playing with the RGB values to get a good threshed image. the default given did a nice job.<br/> 

* i created a function called find_rocks that works very similar to color_thresh but i passed the RGB values that differ from the naviable terrain and the yellow rock.


#### 1. Populate the `process_image()` function with the appropriate analysis steps to map pixels identifying navigable terrain, obstacles and rock samples into a worldmap.  Run `process_image()` on your test data using the `moviepy` functions provided to create video output of your result. 

[please refer to output/my_test_mapping.mp4 for my recorded data video output from notebook.](output/my_test_mapping.mp4)

process_image() is very similar to perception_step(). 
* I sart by appllying the percpective transform to img which takes a image and transforms it into a top down view. 
* I then apply color_thresh to the wraped image to create image to identify navigable terrian and obsticle terrain. 
* Then I create obs_map which is the thresed image with the mask applied to filter out the Horizontal Field of View of the camera.
* Then I create rover centric coordinates from the thresed image (topdown)
* Then I use the predefined functions to convert rover centric pixels to world coordinates so we can build up a map. 
* Then I fill the world map with the navigable terrian and non navigable terrain from the rover img which we got from the pre recorded data then our personal recorded data.
* Using find_rocks to the wraped image we use colour threashold technique to find the yello rocks.  
* if I find one I update the world map.  
* the rest of the code is given to me which sets output_image 


### Autonomous Navigation and Mapping

#### 1. Fill in the `perception_step()` (at the bottom of the `perception.py` script) and `decision_step()` (in `decision.py`) functions in the autonomous mapping scripts and an explanation is provided in the writeup of how and why these functions were modified as they were.

## perception_step()

* In percetion_step() I initialized the dst_size, image, bottom_offset, source, and destination, basically just initailizing variables to be used later
* Next I created a perspective transform of the Rover.img in wraped and mask. the perspective transform takes a camera image and transforms it into an over head view so we can create a 2d representative map
* Then colour_threshed the warped image. by couldour thresholding the wapred image we can then get the navigale terrain and the obstical terrain.
* I then used the threshed image with the mask to create an obsticle map
* Then i updated the Rover.vision_image by multiplyint thresed and obs_map by 255 so they colour stands out due to threshed and obs_map being binary either on/off
* Then I convert map image pixel to rover-centric coords with the functions provided so that we have a reference frame from the rover
* Then I convert rover-centric coords to world coordinates. I did this so that we can construct an absolute map of the world.
* Then update Rover.World map
* Then I convert rover-centric pixel positions to polar coordinates, so that we can process rover.nav_angles and rover.nav_dist
* Then I update Rover.nav_angles and Rover.nav_dist to be used by decision.py
* Then i use a find_rock that is basically colour threshold only with the color propertes of the yellow rocks.

## decision_step()
* In Decision_step() i followed the walkthrough and changed a few variables like making self.stop_forward larger as so not to get too close to the rocks and increased self.go_forward so that we need to see more navigable terrain before starting to go forward and turn.
#the decision tree is as follows:
* check which mode we are in either forward or stop
* if forward wi  check the extend of naviagble terrain if it looks good we check throttle if less than max we set throttle  * else we cost. we set break to 0 and set the steering angle between +/-15 degress based on the nav_angles
* if there is a lack of navigable terrain we set mode to stop and set break and steer angle to 0

* if we are in stop mode we check if we are still moving and set the break.
* if we are stoped and have no navigable terrain we turn on the spot
* if we see navigable terrain after turning we te set throttle and steering angle acoording to nav.angles  and switch mode to forward.

#### 2. Launching in autonomous mode your rover can navigate and map autonomously.  Explain your results and how you might improve them in your writeup.  

* I'm running the simulator on a ubuntu 16.04lts with a Intel Xeon E5-1620 v2 4 core with ht, 16GB of ram and a Nvidia GTX 1070
 
* I'm runign the simulator at 1440x960 at Fantastic graphics quality
I can get usually get between 50-80% of the environment maped with about 50% fidelity.

* I would like to improve on the fidelity by masking off pixels too far away from me to get better accuracy and to read the accelerometer data and only take images that when i'm flat as not to skew the pixels

* The simulator works well my navigation could be improved.
I would like to impement a wall following so that i stays x distance away from the left or right wall and follows it around

* I can usuall find 2-5 rocks i would like the implement mode when rock found then get the angle to the rock and folow theat anle tll is near object is yes then pick the rock and switch back to follow edge mode. 

**Note: running the simulator with different choices of resolution and graphics quality may produce different results, particularly on different machines!  Make a note of your simulator settings (resolution and graphics quality set on launch) and frames per second (FPS output to terminal by `drive_rover.py`) in your writeup when you submit the project so your reviewer can reproduce your results.**




