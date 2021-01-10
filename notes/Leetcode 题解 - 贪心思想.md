# 	Leetcode 题解 - 贪心思想

<!-- GFM-TOC -->
* [Leetcode 题解 - 贪心思想](#leetcode-题解---贪心思想)
    * [1. 分配饼干](#1-分配饼干)
    * [2. 不重叠的区间个数](#2-不重叠的区间个数)
    * [3. 投飞镖刺破气球](#3-投飞镖刺破气球)
    * [4. 根据身高和序号重组队列](#4-根据身高和序号重组队列)
    * [5. 买卖股票最大的收益](#5-买卖股票最大的收益)
    * [6. 买卖股票的最大收益 II](#6-买卖股票的最大收益-ii)
    * [7. 种植花朵](#7-种植花朵)
    * [8. 判断是否为子序列](#8-判断是否为子序列)
    * [9. 修改一个数成为非递减数组](#9-修改一个数成为非递减数组)
    * [10. 子数组最大的和](#10-子数组最大的和)
    * [11. 分隔字符串使同种字符出现在一起](#11-分隔字符串使同种字符出现在一起)
<!-- GFM-TOC -->


保证每次操作都是局部最优的，并且最后得到的结果是全局最优的。

## 1. 分配饼干

455\. Assign Cookies (Easy)

[Leetcode](https://leetcode.com/problems/assign-cookies/description/) / [力扣](https://leetcode-cn.com/problems/assign-cookies/description/)

```html
Input: grid[1,3], size[1,2,4]
Output: 2
```

题目描述：每个孩子都有一个满足度 grid，每个饼干都有一个大小 size，只有饼干的大小大于等于一个孩子的满足度，该孩子才会获得满足。求解最多可以获得满足的孩子数量。

1. 给一个孩子的饼干应当尽量小并且又能满足该孩子，这样大饼干才能拿来给满足度比较大的孩子。
2. 因为满足度最小的孩子最容易得到满足，所以先满足满足度最小的孩子。

在以上的解法中，我们只在每次分配时饼干时选择一种看起来是当前最优的分配方法，但无法保证这种局部最优的分配方法最后能得到全局最优解。我们假设能得到全局最优解，并使用反证法进行证明，即假设存在一种比我们使用的贪心策略更优的最优策略。如果不存在这种最优策略，表示贪心策略就是最优策略，得到的解也就是全局最优解。

证明：假设在某次选择中，贪心策略选择给当前满足度最小的孩子分配第 m 个饼干，第 m 个饼干为可以满足该孩子的最小饼干。假设存在一种最优策略，可以给该孩子分配第 n 个饼干，并且 m \< n。我们可以发现，经过这一轮分配，贪心策略分配后剩下的饼干一定有一个比最优策略来得大。因此在后续的分配中，贪心策略一定能满足更多的孩子。也就是说不存在比贪心策略更优的策略，即贪心策略就是最优策略。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/e69537d2-a016-4676-b169-9ea17eeb9037.gif" width="430px"> </div><br>

```c++
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        if(!g.size() ||!s.size()) return 0;
        sort(g.begin(),g.end());
        sort(s.begin(),s.end());
        int i = 0;//孩子索引
        int j = 0;//饼干索引
        while(i<g.size()&&j<s.size()){//防止下标访问越界
            if(g[i]<=s[j])//如果能，满足 孩子胃口，孩子+1， 饼干 不管满足不满足孩子胃口，每次+1，直到饼干遍历结束。
                i++;
            j++;
        }
        return i;
    }
};
```

## 2. 不重叠的区间个数

435\. Non-overlapping Intervals (Medium)

[Leetcode](https://leetcode.com/problems/non-overlapping-intervals/description/) / [力扣](https://leetcode-cn.com/problems/non-overlapping-intervals/description/)

```html
Input: [ [1,2], [1,2], [1,2] ]

Output: 2

Explanation: You need to remove two [1,2] to make the rest of intervals non-overlapping.
```

```html
Input: [ [1,2], [2,3] ]

Output: 0

Explanation: You don't need to remove any of the intervals since they're already non-overlapping.
```

题目描述：计算让一组区间不重叠所需要移除的区间个数。

先计算最多能组成的不重叠区间个数，然后用区间总个数减去不重叠区间的个数。

在每次选择中，区间的结尾最为重要，选择的区间结尾越小，留给后面的区间的空间越大，那么后面能够选择的区间个数也就越大。

按区间的结尾进行排序，每次选择结尾最小，并且和前一个区间不重叠的区间。

```c++
class Solution {
public:
    //构建 sort排序规则，按照vector中第二个元素从小到大 排序
    static bool cmp(vector<int> &v1,vector<int> &v2){
        return v1[1]<v2[1];
    }
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        if(!intervals.size())return 0;
        sort(intervals.begin(),intervals.end(),cmp);//1.首先按照队尾从小到大排序
        int cnt =1;
        int end = intervals[0][1];                  //2.获取第一个向量队尾的值
        for(int i=0;i<intervals.size();++i){        //3.遍历外层向量，如果有向量中第一个元素 小于第一个向量队尾，则该元素为去掉的重叠空间
            if(intervals[i][0]<end){
                continue;
            }
            end = intervals[i][1];                  //4.否则更新队尾的值，直到循环结束。
            cnt++;                                  //5.记录不重叠空间个数，总数-不重叠 = 重叠的
        }
        return intervals.size() -cnt;
    }
};
```

使用 lambda 表示式创建 Comparator 会导致算法运行时间过长，如果注重运行时间，可以修改为普通创建 Comparator 语句：

```java
Arrays.sort(intervals, new Comparator<int[]>() {
     @Override
     public int compare(int[] o1, int[] o2) {
         return (o1[1] < o2[1]) ? -1 : ((o1[1] == o2[1]) ? 0 : 1);
     }
});
```

实现 compare() 函数时避免使用 `return o1[1] - o2[1];` 这种减法操作，防止溢出。

## 3. 投飞镖刺破气球

452\. Minimum Number of Arrows to Burst Balloons (Medium)

[Leetcode](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/description/) / [力扣](https://leetcode-cn.com/problems/minimum-number-of-arrows-to-burst-balloons/description/)

```
Input:
[[10,16], [2,8], [1,6], [7,12]]

Output:
2
```

题目描述：气球在一个水平数轴上摆放，可以重叠，飞镖垂直投向坐标轴，使得路径上的气球都被刺破。求解最小的投飞镖次数使所有气球都被刺破。

也是计算不重叠的区间个数，不过和 Non-overlapping Intervals 的区别在于，[1, 2] 和 [2, 3] 在本题中算是重叠区间。

```c++
class Solution {
public:
    static bool cmp(vector<int> &a,vector<int> &b){
        return a[1] < b[1];
    }
    int findMinArrowShots(vector<vector<int>>& points) {
        if(!points.size())return 0;
        sort(points.begin(),points.end(),cmp);//按指定条件排序，关键点1：按照vector中的第二个元素排序
        int count = 1;
        int end = points[0][1];
        for(int i=1;i<points.size();++i){
           if(points[i][0] <=end && points[i][1] >=end){//如果第二个气球的区间与第一个气球相交，跳过
               continue;
           }
           end = points[i][1];
           count ++;
        }
        return count;
    }
};
```

## 4. 根据身高和序号重组队列

406\. Queue Reconstruction by Height(Medium)

[Leetcode](https://leetcode.com/problems/queue-reconstruction-by-height/description/) / [力扣](https://leetcode-cn.com/problems/queue-reconstruction-by-height/description/)

```html
Input:
[[7,0], [4,4], [7,1], [5,0], [6,1], [5,2]]

Output:
[[5,0], [7,0], [5,2], [6,1], [4,4], [7,1]]
```

题目描述：一个学生用两个分量 (h, k) 描述，h 表示身高，k 表示排在前面的有 k 个学生的身高比他高或者和他一样高。

为了使插入操作不影响后续的操作，身高较高的学生应该先做插入操作，否则身高较小的学生原先正确插入的第 k 个位置可能会变成第 k+1 个位置。

身高 h 降序、个数 k 值升序，然后将某个学生插入队列的第 k 个位置中。

[图解](https://leetcode-cn.com/problems/queue-reconstruction-by-height/solution/xian-pai-xu-zai-cha-dui-dong-hua-yan-shi-suan-fa-g/)

```c++
class Solution {
public:
    static bool cmp(vector<int> &a,vector<int> &b){
        return a[0] > b[0]||(a[0] == b[0] && a[1] < b[1]);
    }
    vector<vector<int>> reconstructQueue(vector<vector<int>>& people) {
        sort(people.begin(),people.end(),cmp);//先排序，排序方式：按身高从大到小，身高相同时，体重从小到大
        vector<vector<int>> queue;
        for(const vector<int>& p:people){
            queue.insert(queue.begin() + p[1],p);//在插入
        }
        return queue;
    }
};
```

## 5. 买卖股票最大的收益

121\. Best Time to Buy and Sell Stock (Easy)

[Leetcode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/) / [力扣](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/description/)

题目描述：一次股票交易包含买入和卖出，只进行一次交易，求最大收益。

只要**记录前面的最小价格**，将这个最小价格作为买入价格，然后将**当前的价格作为售出价格**，查看当前收益是不是最大收益。

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(!prices.size()) return 0;
        int minValue = prices[0];
        int maxValue = 0;
        for(int i=1;i<prices.size();i++){
            if(prices[i] < minValue){
                minValue = prices[i];
            }else {
                maxValue = (prices[i] - minValue) > maxValue ? (prices[i]-minValue) : maxValue;
            }
        }
        return maxValue;
    }
};
```


## 6. 买卖股票的最大收益 II

122\. Best Time to Buy and Sell Stock II (Easy)

[Leetcode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/) / [力扣](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/description/)

题目描述：可以进行多次交易，多次交易之间不能交叉进行，可以进行多次交易。

对于 [a, b, c, d]，如果有 a \<= b \<= c \<= d ，那么最大收益为 d - a。而 d - a = (d - c) + (c - b) + (b - a) ，因此当访问到一个 prices[i] 且 prices[i] - prices[i-1] \> 0，那么就把 prices[i] - prices[i-1] 添加到收益中。

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(!prices.size()) return 0;
        int sum = 0;
        for(int i = 1;i<prices.size();i++){
            if(prices[i] - prices[i-1] > 0){//如果 a<=b<=c<=d,最大收益为d-a,d-a还等于(d-c)+(c-b)+(b-a)
                sum += prices[i] - prices[i-1];
            }         
        }
        return sum;
    }
};
```

## 7. 种植花朵

605\. Can Place Flowers (Easy)

[Leetcode](https://leetcode.com/problems/can-place-flowers/description/) / [力扣](https://leetcode-cn.com/problems/can-place-flowers/description/)

```html
Input: flowerbed = [1,0,0,0,1], n = 1
Output: True
```

题目描述：flowerbed 数组中 1 表示已经种下了花朵。花朵之间至少需要一个单位的间隔，求解是否能种下 n 朵花。

```c++
class Solution {
public:
    bool canPlaceFlowers(vector<int>& flowerbed, int n) {
        int size = flowerbed.size();
        int cnt = 0;
        for(int i = 0; i <size; ++i){
            if(flowerbed[i]){
                continue;
            }
            //精髓 使用两个?: 即不会越界，还考虑到边界情况
            int pre = i==0 ? 0 :flowerbed[i- 1];
            int next= i==size-1 ? 0 :flowerbed[i+1];
            if(!pre && !next){
                cnt ++;
                flowerbed[i] = 1;
            }
        }
        return cnt >=n;
    }
};
```

## 8. 判断是否为子序列

392\. Is Subsequence (Medium)

[Leetcode](https://leetcode.com/problems/is-subsequence/description/) / [力扣](https://leetcode-cn.com/problems/is-subsequence/description/)

```html
s = "abc", t = "ahbgdc"
Return true.
```

```c++
//定义两个数组指针，遍历t字符串，如果两指针字符相等，子串指针+1，原串指针+1，循环结束，返回子串指针是否等于子串长度，等于则为子序列。
class Solution {
public:
    bool isSubsequence(string s, string t) {
        int si = 0;
        int ti = 0;
        while(ti < t.length()){
            if(s[si] == t[ti]){
                si++;
            }
            ti++;
        }
        return si == s.length() ;
    }
};
```

## 9. 修改一个数成为非递减数组

665\. Non-decreasing Array (Easy)

[Leetcode](https://leetcode.com/problems/non-decreasing-array/description/) / [力扣](https://leetcode-cn.com/problems/non-decreasing-array/description/)

```html
Input: [4,2,3]
Output: True
Explanation: You could modify the first 4 to 1 to get a non-decreasing array.
```

题目描述：判断一个数组是否能只修改一个数就成为非递减数组。

在出现 nums[i] \< nums[i - 1] 时，需要考虑的是应该修改数组的哪个数，使得本次修改能使 i 之前的数组成为非递减数组，并且   **不影响后续的操作**  。优先考虑令 nums[i - 1] = nums[i]，因为如果修改 nums[i] = nums[i - 1] 的话，那么 nums[i] 这个数会变大，就有可能比 nums[i + 1] 大，从而影响了后续操作。还有一个比较特别的情况就是 nums[i] \< nums[i - 2]，修改 nums[i - 1] = nums[i] 不能使数组成为非递减数组，只能修改 nums[i] = nums[i - 1]。



这道题给了我们一个数组，说我们最多有1次修改某个数字的机会，
问能不能将数组变为非递减数组。题目中给的例子太少，不能覆盖所有情况，我们再来看下面三个例子：
4，2，3
-1，4，2，3
2，3，3，2，4
我们通过分析上面三个例子可以发现，当我们发现后面的数字小于前面的数字产生冲突后，
[1]**有时候需要修改前面较大的数字**(比如前两个例子需要修改4)，
[2]**有时候却要修改后面较小的那个数字**(比如前第三个例子需要修改2)，
那么有什么内在规律吗？是有的，判断修改那个数字其实跟再前面一个数的大小有关系，

- 首先如果再前面的数不存在，比如例子1，4前面没有数字了，我们直接修改前面的数字为当前的数字2即可。

- 而当再前面的数字存在，并且小于当前数时，比如例子2，-1小于2，我们还是需要修改前面的数字4为当前数字2；

- 如果再前面的数大于当前数，比如例子3，3大于2，我们需要修改当前数2为前面的数3。



```c++
class Solution {
public:
    bool checkPossibility(vector<int>& nums) {
        int size = nums.size();
        int cnt =0;
        for(int i=1; i< size;i++){
            if(nums[i]>=nums[i-1]){
                continue;
            }
            cnt ++;
            if(i-2 >= 0 && nums[i-2] > nums[i]){
                nums[i] = nums[i -1];
            }
            else {
                nums[i-1] = nums[i];
            }
        }
        return cnt <=1;
    }
};
```



## 10. 子数组最大的和

53\. Maximum Subarray (Easy)

[Leetcode](https://leetcode.com/problems/maximum-subarray/description/) / [力扣](https://leetcode-cn.com/problems/maximum-subarray/description/)

```html
For example, given the array [-2,1,-3,4,-1,2,1,-5,4],
the contiguous subarray [4,-1,2,1] has the largest sum = 6.
```

```java
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int preSum = nums[0];
        int maxSum = preSum;
        for(int i=1; i<nums.size();i++){//注意循环的起点
            preSum = preSum > 0 ? preSum +nums[i] : nums[i]; //如果上一次和为正，则加上当前值，不为正，当前值最大。
            if(preSum > maxSum)
                maxSum = preSum;
        }
        return maxSum;
    }
};
```

## 11. 分隔字符串使同种字符出现在一起

763\. Partition Labels (Medium)

[Leetcode](https://leetcode.com/problems/partition-labels/description/) / [力扣](https://leetcode-cn.com/problems/partition-labels/description/)

```html
Input: S = "ababcbacadefegdehijhklij"
Output: [9,7,8]
Explanation:
The partition is "ababcbaca", "defegde", "hijhklij".
This is a partition so that each letter appears in at most one part.
A partition like "ababcbacadefegde", "hijhklij" is incorrect, because it splits S into less parts.
```

```java
//首先记录每个字符出现的最后位置，如果当前index等于当前元素的最大index，则push,下段起始位置为当前位置i+1
class Solution {
public:
    vector<int> partitionLabels(string S) {
        vector<int> result;
        unordered_map<char,int> map;//记录出现的ch和最后出现的位置，也可以用数组arr[S[i] - 'a'] = i;
        for(int i=0;i<S.size();i++){
            map[S[i]] = i;
        }
        int start = 0,end = 0;
        for(int i=0;i<S.size();i++){
            end = end > map[S[i]] ? end : map[S[i]];//获取当前元素的最大索引
            if(i == end){
                result.push_back( end - start +1);//
                start =i+1;
            }
        }
        return result;
    }
};
```
