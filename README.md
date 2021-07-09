# leetcode
leetcode刷题笔记

###374.猜数字大小

猜数字游戏的规则如下：

每轮游戏，我都会从 1 到 n 随机选择一个数字。 请你猜选出的是哪个数字。
如果你猜错了，我会告诉你，你猜测的数字比我选出的数字是大了还是小了。
你可以通过调用一个预先定义好的接口 int guess(int num) 来获取猜测结果，返回值一共有 3 种可能的情况（-1，1 或 0）：

-1：我选出的数字比你猜的数字小 pick < num

1：我选出的数字比你猜的数字大 pick > num

0：我选出的数字和你猜的数字一样。恭喜！你猜对了！pick == num

返回我选出的数字。

```java
/** 
 * Forward declaration of guess API.
 * @param num   your guess
 * @return 	     -1 if num is lower than the guess number
 *			      1 if num is higher than the guess number
 *               otherwise return 0
 * int guess(int num);
 */

public class Solution extends GuessGame {
    public int guessNumber(int n) {
        int l=1,r=n;
        while(l<=r){
            int m=(r-l)/2+l;
            int t=guess(m);
            if(t==0){
                return m;
            }
            else{
                if(t==-1){
                    r=m-1;
                }
                else{
                    l=m+1;
                }
            }
        }
        return l;
    }
}
```

要点：在实现二分查找，要想清楚实现的是左闭右开还是全闭区间，这决定了初始值的选取、要不要取等号、要不要+1这些很容易出错的细节

###455.分发饼干

假设你是一位很棒的家长，想要给你的孩子们一些小饼干。但是，每个孩子最多只能给一块饼干。

对每个孩子 i，都有一个胃口值 g[i]，这是能让孩子们满足胃口的饼干的最小尺寸；并且每块饼干 j，都有一个尺寸 s[j] 。如果 s[j] >= g[i]，我们可以将这个饼干 j 分配给孩子 i ，这个孩子会得到满足。你的目标是尽可能满足越多数量的孩子，并输出这个最大数值。

```java
class Solution {
    public int findContentChildren(int[] g, int[] s) {
        if(s.length==0){
            return 0;
        }
        Arrays.sort(g);
        Arrays.sort(s);
        int i=0,j=0;
        int count=0;
        while(i<g.length&&j<s.length){
            if(s[j]>=g[i]){
                i++;
                j++;
                count++;
            }
            else {
                j++;
                
            }
        }
        return count;
    }
}
```

要点：贪心算法，在每一步选择中都采取在当前状态下最优的选择，从而希望导致结果是最优的算法。当我们能够通过局部最优推出全局最优时，就使用贪心算法。在这道题中，先将胃口和尺寸分别从高到低排序，每次都将分量最大的饼干分配给胃口最大的小孩，如果能满足，结果+1，否则将当前饼干分配给下一个（胃口更小）的小孩

###310.最小高度树

树是一个无向图，其中任何两个顶点只通过一条路径连接。 换句话说，一个任何没有简单环路的连通图都是一棵树。

给你一棵包含 n 个节点的树，标记为 0 到 n - 1 。给定数字 n 和一个有 n - 1 条无向边的 edges 列表（每一个边都是一对标签），其中 edges[i] = [ai, bi] 表示树中节点 ai 和 bi 之间存在一条无向边。

可选择树中任何一个节点作为根。当选择节点 x 作为根节点时，设结果树的高度为 h 。在所有可能的树中，具有最小高度的树（即，min(h)）被称为 最小高度树 。

请你找到所有的 最小高度树 并按 任意顺序 返回它们的根节点标签列表。

树的 高度 是指根节点和叶子节点之间最长向下路径上边的数量。

```java
class Solution {
    public List<Integer> findMinHeightTrees(int n, int[][] edges) {
        List<Integer> res=new ArrayList<>();
        if(n==1){
            res.add(0);
            return res;
        }
        int[] degree=new int[n];
        List<List<Integer>> map=new ArrayList<>();
        for(int i=0;i<n;i++){
            map.add(new ArrayList<>());

        }
        for(int[] edge:edges){
            degree[edge[0]]++;
            degree[edge[1]]++;
            map.get(edge[0]).add(edge[1]);
            map.get(edge[1]).add(edge[0]);
        }
        Queue<Integer> queue=new LinkedList<>();
        for (int i=0;i<n;i++){
            if(degree[i]==1) queue.offer(i);
        }
        while(!queue.isEmpty()){
            res.clear();
            int size=queue.size();
            for(int i=0;i<size;i++){
                int tem=queue.poll();
                res.add(tem);
                for(int nei : map.get(tem)){
                    degree[nei]--;
                    if(degree[nei]==1){
                        queue.offer(nei);
                    }
                }
            }
        }
        return res;
    }
}
```

