# Homework 1: Rasterizer

> Spring 2024
>
>  ੈ ✩‧₊˚ TASK 1 ⋆·˚ ༘ *.⋆
> *** Walk through how you rasterize triangles in your own words:
> 1) check that the given points are in a counterclockwise format...
> I refered to https://www.geeksforgeeks.org/orientation-3-ordered-points/ to begin to conceptualize how to check for orientation. In the described implentation from geeksforgeeks it is clear that we are looking for the crossproduct of 2 vectors. These vectors being:
> a. The vector from (x0, y0) to (x1, y1), which is (x1 - x0, y1 - y0).
> b. The vector from (x0, y0) to (x2, y2), which is (x2 - x0, y2 - y0).
> if the result of the cross product is negative, they are arranged clockwise; if positive, counterclockwisethen. clockwise orientation should be re-orientated to be counter clockwise, this is achieved with swapping x0 and x1, as well as swapping y0 and y1.
> 2) next I find the bounding box of the triangle to limit the region of interest to only points that are potentially in the triangle. 

[Homework Specs](https://cs184.eecs.berkeley.edu/sp24/docs/hw1-spec)
