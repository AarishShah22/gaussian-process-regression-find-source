# Find the source with Gaussian Process Regression

This project was done as part of the course AEROSP 567 - Inference, Estimation and Learning - at the University of Michigan.

## Problem Setup

You have a robot which has a strong smell sensor and you want to use it to find a smelly object hidden in a fixed field of play. The field is irregular, so instead of the smell dissipating with distance, it creates "pockets" throughout the field. Also, you can only use the sensor a total of 12 times, in 3 batches, i.e., you can make 3 batches of requests with 4 sensor readings at different locations in each batch, or you can just make all 12 requests in one batch. Find the source of the smell!

## Approach and Results

We are given a set of initial sensor readings. Using Gaussian Process Regression, we get the following map for the mean of the smell across the field of play: 
![image](https://github.com/AarishShah22/gaussian-process-regression-find-source/assets/65495374/348a9ad6-726a-4579-887f-e953dc885ab5)

However, this was just fit across the initial sensor readings and this might not be a good indicator of the truth. We use the following process to learn more about the map and get a better indication of where the smell is:

1. Exploration stage:
    1. Pick the point with the highest variance, since we have the least "information" about this point.
    2. For the second point, pick the point with the highest variance in the area of (field of play) - (circle around previous picked point). This is done to force the algorithm to pick points some distance apart, since often the second highest variance might come right next to the point with the highest variance.
    3. Continue doing this to get all four points, and remove a circle around the previous picked point at each iteration. For example, for the last point, you'd have an area which has three circles removed.
    4. Get sensor readings at all four points.
    5. Train GPR on new sensor readings and adjust hyperparameters as necessary.
2. Exploitation stage 1:
    1. Do the same thing as previous stage, but instead of points with highest variance, find the points with highest mean.
    2. Get sensor readings, train GPR, adjust hyperparameters.
3. Exploitation stage 2: Same as previous stage.

In the end, we end up with this map for the mean of the smell across the map:

![image](https://github.com/AarishShah22/gaussian-process-regression-find-source/assets/65495374/f3d3fce0-79ad-4806-be07-c32a688bdb51)

Further, we can find the probability of a point being in a certain region using Monte Carlo. Using the mean and variance at each point, generate samples at each point and generate a random "surface". Now, find the point with the maximum value of the smell on this surface and store it. Repeat this process N times, the probability of the object being in a particular region is (number of times the max smell was in that region)/N.
