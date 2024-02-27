# Homework 1: Rasterizer

> Spring 2024
>
>  ੈ ✩‧₊˚ TASK 1 ⋆·˚ ༘ *.⋆ <br>
>
> *** Walk through how you rasterize triangles in your own words:
> 1) check that the given points are in a counterclockwise format...
> I refered to https://www.geeksforgeeks.org/orientation-3-ordered-points/ to begin to conceptualize how to check for orientation. In the described implentation from geeksforgeeks it is clear that we are looking for the crossproduct of 2 vectors. These vectors being:
> > a. The vector from (x0, y0) to (x1, y1), which is (x1 - x0, y1 - y0). <br>
> > b. The vector from (x0, y0) to (x2, y2), which is (x2 - x0, y2 - y0). <br>
> if the result of the cross product is negative, they are arranged clockwise; if positive, counterclockwisethen. clockwise orientation should be re-orientated to be counter clockwise, this is achieved with swapping x0 and x1, as well as swapping y0 and y1.
>
> 2) next I find the bounding box of the triangle... 
> > todo so the max and min of the x and y coords must be found respectively, this gives 2 diagonal corners of the bounding box of the triangle. This limits the region of interest to only points that are potentially in the triangle rather than looping through every possible point in the framebuffer.
> 
> 3) now I can loop through each point within the bound-box to determine if the pt falls inside the tringle...
> > i) first I find my sample coordinates which should be (0.5, 0.5) per the spec. <br>
> > ii) then I need to check if the point is on the same side of ALL 3 normal edges. <br>
> > > if it is, fill the pixel with color. if not, nothing needs to be done. <br>
>
> *** Explain how your algorithm is no worse than one that checks each sample within the bounding box of the triangle.


[Homework Specs](https://cs184.eecs.berkeley.edu/sp24/docs/hw1-spec)
