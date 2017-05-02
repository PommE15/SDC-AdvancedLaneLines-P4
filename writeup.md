# Writeup Report
This writeup is based on the [rubric points](https://review.udacity.com/#!/rubrics/571/view) and 
the [example template](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md).
All code is located in IPython notebook [Advanced_Lane_Lines.ipynb](./Advanced_Lane_Lines.ipynb).


[//]: # (Image References)

[image0]:  ./output_images/0.undistort_cali.png ""
[image1]:  ./output_images/1.undistort_test.png ""
[image2]:  ./output_images/2.threshold.png ""
[image3]:  ./output_images/3.transform.png ""
[image4]:  ./output_images/4.find_line.jpg ""
[image6a]: ./output_images/6.plot_back.jpg ""
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
`In code cells 6 and 7` I used a combination of gray color (green), s channel (blue), and x gradient (red) thresholds to generate a binary image. Here's an example of my output for this step:

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

`In 10-15 code cells` I tried convolution (left), histogram (middle), and histogram that skipped the sliding windows (right) methods to detect lane lines. I chose histogram (middle) as it gives a better result without too much tuning and here is the comparison:

![alt text][image4]

#### 5. Calculate the radius of curvature of the lane and the position of the vehicle with respect to center

`In code cell 16` I calculated left and right lane curvatures in meters.

#### 6. Plot lane back down onto the road

`In code cell 17` I projected the lane boundaries back onto the road images. Here is an example image with lane boundaries, lane curvatures, and position of the vehicle with respect to center:

![alt text][image6a]

And here are images that demonstrate a summary of how each stage works:

![alt text][image6b]


## Pipeline (video)

`In code cell 18` It shows how I implemented this project. All steps are the same for test images and the video, except in the video there are additional sanity checks and reset of lane boundaries. Some basic rules check if:

* curvature values make sense
* left and right lanes are roughly parallel
* boundarie in current frame doesn't jump too much from the previous one

Here's [the link to my output video](./output_video.mp4)


## Discussion

The `Tips and Tricks for the Project` section of the class gives several good suggestions about how to improve the result, and I did try to apply some of them. For example, some parameter tunning to make sure the curvature values make sense and the other sanity checks. However, the pipeline might fail due to overfitting those values to our test video `project_video.mp4`. Another step that has lots of room to improve is the color and gradient thresholding. For example, s channel (marked in red color below) is efficient for the test video but cause issues in the other two videos `challenge_video.mp4` and `harder_challenge_video.mp4` due to the complexity of the images. Here are two images extracted from those videos that show those issues:

![alt text][image7]
