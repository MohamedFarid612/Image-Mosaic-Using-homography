# Image Mosaic Using homography
 # Applying Image Processing Filters For ImageCartoonifying & Road Lane Detection Using Hough Transform

> This readme file is a summary of the project. For more details, please refer to the notebooks.

## Table of Contents



## Part 1
## Introduction
- In this project, our goal was to develop an image stitcher, seamlessly combining two input images into a unified mosaic using image warping and homographies. We focused on mastering homogeneous coordinates and homography matrices, crucial for transforming and aligning images onto a shared plane. Employing Python and prominent computer vision libraries, we implemented a meticulous solution that computed homography matrices to warp one image into the perspective of another. The report encapsulates our experimental setup, image selection rationale, and the analysis of results, highlighting the effectiveness of our solution in achieving seamless image integration

## Steps 
### Getting Correspondances
- In the project's initial phase, we focused on pinpointing key corresponding points by plotting and clicking on distinctive features in both images. This step is crucial because we need at least four accurate correspondences to calculate the homography matrix effectively. The precision of these chosen points directly impacts the final mosaic's accuracy and coherence. By deliberately selecting recognizable features present in both images, we aimed to facilitate the creation of a seamless mosaic. This emphasizes the significance of this initial step, as the success of subsequent image alignment and stitching directly hinges on the accuracy of these identified correspondences.

![Alt text](image.png)
![Alt text](image-1.png)



### Getting Homography matrix
- To facilitate the computation of homography parameters, we developed a function designed to take a set of corresponding image points and derive the associated 3x3 homography matrix, denoted as H. This matrix plays a pivotal role in transforming any point, represented as p in one view, to its corresponding homogeneous coordinates, p', in the second view. The function expects a list of n (where n is greater than or equal to 4) pairs of corresponding points from the two views, each specified with its 2D image coordinates. Utilizing a system of linear equations Ax = b, we stack the 8 unknowns of H into an 8-vector x, where the 2n-vector b contains image points from one view, and the 2n x 8 matrix A is appropriately filled. The resulting system, λp = Hp, with H3,3 set to 1, allows us to solve for the unknown homography matrix parameters. This function lays the foundation for accurate homography computations essential for subsequent image transformations and mosaics.



- In this part choosing the points where very critical as by shifting one point the warped image could be affected highlt.
![Alt text](image-4.png)
![Alt text](image-2.png)
![Alt text](image-3.png)
### Applying Thresholding & Inverting Image
- This process effectively applies a binary thresholding operation to the Laplacian image, where pixels with Laplacian values below a certain threshold (in this case, 20) are considered as non-edge regions and set to black, while pixels with Laplacian values above or equal to the threshold are considered as edge regions and remain white. The resulting thresholded image is then displayed with a title indicating its purpose![Alt text](images/3.png).![Alt text](images/1.png)


### Applying Bilateral Filter
- The bilateral filter is a non-linear, edge-preserving smoothing filter commonly used in image processing. It aims to reduce noise while preserving the important edges in an image. The strength of the bilateral filter is controlled by several parameters: size, color_strength, and positional_strength.The size parameter determines the neighborhood size over which the filter operates. A larger size considers a wider range of pixels in the filtering process.The color_strength parameter controls the filter's sensitivity to the color differences between neighboring pixels. A higher value leads to a stronger effect of color similarity in the filtering process.The positional_strength parameter determines the filter's sensitivity to the spatial distance between neighboring pixels. A higher value causes the filter to be more influenced by pixel positions.

- The bilateral filter is effective in preserving edges while smoothing flat regions, making it a great choice for automatic cartoonification or painting filters. However, the bilateral filter can be extremely slow, taking seconds or even minutes to process an image. To achieve a balance between performance and quality, we can utilize a trick of performing bilateral filtering at a lower resolution. This lower-resolution filtering produces a similar effect to filtering at full resolution but significantly reduces the computational cost and improves processing speed.

