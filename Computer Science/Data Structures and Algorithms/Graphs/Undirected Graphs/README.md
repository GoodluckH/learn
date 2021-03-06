# Undirected Graphs
**Table of Content**
- [Undirected Graphs](#undirected-graphs)
  * [Undirected Graph Implementation using Adjacency Lists](#undirected-graph-implementation-using-adjacency-lists)
- [Depth-First Search](#depth-first-search)
  * [Connected Components](#connected-components)
  * [Cycles and Bipartite Detection](#cycles-and-bipartite-detection)
- [Breadth-First Search](#breadth-first-search)

Undirected graphs are simply made of vertices and edges with no extra information. We can only know if any two vertices are connected or not. We want to represent an undirected graph using data structures that are space and time efficient. There are a couple of candidates:

***An adjacency matrix***: we construct a V by V 2D-array with boolean values to represent whether two vertices are adjacent. However, the space requirement is V^2, which is very inefficient for large-scale applications like Facebook. More, we can only see if two vertices are connected but cannot see if there are parallel edges.

***An array of edges***: we can create an Edge class with two `int` instance variables denoting the vertices at either end of the edge, and store these Edge classes in an array (note that an array is fixed-sized, you initiate it by saying int[n]; a list, on the other hand, is a generic collection with no size limits because it automatically resizes). If we want to find something, though, we need to iterate through everything, which will be slow.

***An array of adjacency list***: we have an array of lists that store the vertices connected to a specific vertice. The array indices correspond to the vertice number, so we can quickly find a vertice and check its adjacent vertices. We can use `Bag` ADTs to represent the adjacency list. Note that a `Bag` is implemented using linked list, so that means it takes constant time to add an edge. And the space requirement would be proportional to `V + E`, to get adjacent vertices, it simply takes `degree(v)` time. 

***A symbol table of adjacency list***: using a symbol table allows us to add and delete a vertex, and frees us from the restriction of representing vertices using integers only.

We can also use `Set` to implement our adjacency list to disallow parallel edges. However, it will comes at logarithmic costs for adding edges. 

**Performance Summary**

| Data Structure | Space |Add Edge v-w |Check if v-w | adj(v) |
| :-----------: | :-----------: |:-----------: |:-----------: |:-----------: |
| *List of edges* | E | 1 | E | E |
| *Adjacency matrix* | V^2 | 1 | 1 | V |
| *Adjacency lists* | V + E | 1 | degree(v) | degree(v)|
| *Adjacency sets* | V + E | log(V) | log(V) | log(V) + degree(v)|

## Undirected Graph Implementation using Adjacency Lists

*requires algs4.jar for Bag and In*.

```java
public class Graph {
    private final int V;         // number of vertices
    private int E;               // number of edges
    private Bag<Integer>[] adj;  // adjacency lists  

    public Graph(int V) {
        this.V = V; this.E = 0;
        adj = (Bag<Integer>[]) new Bag[V];  // build an array of bag objects
        for (int v = 0; v < V; v++)         // for every slot
            adj[v] = new Bag<Integer>();    // build an empty bag
        }
    
    public Graph(In in) {
        this.(in.readInt());             // get V and constract the graph
        int E = in.readInt();            // get E
        for (int i = 0; i < E; i++) {
            int v = in.readInt();
            int w = int.readInt();
            addEdge(v, w);
            }
        }

    public int V() {return V;}
    public int E() {return E;}

    public void addEdge(int v, int w) {
        adj[v].add(w);
        adj[w].add(v);
        E++;
        }

    public Iterable<Integer> adj(int v) 
    { return adj[v];}
    }
```

# Depth-First Search

Sometimes we want to process a graph by traversing it. DFS is a very simple but powerful method of doing so. Building upon DFS, we can also address path validating and finding algorithms.

To begin, we can define a DFS class:

```java
public class DFS {
    private boolean[] marked;         // we need to mark vertices so we don't recursively call them more than once
    private int count;

    public DFS(Graph G, int s) {
        marked = new boolean[G.V()];
        dfs(G, s)
        }
    
    private void dfs(Graph G, int v) {
        marked[v] = true;
        count++;
        for (int x: G.adj(v)) {
            if (!marked[x]) dfs(G, x);
            }
        }
    
    public int count() { return count; }
   
    public boolean marked(int v) { return marked[v]; }
    }
```

Simple, but we can do more with the API. How about finding a path or simply find out if there is a path between v and w? Now, we can implement a Path class based on DFS. We need to maintain an array of int values to keep track of paths (similar to what we did for the [Union Find](https://github.com/GoodluckH/learn/tree/main/Computer%20Science/Union%20Find) data structure).

```java
public class DFSPath {
    private boolean[] marked;
    private int[] edgeTo;
    private final int s;     // source vertex

    public DFSPath(Graph G, int s) {
        this.s = s;
        marked = new boolean[G.V()];
        edgeTo = new int[G.V()];
        dfsPath(G, s);
        }

    private void dfsPath(Graph G, int v) {
        marked[v] = true;
        for (int x: G.adj(v) {
            if (!marked[x]) {
                edgeTo[x] = v;
                dfsPath(G, x);
                }
            }
        }

    public boolean hasPathTo(int v) { return marked[v]; }

    public Iterable<Integer> pathTo(int v) {
        if (!hasPathTo(v) return null;
        Stack<Integer> path = new Stack<Integer>();
        for (int x = v; x != s; x = edgeTo[x]) 
            path.push(x);
        path.push(s);
        return path;
        }
    }
```

The DFS search algorithm has a runtime proportional to the sum of degrees of all vertices visited. And the path finding algorithm has a runtime proportional to the path's length. Note that DFS does not address the question *what is the shortest path from v to w?*, because the order of how we find a path takes no consideration of the length (number of edges).

## Connected Components

We can extend our DFS to implement a Connected Components class where we can find out about all components in a graph as well as the vertices within each component. To do this, we also need an array `id[]` to group vertices into their respective components.

```java
public class CC {
    private boolean[] marked;
    private int[] id;
    private int count;

    public CC(Graph G) {      // preprocess the graph
        marked = new boolean[G.V()];
        id = new int[G.V()];
        for (int v = 0; v < G.V(); v++) {
            if (!marked[v]){
                dfs(G, v);
                count++;
                }
            }
        }

    private void dfs(Graph G, int v) {
        marked[v] = true;
        id[v] = count;         // group v to a component defined by count 
        for (int x: G.adj(v)) {
            if (!marked[x]) dfs(G, x);
            }
        }

    public boolean connected(int v, int w) { return id[v] == id[w]; }

    public int count() { return count; }
    
    public int id(int v) { return id[v]; }     // component id
    }
```

The runtime of CC should be proportional to V + E because we are essentially using the DFS algorithm. But since we are visiting all vertices, the runtime is putatively 2E. To query `connected`, it's constant time just like the quick find data structure. However, although Union Find takes logarithmic runtime for connectivity queries, it's faster in practice than CC because Union Find does not need to preprocessing a graph, and it also supports edge-adding operations while performing connectivity queries.

## Cycles and Bipartite Detection 

Using DFS we can also detect whether a graph contains cycles. So if a graph contains a cycle, then we can say that we will visit a marked vertext that is not the starting vertex itself. To do this, we need to pass in another argument in `dfs()` to keep track of the previous vertex. The implementation of such detection algo is as follow:

```java
public class Cycle {
    private boolean[] marked;
    private boolean hasCycle;

    public Cycle(Graph G) {
        marked = new boolean[G.V()];
        for (int v = 0; v < G.V(); v++) {
            if (!marked[v]) dfs(G, v, v);
            }
        }
    
    private void dfs(Graph G, int v, int u) {
        marked[v] = true;
        for (int x: G.adj(v)) {
            if (!marked[x]) dfs(G, x, v);
            else if (x != u) hasCycle = true;
            }
        }
    
    public boolean hasCycle() { return hasCycle; }

    }
```

A bipartite graph entails coloring vertices with two colors in such way that no two adjacent vertices are of the same color, otherwise the graph is not bipartite. 

```java
public class Bipartite {
    private boolean[] marked;
    private boolean[] color;            // to mark each vertex with a 'color' of boolean value
    private boolean isBipartite = true;

    public Bipartite(Graph G) {
        marked = new boolean[G.V()];
        color = new boolean[G.V()];
        for (int s = 0; s < G.V(); s++) {
            if (!marked[s]) dfs(G, s);
            }
        }

    private dfs(Graph G, int v) {
        marked[v] = true;
        for (int w: G.adj(v)) {
            if (!marked[w]) {
                color[w] = !color[v];
                dfs(G, w);
            else if (color[w] == color[v]) isBipartite = false;
                }
            }
        }

    public boolean isBipartite() { return isBipartite; }
    }
```



# Breadth-First Search

For DFS, we are essentially using a stack (provided by the call stack during recursion) to store and determine which vertex to visit next. Stack is a LIFO mechanism, so DFS will alwasy want to visit the next vertex added in to make deeper searches. Conversely, we want to implement BFS with a queue and performing the algo iteratively. A queue follows the FIFO mechanism so we visit the vertices that are  *k + 1* away from a vertex w, only after we have visited all of w's adjancent vertices.

The breadth-first principle makes sure that we only store the shortest path to all vertices. That's because we are marking all immediate adjacent vertices every time before exploring further, so the path to each vertices are only 1 edge away each time they are visited and marked. So the longer path to the same vertex will be ignored because we've already marked that vertex.

To implement BFS is feat, we just need to make some modification to our DFS code by utilizing a queue in a `while` loop.

```java
public class BFS {
    private boolean[] marked;
    private int[] edgeTo;
    private final int s;

    public BFS(Graph G, int s) {
        marked = new boolean[G.V()];
        edgeTo = new int[G.V()];
        this.s = s;
        bfs(G, s);
        }

    private void bfs(Graph G, int s) {
        Queue<Integer> queue = new Queue<Integer>();
        marked[s] = true;
        queue.enqueue(s);

        while (!queue.isEmpty()) {
            int v = queue.dequeue();
            for (int x: G.adj(v)) {
                if (!marked[x]) {
                    marked[x] = true;
                    edgeTo[x] = v;
                    queue.enqueue(x);
                    }
                }
            }
        }

    public boolean hasPathTo(int v) { return marked[v]; }

    public Iterable<Integer> pathTo(int v) {
        if (!hasPathTo(v)) return null;
        Stack<Integer> path = new Stack<Integer>();
        for (int x = v; x != s; x = edgeTo[x])
            path.push(x);
        path.push(s);
        return path;
        }
    }
```

Similar to DFS, BFS has a runtime of V + E in worst case because the algorithm involves checking all vertices and their adjacent vertices. If the graph is connected, then the runtime will just be 2E.

