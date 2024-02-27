# Homework 1: Rasterizer

> Spring 2024
>
>  ੈ ✩‧₊˚ TASK 1 ⋆·˚ ༘ *.⋆ <br>
>
> *** Walk through how you rasterize triangles in your own words:
> 1) check that the given points are in a counterclockwise format... <br>
> I refered to [geeksforgeeks.org](https://www.geeksforgeeks.org/orientation-3-ordered-points/) to begin to conceptualize how to check for orientation. In the described implentation from geeksforgeeks it is clear that we are looking for the crossproduct of 2 vectors. These vectors being:
> > a. The vector from (x0, y0) to (x1, y1), which is (x1 - x0, y1 - y0). <br>
> > b. The vector from (x0, y0) to (x2, y2), which is (x2 - x0, y2 - y0). <br>
> > if the result of the cross product is negative, they are arranged clockwise; if positive, counterclockwise. clockwise orientation should be re-orientated to be counterclockwise, this is achieved with swapping x0 and x1, as well as swapping y0 and y1.
>
> 2) next find the bounding box of the triangle... 
> > todo so the max and min of the x and y coords must be found respectively, this gives 2 diagonal corners of the bounding box of the triangle. these found values must also be floor-ed and ceil-ed to ensure no pts are missed (e.i. as wellput by [byjus.com](https://byjus.com/maths/ceiling-function/#:~:text=The%20ceil%20function%20and%20the,equal%20to%20the%20specified%20number). If 5.6 the specified value, then the floor value is 5 and the ceiling value is 6). This limits the region of interest to only points that are potentially in or roughly around the triangle rather than looping through every possible point in the framebuffer.
> 
> 3) now loop through each point within the bound-box to determine if the pt falls inside the tringle + color fill if so...
> > i) first I find my sample coordinates which should be (0.5, 0.5) per the spec. <br>
> > ii) then I need to check if the point is on the same side of ALL 3 normal edges. <br>
> > > if it is, fill the pixel with color. if not, nothing needs to be done. <br>
>
> *** Explain how your algorithm is no worse than one that checks each sample within the bounding box of the triangle: <br>
> As described above, I am only checking each point in the bounded box istead of sampling every single point in the framebuffer, thus my algoritm is no worse as it does the same thing.
>
> *** Show a png screenshot of basic/test4.svg with the default viewing parameters and with the pixel inspector centered on an interesting part of the scene: <br>
> <img width="801" alt="Screen Shot 2024-02-26 at 6 32 52 PM" src="https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/5cb10da4-e2b9-421c-9d6a-c2eba2b4a654">

> *** Extra credit: Explain any special optimizations you did beyond simple bounding box triangle rasterization, with a timing comparison table (we suggest using the c++ clock() function around the svg.draw() command in DrawRend::redraw() to compare millisecond timings with your various optimizations off and on): <br>
> no attempt.
>
>  ੈ ✩‧₊˚ TASK 2 ⋆·˚ ༘ *.⋆ <br>
>
> *** Walk through your supersampling algorithm and data structures. Why is supersampling useful? What modifications did you make to the rasterization pipeline in the process? Explain how you used supersampling to antialias your triangles: <br>
> I am reattempting task 2, my initial attempts unfortantely caused the whole framebuffer screen to turn red and currenlty debugging. 
> *** Show png screenshots of basic/test4.svg with the default viewing parameters and sample rates 1, 4, and 16 to compare them side-by-side. Position the pixel inspector over an area that showcases the effect dramatically; for example, a very skinny triangle corner: <br>
> 
> ** Explain why these results are observed: <br>
>
> *** Extra credit: If you implemented alternative antialiasing methods, describe them and include comparison pictures demonstrating the difference between your method and grid-based supersampling. <br>
> no attempt.
>
>  ੈ ✩‧₊˚ TASK 3 ⋆·˚ ༘ *.⋆ <br>



[Homework Specs](https://cs184.eecs.berkeley.edu/sp24/docs/hw1-spec)
