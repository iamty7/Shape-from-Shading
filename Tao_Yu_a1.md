# CS543 Computer Vision

Yu Tao (yutao2) | Assignment 1 - Shape from Shading

## 1. Implemented Solution

> Briefly describe your implemented solution, focusing especially on the more "non-trivial" or interesting parts of the solution. What implementation choices did you make, and how did they affect the quality of the result and the speed of computation? What are some artifacts and/or limitations of your implementation, and what are possible reasons for them?

For the `preprocess` function, I first reshape the `ambimage` from (h,w) to (h,w,1) so that we can do the subtraction without using any loops because of array broadcasting in Numpy. (loops in python are slow)

After the subtraction, I set all the negative values to zero and rescale the intensities to between 0 and 1.

In the `photometric_stereo` function, I reshape the `imarray` from (h,w,N) to (h*w,1). In this way, we only need a single call for `np.linalg.lstsq` to get the g array. Finally, we need to reshape g array back into the shape we need, which is (h,w,3), to calculate the albedo and surface norms of the surface.

In the `get_surface` function, I wrote 4 methods to find a path from the left-top point to the target, which will be discussed below. 

The reshape operation greatly improves the performance of subtraction and division in the program.

## 2. Integration Methods

### 2.1 Difference between methods

> Discuss the differences between the different integration methods you have implemented.

**Row First**

I use `np.cumsum` to get the cumulative sum - `x_cumsum` and `y_cumsum`. But what we need for the *Row First* method is the first row of `x_cumsum`. Then we can get `height_map` by `height_map = y_cumsum + x_cumsum`, which utilizes array broadcasting to avoid loop and speed up the computation.

**Column First**

Most of the method is just like what we did in the *Row First* method. But we only need the first column of `y_cumsum`.

**Average Method 1&2**

Average the ``height_map`  we got from method 1&2.

**Random**

It should output a better height map if we find some random paths and get the average of them. 

First of all, we calculate the cumulative sum of the first row and column. For each pixel, we randomly chose either its left or above pixel and add the height of that  pixel and the partial derivatives.

In this way, we got several height maps and then output the average of them.

**Summary**

The first and second methods depend on their first row and column, therefore may lead to significant distortion if there are some errors. The random method are less likely to encounter those bad paths.

### 2.2 Outputs for yaleB05

> Specifically, you should choose one subject, display the outputs for all of a-d (be sure to choose viewpoints that make the differences especially visible), and discuss which method produces the best results and why.

**yaleB05 Outputs**

#### 2.2.1 Rows first

| Method 1   |                          Rows first                          |
| :--------- | :----------------------------------------------------------: |
| Height Map | ![yaleB05_heightmap_row_first](outputs/yaleB05_heightmap_row_first.jpg) |
| Albedo Map | ![yaleB05_albedo_row_first](outputs/yaleB05_albedo_row_first.jpg) |
| N_x, y, z  | ![yaleB05_normals_x_row_first](outputs/yaleB05_normals_x_row_first.jpg)![yaleB05_normals_y_row_first](outputs/yaleB05_normals_y_row_first.jpg)![yaleB05_normals_z_row_first](outputs/yaleB05_normals_z_row_first.jpg) |

#### 2.2.2 Column first

| Method 2   |                         Column First                         |
| ---------- | :----------------------------------------------------------: |
| Height Map | ![yaleB05_heightmap_col_first](outputs/yaleB05_heightmap_col_first.jpg) |
| Albedo Map | ![yaleB05_albedo_col_first](outputs/yaleB05_albedo_col_first.jpg) |
| N_x, y, z  | ![yaleB05_normals_x_col_first](outputs/yaleB05_normals_x_col_first.jpg)![yaleB05_normals_y_col_first](outputs/yaleB05_normals_y_col_first.jpg)![yaleB05_normals_z_col_first](outputs/yaleB05_normals_z_col_first.jpg) |

#### 2.2.3 Average

| Method 3   |                           Average                            |
| ---------- | :----------------------------------------------------------: |
| Height Map | ![yaleB05_heightmap_average](outputs/yaleB05_heightmap_average.jpg) |
| Albedo Map | ![yaleB05_albedo_average](outputs/yaleB05_albedo_average.jpg) |
| N_x, y, z  | ![yaleB05_normals_x_average](outputs/yaleB05_normals_x_average.jpg)![yaleB05_normals_y_average](outputs/yaleB05_normals_y_average.jpg)![yaleB05_normals_z_average](outputs/yaleB05_normals_z_average.jpg) |

#### 2.2.4 Random

| Method 4   |                            Random                            |
| ---------- | :----------------------------------------------------------: |
| Height Map | ![yaleB05_heightmap_random](outputs/yaleB05_heightmap_random.jpg) |
| Albedo Map | ![yaleB05_albedo_random](outputs/yaleB05_albedo_random.jpg)  |
| N_x, y, z  | ![yaleB05_normals_z_random](outputs/yaleB05_normals_z_random.jpg)![yaleB05_normals_x_random](outputs/yaleB05_normals_x_random.jpg)![yaleB05_normals_y_random](outputs/yaleB05_normals_y_random.jpg) |

**Summary:**

> Discuss which method produces the best results and why



### 2.3 Running time

> Compare the running times of the different approaches

Take yaleB05 for example

| Method       | Row First | Column First | Average | Random |
| ------------ | :-------- | ------------ | ------- | ------ |
| Running time | 159ms     | 161ms        | 214ms   | 5.3s   |

Since method 1~3 use broadcasting and cumsum to avoid loop, they are much faster than the Random method. Also, randomly choosing path slows down its speed.

### 2.4 Other outputs

|         |         |         |
| :-----: | :-----: | :-----: |
| yaleB01 | yaleB02 | yaleB07 |

## 3. Albedo and Height Maps

> For every subject, display your estimated albedo maps and screenshots of height maps (use display_output and plot_surface_normals). When inserting results images into your report, you should resize/compress them appropriately to keep the file size manageable -- but make sure that the correctness and quality of your output can be clearly and easily judged. For the 3D screenshots, be sure to choose a viewpoint that makes the structure as clear as possible (and/or feel free to include screenshots from multiple viewpoints). 

## 4. Data Violate the Assumptions

> Discuss how the Yale Face data violate the assumptions of the shape-from-shading method covered in the slides. What features of the data can contribute to errors in the results? Feel free to include specific input images to illustrate your points. Choose one subject and attempt to select a subset of all viewpoints that better match the assumptions of the method. Show your results for that subset and discuss whether you were able to get any improvement over a reconstruction computed from all the viewpoints.

## 5. Extra Credits

