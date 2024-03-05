---
title: "Maze Solving Algorithms"
author_profile: true
key: 25
toc: true
excerpt: "Path Search Algorithms, Pygame"
header:
  teaser: /assets/images/Breadth_first.gif
classes: wide
---

## Project Overview
This project was presented during the NU MSR hackathon to challenge students' coding ability and implement algorithms that can solve mazes. Students were given about 6 hours to complete this challenge.

## Project goal
The goals achieved include:
- Create a maze generator.
  - Generates a unique maze every run.
  - Presents the maze in a Pygame window rather than the terminal.
- Solve a maze using a depth-first search algorithm.
- Solve a maze using a breath-first search algorithm.

## Maze Generation
All mazes are generated as an *m* x *n* grid that is specified when the code is started. The mazes each generate a starting point and an end point which are marked as red and green for visibility. The generation of the maze is created using a [Randomized Prim's Algorithm](http://weblog.jamisbuck.org/2011/1/10/maze-generation-prim-s-algorithm) which followings the pseudocode given below.

- All cells are walls.
- Randomly choose a cell Q and mark it as free.
- Add cell Q's neighbors to the wall list.
- While the wall list is not empty:
    - Randomly choose a wall W from the wall list.
    - If wall W is adjacent to exactly one free cell.
       - Let F be the free cell that wall W is adjacent to.
       - W is to a direction DIR (either North, South, East, or West) of F.
       - Let A be the cell to the direction DIR of W.
       - Make W free.
       - Make A free.
       - Add the walls of A to the wall list.
    - Remove W from the wall list.

## Depth-First Search
The depth-first algorithm, also known as recursive backtracking, prioritizes searching down a path until it is completely blocked. Since the function recurses upon itself, when it returns, it will either succeed or search from the last deepest point in the maze. The simplified pseudo code is given below.

- Call the search function from the start point.
  - Return false if the given cell is out of bounds or a wall.
  - Return true if the given cell is the goal.
  - Mark the given cell as visited.
  - Call the search function on the North neighbor.
  - Call the search function on the South neighbor.
  - Call the search function on the East neighbor.
  - Call the search function on the West neighbor.

### This video shows the solver in action
<iframe width="560" height="315" src="https://www.youtube.com/embed/59795WfWeAc?si=g16I8HvcB6THJg3-" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Breadth-First Search
The breadth-first algorithm, unlike the depth-first search, prioritizes checking "fringe" or the neighbors of a cell over the depth of a single viable path. This means that if a cell has two neighbor cells that are open (unexplored and not a wall), each neighbor will be evaluated rather than searching down one path until blocked. This is because the algorithm tracks the hops from the starting point prioritizes searching the fringe with the least hops. 

The pseudocode is as follows:
- Call search function from the start point.
  - Initialize a node and fringe from that node.
  - While the fringe is populated.
    - Set the node as the first item of the fringe.
    - Return true if the node is the solution.
    - Append the node's neighbors to the fringe if not the solution.
    - Mark the node as visited and remove the first item from the fringe list.

### This video shows the solver in action
<iframe width="560" height="315" src="https://www.youtube.com/embed/UAQkEDxIy_c?si=3BGAfT7a5WxGZCMi" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## References
- [Randomized Prim's Algorithm](http://weblog.jamisbuck.org/2011/1/10/maze-generation-prim-s-algorithm)
- [Depth First Search](https://web.archive.org/web/20200125173604/https://www.cs.bu.edu/teaching/alg/maze/)
- [Breadth First Search](https://www.personal.kent.edu/~rmuhamma/Algorithms/MyAlgorithms/GraphAlgor/breadthSearch.htm)
- [Wavefront Search](https://www.cs.tufts.edu/comp/150IR/labs/wavefront.html)

## Group Members
Nader Ahmed, Graham Clifford, Srikanth Schelbert, Kyle Wang

## Source Code
As this is part of the Hackathon Challenges, the code cannot be released at this time.