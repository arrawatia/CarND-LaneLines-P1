# **Finding Lane Lines on the Road** 
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)


**Finding Lane Lines on the Road**

The goals / steps of this project are the following:

* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

---

# Install
(These are based on instructions here [configure_via_anaconda](https://github.com/udacity/CarND-Term1-Starter-Kit/blob/master/doc/configure_via_anaconda.md) )

1. Install miniconda by following instructions in the link above.

   Verify: You should see the following:
   
   ```
   > conda -V
    conda 4.4.10
   ```

2. Clone this repo : 

    ```
    git clone https://github.com/arrawatia/CarND-LaneLines-P1
    ```

3. Setup the conda env

    ```
    cd CarND-LaneLines-P1
    conda env create -f environment.yml
    source activate lanelines
    ```
    
    Verify: Your python should point to lanelines env
    
    ```
    > which python
    /home/arrawatia/miniconda3/envs/lanelines/bin/python
    ```
    
    You should have the following packages installed.
    
    ```
    conda list
    ```
    The package list should match [installed-packages](installed-packages.txt)
    
4. Start Jupyter notebook

   ```
   jupyter notebook
   ```

5. Access the URL shown in logs and run the P1 notebook.

---

# Reflection

## 1. Pipeline

The image processing pipeline is implemented by the `pipeline` function. It has the following steps:

  - Convert the image to greyscale
  - Smoothen it out using Gaussian blur 
  - Use canny edge detection to detect edges
  - Mask the image in a quadrialateral to focus on the section of the image with the highest likelihood of lanes  
  - Detect the lanes using Hough transform
  - Draw the lines and overlay it on the test image 

**Running experiments**

The pipeline is driven through the `run_test_on_images` and `run_test_on_videos` functions. These functions allows us to run different line drawing algorithms on the test images & videos.

### Experiments   
 
I tried 3 algorithms to draw lines. They are described below with a brief description of the results

    
1. **Base case with default function provided in the sample code**
    
    This case verifies that the pipeline works as expected and helps in tuning the values for the vertices of the masking quadrilateral, gaussian blur, thresholds for Canny edge detection and Hough transform.

    **Results**

    The pipeline detects the lanes well on both the test images and videos. The results can be viewed at 
    * [test_images_output/default](test_images_output/default)
    * [test_videos_output/default](test_videos_output/default)


1. **Drawing lines on lanes using average slope**
    
    The idea is to classify lines into left and right lanes using the slope. Once the the lines are classified, calculate the average slope fo each category and draw lines from the bottom of the image to somewhere in the middle.
    
    The algorithm is 
    
    ```
    This function extrapolates the lines found by the Hough tranform. The algorithm is :

	For each line:
    - Calculate slope of the line
    - If slope > 0, then classify the line as left lane
                    else classify the line as right lane
    - Calculate average slope and intercept for left and right lanes
    - Draw lines with the calculated left and right slopes that connects Y=image.height and Y=image.height/1.5
    ```

    **Results**

    The pipeline draws line reasonably well on the lanes. Although some lines are out of alignment in a few of the test images. It fails badly on the challenge. 
    
    The results can be viewed at 
    * [test_images_output/average_slope](test_images_output/average_slope)
    * [test_videos_output/average_slope](test_videos_output/average_slope)

1. **Drawing lines on lanes by fitting a line through the points from the Hough transform**
    
    The idea is to classify lines into left and right lanes using the slope. Once the the lines are classified, fit a line through the points in both these classes and draw lines from the bottom of the image to somewhere in the middle. 
    
    Reference : [Extrapolating lines](http://ottonello.gitlab.io/selfdriving/nanodegree/python/line%20detection/2016/12/18/extrapolating_lines.html
)
    
    The algorithm is 
    
    ```
    This function extrapolates the lines found by the Hough tranform. The algorithm is :

	For each line:
    - Calculate slope of the line
    - If slope > 0, then classify the line as left lane
        else classify the line as right lane
    - Calculate a line that passes through the points for left and right lanes using least squares (L2 norm)
    - Draw lines with the calculated left and right slopes that connects Y=image.height and Y=image.height/1.5

    ```

    **Results**

    The pipeline performs better on than average. This algorithm too fails badly on the challenge. 
    
    The results can be viewed at 
    * [test_images_output/fit](test_images_output/fit)
    * [test_videos_output/fit](test_videos_output/fit)



## 2. Shortcomings
The shortcomings of the pipeline are:

- Expects the lane to be in the same position. If the camera is mounted in a different alignment, the pipeline will need to be tuned differently.
- The pipeline is very sensitive to noise and imperfections on the road.
- The pipeline does not handle steep curves on the road very well.
- The pipeline will not perform well in rainy conditions as the training set does not have any such images.

Some of these shortcomings might be the reason for the bad performance of the pipeline on the challenge (as well on some parts of the test videos).

## 3. Possible improvements

We can improve the pipeline to make it more robust in following ways:

* Devise a algorithm or train a model to figure out the vertices of the masking region based on the alignment of the camera.
* Filter out the noise while drawing lines. Some ideas to try out are - 
	- Drop all lines whose slope is 1 or 2 SD away from the average slope
	- Use L1 norm for fitting lines as it is more resilient to outliers




