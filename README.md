## Advanced Lane Line Detection

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/undistort.png "Undistorted"
[image2]: ./examples/undistort2.png "Undistorted2"
[image3]: ./examples/thresholded_binary.png "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/fit_line.png "Fit Visual"
[image6]: ./examples/final_result.png "Output"

[image7]: ./examples/roi.png "Region of Interest"
[image8]: ./examples/perspective_transform.png "Pespective Transform"
[image9]: ./examples/hard_image.png "Pespective Transform"

[video1]: ./project_video_output.mp4 "Video"


---

### Camera Calibration

#### 1. Get Transform Matrix and Distortion Coefficients through Chessboard Images

The code for this step is contained in the 1 ~ 5 code cell of the IPython notebook located in "find_lane_line.ipynb"

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Get Distortion-corrected Image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Get Thresholded Binary Image

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps in cells 8 ~ 9 in `find_lane_line.ipynb`).  Here's an example of my output for this step.

![alt text][image3]

#### 3. Get Perspective Transformed Image

The code for my perspective transform includes a function called `perspective_transform()`, which appears in cell 15 ~ 16 in the file `find_lane_line.ipynb`.  The `perspective_transform()` function takes as inputs an image (`img`), as well as the perspective transform matrix (`M`).  I chose the hardcode the source and destination points in the following manner:

```python
# Outer points of the trapezoid
src_outer_points = np.float32([[140, image.shape[0]], [1200, image.shape[0]], [800, 480], [520, 480]])
# inner points of the trapezoid
src_inner_points = np.float32([[360, image.shape[0]], [980, image.shape[0]], [660, 480], [660, 480]])
# Perspective transformed points of trapezoid
dst_points = np.float32([[500, image.shape[0]], [900, image.shape[0]], [900, 0], [500, 0]])
```

There are two kinds of source points, `src_outer_points` and `src_inner_points`, I use them to get the rigion of interest like this:
![alt text][image7]

This resulted in the following source and destination points:

| Outer Source  | Inner Source  | Destination   | 
|:-------------:|:-------------:|:-------------:| 
| 140, 720      | 360, 720      | 500, 720      |
| 1200, 720     | 980, 720      | 900, 720      |
| 800, 480      | 660, 480      | 900, 0        |
| 520, 480      | 660, 480      | 500, 0        |

I verified that my perspective transform was working as expected by looking at the source binary image and transformed binary image. The lines appear parallel in the transformed image.

![alt text][image8]

#### 4. Find the Lane Line and Fit a 2nd Order Polynomial

In cell 19 ~ 21 I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

I try to find the lane line with last frame first, if it is not found, then I go back to search it from scratch.
I also try to cache with recent frames, so the lane line would appear smooth. I did this in cell 26 ~ 27
#### 5. Calculated Radius of Curvature of the Lane Line and the Position of the Vehicle

I did this in cell 18 with function `calculate_r_curve()` in `find_lane_line.ipynb`

#### 6. Visualization of the Result

I implemented this step in cell 22 ~ 25 in my code in `find_lane_line.ipynb` in the function `color_image()` and the function `text_image()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Example

![example_gif](example_video_alld.gif)