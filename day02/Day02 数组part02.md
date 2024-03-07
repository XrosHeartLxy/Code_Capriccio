# Day02 数组part02

### 任务URL：[第一章 数组part02 (qq.com)](https://docs.qq.com/doc/DUGRwWXNOVEpyaVpG)

- **977.有序数组的平方**
    
    **题目建议**： 本题关键在于理解双指针思想
    
    [📃力扣题目链接](https://leetcode.cn/problems/squares-of-a-sorted-array/)
    
    [🤓文章讲解](https://programmercarl.com/0977.%E6%9C%89%E5%BA%8F%E6%95%B0%E7%BB%84%E7%9A%84%E5%B9%B3%E6%96%B9.html)
    
     [📽️视频讲解](https://www.bilibili.com/video/BV1QB4y1D7ep)
    
- **209.长度最小的子数组**
    
    **题目建议**： 本题关键在于理解滑动窗口，这个滑动窗口看文字讲解 还挺难理解的，建议大家先看视频讲解。  拓展题目可以先不做。
    
    [📃力扣题目链接](https://leetcode.cn/problems/minimum-size-subarray-sum/)
    
    [🤓文章讲解](https://programmercarl.com/0209.%E9%95%BF%E5%BA%A6%E6%9C%80%E5%B0%8F%E7%9A%84%E5%AD%90%E6%95%B0%E7%BB%84.html)
    
    [📽️视频讲解](https://www.bilibili.com/video/BV1tZ4y1q7XE)
    
- **59.螺旋矩阵II**
    
    **题目建议**：  本题关键还是在转圈的逻辑，在二分搜索中提到的区间定义，在这里又用上了。
    
    [📃力扣题目链接](https://leetcode.cn/problems/spiral-matrix-ii/)
    
    [🤓文章讲解](https://programmercarl.com/0059.%E8%9E%BA%E6%97%8B%E7%9F%A9%E9%98%B5II.html)
    
    [📽️视频讲解](https://www.bilibili.com/video/BV1SL4y1N7mV/)
    

## **977.有序数组的平方**

<aside>
💡 给你一个按 **非递减顺序** 排序的整数数组 nums，返回 **每个数字的平方** 组成的新数组，要求也按 **非递减顺序** 排序。

</aside>

> March 7, 2024 二刷
> 

### 暴力排序

- 思路：每个数平方之后，再排序，直接调用sort()函数（**快速排序**），时间复杂度取决于快排的时间复杂度
- **sort()函数语法：`sort(nums.begin(),nums.end());`** 参数为两个迭代器
- 时间复杂度$O(n + nlogn)$ || 空间复杂度$O(1)$

代码如下

```cpp
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        for(int i =0;i<nums.size();i++){ // 遍历数组计算数字平方覆盖原数组
            nums[i] = nums[i]*nums[i];
        }
        sort(nums.begin(),nums.end()); // 偷懒用现有函数对新数组排序
        return nums;
    }
};
```

### 双指针法

因为数组是有序的，只不过负数平方之后可能成为最大数了

那么数组平方的最大值只会在数组的两端，不是最左边就是最右边，不可能是中间

所以可以考虑双指针法，分别从最前端和最后端开始向中间遍历，输出两个指针中相对较大的那个元素，存放在新数组的高位，直到两个指针相遇退出循环

![977.有序数组的平方.gif](pic1.gif)

**我的代码：**

```cpp
class Solution {
public:
    int square(int x) {
        return x * x;
    }
    vector<int> sortedSquares(vector<int>& nums) {
        int low = 0, high = nums.size() - 1;
        int pointer = nums.size() - 1;//记录新数组下一个存放数据的位置
        vector<int> ans(nums.size(),0);//生成一个与nums长度相同的0数组
        for (int i = 0; i < nums.size(); i++)
        {
            if (square(nums[low]) >= square(nums[high])){
                ans[pointer--] = (square(nums[low++]));
            }
            else{
                ans[pointer--] = (square(nums[high--]));
            }
        }
        return ans;
    }
};
```

**Carl的参考代码**

```cpp
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        int low = 0, high = nums.size() - 1;
        int k = nums.size() - 1;
        vector<int> ans(nums.size(),0);
        while(low<=high){
            if(nums[low]*nums[low]<nums[high]*nums[high]){
                ans[k--]=nums[high]*nums[high];
                high--;
            }
            else{
                ans[k--]=nums[low]*nums[low];
                low++;
            }
        }
        return ans;
    }
};
```

值得借鉴和改进的地方：

- 函数封装：求平方的函数在本题中的意义并不大，不需要专门封装成函数，用最平凡的”A[i]*A[i]“即可，**不要盲目地追求冗余封装**
- 卡尔这里写的循环值得品味和学习，改成while(i≤j)似乎更容易理解一些
- 这里总共用到了3个指针，两个指针用来一头一尾的遍历所给数组元素，第3个指针作为新数组的下标指示，辅助新数组从高位到低位的顺序来存储数据
- 这里平方比较大小也可以考虑用绝对值函数abs()来比较大小，然后在存储时将元素求平方

### 3/7/2024二刷错误思路

- 思路严重瑕疵，比较头尾元素后交换位置会改变元素间相对顺序，且交换位置后的头尾指针中可能都没有指向待排序元素中的最大值
    - 反例如下：
    - **`[-5,-3,-2,-1]`**
    - **`[-1,-3,-2,25]`** //第一次排序后指针指向-1, -2；但下一轮的最大值应该是-3的平方

❌❌❌错误代码如下：

```cpp
~~class Solution {
public:
    void swap(int &a,int &b){
        int temp = a;
        a = b;
        b = temp;
    }
    vector<int> sortedSquares(vector<int>& nums) {
        int low = 0,high = nums.size()-1; //左闭右闭,低位和高位双指针
        while(low <= high){ // 这里用<=是考虑到low==high时有意义
            cout<<"low:"<<low<<" high:"<<high<<endl;
            if(abs(nums[low])>abs(nums[high])){ //当低位指针指向元素的绝对值更大时
                swap(nums[low],nums[high]); //将该元素移到高位指针指向位置
                nums[high--] *= nums[high]; // 将本次确定位置的元素平方
            } 
            else{ // 当高位指针指向元素绝对值更大时
                nums[high--] *= nums[high]; // 将本次确定位置的元素平方
            }
        }
        return nums;
    }
};~~
```

## 209.长度最小的子数组

<aside>
💡 给定一个含有 n 个正整数的数组和一个正整数 s ，找出该数组中满足其和 ≥ s 的长度最小的 连续 子数组，并返回其长度。如果不存在符合条件的子数组，返回 0。

</aside>

> March 7, 2024 二刷
> 

### 方法一：暴力解法

- 两个for循环，然后不断的寻找符合条件的子序列，时间复杂度很明显是O(n^2)。
- 时间复杂度：$O(n^2)$  ||  空间复杂度：$O(1)$

### 方法二：滑动窗口

> 滑动窗口的精妙之处在于根据当前子序列和大小的情况，不断调节子序列的起始位置。从而将$O(n^2)$的暴力解法降为$O(n)$。
> 

![209.长度最小的子数组.gif](pic2.gif)

- 只用一个for循环来表示滑动窗口的终止位置，然后针对每个符合条件`sum>s`的窗口进行起始位置的试探，直到收敛到最短的符合条件的区间，记录该区间长度，并与之前保存下来的最短长度进行比较并取小。
- 在本题中实现滑动窗口，主要确定如下三点：
    - 窗口内是什么？
    - 如何移动窗口的起始位置？
    - 如何移动窗口的结束位置？
- C++中最大int类型元素的表示方法：`INT32_MAX`
- 用三目运算符取两个变量中的最小值以及返回值的确定
    - `result = result < subLength ? result : subLength;`
    - `return result == INT32_MAX ? 0 : result;`
- 代码的主体部分主要是两个循环，外层的`for循环`控制滑动窗口的尾部，索引指针指向窗口的终止位置
- 内层的`while循环`用来收敛窗口的头部，确定开始指针的位置，循环条件则是通过比较sum值和目标值的大小，内层共有三个步骤：
    - **计算**：计算当前子序列长度
    - **比较**：当前子序列长度和记录长度比较取较小值
    - **更新指针**：更新头指针head令其向后滑动
- 时间复杂度：$**O(n)**$  ||   空间复杂度：$**O(1)**$

**代码如下**

```cpp
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int res = INT32_MAX; //将存放结果的变量设最大值
        int head = 0;//滑动窗口的开始位置
        int sum =0;//存放当前窗口中元素的和
        int length = 0; //当前滑动窗口的长度
        for(int end=0;end<nums.size();end++)//end存放滑动窗口的终止位置
        {
            sum+=nums[end]; 
            while(sum>=target) //通过比较当前sum的值来控制头指针的更i更新,不断判断子序列是否符合条件
            {
                length = (end-head+1);//子序列长度
                res = res<length? res : length; //取子序列长度最小值
                sum -= nums[head++]; //滑动窗口的头指针向后滑动,并且更新相应sum值
            }
        }
        return res == INT32_MAX? 0 : res; //如果res的值有更新说明有符合要求的子序列，否则输出0
    }
};
```

**二刷代码如下**

```cpp
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int sum = 0;
        int ans = INT32_MAX;  //将存放结果的变量设最大值
        for(int i = 0,j = 0;i < nums.size();i++){ //设置两个指针,一个遍历数组,另一个寻找最小子集
            sum += nums[i];
            while(sum >= target){ // 这里必须是<=,否则无法处理当子数组的总和刚好为target的情况
                ans = min(ans,i - j + 1); // 保存符合要求的子集长度
                sum -= nums[j++]; // 尝试移动头部指针来寻找最小子集
            }
        }
        return ans == INT32_MAX? 0 : ans; // 需要考虑不存在符合要求的数组时return 0的情况
    }
};
```

## 59.螺旋矩阵II

<aside>
💡 给定一个正整数 n，生成一个包含 1 到 n^2 所有元素，且元素按顺时针顺序螺旋排列的正方形矩阵。

</aside>

> March 7, 2024 二刷
> 

### 思路

- 重点在于坚持**循环不变量原则（左闭右开or左闭右闭），**本题适合采用全程左闭右开的原则，这样一圈才能按照统一的规则画下来
    
    ![20220922102236.png](pic3.png)
    

### 按层循环

- 用vector容器定义n阶二维数组：`vector<vector<int>> res(n, vector<int>(n, 0));`
- 这里有几个重要的值需要搞懂
    - while循环内的条件判断表示总共转几圈，推理归纳后可得共转n/2圈，也可以从另一个角度来理解：每转一圈扫描两行，共有n行，所以是n/2圈
    - 如果n为奇数，则最终必有一个单独的空需要填，这里单独讨论即可
    - 想明白转到某条边时如何确定数组下标的变化模式——把握一变一不变
        - 变的对应**[j]**或者**[n-i-j]**
        - 不变的对应**[i]**或者**[n-i-1]**

**代码如下**

```cpp
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        vector<vector<int>> mat(n,vector<int>(n,0)); //初始化二维数组
        int k = 1;
        for(int i=0;i<n/2;i++){ //共循环n/2圈
            for(int j = i; j<n-i-1;j++){ //向右→ 行不变对应i,列改变对应j
                mat[i][j] = k++;
            }
            for(int j = i;j<n-i-1;j++){ //向下↓ 列不变对应n-i-1,行改变对应j
                mat[j][n-i-1]=k++;
            }
            for(int j = i;j<n-i-1;j++){ //向左← 行不变对应n-i-1,列改变对应n-j-1
                mat[n-i-1][n-j-1]=k++;
            }
            for(int j = i;j<n-i-1;j++){ //向上↑ 列不变对应i,行改变对应n-j-1
                mat[n-j-1][i]=k++;
            }
        }
        if(n%2==1){ //边长为奇数时最中间的元素单独填
            mat[n/2][n/2]=k;
        }
        return mat;
    }
};
```

### 模拟螺旋矩阵✨

- 初始化一个n*n大小的矩阵mat，模拟整个向内环绕的填入过程：
    - 定义当前上下左右边界`top,bottom,left,right` ，初始值`k = 1` ，迭代终止值 `tar = n * n`
    - 当 `k <= tar` 时，始终按照 `**从左到右` `从上到下` `从右到左` `从下到上`** 填入顺序循环，每次填入后：
        - 执行`k++`，得到下一个需要填入的数字
        - 更新边界：例如从左到右填完后，上边界`top++`，相当于上边界向内缩1
    - 使用`k <= tar`而不是`l < r || t < b`作为迭代条件，是为了解决当`n`为奇数时，矩阵中心数字无法在迭代过程中被填充的问题。
        
        ![ccff416fa39887c938d36fec8e490e1861813d3bba7836eda941426f13420759-Picture1.png](pic4.png)
        

**代码如下**

```cpp
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        int top = 0,left = 0,bottom = n-1,right = n-1;
        int k = 1;
        vector<vector<int>> matrix(n,vector<int> (n,0));
        while(k<=n*n){
            // 向右→ 行恒为top,列从left到right
            for(int i = left;i <= right;i++) matrix[top][i] = k++;
            top++; //缩小上边界
            // 向下↓ 列恒为right,行从top到bottom
            for(int i = top;i <= bottom;i++) matrix[i][right] = k++;
            right--; //缩小右边界
            // 向左← 行恒为bottom,列从right到left
            for(int i = right;i >= left;i--) matrix[bottom][i] = k++;
            bottom--; //缩小下边界
            // 向上↑ 列恒为left,行从bottom到top
            for(int i = bottom;i >= top;i--) matrix[i][left] = k++;
            left++; //缩小左边界
        }
        return matrix;
    }
};
```