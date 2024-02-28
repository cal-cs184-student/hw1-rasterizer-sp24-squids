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
> > todo so the max and min of the x and y coords must be found respectively, this gives 2 diagonal corners of the bounding box of the triangle. these found values must also be floor-ed and ceil-ed to ensure no pts are missed (e.i. as wellput by [byjus.com](https://byjus.com/maths/ceiling-function/#:~:text=The%20ceil%20function%20and%20the,equal%20to%20the%20specified%20number): If 5.6 the specified value, then the floor value is 5 and the ceiling value is 6). This limits the region of interest to only points that are potentially in or roughly around the triangle rather than looping through every possible point in the framebuffer.
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
> <img width="801" alt="HW1 - TASK1" src="https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/29182514-8fa4-467b-9e0e-28b3adc20328">

> *** Extra credit: Explain any special optimizations you did beyond simple bounding box triangle rasterization, with a timing comparison table (we suggest using the c++ clock() function around the svg.draw() command in DrawRend::redraw() to compare millisecond timings with your various optimizations off and on): <br>
> no attempt.
>
>  ੈ ✩‧₊˚ TASK 2 ⋆·˚ ༘ *.⋆ <br>
>
> *** Walk through your supersampling algorithm and data structures. Why is supersampling useful? What modifications did you make to the rasterization pipeline in the process? Explain how you used supersampling to antialias your triangles: <br>
> > **Why is supersampling useful?** Supersampling is particularly helpful in areas prone to aliasing effects, such as jagged edges or flickering. It involves subdividing each pixel into smaller sub-pixels and testing each sub-pixel for coverage by the triangle. This improves the visual quality of the rendered image by reducing aliasing effects. <br>
> > **Walk through algorithm and data structures + What mods did you make to the rasterization pipeline in the process?** I maintained my triangle orientation check, my bounding box calculations, and my floor and ceiling adjustments. The code then enters a loop to iterate over each pixel within the bounding box. Within this loop, there's another loop for super-sampling. Within the sampling loop I maintian my inside_edge() test from Task 1 but condesnsed and clarified it by making a helper function, inside_edges(...), which checks if a point is on the same side of all 3 edges of the triangle. If it is inside the triangle then... 
> >
> >  As discussed in the lecture 3, espeicially on slide 20, slight color distortions in some antialiased images are sort of a natural effect of the averaging process when supersampling. Here I attempted to antialiase without the distortions by rasterizing my points using the rasterize_point function and then _modifying_ the provided fill_pixel(...) function to sample_buffer while accounting for sample_rate (this also better supports rasterize_point and rasterize_line) instead of purely setting the "buffer" to the provided color with each averaged coordinate inside the rasterize_triangle function itself. Evident by my images, there is no color distortion but my edges become smoother and/or more coherent! I also _modified_ my resolve_to_framebuffer() function as it is called at the end of every rasterization. The reason for the modifcation was to average the pixels according to the sample_rate as done by for rgb_framebuffer_target in the following for loop. <br>
>
> *** Show png screenshots of basic/test4.svg with the default viewing parameters and sample rates 1, 4, and 16 to compare them side-by-side. Position the pixel inspector over an area that showcases the effect dramatically; for example, a very skinny triangle corner: <br>
> > sample rate: 16 !!! <br>
>
> <img width="801" alt="HW1 - TASK2  red, 16p" src="https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/9a34ae26-f094-4400-b9de-482d69ce1aab">
> > <br> sample rate: 4 !! <br>
>
> <img width="800" alt="HW1 - TASK2  red, 4p" src="https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/a92453e7-a7cd-4f72-ae30-3a7ac8ee0526">
> > <br> sample rate: 1 (no supersampling) <br>
>
> <img width="804" alt="HW1 - TASK2  red, 1p" src="https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/ffea8c47-31f9-4382-b6f6-67cd9afcd6ef">
>
> > <br> (note the left and right tips of the red triangle becoming more connected as well) <br>
>
> ** Explain why these results are observed: <br>
> > <br> Why, in general, are these results observed?
> > <br> Well, the more samples (aka higher sample rate) the more "accurate" (aka better box aproximate) and of "higher quality" (aka smoother lines) the triangle becomes. I also found this link provided by a staff member in the lecture slides really helpful in understanding blurry vs crisp edges (in relation to a digital painting brush but still super helpful in understanding how to create a crisper supersampling: [graphicdesign.com](https://graphicdesign.stackexchange.com/questions/150556/how-do-i-make-the-brush-tool-less-antialiased) )
> >
> 
> *** Extra credit: If you implemented alternative antialiasing methods, describe them and include comparison pictures demonstrating the difference between your method and grid-based supersampling. <br>
> no attempt.
>
>  ੈ ✩‧₊˚ TASK 3 ⋆·˚ ༘ *.⋆ <br>
> ***Create an updated version of svg/transforms/robot.svg with cubeman doing something more interesting, like waving or running. Feel free to change his colors or proportions to suit your creativity. Save your svg file as my_robot.svg in your docs/ directory and show a png screenshot of your rendered drawing in your write-up. Explain what you were trying to do with cubeman in words. <br>
> [IMG HERE] <br>
>
>  ੈ ✩‧₊˚ TASK 4 ⋆·˚ ༘ *.⋆ <br>
> *** Explain barycentric coordinates in your own words and use an image to aid you in your explanation. One idea is to use a svg file that plots a single triangle with one red, one green, and one blue vertex, which should produce a smoothly blended color triangle. <br>
> [ANSWER HERE]
> 
>
> *** Show a png screenshot of svg/basic/test7.svg with default viewing parameters and sample rate 1. If you make any additional images with color gradients, include them. <br>
> [IMG HERE]
> 

>  ੈ ✩‧₊˚ TASK 5 ⋆·˚ ༘ *.⋆ <br>
> *** Explain pixel sampling in your own words and describe how you implemented it to perform texture mapping. Briefly discuss the two different pixel sampling methods, nearest and bilinear. <br>
> [ANSWER HERE]
>
> 
> *** Check out the svg files in the svg/texmap/ directory. Use the pixel inspector to find a good example of where bilinear sampling clearly defeats nearest sampling. Show and compare four png screenshots using nearest sampling at 1 sample per pixel, nearest sampling at 16 samples per pixel, bilinear sampling at 1 sample per pixel, and bilinear sampling at 16 samples per pixel. <br>
> [ANSWER HERE]
>
> 
> *** Comment on the relative differences. Discuss when there will be a large difference between the two methods and why. <br>
> [ANSWER HERE]
> 
>
>  ੈ ✩‧₊˚ TASK 6 ⋆·˚ ༘ *.⋆ <br>
> *** Explain level sampling in your own words and describe how you implemented it for texture mapping. <br>
> [ANSWER HERE]
>
> 
> *** You can now adjust your sampling technique by selecting pixel sampling, level sampling, or the number of samples per pixel. Describe the tradeoffs between speed, memory usage, and antialiasing power between the three various techniques. <br> 
> [ANSWER HERE]
>
> 
> *** Using a png file you find yourself, show us four versions of the image, using the combinations of L_ZERO and P_NEAREST, L_ZERO and P_LINEAR, L_NEAREST and P_NEAREST, as well as L_NEAREST and P_LINEAR. <br>
> [ANSWER HERE]
>
> 
> > ** To use your own png, make a copy of one of the existing svg files in svg/texmap/ (or create your own modelled after one of the provided svg files). Then, near the top of the file, change the texture filename to point to your own png. From there, you can run ./draw and pass in that svg file to render it and then save a screenshot of your results. **Note**: Choose a png that showcases the different sampling effects well. You may also want to zoom in/out, use the pixel inspector, etc. to demonstrate the differences. <br>
> > [IMG HERE] 
> >
>
> *** Extra credit: If you implemented any extra filtering methods, describe them and show comparisons between your results with the other above methods. <br>
> no attempt. <br>
>




[Homework Specs](https://cs184.eecs.berkeley.edu/sp24/docs/hw1-spec)
