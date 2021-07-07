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

```aidl
/** 
 * Forward declaration of guess API.
 * @param  num   your guess
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

```aidl
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