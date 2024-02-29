# * .✧.*✧.*✧.*✧.*✧.* Homework 1: Rasterizer ✧.*✧.*✧.*✧.*. *

> Spring 2024 <br>
>
>  ੈ ✩‧₊ ˚Overview ⋆·˚ ༘ *.⋆ <br> 
> *** Give a high-level overview of what you implemented in this homework. Think about what you've built as a whole. Share your thoughts on what interesting things you've learned from completing the homework: <br>
> In this HW I was tasked with creating a rasterizer that can handle different forms of pixel and level sampling on provided images which are in an SVG format. This includes pixel nearest and pixel bilinear sampling as well as no level sampling to nearest level sampling to bilinear level sampling. The goal for this assignment was to ensure the rasterizer can draw basic points, lines, and shapes, then apply color and texture. <br>
> I have had to utilize equations and concepts provided during the course as well as the wider web to best digest the core concepts of the assignment. The following write up is a reflection of my efforts and what I have learned from completing this HW. Overall, though, I would say the biggest thing I have learned would be navigating XCode and familiarzing myself with a new language, C++! 
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
> > [please note: the S feature for my HW was not producing .png screeshots in the working directory for roughly the first half of the assignment, it was my computer key issue (sticky key), therefore some of the photos are direct screenshots using my computers built in feature.]

