# Leetcode 题解 - 排序
<!-- GFM-TOC -->
* [Leetcode 题解 - 排序](#leetcode-题解---排序)
    * [快速选择](#快速选择)
    * [堆](#堆)
        * [1. Kth Element](#1-kth-element)
    * [桶排序](#桶排序)
        * [1. 出现频率最多的 k 个元素](#1-出现频率最多的-k-个元素)
        * [2. 按照字符出现次数对字符串排序](#2-按照字符出现次数对字符串排序)
    * [荷兰国旗问题](#荷兰国旗问题)
        * [1. 按颜色进行排序](#1-按颜色进行排序)
<!-- GFM-TOC -->


## 快速选择

用于求解   **Kth Element**   问题，也就是第 K 个元素的问题。

可以使用快速排序的 partition() 进行实现。需要先打乱数组，否则最坏情况下时间复杂度为 O(N<sup>2</sup>)。

## 堆

用于求解   **TopK Elements**   问题，也就是 K 个最小元素的问题。使用最小堆来实现 TopK 问题，最小堆使用大顶堆来实现，大顶堆的堆顶元素为当前堆的最大元素。实现过程：不断地往大顶堆中插入新元素，当堆中元素的数量大于 k 时，移除堆顶元素，也就是当前堆中最大的元素，剩下的元素都为当前添加过的元素中最小的 K 个元素。插入和移除堆顶元素的时间复杂度都为 log<sub>2</sub>N。

堆也可以用于求解 Kth Element 问题，得到了大小为 K 的最小堆之后，因为使用了大顶堆来实现，因此堆顶元素就是第 K 大的元素。

快速选择也可以求解 TopK Elements 问题，因为找到 Kth Element 之后，再遍历一次数组，所有小于等于 Kth Element 的元素都是 TopK Elements。

可以看到，快速选择和堆排序都可以求解 Kth Element 和 TopK Elements 问题。

### 1. Kth Element

215\. Kth Largest Element in an Array (Medium)

[Leetcode](https://leetcode.com/problems/kth-largest-element-in-an-array/description/) / [力扣](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/description/)

```text
Input: [3,2,1,5,6,4] and k = 2
Output: 5
```

题目描述：找到倒数第 k 个的元素。

**排序**  ：时间复杂度 O(NlogN)，空间复杂度 O(1)

```c++
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        std::sort(nums.begin(),nums.end());
        return nums[nums.size() - k];
    }
};
```

**堆**  ：时间复杂度 O(NlogK)，空间复杂度 O(K)。

```c++
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        priority_queue<int, vector<int>, greater<int>> q;//小顶堆
        for(const int num :nums){
            q.push(num);
            if(q.size() > k) //维护堆大小 为k
                q.pop();
        }
        return q.top();
    }
};
```

**快速选择**  ：时间复杂度 ？，空间复杂度？

```java
class Solution {
public:
    void swap(int &i1,int &i2){
        int temp = i1;
        i1 = i2;
        i2 = temp;
    }
    void quickSort(std::vector<int > &nums,int left,int right){
        int i,j,t,tmp;
        if(left > right)return ;
        tmp = nums[left];//赋初值 
        i = left;
        j = right;
        while(i!=j){
            while(nums[j] >= tmp && i<j)--j;
            while(nums[i] <= tmp && i<j)++i;
            if(i<j){
                swap(nums[i],nums[j]);
            }
        } 
        swap(nums[left],nums[i]);
        quickSort(nums,left,i-1);
        quickSort(nums,i+1,right);	
    }

    int findKthLargest(vector<int>& nums, int k) {
        quickSort(nums,0,nums.size()-1);
        return nums[nums.size() - k];
    }
};
```

## 桶排序

### 1. 出现频率最多的 k 个元素

347\. Top K Frequent Elements (Medium)

[Leetcode](https://leetcode.com/problems/top-k-frequent-elements/description/) / [力扣](https://leetcode-cn.com/problems/top-k-frequent-elements/description/)

```html
Given [1,1,1,2,2,3] and k = 2, return [1,2].
```

设置若干个桶，每个桶存储出现频率相同的数。桶的下标表示数出现的频率，即第 i 个桶中存储的数出现的频率为 i。

把数都放到桶之后，从后向前遍历桶，最先得到的 k 个数就是出现频率最多的的 k 个数。

```java
public int[] topKFrequent(int[] nums, int k) {
    Map<Integer, Integer> frequencyForNum = new HashMap<>();
    for (int num : nums) {
        frequencyForNum.put(num, frequencyForNum.getOrDefault(num, 0) + 1);
    }
    List<Integer>[] buckets = new ArrayList[nums.length + 1];
    for (int key : frequencyForNum.keySet()) {
        int frequency = frequencyForNum.get(key);
        if (buckets[frequency] == null) {
            buckets[frequency] = new ArrayList<>();
        }
        buckets[frequency].add(key);
    }
    List<Integer> topK = new ArrayList<>();
    for (int i = buckets.length - 1; i >= 0 && topK.size() < k; i--) {
        if (buckets[i] == null) {
            continue;
        }
        if (buckets[i].size() <= (k - topK.size())) {
            topK.addAll(buckets[i]);
        } else {
            topK.addAll(buckets[i].subList(0, k - topK.size()));
        }
    }
    int[] res = new int[k];
    for (int i = 0; i < k; i++) {
        res[i] = topK.get(i);
    }
    return res;
}
```

### 2. 按照字符出现次数对字符串排序

451\. Sort Characters By Frequency (Medium)

[Leetcode](https://leetcode.com/problems/sort-characters-by-frequency/description/) / [力扣](https://leetcode-cn.com/problems/sort-characters-by-frequency/description/)

```html
Input:
"tree"

Output:
"eert"

Explanation:
'e' appears twice while 'r' and 't' both appear once.
So 'e' must appear before both 'r' and 't'. Therefore "eetr" is also a valid answer.
```

```java
public String frequencySort(String s) {
    Map<Character, Integer> frequencyForNum = new HashMap<>();
    for (char c : s.toCharArray())
        frequencyForNum.put(c, frequencyForNum.getOrDefault(c, 0) + 1);

    List<Character>[] frequencyBucket = new ArrayList[s.length() + 1];
    for (char c : frequencyForNum.keySet()) {
        int f = frequencyForNum.get(c);
        if (frequencyBucket[f] == null) {
            frequencyBucket[f] = new ArrayList<>();
        }
        frequencyBucket[f].add(c);
    }
    StringBuilder str = new StringBuilder();
    for (int i = frequencyBucket.length - 1; i >= 0; i--) {
        if (frequencyBucket[i] == null) {
            continue;
        }
        for (char c : frequencyBucket[i]) {
            for (int j = 0; j < i; j++) {
                str.append(c);
            }
        }
    }
    return str.toString();
}
```

## 荷兰国旗问题

荷兰国旗包含三种颜色：红、白、蓝。

有三种颜色的球，算法的目标是将这三种球按颜色顺序正确地排列。它其实是三向切分快速排序的一种变种，在三向切分快速排序中，每次切分都将数组分成三个区间：小于切分元素、等于切分元素、大于切分元素，而该算法是将数组分成三个区间：等于红色、等于白色、等于蓝色。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/7a3215ec-6fb7-4935-8b0d-cb408208f7cb.png"/> </div><br>


### 1. 按颜色进行排序

75\. Sort Colors (Medium)

[Leetcode](https://leetcode.com/problems/sort-colors/description/) / [力扣](https://leetcode-cn.com/problems/sort-colors/description/)

```html
Input: [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]
```

题目描述：只有 0/1/2 三种颜色。

```c++
class Solution {
public:
    void swap(int &i1,int &i2){
        int temp = i1;
        i1 = i2;
        i2 = temp;
    }
    void sortColors(vector<int>& nums) {
        //方法1
        /*
        sort(nums.begin(),nums.end());
        */
        //进阶-方法2 单指针 
        /*
        int p = 0;
        for(int i=0;i<nums.size();i++){
            if(nums[i] == 0){
                swap(nums[p],nums[i]);
                p++;
            }
        }
        for(int i=p;i<nums.size();i++){
            if(nums[i] == 1){
                swap(nums[p],nums[i]);
                p++;
            }
        }*/
        //进阶-方法3 双指针
        /*
        int p0=0,p1=0;
        for(int i=0;i<nums.size();i++){
            if(nums[i] == 1){
                swap(nums[p1],nums[i]);
                ++p1;
            }
            else if(nums[i] == 0){
                swap(nums[p0],nums[i]);
                if(p0 < p1)
                    swap(nums[p1],nums[i]);
                ++p0;
                ++p1;
            }
        }*/
        //方法3 双指针
        int l=0,mid=0,r=nums.size()-1;
        while(mid<=r){ 
            if(nums[mid] == 0){
                swap(nums[l],nums[mid]);
                l++;
                mid++;
            }
            else if(nums[mid] == 1){
                mid++;
            }
            else if(nums[mid] == 2)
            {
                swap(nums[mid],nums[r]);
                r--;
            }
        }

    }
};
```
