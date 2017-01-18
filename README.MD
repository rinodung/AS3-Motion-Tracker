===================
AS3 MOTION TRACKER
===================
http://blog.soulwire.co.uk/code/actionscript-3/webcam-motion-detection-tracking
http://blog.soulwire.co.uk/code/actionscript-3/extract-average-colours-from-bitmapdata

For those of you without access to a webcam (and as an example of a practical use for this class) here is a short video demonstrating the program I wrote for the installation piece.


## End of update

Webcam required to view the demo (obviously…)

I’m currently working on putting up a show, part of which will be a live generative piece, constructed from the movement of visitors in the gallery space. So of course I needed a method for not only detecting but tracking motion. I’ve seen some of Grant Skinner and Mario Klingemann’s incredible experiments in this field, the most accurate of which used a coloured object in the camera’s field of vision (such as a ball or glove) to track movement, however I wanted the live response of the artwork to be a surprise to the viewer, and so asking them to hold a coloured object would have detracted from the overall impact.

If you ever want to be amazed by the human mind, try recreating the tasks that it performs, particularly those which we take for granted; you will quickly realise the incredible amount of computation which must be happening just to, for example, detect movement and track an object, or to discern more than one object and their relative motions, velocities and distances. This isn’t an essay on Neuroscience though so I’ll save that for another time…

Anyway; I researched the concept of motion tracking, and made some notes on my own ideas. A few possible approaches emerged.

One of the most attractive ideas was to divide the screen into a grid, and average the colours within each segment at regular intervals. The difference in brightness within each segment (from one calculation to the next) would determine whether or not motion had occurred in this area of the screen. Using this data, clusters of segments with a high rate of change could be used to assume an object in motion. This approach is quite accurate, however it would require a lot of getPixel and setPixel, as well as converting these results into Hue, Saturation, Brightness objects; all of which would be quite processor heavy. A decent computer could handle this fairly easily, however I had to also consider the processing load of the actual generative work running over the top.

The method I used in the end was much lighter, and providing the parameters could be adjusted to suit the environment, it would yield enough accuracy to provide a quite responsive tracking point for the artwork to follow.

## Here’s how it works

- After the camera object is set up, it is passed to a MotionTracker class I wrote, which extends the Point object. 
- The MotionTracker class contains two BitmapData objects; one representing the current data from the webcam, the other is used to store the previous frame.
- When the update method is called, the new frame is drawn on top of the previous using the difference filter.
- The result then has a ColorMatrixFilter applied to it, increasing the contrast of the image and dropping the darker areas (with less movement) further into the background.
- A blur filter is then applied in order to further reduce noise and to form blobs from areas where more movement is occurring.
- The threshold method is then called in the resulting BitmapData, effectively causing all near black pixels to be ignored and all light pixels (where movement has been detected) to be set to a certain colour.

So that takes care of the motion detection, but what about the tracking?

- First we call the getColorBoundsRect method on the BitmapData object, which gives us a Rectangle object representing the area occupied by pixels of the colour we used when applying the threshold filter.
- If the rectangle has an area larger than a specified constant, then sufficient movement is occurring and it can be tracked (choosing to ignore small areas of movement helps to eradicate noise caused by background and other interferences).
- We then find the centre point of this rectangle, and then move the x and y position of the MotionTracker instance to these coordinates, applying some gentle easing to give more continuity to the movement.
- I will post some source code once the MotionTracker class has been refined, and once I have build a nicer API so that, amongst other things, you can create your own UI for tweaking parameters at runtime; a useful feature if you have requirements such as mine where getting the most accuracy from a particular environment is important.
