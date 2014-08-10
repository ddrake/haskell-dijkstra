Dijkstra's Algorithm (Version 2)
--------------------------------

This version structures the weighted graphs differently than [my first approach](https://github.com/ddrake/haskell-graphs), which simplifies the code considerably.  I think it also makes it easier to explore the solutions.  A weighted graph is defined here as a list of pairs, where the first element in each pair is a Node (just a type alias for string) and the second member is a list of the Edges incident on the Node.  Since each Edge in this list is already associated with the node, it only needs to record the **opposite** node and the weight.

In this version, a weighted graph is just a weighted graph -- it doesn't carry any information about the start node or the associated distances and predecessors for the nodes.  That information is tacked on to a node via the 'Dnode' structure (a type alias that associates a distance/predecessor pair with a node).

Directed and Non-Directed Graphs

This version can handle both directed and non-directed graphs simply by specifying an "IsDirected" flag when reading a graph from a file.  If "IsDirected" is True, each line of the input file maps to one edge in the graph.  If it's false, then for each line in the file, the created graph will contain two edges, one in the forward direction and one in the reverse direction.  This helps keep the input data and code DRY.

Sample Usage
------------

~~~
dow@dow-laptop ~/haskell/dijkstra $ ghci

Prelude> :l main.hs 
[1 of 2] Compiling Dijkstra         ( Dijkstra.hs, interpreted )
[2 of 2] Compiling Main             ( main.hs, interpreted )
Ok, modules loaded: Dijkstra, Main.

*Main> :browse Dijkstra 
data Edge = Edge {node :: Node, weight :: Float}
type Node = String
type Graph = [(Node, [Edge])]
type Dnode = (Node, (Float, Node))
fromText :: String -> Graph
edgesFor :: Graph -> Node -> [Edge]
dijkstra :: Graph -> Node -> [Dnode]
pathToNode :: [Dnode] -> Node -> [Node]
~~~

The second argument of `fromText` specifies whether the text contents represents the edges of a **directed** graph.

~~~
*Main> txt <- readFile "graph2.txt"
*Main> let g = fromText txt False
~~~

To see the edges incident on a specific node, use `edgesFor`

~~~
*Main> edgesFor g "c"
[Edge {node = "a", weight = 19.0},Edge {node = "d", weight = 30.0},Edge {node = "f", weight = 22.0},Edge {node = "h", weight = 15.0},Edge {node = "j", weight = 2.0},Edge {node = "m", weight = 7.0}]
~~~

To get the solution, just pass your graph and a start node to `dijkstra`

~~~
*Main> let soln = dijkstra g "a"
*Main> soln
[("a",(0.0,"a")),("b",(12.0,"a")),("c",(13.0,"m")),("k",(3.0,"a")),("m",(6.0,"a")),("d",(7.0,"k")),("i",(13.0,"e")),("l",(19.0,"d")),("j",(15.0,"c")),("f",(9.0,"m")),("h",(28.0,"c")),("e",(10.0,"f")),("g",(11.0,"d"))]
~~~

To back trace a path to a specific destination node, use `pathToNode`

~~~
*Main> pathToNode soln "h"
["a","m","c","h"]
~~~
