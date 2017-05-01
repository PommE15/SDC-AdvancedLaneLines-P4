# Writeup Report
This writeup is based on the [rubric points](https://review.udacity.com/#!/rubrics/571/view) and 
the [example template](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md).
All code is located in IPython notebook [Advanced_Lane_Lines.ipynb](./Advanced_Lane_Lines.ipynb).


[//]: # (Image References)

[image0]:  ./output_images/0.undistort_cali.png ""
[image1]:  ./output_images/1.undistort_test.png ""
[image2]:  ./output_images/2.threshold.png ""
[image3]:  ./output_images/3.transform.png ""
[image4]:  ./output_images/4.fit_lines.jpg ""
[image6a]: ./output_images/6.plot_back.png ""
[image6b]: ./output_images/6.plot_back_imgs.png ""
[image7]:  ./output_images/7.issues.png ""
[video1]:  ./output_video.mp4 ""


## Camera Calibration

#### Compute camera matrix and distortion coefficients

`In 1-4 code cells` I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image. Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image. `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera matrix `mtx` and distortion coefficients `dist` using the `cv2.calibrateCamera()` function. I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image0]


## Pipeline (image)

#### 1. Apply distortion correction
`In code cell 5` Here is an example of a distortion-corrected image:

![alt text][image1]

#### 2. Create a binary image using color transforms and gradients
`In code cells 6 and 7` I used a combination of gray color, s channel, and x gradient thresholds to generate a binary image. Here's an example of my output for this step:

![alt text][image2]

#### 3. Apply perspective transform to rectify the image

`In code cell 8` As stated in the class video, I manually defined 4 source and destination coord points in the following manner:

```
pt_lt = [ 588, 455]
pt_rt = [ 695, 455]
pt_rb = [1075, 700]
pt_lb = [ 245, 700]

pts_src = np.float32((
    pt_lt, pt_rt,
    pt_rb, pt_lb
))
pts_dst = np.float32((
    [pt_lb[0], 0], [pt_rb[0], 0],
    [pt_rb[0], img_size[1]], [pt_lb[0], img_size[1]]
))
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
|  588, 455     |  245,   0     | 
|  695, 455     |  245, 720     |
| 1075, 700     | 1075, 720     |
|  245, 700     | 1075,   0     |

Then I computed the perspective transform `M` with the defined points using the `cv2.getPerspectiveTransform()` function and transformed the test image with the `cv2.warpPerspective()` function.

I verified that my perspective transform was working as expected by drawing the `pts_src` and `pts_dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image. Here is an example:

![alt text][image3]

#### 4. Identify lane-line pixels and fit their positions with a polynomial

`In 10-15 code cells` I tried both histogram (2 on the right) and convolution (left) methods to detect lane lines. I chose histogram as it gives a better result without too much tuning and here is the comparison:

![alt text][image4]

#### 5. Calculate the radius of curvature of the lane and the position of the vehicle with respect to center

`In code cell 16` I calculated left and right lane curvatures in meters.

#### 6. Plot lane back down onto the road

`In code cell 17` I projected the lane boundaries back onto the road iamge. Here is an example image with lane boundaries, lanes, curvature, and position from center:

![alt text][image6a]

`Code cell 18` shows a summary of the pipeline I applied in this projec. Here are images that demonstrat how each stage works:

![alt text][image6b]


## Pipeline (video)
#### Sanity check

#### Final video output

Here's a [link to my video result](./output_video.mp4)


## Discussion

#### Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
