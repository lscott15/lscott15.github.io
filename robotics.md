# Ceiling Localization
The goal of this project was to design and implement a system that could accurately localize the Turtlebot using an overhead camera.  The motivation was the drawbacks and general inaccuracy of the built-in localization of the Turtlebot using AMCL.  I opted for a camera on the ceiling and a marker on the robot instead of an upward facing camera on the robot and markers on the ceiling because:

1. I understood the ceiling camera option more fully.
2. The space I had to use had low hanging lights that could obstruct the view of the markers from below.

I originally imagined the flow of the program as thus:
1. Recognize the robot with the ceiling camera and extract position and orientation from image
2. Use image_geometry to turn that pixel in image space into a 3d vector facing the robot in camera space
3. Use depth image data to find how far our vector must travel to reach the robot
4. Publish that point and orientation as a frame in tf that is the parent of base_footprint

That is about it, all we need in addition to that is to publish our ceiling camera frames as a child of our world frame and made sure base_footprint has no other parents.  To accomplish those I chose to use a static_frame_publisher node and to modify the turtlebot bringup to not publish the odom frame, the normal parent of base_footprint. The final tf tree should look something like this.

![TF diagram](/images/tf_tree.png)

To simplify my first pass I ended up removing the use of depth data and instead getting my point in camera space from the 3d vector and the fact that I already know the z coordinate as long as the ceiling camera is perfectly perpendicular to the floor.

![Basic localizer approach](/images/localizer_drawing.png)

This method should be equally accurate and reduced development time and processing load slightly by not requiring use of the depth sensor in the kinect.

## Object tracking through color blobs

My first pass at recognizing and tracking the robot with the ceiling camera was based off this tutorial that takes a given color (red for example) and separates that color channel into a binary image.  Then it finds the largest closed shape in that image and tracks it.  This was done for two colored pieces of tape on the robot, one in back and one in front.  To get position in 3d space I found the midpoint of the centroids of those two shapes and used the projected vector to get position like in the above diagram.  Orientation was calculated using the angle between the line going through our two tape centroids points and the origin.  Finally, this position and orientation is published to a tf frame whose parent is the optical frame of the ceiling camera.  A static_frame_publisher can then connect our base ceiling camera frame to the map and the tf frame we published to the base of the robot.
A video of this method can be found [here](https://www.youtube.com/watch?v=YujTeesezbw).  As you can see, the centroids of our colored pieces of tape move quite a bit, leading to a very unreliable form of localization.  While this might be suitable for some applications we need a much more accurate and reliable implementation.  While the implementation above is quite simple, I did experiment with using opencv to approximate a more regular shape from the color blobs but the variation from frame to frame remained.

## Object tracking with shape matching

I thought it might be more reliable to find the center of a pattern rather than a solid color and so after a few iterations on the marker I ended up with this.\

![Robot marker](/images/robot_marker.jpb)

My plan was to detect the center of the checkerboard markers and then detect front from the colors.  I thresholded a grayscale version of my image to try to select just the white parts of my checkerboard.  The thresholded image is pictures on the left.  From this I found the closed contours in the image and discarded those two small to be my markers.  Finally I sorted the contours based on how well they matched a pattern I drew in gimp (pictured to the right).

![Closed contours](/images/thresh.png)
![Matching pattern](/images/pattern.png)

Finding the centroids of the checkerboard shapes proved to be more accurate from frame to frame and yielded a more satisfactory result.  You can find a video [here](https://www.youtube.com/watch?v=opjV2vWPePo).

While this method is slightly more accurate and much more robust because of the shape matching it still leaves something wanting.  I looked into averaging the centroids of my markers from frame to frame to hopefully remove the jitter that affects the orientation of the robot so much but it offered little improvement when averaging a small number of samples and with larger n the position and orientation lagged behind the robot while moving and rotating.

My next thought was to try to use opencv’s corner detection methods to adjust the points I have from the contour centroids and hopefully place them on the center intersection of my checkerboard marker.  I was unable to adjust the corner detection methods in opencv to reliably find those intersections though.

![Prototype corner approach](/images/corners.png)

The blue dots are the centroids of our checkerboard contours and the red corners are corners detected with opencv’s goodFeaturesToTrack.  I also tried using cornerSubPix to the positions of the contour centroids but it yielded very similar results.