要点：本题考察点主要是图和BFS。首先要学会建图的方法，这道题用邻接表就可以（即：针对每个点，都存下与它相邻的一组点）。

接着本题的重点在于BFS的思路——对建好的图进行拓扑排序：每次找到度为1的点，然后把它从图中删去，直到最后只剩下两个度为1的点或者一个度为0的点，就是我们要找的答案了（是的，答案只可能是一个点或者两个点，可以想想为什么）。

我们可以把它想象成“剥洋葱”的过程，一层层的把不可能是答案的点剔除掉（度为1的点是叶子结点，叶子结点不可能是我们要找的根结点）最终剩下的就是我们要的答案。

本解法每个点都只被遍历了一次，因此时间复杂度是O(n)。还有一种解法是先求该图的直径，其中点就是我们要的答案，复杂度会稍微高一些但也是O(n)的，大家感兴趣的话可以再去扩展了解。

###94.二叉树的中序遍历

给定一个二叉树的根节点 root ，返回它的中序遍历。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res=new ArrayList<>();
        DFS(root,res);
        return res;

    }
    public void DFS(TreeNode root,List<Integer> res){
        if(root==null){
            return;
        }
        DFS(root.left,res);
        res.add(root.val);
        DFS(root.right,res);
    }
}
```

要点：二叉树的中序遍历。这道题考察的是对树这个数据结构的DFS。做完这道题，大家应该要举一反三，同时学会对树的前序、后序遍历。还有对树的BFS，也就是层次遍历，也可以一并掌握。

所谓“前、中、后”指的是遍历时访问“根结点”的次序，分别对应“根结点-左子树-右子树”，“左子树-根结点-右子树”和“左子树-右子树-根结点”。

不难发现，对树的遍历天然就具有递归的性质，我们可以很容易的得出递归的解法：f(root)表示题目要求的答案，那么只需递归调用f(root.left)表示左子树的答案，加入答案中，再往答案加入root.val，最后再加入f(root.right)即可。递归时不要忘记在最前面加上递归终止条件，在这里即访问节点为空的时候。

题干里还要求尝试迭代的方法。同学们遇到树的问题，最好都能用递归和迭代两种解法写出来。两种方法本质上是等价的，递归相当于隐性的用了一个函数栈，而迭代则是将这个栈显性的调用，自己定义、自己维护。

###275. H 指数 II

给定一位研究者论文被引用次数的数组（被引用次数是非负整数），数组已经按照 升序排列 。编写一个方法，计算出研究者的 h 指数。

h 指数的定义: “h 代表“高引用次数”（high citations），一名科研人员的 h 指数是指他（她）的 （N 篇论文中）总共有 h 篇论文分别被引用了至少 h 次。（其余的 N - h 篇论文每篇被引用次数不多于 h 次。）"

 

```java
class Solution {
    public int hIndex(int[] citations) {
        int l=0,r=citations.length,mid=0;
        int n=citations.length;
        while(l<r){
            mid=(r-l)/2+l;
            if(citations[mid]<n-mid){
                l=mid+1;
            }
            else{
                r=mid;
            } 
        }
        return n-l;

    }
}
```

要点：本题可以用O(n)线性搜索，更好的解法是O(logn)二分搜索。而本题可以二分搜索的原因是数组是已经排好序的（这也是本题和274题的区别）。

本题首要的关键点是理解题意。首先要明确H指数的定义，“被引用次数”和“论文篇数”这两个数必须是相同的，理解了这一点才能继续往下做。

假设有数组citation有n个数，我们用i表示其下标，c表示其值，也就是citation[i]=c。由于数组是升序排列的，因此i前面的数（有i个）不会比它大，i后面的数（有n-i-1个）不会比它小，因此当我们访问到数组中任意一个数的位置时，都满足“有n-i个数的值至少为c”。

由于之前说的，被引用次数（c）和论文篇数（n-i）必须是同一个数，我们设这个数为x，x=min(c, n-i)。比如有2个数的值至少为3，那么x为2，同理有3个数的值至少为2，x的值也是2。我们访问到任意一个数，都有对应的x，而题目要求的h其实就是整个数组中的max(x)。

综上所述，我们要求的就是最小值的最大值：max(min(n-i, c))。这是用二分求解的经典问题，同理还有求最大值的最小值。

所以，找到这个i，也就找到了答案。如果我们从数组中线性搜索i，那就是O(n)的解法，如果二分搜索这个i，那就是O(logn)的解法。

最后，实现二分搜索时，要注意我边界问题和之前说过的溢出问题。