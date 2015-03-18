# **TSP ART** #
_Andrew Frye and Samyukta Jadhwani_

# Introduction #

GOAL: To take any input image and generate a TSP art image based on it. If the image is coloured, it
is reduced to a grayscale image. The image is then stippled (ie. dots are generated) and each dot is
considered as a city and a heuristic for TSP is used to connect them using a continuous path with no
crossings.

APPROACH: TSP Art has 2 subproblems- Stippling and TSP Connections without crossings


# Details #

**STIPPLING-** Stippling creates the dots or cities that are the basis for the TSP algorithm. After the image is converted to grayscale, we choose pixels that will remain black and must eliminate pixels(or convert to white) so that each dot is distinct and discrete and lines do no trivially emerge.

We tried adding noise to the image , halftoning, quantizing and dithering the image because those effects typically produce an effect of dots. We convolved the image with various masks of our creation in an attempt to produce dots depending on the grayscale intensity at a given pixel.

The first useful result was determining a threshold grayscale value and laying down dots alternately (such that no two were adjacent) in any region with that value. However this resulted in nearest neighbour
drawing parallel lines which weren't very artistic.

The final implementation uses an approach where a 5x5 region is considered and the average grayscale value is computed. If the value is closer to 0 (black) then a denser set of black pixels (5 to be precise) are set in that 5x5 region. If the value is closer to 255 (white), and more likely to be the background, only 1 pixel is set in the centre of the 5x5 region. Different patterns of dots >1 but <5 are used for average grayscales between the two extremes.

**TSP CONNECTIONS-** We connected it using the Nearest neighbour algorithm which uses the Greedy approach.
That resulted in lot of crossings however. Running other complicated heuristics involved large computation times and memory usage. We tried running an algorithm that uses brute force to ensure that no 2 nodes are connected with crossings by maintaining all nodes and edges of the graph in memory and checking for intersections if the x-y coordinates overlapped for any two of all of the nodes.

The code ran for over a day and night without generating output. Minimizing the total path length also didn't work because it was not unique for the graph.

So we focussed on eliminating crossings instead.

We used an approach where if connecting node A to B and node C
to D generated a crossing- then either connecting A to D and C to B
or separately A to C and B to D will eliminate that crossing without
generating another crossing between those 4 nodes however this tends to segregate the graph into discrete clumps. The only way to ensure that the clumping does not occur is to invert the direction of the second half of the loop/clump where the intersection occurs as it is maintained as a directed graph in memory.

We created our own hybrid algorithm which starts with solved subset (that is 2 connected nodes), and adds 1 dot from unsolved set at a time.

**CODE:** The files all interact with each other. To begin, you need to run gui. m in MATLAB. You can specify the image to be loaded and use the GUI to lay dots and use either nearest neighbour which eliminates
dots or our algorithm which grows the graph from 2 initial dots. As an extra feature- you can do this in multicolour mode.