> *** Extra credit: Explain any special optimizations you did beyond simple bounding box triangle rasterization, with a timing comparison table (we suggest using the c++ clock() function around the svg.draw() command in DrawRend::redraw() to compare millisecond timings with your various optimizations off and on): <br>
> no attempt.
>
>  ੈ ✩‧₊˚ TASK 2 ⋆·˚ ༘ *.⋆ <br>
>
> *** Walk through your supersampling algorithm and data structures. Why is supersampling useful? What modifications did you make to the rasterization pipeline in the process? Explain how you used supersampling to antialias your triangles: <br>
> > **Why is supersampling useful?** Supersampling is particularly helpful in areas prone to aliasing effects, such as jagged edges or flickering. It involves subdividing each pixel into smaller sub-pixels and testing each sub-pixel for coverage by the triangle. This improves the visual quality of the rendered image by reducing aliasing effects. <br>
> > **Walk through algorithm and data structures + What mods did you make to the rasterization pipeline in the process?** I maintained my triangle orientation check, my bounding box calculations, and my floor and ceiling adjustments. The code then enters a loop to iterate over each pixel within the bounding box. Within this loop, there's another loop for super-sampling. Within the sampling loop I maintian my inside_edge() test from Task 1 but condesnsed and clarified it by making a helper function, inside_edges(...), which checks if a point is on the same side of all 3 edges of the triangle. If it is inside the triangle then I rasterize the point. 
> >
> >  As discussed in [lecture 3, slide 20](https://cs184.eecs.berkeley.edu/sp24/lecture/3-20/antialiasing), slight color distortions in some antialiased images are sort of a natural effect of the averaging process when supersampling. Here I attempted to antialiase without the distortions by rasterizing my points using the rasterize_point function and then _modifying_ the provided fill_pixel(...) function to sample_buffer while accounting for sample_rate (this also supports rasterize_point and rasterize_line which do not need to be supersampled but still ensures correct color is sampled) instead of purely setting the "buffer" to the provided color with each averaged coordinate inside the rasterize_triangle function itself. Evident by my images, there is no color distortion but my edges become smoother and/or more coherent! If the color distoration should be present for the sake of the HW, a possible quick fix would be to perform sample_buffer() directly in the rasterize_triangle(...) function and instead of [sample_buffer[y * width + x] = c;] it would be  (vaguely pseudocode) [sample_buffer[**sample_rate** * (**y_sample** * width + **x_sample**) + s] = c;]. I also _modified_ my resolve_to_framebuffer() function as it is called at the end of every rasterization. The reason for the modifcation was to average the pixels according to the sample_rate as done by rgb_framebuffer_target in the following for loop. <br>
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
> > <br> (note the left and right tips of the red triangle !!) <br>
>
> ** Explain why these results are observed: <br>
> > <br> **Why, in general, are these results observed?**
> > <br> Well, the more samples (aka higher sample rate) the more "accurate" (aka better box aproximate) and of "higher quality" (aka smoother lines) the triangle becomes. 
> >
> > I also found this link provided by a staff member in [lecture 3, slide 20](https://cs184.eecs.berkeley.edu/sp24/lecture/3-20/antialiasing) really helpful in understanding blurry vs crisp edges (in relation to a digital painting brush but still super helpful in understanding how to create a crisper supersampling: [graphicdesign.com](https://graphicdesign.stackexchange.com/questions/150556/how-do-i-make-the-brush-tool-less-antialiased) )
> >
> 
> *** Extra credit: If you implemented alternative antialiasing methods, describe them and include comparison pictures demonstrating the difference between your method and grid-based supersampling. <br>
> no attempt.
>
>  ੈ ✩‧₊˚ TASK 3 ⋆·˚ ༘ *.⋆ <br>
> ***Create an updated version of svg/transforms/robot.svg with cubeman doing something more interesting, like waving or running. Feel free to change his colors or proportions to suit your creativity. Save your svg file as my_robot.svg in your docs/ directory and show a png screenshot of your rendered drawing in your write-up. Explain what you were trying to do with cubeman in words. <br>
> <img width="481" alt="dancing_man" src="https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/a0566494-cd1d-403d-a75b-59f5ebacd4fa"> <br>
> > ( dancing queen !! )
>
>  ੈ ✩‧₊˚ TASK 4 ⋆·˚ ༘ *.⋆ <br>
> *** Explain barycentric coordinates in your own words and use an image to aid you in your explanation. One idea is to use a svg file that plots a single triangle with one red, one green, and one blue vertex, which should produce a smoothly blended color triangle. <br>
> 
> <img width="1074" alt="Screen Shot 2024-02-28 at 6 24 39 PM" src="https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/fc381d88-a0b3-433a-942e-f000a5547826"> <br> 
> > [image from [lecture 5, slide 18](https://cs184.eecs.berkeley.edu/sp24/lecture/5-18/texture)] <br>
>
> [helpful read that helped me frame my write-up answer and code! [scratchapixel](https://www.scratchapixel.com/lessons/3d-basic-rendering/ray-tracing-rendering-a-triangle/barycentric-coordinates.html) ]
>
> 
> <br> barycentric coordinates assign three numbers (alpha, beta, gamma) to any point inside the triangle, representing its position relative to the triangle's color vertices (R, G, B) to obtain its color. 
> To expand, imagine you have a blank triangle. And also pretend there are three ants, one at each corner of the triangle (the points in each corner, V_c, V_b, V_a). These ants want to tell each other where they are. But instead of saying, "I'm two inches to the right and three inches up," they use a special way called barycentric coordinates.
> <br> Each ant gives a number between 0 and 1. This number shows how close they are to each corner. For example, if an ant is travelling along the edge of the triangle and stops exaclty at corner V_a, it would say "1" for that corner and "0" for the other corners so (1, 0, 0). If it's halfway between two V_a and V_B, it says "0.5" for those two corners and "0" for the other one so (0.5, 0.5, 0). 
> <br> With the basics covered, the ants want to make the triangle colorful! If each ant represents a pixel that "fills" the triangle and our same three ants find homes in each of the three corners: Red ant, Blue ant, and Green ant. The ants happily coexist, however, there are many more ants living in between the corners. These ants don't have a color yet but would love to join the colorful community and be purple, orange, or any other color they desire. Luckily, the ants can use barycentric coordinates to figure out their color! For example, if an ant is standing closer to the red corner, it will pick up more red paint and the barycentric coordinates describe the ants location in the triangle. If it's equally close to the red and blue corners, it will mix red and blue paint together, creating a purple color. If we imagine every pixel that makes up the triangle and each pixel is an ant, eventually there will be a complete gradient once all the ants of their color.
> 
> *** Show a png screenshot of svg/basic/test7.svg with default viewing parameters and sample rate 1. If you make any additional images with color gradients, include them. <br>
> <img width="799" alt="color_wheel" src="https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/6c16ef46-3811-41b8-bbda-61a2d812ab29">
> >
>
> <br> not an additional but a "happy accident"  <-- Shout out Bob Ross 
> <img width="798" alt="happy accident" src="https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/d5ac3297-a163-402f-9eee-7f436379b484">


>  ੈ ✩‧₊˚ TASK 5 ⋆·˚ ༘ *.⋆ <br>
> *** Explain pixel sampling in your own words and describe how you implemented it to perform texture mapping. Briefly discuss the two different pixel sampling methods, nearest and bilinear. <br>
> **What is pixel sampling?** pixel sampling involves selecting specific points/pixels from an img to gather info about color and other attributes. In other words, imagine a grid laid over an image where each intersection represents a pixel. Sampling involves looking at these individual points and recording the values that represent them. The process of pixel sampling strongly determines the quality of an img. Here there are two different pixel sampling methods, **nearest** and **bilinear**. <br>
> > **nearest**:
> > <br> Picture a leaf with various shades of green, and imagine this leaf has been cut up by ants who are now trying to peice it back together (why would they do that? ... ¯\_(ツ)_/¯ ). _In nearest pixel sampling, when you want to sample a specific point on the leaf, you'll look for the closest ant (representing a pixel) to that point_. Each ant is their own green shade so their neighboring ant should be too. As the ants attempt to piece the leaf back together, they work diligently to ensure that each piece they carry matches the shade of green of its neighboring pieces. Since each ant represents a pixel with its own green shade, they carefully select the neighboring ants that have similar shades of green to maintain consistency across the reconstructed leaf. This process is repeated until the leaf is all back in one piece! <br>
> 
> > **bilinear**:
> >  _In bilinear pixel sampling, when the ants want to sample a point on the leaf, they don't just look at the nearest ant; instead, they consider the four nearest ants surrounding the sampling point. These ants represent the corners of a square surrounding the point_. Each of the four ants surrounding the sampling point carries a specific shade of green but instead of choosing just one of these shades, the ants blend the shades together to approximate the color at the sampling point. When the ants sample a point on the leaf using bilinear sampling, they consider not only the color of the nearest ant but rather the colors of all its surrounding ants. By blending these colors appropriately, they arrive at a more nuanced and interpolated color value for the sampling point. <br>
>
> > **implentation**: <br>
> > I used my explainations above to guide me through my code, hence the sample_nearest(...) function performs nearest-neighbor interpolation, where it retrieves the texel value from the nearest integer coordinates to the provided UV coordinates. This method is quick but can produce blocky artifacts. Here, round() is used because I want to find the texel that is closest to the UV coordinates. 
> > In contrast, the sample_bilinear(...) function calculates the weighted average of the four nearest texels, considering both the horizontal and vertical distances from the provided UV coordinates. To find these surrounding texels, floor() and ceil() are used to determine the integer coordinates of the texels immediately below and above the UV coordinates along both axes. This is because bilinear interpolation requires the texels around the UV coordinates to form a rectangle. This method smooths transitions between texels, resulting in a more visually pleasing interpolation, but at a slightly higher computational cost.
> 
> 
> *** Check out the svg files in the svg/texmap/ directory. Use the pixel inspector to find a good example of where bilinear sampling clearly defeats nearest sampling. Show and compare four png screenshots using nearest sampling at 1 sample per pixel, nearest sampling at 16 samples per pixel, bilinear sampling at 1 sample per pixel, and bilinear sampling at 16 samples per pixel. <br>
> > bilinear 16 !! <br>
> ![bilinear16](https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/2948ad3e-2917-4067-a260-0ace3c38d772)
> > nearest 16  !! <br>
> ![near16](https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/f6d5f2a1-7d23-49ca-a266-64201f04ea3d)
> >
> > 
> > bilinear 1 !! <br>
> ![bilinear1](https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/3262b3ce-0c4e-43c2-99da-84c341efad02)
> > nearest 1 !! <br>
> ![near1](https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/ccb4d07c-3eac-4a66-8dcf-6af1fb57b111)
> 
> *** Comment on the relative differences. Discuss when there will be a large difference between the two methods and why. <br>
> In the IMGs above its clear that the bilinear sampling imgs are, overall, smoother and have less noise while the nearest sampling is pixally in comparison. In the future, I hope to refine my bilinear function to create an even smoother finish (more samples mwahahahaaa). <br>
> A large difference between nearest pixel sampling and bilinear sampling primarily depends on the nature of the image being sampled and the properties of the sampling methods. The most noticable differences will be present with sharp edges as nearest pixel sampling tends to produce a blocky or pixelated appearance, especially along sharp edges or high-contrast boundaries in the IMG. This is due to nearest sampling picking the "nearest" pixel without consieration for the other neighbors of this selected pixel. bilinear sampling, on the other hand, considers the colors of neighboring pixels and performs interpolation to generate a smoother transition between them. This interpolation helps to blend colors along edges and boundaries. The same goes for naunces in detail as well as how well maintained all of these things are when transforming an IMG. <br>
>
>  ੈ ✩‧₊˚ TASK 6 ⋆·˚ ༘ *.⋆ <br>
> *** Explain level sampling in your own words and describe how you implemented it for texture mapping. <br>
> > **level sampling**: <br>
> > In level sampling, the choice of which mipmap level to use depends on factors such as the viewing distance, the size of the texture on the screen, and the level of detail. When viewing an object up close or when the texture occupies a large portion of the screen, it is usually best to use a higher resolution mipmap level to capture fine details + prevent aliasing. Conversely, when viewing an object from a distance or when the texture appears small on the screen, using a lower resolution mipmap level would be better as this helps to conserve memory and improve rendering performance. <br>
> >
> > this article helped me conceptualize a bit: [nvidia](https://developer.nvidia.com/gpugems/gpugems2/part-iii-high-quality-rendering/chapter-28-mipmap-level-measurement) <br>
> 
> > **implentation**: <br>
> > The sample(...) function first determines the appropriate mip level of the texture based on the provided sampling parameters (sp). It calculates the mip level using the get_level(...) function and then compares the passed in sp with the returned value from the get_level() to use the correct sample (determined by the keys L and P). The get_level(...) function calculates the mip level based on the provided sampling parameters. It computes the difference vectors (x and y) and then these vectors are  scaled by the width and height of the texture to bring them into the texel space. Then a boundary check is done to make sure the found level is within the range of available, valid textures. <br>
> > Both functions have been implemented with the requests of the spec and comments in the assignent itself have been added for further clarity. 
> 
> *** You can now adjust your sampling technique by selecting pixel sampling, level sampling, or the number of samples per pixel. Describe the tradeoffs between speed, memory usage, and antialiasing power between the three various techniques. <br> 
> > **pixel sampling** [L_ZERO]
> > <br> pixel sampling tends to be faster compared to other techniques because it involves sampling only the nearest texel or pixel without any interpolation or filtering. this also allows it to require the least amount of computation or memeory usage. however, due to its simplicity, pixel sampling has no additional support for antialiasing which leads to more jagged edges and other undesirable effects.   
> > **level sampling** [L_NEAREST]
> > <br> besides what has been mentioned previously, level sampling helps to avoid unnecessary texture fetches by selecting an appropriate mipmap level based on the texture size and viewing context. this, in turn, consumes more memory than L_ZERO because it requires storing multiple pre-filtered versions of the texture at different levels of detail (mipmaps). It also enhances anti-aliasing by providing smoother transitions between texels. Here the draw popout takes a little longer than a split second to render the img as I increase the sample_rate. 
> > <br> **number of samples** [L_LINEAR]
> > <br> using the lerp of 2 reletated levels, floor, and ceiling, number of samples averages the color value recieved from both flooring and ceiling. However, this causes L_LINEAR to have the greatest computational strain but does provide the greatest amount of anti-aliasing. A peak into the computations of L_LINEAR is when calculating the level, the change of texture space is accounted for to determine a higher or lower level. The level L can be calculated by: L = max( sqrt( dx^2/du + dx^2/dv,   dy^2/du + dy^2/dv)). 
> 
> *** Using a png file you find yourself, show us four versions of the image, using the combinations of L_ZERO and P_NEAREST, L_ZERO and P_LINEAR, L_NEAREST and P_NEAREST, as well as L_NEAREST and P_LINEAR. <br>
> L-NEAR, P-NEAR: <br>
> ![L_NEAREST and P_NEAREST](https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/5f438dc6-eba4-4bcb-82ac-f0bf17b240e9)
> L-NEAR, P-LINE: <br>
> ![ L_NEAREST and P_LINEAR](https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/8d4e48a7-c012-4fe9-971e-a21a9c592ca3)
> L-ZERO P-NEAR: <br>
> ![L_ZERO and P_NEAREST](https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/13bedbf3-340e-42aa-8e10-b4f8158fe31c)
> L-ZERO P-LINE: <br>
> ![L_ZERO and P_LINEAR](https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/7572c52d-7c8a-4eeb-8b63-e1d39f174ebe)
>
> 
> > ** To use your own png, make a copy of one of the existing svg files in svg/texmap/ (or create your own modelled after one of the provided svg files). Then, near the top of the file, change the texture filename to point to your own png. From there, you can run ./draw and pass in that svg file to render it and then save a screenshot of your results. **Note**: Choose a png that showcases the different sampling effects well. You may also want to zoom in/out, use the pixel inspector, etc. to demonstrate the differences. <br>
> > <img width="351" alt="fishy" src="https://github.com/cal-cs184-student/hw1-rasterizer-sp24-squids/assets/90167734/dc5df5ad-2bda-4fa2-961f-d0022a4d8dce">
>
> *** Extra credit: If you implemented any extra filtering methods, describe them and show comparisons between your results with the other above methods. <br>
> no attempt. <br>
>




[Homework Specs](https://cs184.eecs.berkeley.edu/sp24/docs/hw1-spec)
