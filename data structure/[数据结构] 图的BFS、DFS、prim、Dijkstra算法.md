#1.图的定义

 　　图G是由顶点的有穷集合，以及顶点之间的关系组成，顶点的集合记为V，顶点之间的关系构成边的集合E，G=(V,E).

　　如果给图的每条边规定一个方向，那么得到的图称为**有向图**，其边也称为有向边。在有向图中，与一个节点相关联的边有出边和入边之分，而与一个有向边关联的两个点也有始点和终点之分。相反，边没有方向的图称为**无向图**。

#２.图的存储

　　一般图的存储有两种方式
　　
　　1)邻接表：需要保存一个顺序存储的顶点表和每个顶点上的边的链接表。
　　
　　2)相邻矩阵：用一个矩阵来保持边的情况
　　　　

![这里写图片描述](http://img.blog.csdn.net/20160613103617205)　

下图展示了两种表示上面这个图的方法：

![这里写图片描述](http://img.blog.csdn.net/20160613103702050)　

#３.图的遍历

　　①　DFS（Depth First Search）深度优先搜索，为每个顶点设立一个“访问标志”。首先将图中每个顶点的访问标志设为 FALSE,  之后搜索图中每个顶点，如果未被访问，则以该顶点为起始点，进行遍历。

　　若当前访问的顶点的邻接顶点有未被访问的，则任选一个访问之。反之，退回到最近访问过的顶点；直到与起始顶点相通的全部顶点都访问完毕；

> 遍历图的过程实质上是对每个顶点查找其邻接点的过程，所耗费的时间取决于所采用的存储结构。
对图中的每个顶点至多调用1次DFS算法，因为一旦某个顶点已访问过，则不再从它出发进行搜索。

> 邻接链表表示：查找每个顶点的邻接点所需时间为O(e)，e为边(弧)数，算法时间复杂度为O(n+e)

> 数组表示：查找每个顶点的邻接点所需时间为O(n2)，n为顶点数，算法时间复杂度为O(n2)


　　②　BFS（Breadth First Search）广度优先遍历，从图的某一结点出发，首先依次访问该结点的所有邻接顶点 Vi1, Vi2, …, Vin 再按这些顶点被访问的先后次序依次访问与它们相邻接的所有未被访问的顶点，重复此过程，直至所有顶点均被访问为止。

##３.１遍历代码（邻接矩阵）

　　 

```
// 邻接矩阵存储图
public class Graph {
    // 顶点数
    private int number = 9;
    // 记录顶点是否被访问
    private boolean[] flag;
    // 顶点
    private String[] vertexs = { "A", "B", "C", "D", "E", "F", "G", "H", "I" };
    // 边
    private int[][] edges = { 
            { 0, 1, 0, 0, 0, 1, 1, 0, 0 }, 
            { 1, 0, 1, 0, 0, 0, 1, 0, 1 }, 
            { 0, 1, 0, 1, 0, 0, 0, 0, 1 },
            { 0, 0, 1, 0, 1, 0, 1, 1, 1 },
            { 0, 0, 0, 1, 0, 1, 0, 1, 0 }, 
            { 1, 0, 0, 0, 1, 0, 1, 0, 0 },
            { 0, 1, 0, 1, 0, 1, 0, 1, 0 },
            { 0, 0, 0, 1, 1, 0, 1, 0, 0 }, 
            { 0, 1, 1, 1, 0, 0, 0, 0, 0 } 
            };

    // 图的深度遍历操作(递归)
    void DFSTraverse() {
        flag = new boolean[number];
        for (int i = 0; i < number; i++) {
            if (flag[i] == false) {// 当前顶点没有被访问
                DFS(i);
            }
        }
    }

    // 图的深度优先递归算法
    void DFS(int i) {
        flag[i] = true;// 第i个顶点被访问
        System.out.print(vertexs[i] + " ");
        for (int j = 0; j < number; j++) {
            if (flag[j] == false && edges[i][j] == 1) {
                DFS(j);
            }
        }
    }

    // 图的广度遍历操作
    void BFSTraverse() {
        flag = new boolean[number];
        Queue<Integer> queue = new LinkedList<Integer>();
        for (int i = 0; i < number; i++) {
            if (flag[i] == false) {
                flag[i] = true;
                System.out.print(vertexs[i] + " ");
                queue.add(i);
                while (!queue.isEmpty()) {
                    int j = queue.poll();
                    for (int k = 0; k < number; k++) {
                        if (edges[j][k] == 1 && flag[k] == false) {
                            flag[k] = true;
                            System.out.print(vertexs[k] + " ");
                            queue.add(k);
                        }
                    }
                }
            }
        }
    }

    public static void main(String[] args) {
        Graph graph = new Graph();
        System.out.println("-----------DFS-----------------");
        graph.DFSTraverse();
        System.out.println();
        System.out.println("-----------BFS-----------------");
        graph.BFSTraverse();
    }
}
```

运行结果：

![这里写图片描述](http://img.blog.csdn.net/20160613112121895)　

#4.最小生成树prim算法

　　从单一顶点开始，普里姆算法按照以下步骤逐步扩大树中所含顶点的数目，直到遍及连通图的所有顶点。

1. 输入：一个**加权连通图**，其中顶点集合为V，边集合为E；

2. 初始化：Vnew = {x}，其中x为集合V中的任一节点（起始点），Enew = {}；

3. 重复下列操作，直到Vnew = V：

  3.1 在集合E中选取权值最小的边（u, v），其中u为集合Vnew中的元素，而v则是V中没有加入Vnew的顶点（如果存在有多条满足前述条件即具有相同权值的边，则可任意选取其中之一）；
3.2 **将v加入集合Vnew**中，将（u, v）加入集合Enew中；

4. 输出：使用集合Vnew和Enew来描述所得到的最小生成树。

> 此为原始的加权连通图。每条边一侧的数字代表其权值。

![这里写图片描述](https://upload.wikimedia.org/wikipedia/commons/thumb/a/a8/Prim_Algorithm_0.svg/200px-Prim_Algorithm_0.svg.png)

> 顶点D被任意选为起始点。顶点A、B、E和F通过单条边与D相连。A是距离D最近的顶点，因此将A及对应边AD以高亮表示。

![这里写图片描述](https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Prim_Algorithm_1.svg/200px-Prim_Algorithm_1.svg.png)

> 下一个顶点为距离D或A最近的顶点。B距D为9，距A为7，E为15，F为6。因此，F距D或A最近，因此将顶点F与相应边DF以高亮表示。

![这里写图片描述](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f5/Prim_Algorithm_2.svg/200px-Prim_Algorithm_2.svg.png)

> 算法继续重复上面的步骤。距离A为7的顶点B被高亮表示。

![这里写图片描述](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b4/Prim_Algorithm_3.svg/200px-Prim_Algorithm_3.svg.png)

> 在当前情况下，可以在C、E与G间进行选择。C距B为8，E距B为7，G距F为11。E最近，因此将顶点E与相应边BE高亮表示。

![这里写图片描述](https://upload.wikimedia.org/wikipedia/commons/thumb/7/7e/Prim_Algorithm_4.svg/200px-Prim_Algorithm_4.svg.png)

> 这里，可供选择的顶点只有C和G。C距E为5，G距E为9，故选取C，并与边EC一同高亮表示。

![这里写图片描述](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Prim_Algorithm_5.svg/200px-Prim_Algorithm_5.svg.png)
 
 

> 顶点G是唯一剩下的顶点，它距F为11，距E为9，E最近，故高亮表示G及相应边EG。

![这里写图片描述](https://upload.wikimedia.org/wikipedia/commons/thumb/9/99/Prim_Algorithm_6.svg/200px-Prim_Algorithm_6.svg.png) 

> 现在，所有顶点均已被选取，图中绿色部分即为连通图的最小生成树。在此例中，最小生成树的权值之和为39。

![这里写图片描述](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b9/Prim_Algorithm_7.svg/200px-Prim_Algorithm_7.svg.png)

##4.1 java代码实现

　　

```
public class Prim {
	//结点集
	public static List<Vertex> vertexList = new ArrayList<Vertex>();
	//边集
	public static List<Edge> EdgeList = new ArrayList<Edge>();
	//已经访问过的结点集
	public static List<Vertex> containVertexList = new ArrayList<Vertex>();
	
	public static void main(String[] args) {
		primTree();
	}
	public static void primTree(){
		//初始化图
		buildGraph();
		//起始点
		Vertex start = vertexList.get(0);
		containVertexList.add(start);
		for(int n=0;n<vertexList.size()-1;n++){
			//临时节点a
			Vertex temp = new Vertex(start.key);
			Edge tempedge = new Edge(start,start,1000);
			
			for(Vertex v : containVertexList){
				for(Edge e : EdgeList){
					//找出相邻最小边
					if(e.start==v && !containVertex(e.end)){
						if(e.Len<tempedge.Len){
							temp = e.end;
							tempedge = e;
						}
					}
				}
			}
			//把该点加入
			containVertexList.add(temp);
		}
		
		//打印输出
		Iterator it = containVertexList.iterator();
		while(it.hasNext()){
			Vertex v =(Vertex) it.next();
			System.out.println(v.key);
		}
	}
	
	public static void buildGraph() {
		Vertex v1 = new Vertex("a");
		Prim.vertexList.add(v1);
		Vertex v2 = new Vertex("b");
		Prim.vertexList.add(v2);
		Vertex v3 = new Vertex("c");
		Prim.vertexList.add(v3);
		Vertex v4 = new Vertex("d");
		Prim.vertexList.add(v4);
		Vertex v5 = new Vertex("e");
		Prim.vertexList.add(v5);
		addEdge(v1, v2, 6);
		addEdge(v1, v3, 7);
		addEdge(v2, v3, 8);
		addEdge(v2, v5, 4);
		addEdge(v2, v4, 5);
		addEdge(v3, v4, 3);
		addEdge(v3, v5, 9);
		addEdge(v5, v4, 7);
		addEdge(v5, v1, 2);
		addEdge(v4, v2, 2);
	}
	public static void addEdge(Vertex a, Vertex b, int w) {
		Edge e = new Edge(a, b, w);
		Prim.EdgeList.add(e);
	}
	public static boolean containVertex(Vertex vte){
		for(Vertex v : containVertexList){
			if(v.key.equals(vte.key))
				return true;
		}
		return false;
	}
}
class Vertex {
	String key;
	Vertex(String key){
		this.key = key;
	}
}
class Edge{
	Vertex start;
	Vertex end;
	int Len;
	Edge(Vertex start,Vertex end,int key){
		this.start = start;
		this.end  = end;
		this.Len = key;
		
	}
}
```

#５.Dijkstra算法

　　使用了**广度优先搜索**解决非负权有向图的**单源最短路径**问题，算法最终得到一个最短路径树（一个节点到其他所有节点的最短路径）。该算法常用于路由算法或者作为其他图算法的一个子模块。主要特点是以起始点为中心向外层层扩展，直到扩展到终点为止。

算法思想：

> 　　设G=(V,E)是一个带权有向图，把图中顶点集合V分成两组，第一组为已求出最短路径的顶点集合（用S表示，初始时S中只有一个源点，以后每求得一条最短路径 , 就将加入到集合S中，直到全部顶点都加入到S中，算法就结束了）
> 
> 　　第二组为其余未确定最短路径的顶点集合（用U表示），按最短路径长度的递增次序依次把第二组的顶点加入S中。在加入的过程中，总保持从源点v到S中各顶点的最短路径长度不大于从源点v到U中任何顶点的最短路径长度。此外，每个顶点对应一个距离，S中的顶点的距离就是从v到此顶点的最短路径长度，U中的顶点的距离，是从v到此顶点只包括S中的顶点为中间顶点的当前最短路径长度。


算法步骤：

a.　初始时，S只包含源点，即S＝{v}，v的距离为0。U包含除v外的其他顶点，即:U={其余顶点}，若v与U中顶点u有边，则（u,v）正常有权值，若u不是v的出边邻接点，则（u,v）权值为∞。

b.　从U中选取一个距离v最小的顶点k，把k，加入S中（该选定的距离就是v到k的最短路径长度）。

c.　以k为新考虑的中间点，修改U中各顶点的距离；若从源点v到顶点u的距离（经过顶点k）比原来距离（不经过顶点k）短，则修改顶点u的距离值，修改后的距离值的顶点k的距离加上边上的权。

d.　重复步骤b和c直到所有顶点都包含在S中。

　　![这里写图片描述](http://img.blog.csdn.net/20160613154953498)　

　　Dijkstra算法演示


##５.１Dijkstra算法（Java实现）

  

```
public class Dijkstra {
	 private static int M = 10000; //此路不通
	 public static void main(String[] args) {
	//邻接矩阵 
	    int[][] weight = { 
	        {0,10,M,30,100}, 
	        {M,0,50,M,M}, 
	        {M,M,0,M,10}, 
	        {M,M,20,0,60}, 
	        {M,M,M,M,0} 
	    };
	     
	    int start=0; 
	    int[] shortPath = dijkstra(weight,start); 
	      
	    for(int i = 0;i < shortPath.length;i++) 
	       System.out.println("从"+start+"出发到"+i+"的最短距离为："+shortPath[i]); 
	 }
	  
	 public static int[] dijkstra(int[][] weight, int start) {
	 //接受一个有向图的权重矩阵，和一个起点编号start（从0编号，顶点存在数组中） 
	 //返回一个int[] 数组，表示从start到它的最短路径长度 
	 int n = weight.length;      //顶点个数
	 int[] shortPath = new int[n];  //保存start到其他各点的最短路径
	 String[] path = new String[n];  //保存start到其他各点最短路径的字符串表示
	 for(int i=0;i<n;i++) 
	       path[i]=new String(start+"-->"+i); 
	 int[] visited = new int[n];   //标记当前该顶点的最短路径是否已经求出,1表示已求出 
	  
	 //初始化，第一个顶点已经求出
	 shortPath[start] = 0;
	 visited[start] = 1;
	  
	 for(int count = 1; count < n; count++) {   //要加入n-1个顶点
	  int k = -1;        //选出一个距离初始顶点start最近的未标记顶点 
	  int dmin = Integer.MAX_VALUE;
	  for(int i = 0; i < n; i++) {
	  if(visited[i] == 0 && weight[start][i] < dmin) {
	   dmin = weight[start][i];
	   k = i;
	  }
	  }
	   
	  //将新选出的顶点标记为已求出最短路径，且到start的最短路径就是dmin 
	  shortPath[k] = dmin;
	  visited[k] = 1;
	   
	  //以k为中间点，修正从start到未访问各点的距离 
	  for(int i = 0; i < n; i++) {
	  if(visited[i] == 0 && weight[start][k] + weight[k][i] < weight[start][i]) {
	   weight[start][i] = weight[start][k] + weight[k][i];
	   path[i] = path[k] + "-->" + i; 
	  }
	  }
	 }
	 for(int i = 0; i < n; i++) {
	  System.out.println("从"+start+"出发到"+i+"的最短路径为："+path[i]);
	 }
	 System.out.println("====================================="); 
	 return shortPath;
	 }
	}
```

运行结果：

```
从0出发到0的最短路径为：0-->0
从0出发到1的最短路径为：0-->1
从0出发到2的最短路径为：0-->3-->2
从0出发到3的最短路径为：0-->3
从0出发到4的最短路径为：0-->3-->2-->4
=====================================
从0出发到0的最短距离为：0
从0出发到1的最短距离为：10
从0出发到2的最短距离为：50
从0出发到3的最短距离为：30
从0出发到4的最短距离为：60
```