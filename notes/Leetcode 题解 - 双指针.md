<!-- GFM-TOC -->
* [1. 有序数组的 Two Sum](#1-有序数组的-two-sum)
* [2. 两数平方和](#2-两数平方和)
* [3. 反转字符串中的元音字符](#3-反转字符串中的元音字符)
* [4. 回文字符串](#4-回文字符串)
* [5. 归并两个有序数组](#5-归并两个有序数组)
* [6. 判断链表是否存在环](#6-判断链表是否存在环)
* [7. 最长子序列](#7-最长子序列)
<!-- GFM-TOC -->


双指针主要用于遍历数组，两个指针指向不同的元素，从而协同完成任务。

# 1. 有序数组的 Two Sum

[Leetcode ：167. Two Sum II - Input array is sorted (Easy)](https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted/description/)

```html
Input: numbers={2, 7, 11, 15}, target=9
Output: index1=1, index2=2
```

题目描述：在有序数组中找出两个数，使它们的和为 target。

使用双指针，一个指针指向值较小的元素，一个指针指向值较大的元素。指向较小元素的指针从头向尾遍历，指向较大元素的指针从尾向头遍历。

- 如果两个指针指向元素的和 sum == target，那么得到要求的结果；
- 如果 sum > target，移动较大的元素，使 sum 变小一些；
- 如果 sum < target，移动较小的元素，使 sum 变大一些。

```c++
// 方法1 
// 左右两指针，如左指针数据 + 右指针数据 小于 目标值，则右移左指针
// 如 左指针数据 + 右指针数据 大于 目标值，则左移右指针，直接与目标值相等，返回两个元素索引（+1）
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        int left = 0;
        int right = numbers.size() -1;
        while(left<=right){
            if(numbers[left] == target -numbers[right]){
                return {left + 1, right + 1};
            }else if(numbers[left] < target - numbers[right]){
                ++left;
            }else if(numbers[left] > target - numbers[right]){
                --right;
            }
        }
        return {};
    }
};

// 方法2
// 使用unordered_map，在该示例中key为number，value为index,
// 遍历整个数组，将索引存至map中，同时在map中查找是否含有 符合条件的数据（=target - numbers[i]）,如果有返回索引 
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
       unordered_map<int, int> indies;
       for(int i=0;i<numbers.size();i++){
           if(indies.count(target - numbers[i]))
                return {indies[target - numbers[i]]+1,i+1};
            indies[numbers[i]] = i;
       }
       return {};
    }
};
```

# 2. 两数平方和

[633. Sum of Square Numbers (Easy)](https://leetcode-cn.com/problems/sum-of-square-numbers/description/)

```html
Input: 5
Output: True
Explanation: 1 * 1 + 2 * 2 = 5
```

题目描述：判断一个数是否为两个数的平方和。

```c++
class Solution {
public:
    bool judgeSquareSum(int c) {
        unsigned int left = 0;
        unsigned int right = sqrt(c);
        while(left<=right){
            if(left * left + right * right == c){
                return true;
            } else if(left * left + right * right < c ){
                ++left;
            } else if(left * left + right * right > c){
                --right;
            }
        }
        return false;
    }
};
```

# 3. 反转字符串中的元音字符

[345. Reverse Vowels of a String (Easy)](https://leetcode-cn.com/problems/reverse-vowels-of-a-string/description/)

```html
Given s = "leetcode", return "leotcede".
```

使用双指针指向待反转的两个元音字符，一个指针从头向尾遍历，一个指针从尾到头遍历。

```c++
class Solution {
public:
    bool isVowel(char ch){
        switch(ch){
            case 'a':
            case 'e':
            case 'i':
            case 'o':
            case 'u':
            case 'A':
            case 'E':
            case 'I':
            case 'O':
            case 'U':
                return true;
            default:
                return false;
        }
        return false;
    }
    string reverseVowels(string s) {
        int left = 0;
        int right = s.length() - 1;
        while(left <= right){
            if(isVowel(s[left]) && isVowel(s[right])){
                swap(s[left],s[right]);
                ++left;
                --right;
            }else if(!isVowel(s[left])){
                ++left;
            }else if(!isVowel(s[right])){
                --right;
            }
        }
        return s;
    }
};
```

# 4. 回文字符串

[680. Valid Palindrome II (Easy)](https://leetcode-cn.com/problems/valid-palindrome-ii/description/)

```html
Input: "abca"
Output: True
Explanation: You could delete the character 'c'.
```

题目描述：可以删除一个字符，判断是否能构成回文字符串。

```c++

class Solution {
public:
    bool isPalidrome(string &s,int left,int right){
        while(left <= right){
            if(s[left]!=s[right]){
                return false;
            }
            ++left;
            --right;
        }
        return true;
    }
    bool validPalindrome(string s) {
        int left =0;
        int right = s.length() -1;

        while(left <= right){
            if(s[left]!=s[right]){
                if(!isPalidrome(s,left+1,right) &&  !isPalidrome(s,left,right-1)){
                    return false;
                }else{
                    return true;
                }
            }
            ++left;
            --right;
        }
        return true;
    }
};
```

# 5. 归并两个有序数组

[88. Merge Sorted Array (Easy)](https://leetcode-cn.com/problems/merge-sorted-array/description/)

```html
Input:
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3

Output: [1,2,2,3,5,6]
```

题目描述：把归并结果存到第一个数组上。

需要从尾开始遍历，否则在 nums1 上归并得到的值会覆盖还未进行归并比较的值。

```c++
//需考虑异常情况nums1为空时的情况
class Solution {
public:
    void swap(int &m,int &n){
        int t = m;
        n = m;
        m = t;
    }
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int s1 = m -1;
        int s2 = n -1;
        int s1_max = m + n -1;
        while(s1 > -1 && s2 > -1){ // 当nums1不为空时，m > 0
            if(nums2[s2] >= nums1[s1]){
                swap(nums2[s2],nums1[s1_max]);
                --s2;
            }else if(nums2[s2] < nums1[s1]){
                swap(nums1[s1],nums1[s1_max]);
                --s1;
            }
            --s1_max;
        }
        while(s1 == -1 && s2 > -1){ //当nums1为空时，m=0 
            nums1[s1_max--] = nums2[s2--];
        }
    }
};
```

# 6. 判断链表是否存在环

[141. Linked List Cycle (Easy)](https://leetcode-cn.com/problems/linked-list-cycle/description/)

使用双指针，一个指针每次移动一个节点，一个指针每次移动两个节点，如果存在环，那么这两个指针一定会相遇。

```c++
//一个指针移动一个节点，一个指针移动两个节点,如果存在环，两指针定会相遇
// 需考虑 head指针是否为空，需考虑指针是否越界（指针移动两个节点时）
class Solution {
public:
    bool hasCycle(ListNode *head) {
        if(!head)return false;
        ListNode *l1 = head;
        ListNode *l2 = head->next;
        while(l1 && l2 && l2->next){
            if(l1 == l2){
                return true;
            }
            l1 = l1->next;
            l2 = l2->next->next;
        }
        return false;
    }
};
```

# 7. 最长子序列

[524. Longest Word in Dictionary through Deleting (Medium)](https://leetcode-cn.com/problems/longest-word-in-dictionary-through-deleting/description/)

```
Input:
s = "abpcplea", d = ["ale","apple","monkey","plea"]

Output:
"apple"
```

题目描述：删除 s 中的一些字符，使得它构成字符串列表 d 中的一个字符串，找出能构成的最长字符串。如果有多个相同长度的结果，返回字典序的最小字符串。

通过删除字符串 s 中的一个字符能得到字符串 t，可以认为 t 是 s 的子序列，我们可以使用双指针来判断一个字符串是否为另一个字符串的子序列。

```c++
class Solution {
public:
    //s1是s2的子串，返回 true
    bool isSubstring(string s1,string s2){
        int ps1 = 0;
        int ps2 = 0;
        while(ps1 < s1.length() && ps2 < s2.length()){
            if(s1[ps1] == s2[ps2])
                ++ps1;
            ++ps2;
        }        
        return ps1 == s1.length();
    }
    string findLongestWord(string s, vector<string>& d) {
        string longestWord = "";
        for(const string target : d){
            int l1 = longestWord.length();
            int l2 = target.length();
            if(l1>l2 || (l1 == l2 && longestWord.compare(target)< 0))// 第一个判断找到最长的子串，第二个判断找出字典序最小的子串 
                continue;
            if(isSubstring(target,s)){
                longestWord = target;
            }
        }
        return longestWord;
    }
};
```




# 微信公众号


更多精彩内容将发布在微信公众号 CyC2018 上，你也可以在公众号后台和我交流学习和求职相关的问题。另外，公众号提供了该项目的 PDF 等离线阅读版本，后台回复 "下载" 即可领取。公众号也提供了一份技术面试复习大纲，不仅系统整理了面试知识点，而且标注了各个知识点的重要程度，从而帮你理清多而杂的面试知识点，后台回复 "大纲" 即可领取。我基本是按照这个大纲来进行复习的，对我拿到了 BAT 头条等 Offer 起到很大的帮助。你们完全可以和我一样根据大纲上列的知识点来进行复习，就不用看很多不重要的内容，也可以知道哪些内容很重要从而多安排一些复习时间。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公众号海报.png"></img></div>
