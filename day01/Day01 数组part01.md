# Day01 数组part01

### 任务URL：[第一章 数组part01 (qq.com)](https://docs.qq.com/doc/DUG9UR2ZUc3BjRUdY)

- **数组理论基础**
    
    [🔗文章链接](https://programmercarl.com/%E6%95%B0%E7%BB%84%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%80.html)
    
    **题目建议**： 了解一下数组基础，以及数组的内存空间地址，数组也没那么简单。
    
- **704. 二分查找**
    
    **题目建议**： 大家能把 704 掌握就可以，35.搜索插入位置 和 34. 在排序数组中查找元素的第一个和最后一个位置 ，如果有时间就去看一下，没时间可以先不看，二刷的时候在看。
    
    先把 704写熟练，要**熟悉 根据 左闭右开，左闭右闭 两种区间规则 写出来的二分法**。
    
    [📃力扣题目链接](https://leetcode.cn/problems/binary-search/)
    
    [🤓文章讲解](https://programmercarl.com/0704.%E4%BA%8C%E5%88%86%E6%9F%A5%E6%89%BE.html)
    
    [📽️视频讲解](https://www.bilibili.com/video/BV1fA4y1o715)
    
- **27. 移除元素**
    
    **题目建议**：  暴力的解法，可以锻炼一下我们的代码实现能力，建议先把暴力写法写一遍。 **双指针法 是本题的精髓，今日需要掌握**，至于拓展题目可以先不看。
    
    [📃题目链接](https://leetcode.cn/problems/remove-element/)
    
    [🤓文章讲解](https://programmercarl.com/0027.%E7%A7%BB%E9%99%A4%E5%85%83%E7%B4%A0.html)
    
    [📽️视频讲解](https://www.bilibili.com/video/BV12A4y1Z7LP)
    

## 数组理论基础

- **`数组`**是存放在【**连续内存**】空间上的相同类型数据的集合。
    - 数组**下标**都是从0开始
    - 数组**内存空间**的地址是连续的
    - 数组元素**不能直接删除**，只能通过覆盖或者移动后面元素的方式来删除某个元素
- **`数组<array>`**与**`容器<vector>`**的区别在于数组是**静态大小**的、**连续存储**的数据结构，而容器是**动态大小**的、可自动调整大小的数据结构。
    - 因此在**删除或增加`数组`元素**时，必须移动其他元素的地址以保持它们的相对顺序，会**增加开销**。
    - **`动态数组`**或**`容器<vector>`**不需要连续的内存块，因此在插入或删除元素时，不会导致其他元素的地址移动，从而提供了更高的**灵活性和效率**。
- **`二维数组`**在内存的空间地址管理与编程语言有关
    - **C++**的二维数组空间地址**连续分布**
        
        ![pic.png](pic1.png)
        
    - **JAVA**的二维数组则是***非连续分布***，因为JAVA没有指针且不对程序员暴露其元素地址，寻址操作完全交给虚拟机
        
        ![20201214111631844.png](pic2.png)
        

## 二分查找

<aside>
💡 给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。

</aside>

- 首先要确保前提是数组【***有序***】且【***无重复元素***】，一旦有重复元素，返回的下标就失去了唯一性，所以只有题目满足上述条件的时候，就可以考虑是不是可以用二分法了。
- 对于区间的判断要从一而终，确定好**区间定义**，后面的代码遇到条件判断和与区间端点有关的语句时就要进行灵魂拷问，想一下是否符合定义的区间。（左闭右闭/左闭右开）

### 第一种写法：左闭右闭[left,right]

- 外层的`while (left <= right)` 要使用 <= ，因为**left == right时有意义**（比如区间[1,1]）
- 内层的`if (nums[mid] > target)` ：right 要赋值为 **mid - 1**，因为当前这个nums[mid]一定不是target，那么接下来要查找的区间为[left,mid-1]
- 每次循环伊始要记得用变量num来保存当前的nums[mid]，可以减少后面比较时的时间开销
- 计算中值mid时用“ `(right-left)/2+left` ”来代替 “ `(left+right)/2` ” ，防止溢出
- 时间复杂度：**O(log n)** ||  空间复杂度：**O(1)**

![20210311153055723.jpg](pic3.jpg)

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0,right = nums.size()-1; //左闭右闭
        int mid;
        while(left<=right){ //这里要用<=,因为left==right有意义
            mid = (right - left) / 2 + left; //防止溢出
            if(target == nums[mid]){
                return mid;
            }
            else if(target>nums[mid]){ //目标值在mid右侧区域内,修改下边界left
                left = mid + 1; //此时mid所在位置的元素已经可以排除
            }
            else{ //修改上边界right
                right = mid - 1; //同上
            }
        }
        return -1;
    }
};
```

### 第二种写法：左闭右开[left,right)

- 外层的`while (left < right)` 要使用 < ，因为left == right没有意义，所以使用 <
- 内层的`if (nums[mid] > target)` ：right 要赋值为 mid，因为当前这个nums[mid]不是target，要去左区间继续寻找，而接下来要查找的区间为[left,mid)，所以right更新为mid即可
- 与第一种写法的不同主要体现在两个循环的区间定义上，要多加练习体会开闭区间的不同
- 时间复杂度：**O(log n)** ||  空间复杂度：**O(1)**

![20210311153123632.jpg](pic4.jpg)

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0,right = nums.size();
        while(left<right) //左闭右开，left==right无意义
        {
            int mid = (right - left) / 2 + left; //防止溢出，等价于(left+right)/2
            int num = nums[mid];  //将nums[mid]保存为int型变量再与target比较大小，效率更高
            if(num == target) return mid;
            else if(num > target) right = mid; //左闭右开时，target在[left,mid)中
            else left = mid + 1;  //target在[mid+1,right)中
        }
        return -1;
    }
};
```

### 总结反思：

- 写代码时一定要对于区间的开闭情况进行细致的探究，多写多思考，唯手熟尔。
- 要注意返回值的处理，当目标元素不存在时要返回-1（约定俗成）

## 移除元素

<aside>
💡 给你一个数组 nums 和一个值 val，你需要 **原地** 移除所有数值等于 val 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并**原地**修改输入数组。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

</aside>

### 排序法

### 双指针法

双指针法（快慢指针法）： **通过一个快指针和慢指针在一个for循环下完成两个for循环的工作。**

- 快指针：遍历寻找新数组的元素 ，新数组就是不含有目标元素的数组
- 慢指针：指向更新 新数组下标的位置，数值等于新数组长度
    
    ![27.移除元素-双指针法.gif](pic5.gif)
    

代码如下：

```cpp
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int len=0,pointer=0; //len记录新数组长度，pointer用来扫描数组元素
        for(int i = 0;i<nums.size();i++)
        {
            if (nums[i] !=val){
                nums[len++]=nums[i]; //每次记录有用的数据后len都要指向下一个位置
            }
        }
        return len;
    }
};
```

- 可以改进的部分：内层的if循环可以直接令判断(nums[i] ! =val)然后进行元素的赋值和指针位移，判断“num[i]==val”显得多此一举。
- 时间复杂度：O(n) ||  空间复杂度：O(1)

## 其他

- 学习了如何在Visual Studio上调用自己的测试案例，主要是vector的相关知识点

```cpp
int main() {
    vector<int> nums = {-5, 1, 3, 7, 11, 14};
    int target = 5;
    Solution solution;
    cout << solution.search(nums, target) << endl;
    system("pause");
}
```

- 力扣网快捷键
    - 编译: `**Ctrl + ’**`
    - 提交: `**Ctrl + Enter**`
    - 注释: **`Ctrl + ？`**

## **GitHub遇到的问题&解决方案**

**✨git常用指令：**

```bash
***基础指令***
git clone //克隆
git init //将当前路径文件夹初始化为git仓库
git add -A //全部暂存
git commit -m "提交信息"  //提交信息为备注
git log --stat  //查看提交历史
git checkout <filename>  //未提交状态回滚
git reset HEAD^ //提交后回滚
***分支***
git checkout -b <branchname> //从当前节点新建分支
git branch // 列举所有的分支
git checkout <branchname> //单纯地切换到某个分支
git branch -D/-d <branchname> //（强制）删掉特定的分支
git merge <branchname> //合并分支 
git merge --abort //放弃合并
***Git与Github远程仓库***
git push //推送当前分支最新版本提交到远程
git pull //拉取远程分支最新的提交到本地
```

**🆘使用"git add"命令提示"warning: LF will be replaced by CRLF"❓**

- 问题分析：在文本处理中，CR (CarriageReturn)、 LF (LineFeed)、 CR/LF 是不同操作系统上使用的换行符
    - 回车符就是回到一行的开头，用符号r表示，十进制ASCII代码是13，十六进制代码为0x0D，回车（return）；
    - 换行符就是另起一行，用n符号表示，ASCII代码是10，十六制为0x0A， 换行（newline）
- 应用情况
    - Dos和Windows平台： 使用回车（CR）和换行（LF）两个字符来结束一行，回车+换行(CR+LF)，即“\r\n”；
    - Mac 和 Linux平台：只使用换行（LF）一个字符来结束一行，即“\n”；
    - 最早Mac每行结尾是回车CR 即'\r'，后mac os x 也投奔了 unix。
- 解决方法
    - Git 可以在你提交时自动地把回车（CR）和换行（LF）转换成换行（LF），而在检出代码时把换行（LF）转换成回车（CR）和换行（LF）。
        
        ```bash
        #提交时转换为LF，检出时转换为CRLF
        $ git config --global core.autocrlf true
        ```
        
- ✅大功告成

**🆘使用"git push"命令提示"Failed to connect to github.com port 443 : Timed out"❓**

- 问题分析：Git 所设端口与系统代理不一致，需重新设置
- 解决方法：将Git的网络设置修改为系统当前对应的IP和端口号
    - 打开 设置 --> 网络与Internet --> 查找代理
    - 记录下当前系统代理的 IP 地址和端口号
    - 修改Git的网络设置
        
        ```bash
        # 注意修改成自己的IP和端口号
        git config --global http.proxy http://127.0.0.1:7890
        git config --global https.proxy http://127.0.0.1:7890
        ```
        
- ✅大功告成