## What I Understood About the Problem

So basically, I had to work with a rover that gets GPS data from a u-blox receiver and needs to navigate from one point to another. This whole thing breaks down into three main parts:

First, I needed to decode the GPS data that comes in UBX format - which is just a specific way u-blox devices package their GPS information. The data gives me coordinates for where the rover starts and where it needs to go.

Then I had to figure out how to get from point A to point B, there's a grid map with obstacles that the rover can't drive through. So I needed a path planning algorithm that could find a route around these obstacles.

Finally, once I have the path, I need to convert it into actual commands the rover can understand - basically how long to drive and what angles to turn through based on the rover's wheel specifications.

## My Thought Process

When I first looked at this, I realized it was really three separate problems that connect together:

**For path planning**, I knew I needed something that could handle obstacles, so I searched online and implemented A*. It's perfect for grid-based pathfinding because it finds the shortest path while avoiding blocked cells. I set it up to allow 8-directional movement (including diagonals) since that gives more natural paths.

**For odometry**, this was tricky because I had to figure out what "total angle traversed" actually meant. At first I thought it was just the sum of turning angles when the rover changes direction, but after some debugging, I realized it wanted the sum of all the absolute heading angles along the entire path.

## Implementation Details

**GPS Decoding**: The main bug was in the NAV_POSLLH function where I was reading longitude and latitude from the same memory location instead of using proper byte offsets. The UBX NAV-POSLLH payload has a specific structure with iTOW at offset 0, longitude at offset 4, latitude at offset 8, etc. Once I fixed those offsets, the coordinate conversion worked perfectly.

**Path Planning**: I implemented A* with a priority queue based on the f-cost (g + h). The algorithm explores neighbors in 8 directions, calculates costs for movement (1.0 for straight, 1.414 for diagonal), and uses Euclidean distance as the heuristic. The path reconstruction works backwards from goal to start using parent pointers, then reverses the result.

**Odometry**: This took some trial and error. Initially I was calculating direction changes between path segments, but the expected output showed that wasn't right. After debugging with different methods, I found that "total angle traversed" means summing up all the absolute values of the heading angles for each path segment. So if the rover travels northeast (45°) then east (0°) then northeast (45°) again, the total is 45+0+45 = 90°, not just the turning angles.

**Testing**: I used the provided test cases with hex GPS data.

The whole thing came together when I realized each part builds on the previous one.