- To apply the bilateral filter, the first step is to convert the input image to grayscale if it's a color image. Next, determine the size of the filter kernel, typically a square or circular window centered on each pixel. Calculate the standard deviations for both the spatial and intensity domains, controlling the range of influence for neighboring pixels. Iterate over each pixel in the image and, for each pixel, iterate over the pixels within the kernel window. Compute the spatial distance using Euclidean distance between the central pixel and its neighboring pixel within the kernel. Compute the intensity difference between them and calculate the weight for each neighboring pixel using a Gaussian function that considers both spatial and intensity distances. Multiply the weight by the intensity value of the neighboring pixel and sum up the weighted intensity values within the kernel window. Then, compute the normalization factor by summing up the weights and divide the sum of the weighted intensity values by the normalization factor to obtain the filtered pixel value. Repeat these steps for all pixels in the image, resulting in the final output of the bilateral filtering process.



- positional_strength Threshold: The spatial threshold typically represents the size of the neighborhood and is often defined as a positive, non-zero value. Common values for the spatial threshold range from 1 to 10, where smaller values result in a more localized effect, while larger values encompass a larger spatial neighborhood for smoothing.

- color_strength Threshold: The range threshold usually represents the similarity in pixel values and can be defined as a positive value. The range threshold can vary widely depending on the image data and the desired effect. Typical values for the range threshold are in the range of 0 to 255, where a lower value restricts smoothing to pixels with very similar values, preserving more details and edges, while a higher value allows for a broader range of pixel values to be considered similar, resulting in more smoothing.![Alt text](images/image-8.png)![Alt text](images/image-5.png)

### Adding all together
- Our code essentially takes the input image, applies a binary threshold to create a mask, and then replaces the pixel values in the resulting mask with the corresponding pixel values from the input image, resulting in a modified black image where only the pixels that satisfy the specified threshold are preserved.
![Alt text](images/2.png)

## Road Lane Detection
This Jupyter notebook, `Hough.ipynb`, demonstrates the use of the Hough Transform for image processing tasks, such as line detection in road lanes.

![Alt text](images/road_lanes.png)

### Overview

The Hough Transform is a feature extraction technique used in image analysis, computer vision, and digital image processing. It's particularly used for detecting imperfect instances of objects within a certain class of shapes, with circles and lines being the most common shapes.

### Hough Transform Steps

#### 1. Edge Detection

The Hough Transform is most commonly used for detecting lines in an image, so the first step is to apply an edge detection algorithm, such as the Canny edge detector, to the image. The output of the Canny edge detector is a binary image with white pixels tracing out the detected edges and black pixels everywhere else.

#### 2. Mapping of Edge Points to the Hough Space and Accumulator

The next step is to map the edge points from the image space to the Hough space and accumulator. The Hough space is a parameter space defined by the parameters of the object we're trying to detect. In the case of lines, the Hough space is defined by the two parameters of a line: slope and intercept. The accumulator is a 2D array whose dimensions are determined by the range of values for the parameters. Each cell in the accumulator represents a possible line in the image space. The value of each cell is the number of edge points that could be part of the line represented by that cell.

#### 3. Interpretation of the Accumulator to Yield Lines

The final step is to interpret the accumulator to yield lines. The cells in the accumulator with the highest values represent the lines that are most likely to be present in the image. The threshold for determining whether a cell represents a line is determined by the minimum number of edge points needed to detect a line. For each cell that meets the threshold, the corresponding line is drawn on the original image.

### Algorithm
```
1. Read the image from the file "images/street.png" and convert its color from BGR to RGB.
2. Apply a median blur to the image to reduce noise.
3. Convert the blurred image to grayscale.
4. Apply the Canny edge detection algorithm to the grayscale image.
5. Extract the region of interest from the image. This region is defined as the lower half of the image, excluding the rightmost 120 pixels.
6. Initialize the Hough Transform accumulator. This is a 2D array where each cell represents a possible line in the image. The size of the accumulator is determined by the maximum possible value of rho (the distance from the origin to the line) and the range of theta (the angle of the line).
7. For each non-zero pixel in the region of interest, calculate its rho and theta values for each possible theta, and increment the corresponding cell in the accumulator.
8. Apply non-maximum suppression to the accumulator to find the local maxima. These represent the most likely lines in the image.
9. For each local maximum, calculate the parameters of the corresponding line (slope and intercept) and draw the line on the original image.
```

## Authors
- [Yousef Kotp](https://www.github.com/yousefkotp)
- [Mohammed Farid](https://www.github.com/MohamedFarid612)
- [Adham Mohammed](https://www.github.com/adhammohamed1)


