题003 二维数组中的查找

复杂问题从具体问题入手，分析简单例子，找出规律

在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

**示例:**

现有矩阵 matrix 如下：

```
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```

给定 target = `5`，返回 `true`。

给定 target = `20`，返回 `false`。

**代码：**

```
public boolean findNumberIn2DArray(int[][] matrix, int target) {
    
        
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return false;
        }
        
        int rowLength = matrix.length; //总行数
        int colLength = matrix[0].length; //总列数
        
        int currentRow = 0; //起始位置是右上角 行号是0
        int currentCol = colLength - 1; //起始位置是右上角 列号最大值
        
        while (currentRow < rowLength && currentCol >= 0) {//防止越界
            if (matrix[currentRow][currentCol] == target) {
                return true;
            } else if (matrix[currentRow][currentCol] > target)//比要找的数大 排除这一列
            {
                currentCol--;
            } else { //比要找的数小 排除更小的数  排除这一行
                currentRow++;
            }
        }
        return false;
        
    }
```



**复杂度分析**

时间复杂度：O(n+m)。访问到的下标的行最多增加 n 次，列最多减少 m 次，因此循环体最多执行 n + m 次。

### 题004 替换空格

从头到尾扫描字符串碰到空格替换，  需要空格后面的·字符都移动移动字符串     时间复杂度 O(n^2)

**优化方案：**

优化的方法是先遍历一编字符串，知道字符串的空格数，然后计算得到替换后的长度=原来长度+2*空格数，然后从字符串的末尾进行遍历，每次把元素移动到计算后的数组下标的位置，并且对空格进行替换。

**代码：**

```
    public String replaceSpace(String s) {
        int cnt = 0;// 空格数
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == ' ') {
                cnt++;
            }
        }
        int oldLength = s.length();
        int newLength = s.length() + 2 * cnt;//新字符串长度
        StringBuilder str = new StringBuilder();
        str.setLength(newLength);
        int newIndex = newLength - 1;
        for (int i = oldLength - 1; i >= 0; i--) {//倒序遍历
            if (s.charAt(i) != ' ') {
                str.setCharAt(newIndex, s.charAt(i));
                newIndex--;
            } else {
                str.setCharAt(newIndex, '0');
                str.setCharAt(newIndex - 1, '2');
                str.setCharAt(newIndex - 2, '%');
                newIndex = newIndex - 3;
            }
        }
        return str.toString();
    }
```

注意内存覆盖

合并两个数组包括字符串时，如果从前往后复制每个数字需要重复移动数字多次，可以考虑从后往前复制，减少移动次数

### 题005 从尾到头打印链表

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

 

**示例 1：**

```
输入：head = [1,3,2]
输出：[2,3,1]
```

```
    public int[] reversePrint(ListNode head) {
        Stack<ListNode> stack = new Stack<>();
        ListNode dummyHead = head;
        while (dummyHead != null) {
            stack.push(dummyHead);
            dummyHead = dummyHead.next;
        }
        int size = stack.size();
        int[] result = new int[size];
        for (int i = 0; i < size; i++) {
            result[i] = stack.pop().val;
        }
        return result;
    }
```

### 题006重建二叉树

二叉树的前序遍历顺序是：根节点、左子树、右子树，每个子树的遍历顺序同样满足前序遍历顺序。

二叉树的中序遍历顺序是：左子树、根节点、右子树，每个子树的遍历顺序同样满足中序遍历顺序。

**方法：分治思想**

二叉树的问题一般都是分治思想，递归去做。因为二叉树本身就是递归实现的

解题思路：

前序遍历的第 1 个结点一定是二叉树的根结点；
在中序遍历中，根结点把中序遍历序列分成了两个部分，左边部分构成了二叉树的根结点的左子树，右边部分构成了二叉树的根结点的右子树。
查找根结点在中序遍历序列中的位置，可以遍历，也可以在一开始就记录下来。

![image-20200724155209421](..\images\image-20200724155209421.png)

```
public class Solution {

    // 使用全局变量是为了让递归方法少传一些参数，不一定非要这么做

    private Map<Integer, Integer> reverses;
    private int[] preorder;

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        int preLen = preorder.length;
        int inLen = inorder.length;

        // 可以不做判断，因为题目中给出的数据都是有效的
        if (preLen != inLen) {
            return null;
        }

        this.preorder = preorder;

        // 以空间换时间，否则，找根结点在中序遍历中的位置需要遍历
        reverses = new HashMap<>(inLen);
        for (int i = 0; i < inLen; i++) {
            reverses.put(inorder[i], i);
        }

        return buildTree(0, preLen - 1, 0, inLen - 1);
    }

    /**
     * 根据前序遍历数组的 [preL, preR] 和 中序遍历数组的 [inL, inR] 重新组建二叉树
     *
     * @param preL 前序遍历数组的区间左端点
     * @param preR 前序遍历数组的区间右端点
     * @param inL  中序遍历数组的区间左端点
     * @param inR  中序遍历数组的区间右端点
     * @return 构建的新二叉树的根结点
     */
    private TreeNode buildTree(int preL, int preR,
                               int inL, int inR) {
        if (preL > preR || inL > inR) {
            return null;
        }
        // 构建的新二叉树的根结点一定是前序遍历数组的第 1 个元素
        int pivot = preorder[preL];
        TreeNode root = new TreeNode(pivot);

        int pivotIndex = reverses.get(pivot);

        // 这一步得画草稿，计算边界的取值，必要时需要解方程，并不难
        root.left = buildTree(preL + 1, preL + (pivotIndex - inL), inL, pivotIndex - 1);
        root.right = buildTree(preL + (pivotIndex - inL) + 1, preR, pivotIndex + 1, inR);
        return root;
    }
}
```

###  题007两个栈实现队列

现在栈的实现最好就是用Deque<Integer> stack = new LinkedList<>()

Stack性能不高

- 所有操作都是同步的（当然，在题目的单线程场景下，锁可能会被优化掉）
- 内部用数组来装元素，所以需要扩容，但是“栈”的读写操作都是在栈顶，不需要数组的随机访问，所以用链表来实现栈更合适

```
class CQueue {
    
    Deque<Integer> stack1;
    Deque<Integer> stack2;
    
    public CQueue() {
        stack1 = new LinkedList<Integer>();
        stack2 = new LinkedList<Integer>();
        
    }
    
    public void appendTail(int value) {
        stack1.push(value);
    }
    
    public int deleteHead() {
        
        if (stack2.isEmpty()) {
            while (!stack1.isEmpty()) {
                stack2.push(stack1.pop());
            }
        }
        
        if (stack2.isEmpty()) {
            return -1;
        } else {
            return stack2.pop();
        }
    }
}
```

### 题008旋转数组的最小数字

有序数组查找  优先考虑二分法（顺序查找  元素有序）

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个递增排序的数组的一个旋转，输出旋转数组的最小元素。例如，数组 [3,4,5,1,2] 为 [1,2,3,4,5] 的一个旋转，该数组的最小值为1。  

示例 1：

```
输入：[3,4,5,1,2]
输出：1
```

**思路：**

![](https://pic.leetcode-cn.com/5884538fb9541a31a807d59c81226ded3dcd61df66efcdeb000165036ea68bb9-Picture1.png)

**代码：**

```
    public int minArray(int[] numbers) {
        int low = 0;
        int height = numbers.length - 1;
        while (low < height) {
            int mid = low + (height - low) / 2;  //不适用（low+height）/2 防止溢出
            if (numbers[mid] < numbers[height]) { //mid一定在右序列 旋转点一定在[left，mid]之间
                height = mid;
            } else if (numbers[mid] > numbers[height]) { //mid一定在左序列，旋转点一定在[mid+1,height]之间
                low = mid + 1;
            } else { //nums[l] == nums[m] == nums[h]，此时无法确定解在哪个区间，需要切换到顺序查找
                height--;
            }
        }
        return numbers[low];
    }
```

### 题009-1斐波那契数列

写一个函数，输入 n ，求斐波那契（Fibonacci）数列的第 n 项。斐波那契数列的定义如下：

F(0) = 0,   F(1) = 1
F(N) = F(N - 1) + F(N - 2), 其中 N > 1.
斐波那契数列由 0 和 1 开始，之后的斐波那契数就是由之前的两数相加而得出。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

递归

```
int Fibonacci(int n) {
    if (n == 0){
        return 0;
    } else if (n==1) {
        return 1;
    }
    return Fibonacci(n-1) + Fibonacci(n-2);
}
```

递归是将一个问题划分成多个子问题求解，动态规划也是如此，但是动态规划会把子问题的解缓存起来，从而避免重复求解子问题。

```
public int Fibonacci(int n) {
    if (n <= 1)
        return n;
    int[] fib = new int[n + 1];
    fib[1] = 1;
    for (int i = 2; i <= n; i++)
        fib[i] = fib[i - 1] + fib[i - 2];
    return fib[n];
}
```

考虑到第 i 项只与第 i-1 和第 i-2 项有关，因此只需要存储前两项的值就能求解第 i 项，从而将空间复杂度由 O(N) 降低为 O(1)。

```
    public int fib(int n) {
        if (n <= 1) {
            return n;
        }
        int a1 = 0, a2 = 1, sum = 0;
        for (int i = 2; i <= n; i++) {
            sum = (a1 + a2) % 1000000007;
            a1 = a2;
            a2 = sum;
        }
        return sum;
    }
```

### 题009-2青瓜跳台阶问题

一只青蛙一次可以跳上1级台阶，也可以跳上2级台阶。求该青蛙跳上一个 n 级的台阶总共有多少种跳法。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

示例 1：

输入：n = 2
输出：2

```
    public int numWays(int n) {
        if (n == 0 || n == 1) {
            return 1;
        }
        int[] fib = new int[n + 1];
        fib[1] = 1;
        fib[2] = 2;
        for (int i = 3; i <= n; i++) {
            fib[i] = (fib[i - 1] + fib[i - 2]) % 1000_000_007;
        }
        return fib[n];
    }
```

### 题010二进制中1的个数

请实现一个函数，输入一个整数，输出该数二进制表示中 1 的个数。例如，把 9 表示成二进制是 1001，有 2 位是 1。因此，如果输入 9，则该函数输出 2。

示例 1：

```
输入：00000000000000000000000000001011
输出：3
解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为 '1'。
```

解法一：

```
    public int hammingWeight(int n) {
        int count = 0;
        while (n != 0) {
            count += n & 1;  //判断 nn 最右一位是否为 11 ，根据结果计数。
            n >>>= 1;    //本题要求把数字 nn 看作无符号数，因此使用 无符号右移 操作
        }
        return count;
    }
```

解法二：巧用 n \& (n - 1)

(n−1) 解析： 二进制数字 nn 最右边的 11 变成 00 ，此 11 右边的 00 都变成 11 。
n \& (n - 1)n&(n−1) 解析： 二进制数字 nn 最右边的 11 变成 00 ，其余不变。

![Picture10.png](https://pic.leetcode-cn.com/9bc8ab7ba242888d5291770d35ef749ae76ee2f1a51d31d729324755fc4b1b1c-Picture10.png)

```
    public int hammingWeight(int n) {
        int res = 0;
        while(n != 0) {
            res++;
            n &= n - 1;  //循环消去最右边的 11 ：当 n = 0n=0 时跳出
        }
        return res;
    }
```

### 题011数值的整数次方

实现函数double Power(double base, int exponent)，求base的exponent次方。不得使用库函数，同时不需要考虑大数问题。

示例 1:

```
输入: 2.00000, 10
输出: 1024.00000
```

解法：二分法思想

当我们指数是32，我们需要最31次乘法。 但我们可以换一种思路。 我们可以知道它的16次方，然后再16次方的基础上平方一次就行。而16是8平方的平方，依次递推

![](..\images\image-20200729153048891.png) 

![image-20200729154321429](..\images\image-20200729154321429.png)

```
    public double myPow(double x, int n) {
        if (x == 0) {
            return 0;  //0无意义
        }
        long b = n;   //Java 代码中 int32 当 n = -2147483648n=−2147483648 时执行 n = -n 会因越界而赋值出错。
        double res = 1.0;
        if (b < 0) {
            x = 1 / x;
            b = -b;
        }
        while (b > 0) {
            if ((b & 1) == 1) {  // n%2==1
                res = res * x;  // n是奇数
            }
            x = x * x;
            b = b >> 1;  // n/2
        }
        return res;
    }
```

### 题012打印从1到最大的n位数

输入数字 n，按顺序打印出从 1 到最大的 n 位十进制数。比如输入 3，则打印出 1、2、3 一直到最大的 3 位数 999。

示例 1:

```
输入: n = 1
输出: [1,2,3,4,5,6,7,8,9]
```

大数打印解法：

1. 表示大数的变量类型

无论是 short / int / long ... 任意变量类型，数字的取值范围都是有限的。因此，大数的表示应用字符串 String 类型。

2.生成数字的字符串集：

String 无法加1

3.递归生成全排列

基于分治算法思想，固定高位向低位递归

![image-20200730142920687](..\images\image-20200730142920687.png)

```
int[] res;

int  n,count = 0;

char[] num, loop = { '0','1', '2', '3', '4', '5', '6', '7', '8', '9'};

public int[] printNumbers(int n) {
    this.n = n;
    res = new int[(int)Math.pow(10, n) - 1];; // 数字字符串集
    num = new char[n]; // 定义长度为 n 的字符列表
    dfs(0); // 开启全排列递归
    return res;
}

void dfs(int x) {
    if (x == n) { // 终止条件：已固定完所有位
        Integer num2 = Integer.valueOf(String.valueOf(num));
        if (num2 != 0) {
            res[count++] = num2;; // 拼接 num 并添加至 res 尾部，使用逗号隔开
        }
        return;
    }
    for (char i : loop) { // 遍历 ‘0‘ - ’9‘
        num[x] = i; // 固定第 x 位为 i
        dfs(x + 1); // 开启固定第 x + 1 位
    }
}
```

### 题013删除链表的节点

给定单向链表的头指针和一个要删除的节点的值，定义一个函数删除该节点。

返回删除后的链表的头节点。

注意：此题对比原题有改动

示例 1:

```
输入: head = [4,5,1,9], val = 5
输出: [4,1,9]
解释: 给定你链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9.
```

```
public ListNode deleteNode(ListNode head, int val) {
    if (head == null) {
        return null;
    }
    if (head.val == val) {
        return head.next;
    }
    ListNode cur = head;
    while (cur.next != null && cur.next.val != val) {
        cur = cur.next;
    }
    if (cur.next != null) {
        cur.next = cur.next.next;
    }
    return head;
}
```

### 题014调整数组顺序使奇数位于偶数前面

输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有奇数位于数组的前半部分，所有偶数位于数组的后半部分。

 

示例：

```
输入：nums = [1,2,3,4]
输出：[1,3,2,4] 
注：[3,1,2,4] 也是正确的答案之一。
```

**首尾双指针**

- 定义头指针 left，尾指针 right

- left 一直往右移，直到它指向的值为偶数

- right 一直往左移， 直到它指向的值为奇数、

- 交换 nums[left和nums[right] .

- 重复上述操作，直到 left == right .

  ```
  public int[] exchange(int[] nums) {
          int left = 0;
          int right = nums.length - 1;
          while (left < right) {
              if ((nums[left] & 1) != 0) {
                  left++;
                  continue;
              }
              if ((nums[right] & 1) != 1) {
                  right--;
                  continue;
              }
              int temp = nums[left];
              nums[left] = nums[right];
              nums[right] = temp;
              left++;
              right--;
          }
          return nums;
      }
  ```

  

**快慢双指针**

- 定义快慢双指针 fast和 low ，fast 在前， low 在后 .
- fast 的作用是向前搜索奇数位置，low的作用是指向下一个奇数应当存放的位置
- fast 向前移动，当它搜索到奇数时，将它和 nums[low] 交换，此时 low 向前移动一个位置 .
- 重复上述操作，直到 fast指向数组末尾 .

```
public int[] exchange(int[] nums) {
    int low = 0,fast = 0;
    while (fast < nums.length) {
        if ((nums[fast] & 1) == 1) {
            int temp = nums[low];
            nums[low] = nums[fast];
            nums[fast] = temp;
            low++;   //交换之后low才向前移动
        }
        fast++;
    }
    return nums;
}
```

### 题015链表中倒数第k个节点

输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。例如，一个链表有6个节点，从头节点开始，它们的值依次是1、2、3、4、5、6。这个链表的倒数第3个节点是值为4的节点。

示例：

```
给定一个链表: 1->2->3->4->5, 和 k = 2.

返回链表 4->5.
```

**前后指针**

前指针先向前走k步，然后前后指针共同移动，直到前指针走过链表尾节点时跳出  返回后指针

时间复杂度O(N)

```
public ListNode getKthFromEnd(ListNode head, int k) {
    ListNode front = head, back = head;
    for (int i = 0; i < k; i++) {
        front = front.next;
    }
    while (front != null) {
        front = front.next;
        back = back.next;
    }
    return back;
}
```

### 题016反转链表

定义一个函数，输入一个链表的头节点，反转该链表并输出反转后链表的头节点。

示例:

```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

**双指针**

![](https://pic.leetcode-cn.com/9ce26a709147ad9ce6152d604efc1cc19a33dc5d467ed2aae5bc68463fdd2888.gif)

```
    public ListNode reverseList(ListNode head) {
        ListNode cur = null, pre = head;
        while (pre != null) {
            ListNode temp = pre.next;  //由于指针变动 需要先记着节点
            pre.next = cur;
            cur = pre;
            pre = temp;
        }
        return cur;
    }
```

**递归**

- 使用递归函数，一直递归到链表的最后一个结点，该结点就是反转后的头结点，记作 ret.
- 此后，每次函数在返回的过程中，让当前结点的下一个结点的 next 指针指向当前节点。
- 同时让当前结点的 next 指针指向 NULL ，从而实现从链表尾部开始的局部反转
- 当递归函数全部出栈后，链表反转完成。

![](https://pic.leetcode-cn.com/8951bc3b8b7eb4da2a46063c1bb96932e7a69910c0a93d973bd8aa5517e59fc8.gif)

```
public ListNode reverseList(ListNode head) {
    if (head == null || head.next == null) {
        return head;
    }
    ListNode ret = reverseList(head.next);
    head.next.next = head;
    head.next = null;
    return ret;
}
```

### 题017合并两个排序的链表

输入两个递增排序的链表，合并这两个链表并使新链表中的节点仍然是递增排序的。

示例1：

```
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```

```
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    ListNode head = new ListNode(0), cur = head; //由于初始状态链表无节点，引入伪节点
    while (l1 != null && l2 != null) {  //当某个列表为空 跳出循环
        if (l1.val < l2.val) { //当L1的值小的时候 存入 L1指针后移
            cur.next = l1;
            l1 = l1.next;
        } else {  //l2小  l2存入  L2指针后移
            cur.next = l2;
            l2 = l2.next;
        }
        cur = cur.next;
    }
    cur.next = l1 != null ? l1 : l2;  // 跳出时有两种情况 即l1为空 或 l2为空
    return head.next;
}
```

### 题018树的子结构

输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)

B是A的子结构， 即 A中有出现和B相同的结构和节点值。

例如:
给定的树 A:

```
 	 3
    / \
   4   5
  / \
 1   2
```

给定的树 B：

```
   4 
  /
 1
```

返回 true，因为 B 与 A 的一个子树拥有相同的结构和节点值。

示例 1：

```
输入：A = [1,2,3], B = [3,1]
输出：false
```

示例 2：

```
输入：A = [3,4,5,1,2], B = [4,1]
输出：true
```

![image-20200731005926977](..\images\image-20200731005926977.png)

![](..\images\image-20200731005555080.png)

```
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        return (A != null && B != null) && (recur(A, B) || isSubStructure(A.left, B) || isSubStructure(A.right, B));
    }
    
    boolean recur(TreeNode A, TreeNode B) {
        if (B == null) {
            return true;
        }
        if (A == null || A.val != B.val) {
            return false;
        }
        return recur(A.left, B.left) && recur(A.right, B.right);
    }
```

### 题019二叉树的镜像

请完成一个函数，输入一个二叉树，该函数输出它的镜像。

例如输入：

     	 4
       /   \
      2     7
     / \   / \
    1   3 6   9

镜像输出：

     	  4
        /   \
      7      2
     / \   /  \
    9   6 3    1
示例 1：

```
输入：root = [4,2,7,1,3,6,9]
输出：[4,7,2,9,6,3,1]
```

**递归法**

```
public TreeNode mirrorTree(TreeNode root) {
    if (root == null) {
        return null;
    }
    TreeNode temp = root.left;  //缓存是因为 执行后 left值发生变化
    root.left = mirrorTree(root.right);
    root.right = mirrorTree(temp);
    return root;
}
```

### 题020 顺时针打印矩阵

输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字。

示例 1：

```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
```

示例 2：

```
输入：matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
输出：[1,2,3,4,8,12,11,10,9,5,6,7]
```

**解法：**

```
public int[] spiralOrder(int[][] matrix) {
    if (matrix.length == 0) {
        return new int[0];
    }
    //  矩阵 左、右、上、下 四个边界 l , r , t , b
    int l = 0, r = matrix[0].length - 1, t = 0, b = matrix.length - 1, x = 0;
    int[] res = new int[(r + 1) * (b + 1)];
    while (true) {
        for (int i = l; i <= r; i++) {
            res[x++] = matrix[t][i]; // 从左到右 上边界加一
        }
        if (++t > b) {
            break;
        }
        for (int i = t; i <= b; i++) {
            res[x++] = matrix[i][r]; // 从上到小   右边界减一
        }
        if (l > --r) {
            break;
        }
        for (int i = r; i >= l; i--) {
            res[x++] = matrix[b][i]; // 从右向左  下边界减一
        }
        if (t > --b) {
            break;
        }
        for (int i = b; i >= t; i--) {
            res[x++] = matrix[i][l]; // 从下向上  左边界加一
        }
        if (++l > r) {
            break;
        }
    }
    return res;
}
```

### 题021包含min的函数栈

定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的 min 函数在该栈中，调用 min、push 及 pop 的时间复杂度都是 O(1)。

示例:

```
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.min();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.min();   --> 返回 -2.
```

**辅助栈**

![image-20200731151100416](..\images\image-20200731151100416.png)

```
//leetcode submit region begin(Prohibit modification and deletion)
class MinStack {
    
    /**
     * initialize your data structure here.
     */
    Deque<Integer> A, B;
    
    public MinStack() {
        A = new LinkedList<>();
        B = new LinkedList<>();
    }
    
    public void push(int x) {
        A.push(x);
        if (B.isEmpty()|| B.peek() >= x) {  //当辅助栈为空 或者 辅助栈顶 大于等于入栈值 则入栈
            B.push(x);
        }
    }
    
    public void pop() {
        if (A.pop().equals(B.peek())) {  //当A的栈顶等于B的栈顶 B出栈
            B.pop();
        }
    }
    
    public int top() {
        return A.peek();
    }
    
    public int min() {
        return B.peek();
    }
}
```

### 题022栈的压入、弹出序列

输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如，序列 {1,2,3,4,5} 是某栈的压栈序列，序列 {4,5,3,2,1} 是该压栈序列对应的一个弹出序列，但 {4,3,5,1,2} 就不可能是该压栈序列的弹出序列。

示例 1：

```
输入：pushed = [1,2,3,4,5], popped = [4,5,3,2,1]
输出：true
解释：我们可以按以下顺序执行：
push(1), push(2), push(3), push(4), pop() -> 4,
push(5), pop() -> 5, pop() -> 3, pop() -> 2, pop() -> 1
```

示例 2：

```
输入：pushed = [1,2,3,4,5], popped = [4,3,5,1,2]
输出：false
解释：1 不能在 2 之前弹出。
```

**解法：** 辅助栈

给定弹入和弹出顺序，则压入和弹出的顺序是唯一确定的

- 元素 num入栈；
- 循环出栈：若 stack 的栈顶元素 == 弹出序列元素 popped[i] ，则执行出栈与 i++ 

```
public boolean validateStackSequences(int[] pushed, int[] popped) {
    Stack<Integer> stack = new Stack<>();
    int i = 0;
    for (int num : pushed) {
        stack.push(num);
        // 循环判断 栈顶元素 == 弹出序列元素 则popped[i]
        while (!stack.isEmpty() && stack.peek() == popped[i]) {
            stack.pop();
            i++;
        }
    }
    return stack.isEmpty();
    
}
```

### 题023从上往下打印二叉树

从上到下打印出二叉树的每个节点，同一层的节点按照从左到右的顺序打印。

例如:
给定二叉树: [3,9,20,null,null,15,7],

```
    3

   / \
  9  20
    /  \
   15   7
```

返回：

```
[3,9,20,15,7]
```

**解法：BFS 循环  广度优先遍历**  

-  当队列 queue 为空时跳出；
- 出队： 队首元素出队，记为 node；
- 打印： 将 node.val 添加至列表 tmp 尾部；
- 添加子节点： 若 node 的左（右）子节点不为空，则将左（右）子节点加入队列 queue ；

```
public int[] levelOrder(TreeNode root) {
    if (root == null) {
        return new int[0];
    }
    Queue<TreeNode> queue = new LinkedList<>();  //linkedlist 继承deque双端队列接口，可以作为栈或者队列  Deque是Queue子接口
    queue.add(root);
    ArrayList<Integer> ans = new ArrayList<>();
    while (!queue.isEmpty()) {
        TreeNode node = queue.poll();
        ans.add(node.val);
        if (node.left != null) {
            queue.add(node.left);
        }
        if (node.right != null) {
            queue.add(node.right);
        }
    }
    int[] res = new int[ans.size()];
    for (int i = 0; i < ans.size(); i++) {
        res[i] = ans.get(i);
    }
    return res;
}
```

### 题024二叉搜索树得后序遍历

输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历结果。如果是则返回 true，否则返回 false。假设输入的数组的任意两个数字都互不相同。

参考以下这颗二叉搜索树：

```
     5
    / \

   2   6
  / \
 1   3
```

示例 1：

```
输入: [1,6,3,2,5]
输出: false
```

示例 2：

```
输入: [1,3,2,6,5]
输出: true
```

**定义：**

后续遍历：左  右 根

二叉搜索树：所有节点   左 < 根   < 右

**递归分治**

![image-20200801013207759](..\images\image-20200801013207759.png)

**终止条件**： 当 i ≥j ，说明此子树节点数量 ≤1 ，无需判别正确性，因此直接返回 true；
**递推工作**：

-   划分左右子树： 遍历后序遍历的 [i, j][i,j] 区间元素，寻找 第一个大于根节点 的节点，索引记为 m 。此时，可划分出左子树区间 [i,m-1][i,m−1] 、右子树区间 [m, j - 1][m,j−1] 、根节点索引 j 。
- 判断是否为二叉搜索树：
  **左子树区间** [i, m - 1][i,m−1] 内的所有节点都应 <postorder[j] 。而第 1.划分左右子树 步骤已经保证左子树区间的正确性，因此只需要判断右子树区间即可。
  **右子树区间** [m, j-1][m,j−1] 内的所有节点都应 >postorder[j] 。实现方式为遍历，当遇到≤postorder[j] 的节点则跳出；则可通过 p = j 判断是否为二叉搜索树。

**返回值**： 所有子树都需正确才可判定正确，因此使用 与逻辑符 \&& 连接。

- p = j ： 判断 此树 是否正确。
- recur(i, m - 1)： 判断 此树的左子树 是否正确。
- recur(m, j - 1)： 判断 此树的右子树 是否正确。

```
public boolean verifyPostorder(int[] postorder) {
    return recur(postorder, 0, postorder.length - 1);
}

boolean recur(int[] postorder, int i, int j) {
    if (i >= j) {   //节点数量<=1
        return true;
    }
    int p = i;
    while (postorder[p] < postorder[j]) {
        p++;
    }
    int m = p;   // 第一个大于根节点  可划分出左子树区间 [i,m-1][i,m−1] 、右子树区间 [m, j - 1][m,j−1] 、根节点索引 j 。
    while (postorder[p] > postorder[j]) {
        p++;    //如过满足二叉搜索树定义 则继续++ 到根节点
    }
    // 判断该树  左  右 树是否正确
    return p == j && recur(postorder, i, m - 1) && recur(postorder, m, j - 1);
}
```

### 题025二叉树中和为某一值的路径

输入一棵二叉树和一个整数，打印出二叉树中节点值的和为输入整数的所有路径。从树的根节点开始往下一直到叶节点所经过的节点形成一条路径。

示例:
给定如下二叉树，以及目标和 sum = 22，

              5
             / \
            4   8
           /   / \
          11  13  4
         /  \    / \
        7    2  5   1
返回:

```
[
   [5,4,11,2],
   [5,8,4,5]
]
```

**解法：DFS+ 回溯**

```
class Solution {
    
    LinkedList<List<Integer>> res = new LinkedList<>();
    LinkedList<Integer> path = new LinkedList<>();
    
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        recure(root, sum);
        return res;
    }
    
    void recure(TreeNode root, int sum) {
        if (root ==null ){  //结束条件
            return;
        }
        path.add(root.val);
        sum = sum - root.val;
        if (sum == 0 && root.left == null && root.right == null) {
            res.add(path);
        }
    
        recure(root.left, sum);
        recure(root.right, sum);
        path.removeLast();  //回溯把path元素移除
    }
}
```

### 题026复杂链表的复制

请实现 copyRandomList 函数，复制一个复杂链表。在复杂链表中，每个节点除了有一个 next 指针指向下一个节点，还有一个 random 指针指向链表中的任意节点或者 null。

示例 1：

![image-20200803024928397](..\images\image-20200803024928397.png)

输入：head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
输出：[[7,null],[13,0],[11,4],[10,2],[1,0]]

**解法：**

首先想到的第一步：   复制每个节点，让后用next连接起来  第二部 设置随机指针  需要从头部找

每个随即指针都需要O(n）  所以复杂度为O(n^)

![image-20200803032744416](..\images\image-20200803032744416.png)

1. 复制每一个节点，使得复制后的节点都在当前节点的下一个节点
2. 原生链表的节点的指向任意节点，使复制的节点也都指向某一任意节点
3. 重新连接节点，把原生节点重新连接起来，把克隆后的节点连接起来

```
class Solution {
    public Node copyRandomList(Node pHead) {
        if (pHead == null)
            return null;
        // 插入新节点
        Node cur = pHead;
        while (cur != null) {
            Node clone = new Node(cur.val);  //复制当前节点
            clone.next = cur.next;     //复制节点指向下一个节点
            cur.next = clone;       //当前节点指向复制节点
            cur = clone.next;   //当前节点 变为下个节点
        }
        // 建立 random 链接
        cur = pHead;
        while (cur != null) {
            Node clone = cur.next;
            if (cur.random != null)
                clone.random = cur.random.next;      
            cur = clone.next;   //当前节点 变为下个节点
        }
        // 拆分
        cur = pHead;
        Node pCloneHead = pHead.next;
        while (cur.next != null) {
            Node next = cur.next;
            cur.next = next.next;
            cur = next;
        }
        return pCloneHead;
    }
}
```

### 题027二叉搜索树与双向链表  

输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的循环双向链表。要求不能创建任何新的节点，只能调整树中节点指针的指向。

**解法：**

二叉搜索树得中序遍历为递增序列

![image-20200803172805477](..\images\image-20200803172805477.png)  

![image-20200803193055429](..\images\image-20200803193055429.png)

```
class Solution {
    
    Node head, pre;
    public Node treeToDoublyList(Node root) {
        if (root == null) {
            return null;
        }
        dfs(root);
        head.left = pre;    // 首尾指针指针相连
        pre.right = head;
        return head;
    }
    
    void dfs(Node cur) {
        if (cur == null) {
            return;
        }
        dfs(cur.left);
        if (pre != null) {
            pre.right = cur;
        } else {
            head = cur;
        }
        cur.left = pre;   // pre 是否为 null无影响
        pre = cur;  // pre指向当前cur
        dfs(cur.right);
    }
    
}
```

### 题028字符串得排列

输入一个字符串，打印出该字符串中字符的所有排列。

你可以以任意顺序返回这个字符串数组，但里面不能有重复元素。

示例:

```
输入：s = "abc"
输出：["abc","acb","bac","bca","cab","cba"]
```

```
class Solution {
    List<String> res = new LinkedList<>();
    char[] c;
    public String[] permutation(String s) {
        c = s.toCharArray();
        dfs(0);
        return res.toArray(new String[res.size()]);
    }
    void dfs(int x) {
        if(x == c.length - 1) {   //结束条件 达到第三层
            res.add(String.valueOf(c)); // 添加排列方案
            return;
        }
        HashSet<Character> set = new HashSet<>();
        for(int i = x; i < c.length; i++) {
            if(set.contains(c[i])) continue; // 重复，因此剪枝
            set.add(c[i]);
            swap(i, x); // 交换，将 c[i] 固定在第 x 位
            //返回时交换回来，这样保证到达第1层的时候，一直都是abc。这里捋顺一下，开始一直都是abc，那么第一位置总共就3个位置
            //分别是a与a交换，这个就相当于 x = 0, i = 0;
            //     a与b交换            x = 0, i = 1;
            //     a与c交换            x = 0, i = 2;
            //就相当于上图中开始的三条路径
            //第一个元素固定后，每个引出两条路径,
            //     b与b交换            x = 1, i = 1;
            //     b与c交换            x = 1, i = 2;
            //所以，结合上图，在每条路径上标注上i的值，就会非常容易好理解了
            dfs(x + 1); // 开启固定第 x + 1 位字符
            swap(i, x); // 恢复交换   回溯
        }
    }
    void swap(int a, int b) {
        char tmp = c[a];
        c[a] = c[b];
        c[b] = tmp;
    }
    
}
```

**举一反三**

按照一定要求摆放若干个数字，先求出这些数字的所有排列，然后再一一判断每个排列是否满足

> 复杂问题：  画图  举例子  分解 能够帮助解决复杂问题

### 题029 数组中出现次数超过一半的数字

数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。

你可以假设数组是非空的，并且给定的数组总是存在多数元素

**示例 1:**

```
输入: [1, 2, 3, 2, 2, 2, 5, 4, 2]
输出: 2
```

**解法一： 哈希表**

```
public int majorityElement(int[] nums) {
    if (nums.length == 1) {
        return nums[0];
    }
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        if (map.containsKey(nums[i])) {
            //如果已经存在key，将值+1
            int cnt = map.get(nums[i]) + 1;
            if ((cnt * 2) > nums.length) {
                return nums[i];
            }
            map.put(nums[i], cnt);
        } else {
            map.put(nums[i], 1);
        }
    }
    return 0;
}
```

**解法二：摩尔投票法**

若一个数字出现的次数超过数组长度的一半，则该数字减去其他数字个数必定大于0，根据这个原理，使用摩尔法投票法，记录一个数字，遍历数组，若是该数，票数加一，不是减一，为零下次出现什么都投一票，记录值变为下次出现的值。最后，记录中必定是出现过半的数字！

核心理念为 **“正负抵消”** ；时间和空间复杂度分别为 O(N) 和 O(1) 

```
public int majorityElement(int[] nums) {
    int x = 0, votes = 0;  //众数 统计票数
    for (int num : nums) {
        if (votes == 0) {
            x = num;  //如果票数为0 则当前数为众数
        }
        int vote = num == x ? 1 : -1;     //如果为众数,加一 否则减一
        votes = votes + vote;
    }
    return x;
}
```

### 题030最小的k个数

输入整数数组 arr ，找出其中最小的 k 个数。例如，输入4、5、1、6、2、7、3、8这8个数字，则最小的4个数字是1、2、3、4。

示例 1：

```
输入：arr = [3,2,1], k = 2
输出：[1,2] 或者 [2,1]
```

示例 2：

```
输入：arr = [0,1,2,1], k = 1
输出：[0]
```

**对于经典TopK问题，本文给出 4 种通用解决方案。**

**解法一：使用快排切分思想  当我们可以修改输入数组可用**     

不需要对整个数组进行他O(nlogn)的排序

时间复杂度O(n) 

```
public int[] getLeastNumbers(int[] arr, int k) {
    if (k == 0 || arr.length == 0) {
        return new int[0];
    }
    // 找前k个 最后一个下标为k -1   即基准的下标
    return quick(arr, 0, arr.length - 1, k - 1);
}

private static int[] quick(int[] arr, int left, int right, int k) {
    //如果k == j 就返回 j  及 j左边所有的数
    int j = partition(arr, left, right);
    if (k == j) {
        return Arrays.copyOf(arr, j + 1);
    }
    // 否则根据j  与 k的关系来决定 继续切分 左段  和 右端
    return j > k ? quick(arr, left, j - 1, k) : quick(arr, j + 1, right, k);
    
}

private static int partition(int[] a, int low, int high) {
    //取每个序列的第一个值作为基准值
    int pivotkey = a[low];
    while (low < high) {
        //从序列的右边开始往左遍历，直到找到小于基准值的元素
        while (high > low && a[high] >= pivotkey) {
            high--;
        }
        //将元素直接赋予给左边第一个，即pivotkey所在的位置
        a[low] = a[high];
        //从序列的左边边开始往右遍历，直到找到大于基准值的元素
        while (high > low && a[low] <= pivotkey) {
            low++;
        }
        //此时的a[high]<pivotkey,已经被赋予到左边，所以可以将元素赋予给a[high]
        a[high] = a[low];
    }
    //最后的low是基准值所在的位置
    a[low] = pivotkey;
    return low;
}
```

**解法二：堆 大根堆(前 K 小) / 小根堆（前 K 大) O(NlogK)   海量数据**

本题是求前 K 小，因此用一个容量为 K 的大根堆，每次 poll 出最大的数，那堆中保留的就是前 K 小啦（注意不是小根堆！小根堆的话需要把全部的元素都入堆，那是 O(NlogN)，就不是 O(NlogK)啦～～）

```
class Solution {
    
    // 保持堆的大小为K，然后遍历数组中的数字，遍历的时候做如下判断：
    // 1. 若目前堆的大小小于K，将当前数字放入堆中。
    // 2. 否则判断当前数字与大根堆堆顶元素的大小关系，如果当前数字比大根堆堆顶还大，这个数就直接跳过；
    //    反之如果当前数字比大根堆堆顶小，先poll掉堆顶，再将该数字放入堆中。
    public int[] getLeastNumbers(int[] arr, int k) {
        if (k == 0 || arr.length == 0) {
            return new int[0];
        }
        // 默认是小根堆，实现大根堆需要重写一下比较器。
        Queue<Integer> pq = new PriorityQueue<>((v1, v2) -> v2 - v1);
        for (int num: arr) {
            if (pq.size() < k) {
                pq.offer(num);
            } else if (num < pq.peek()) {
                pq.poll();
                pq.offer(num);
            }
        }
        
        // 返回堆中的元素
        int[] res = new int[pq.size()];
        int idx = 0;
        for(int num: pq) {
            res[idx++] = num;
        }
        return res;
    }
}
```

**解法三：二叉搜索树O(NlogK)  海量数据	**

与前两种方法相比，最大的好处是前k大的数字是有序的

因为有重复的数字，所以用的是 TreeMap 而不是 TreeSet

TreeMap的key 是数字，value 是该数字的个数。
我们遍历数组中的数字，维护一个数字总个数为 K 的 TreeMap：
1.若目前 map 中数字个数小于 K，则将 map 中当前数字对应的个数 +1；
2.否则，判断当前数字与 map 中最大的数字的大小关系：若当前数字大于等于 map 中的最大数字，就直接跳过该数字；若当前数字小于 map 中的最大数字，则将 map 中当前数字对应的个数 +1，并将 map 中最大数字对应的个数减 1

```
class Solution {
    
    public int[] getLeastNumbers(int[] arr, int k) {
        if (k == 0 || arr.length == 0) {
            return new int[0];
        }
        // TreeMap的key是数字, value是该数字的个数。
        // cnt表示当前map总共存了多少个数字。
        TreeMap<Integer, Integer> map = new TreeMap<>();
        int cnt = 0;
        for (int num : arr) {
            // 1. 遍历数组，若当前map中的数字个数小于k，则map中当前数字对应个数+1
            if (cnt < k) {
                map.put(num, map.getOrDefault(num, 0) + 1);
                cnt++;
                continue;
            }
            // 2. 否则，取出map中最大的Key（即最大的数字), 判断当前数字与map中最大数字的大小关系：
            //    若当前数字比map中最大的数字还大，就直接忽略；
            //    若当前数字比map中最大的数字小，则将当前数字加入map中，并将map中的最大数字的个数-1。
            Map.Entry<Integer, Integer> entry = map.lastEntry();
            if (entry.getKey() > num) {
                map.put(num, map.getOrDefault(num, 0) + 1);
                if (entry.getValue() == 1) {
                    map.pollLastEntry();
                } else {
                    map.put(entry.getKey(), entry.getValue() - 1);
                }
            }
            
        }
        
        // 最后返回map中的元素
        int[] res = new int[k];
        int idx = 0;
        for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
            int freq = entry.getValue();
            while (freq-- > 0) {
                res[idx++] = entry.getKey();
            }
        }
        return res;
    }
}
```

**解法四：数据范围有限时直接计数排序就行了：O(N)**

```
class Solution {
    
    public int[] getLeastNumbers(int[] arr, int k) {
        if (k == 0 || arr.length == 0) {
            return new int[0];
        }
        // 统计每个数字出现的次数
        int[] counter = new int[10001];
        for (int num: arr) {
            counter[num]++;
        }
        // 根据counter数组从头找出k个数作为返回结果
        int[] res = new int[k];
        int idx = 0;
        for (int num = 0; num < counter.length; num++) {
            while (counter[num]-- > 0 && idx < k) {
                res[idx++] = num;
            }
            if (idx == k) {
                break;
            }
        }
        return res;
    }
    
}
```

### **题031连续子数组的最大和**

输入一个整型数组，数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。

要求时间复杂度为O(n)。 

示例1:

```
输入: nums = [-2,1,-3,4,-1,2,1,-5,4]
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```

**动态规划**

![image-20200812030055646](..\images\image-20200812030055646.png)

```
/**
     * 考虑到在dp列表中，dp[i]只和dp[i-1]有关,
     * 所以用两个参数存储循环过程中的dp[i]和dp[i-1]的值即可
     */
    public int maxSubArray(int[] nums) {
        int max = nums[0];
        int former = 0;//用于记录dp[i-1]的值，对于dp[0]而言，其前面的dp[-1]=0
        int cur = nums[0];//用于记录dp[i]的值
        for (int num : nums) {
            
            cur = num;
            //如果dp[i-1]即former大于0 则dp[i] = dp[i-1] + num[i]  否则为num[i]
            if (former > 0) {
                cur += former;
            }
            
            //记录最大值  返回
            if (cur > max) {
                max = cur;
            }
            former = cur;
        }
        return max;
    }
```

### 题032 1~n整数中1出现的次数

输入一个整数 n ，求1～n这n个整数的十进制表示中1出现的次数。

例如，输入12，1～12这些整数中包含1 的数字有1、10、11和12，1一共出现了5次。

示例 1：

```
输入：n = 12
输出：5
```

**解法：找规律**

![image-20200813185909744](..\images\image-20200813185909744.png)

```
class Solution {
    
    public int countDigitOne(int n) {
        int digit = 1, res = 0;
        int high = n / 10, cur = n % 10, low = 0;
        while (high != 0 || cur != 0) {
            if (cur == 0) {
                res += high * digit;
            } else if (cur == 1) {
                res += high * digit + low + 1;
            } else {
                res += (high + 1) * digit;
            }
            low += cur * digit;
            cur = high % 10;
            high /= 10;
            digit *= 10;
        }
        return res;
    }
}
```

### 题033把数组排成最小的数

输入一个非负整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。

示例 1:

```
输入: [3,30,34,5,9]
输出: "3033459"
```

  **解法：**

![image-20200813214306613](..\images\image-20200813214306613.png)

```
class Solution {
    
    public String minNumber(int[] nums) {
        String[] strs = new String[nums.length];
        for (int i = 0; i < nums.length; i++) {
            strs[i] = String.valueOf(nums[i]);
        }
        fastSort(strs, 0, strs.length - 1);
        StringBuilder res = new StringBuilder();
        for (String s : strs) {
            res.append(s);
        }
        return res.toString();
    }
    
    private String[] fastSort(String[] arr, int left, int right) {
        if (left < right) { //left right一直变化 直到left <right递归结束
            int partitionIndex = partition(arr, left, right);
            fastSort(arr, left, partitionIndex - 1);
            fastSort(arr, partitionIndex + 1, right);
        }
        return arr;
    }
    
    private int partition(String[] a, int low, int high) {
        //取每个序列的第一个值作为基准值
        String pivotkey = a[low];
        while (low < high) {
            //从序列的右边开始往左遍历，直到找到小于基准值的元素
            while (high > low && (a[high] + pivotkey).compareTo(pivotkey + a[high]) >= 0) {
                high--;
            }
            //将元素直接赋予给左边第一个，即pivotkey所在的位置
            a[low] = a[high];
            //从序列的左边边开始往右遍历，直到找到大于基准值的元素
            while (high > low && (a[low] + pivotkey).compareTo(pivotkey + a[low]) <= 0) {
                low++;
            }
            //此时的a[high]<pivotkey,已经被赋予到左边，所以可以将元素赋予给a[high]
            a[high] = a[low];
        }
        //最后的low是基准值所在的位置
        a[low] = pivotkey;
        return low;
    }
}
```

### 题034丑数

我们把只包含质因子 2、3 和 5 的数称作丑数（Ugly Number）。求按从小到大的顺序的第 n 个丑数。

**示例:**

```
输入: n = 10
输出: 12
解释: 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 是前 10 个丑数。
```

**解法：动态规划**

**一个丑数乘以 2， 3， 5 之后， 一定还是一个丑数**。

并且如果从丑数序列首个元素开始 *2 *3 *5 来计算， 丑数序列也是不会产生漏解的。



丑数的排列
假设当前存在 3 个数组 nums2, nums3, nums5 分别代表丑数序列从 1 开始分别乘以 2, 3, 5 的序列， 即

```
nums2 = {1*2, 2*2, 3*2, 4*2, 5*2, 6*2, 8*2...}
nums3 = {1*3, 2*3, 3*3, 4*3, 5*3, 6*3, 8*3...}
nums5 = {1*5, 2*5, 3*5, 4*5, 5*5, 6*5, 8*5...}
```

注意 7 不是丑数. 

2, 3, 5 这前 3 个丑数一定要乘以其它的丑数， 所得的结果才是新的丑数， 所以上例中没有出现 7*2, 7*3, 7*5

那么， 最终的丑数序列实际上就是这 3 个有序序列对的合并结果， 计算丑数序列也就是相当于 合并 3 个有序序列。

合并 3 个有序序列
合并 3 个有序序列， 最简单的方法就是每一个序列都各自维护一个指针， 然后比较指针指向的元素的值， 将最小的放入最终的合并数组中， 并将相应指针向后移动一个元素。 这也就是：

```
class Solution {
public:
	int nthUglyNumber(int n) {
		// ....
		dp[i] = min(min(dp[p2] * 2, dp[p3] * 3), dp[p5] * 5);
		if (dp[i] == dp[p2] * 2) 
			p2++;
		if (dp[i] == dp[p3] * 3) 
			p3++;
		if (dp[i] == dp[p5] * 5) 
			p5++;
                  // ......
};
```

合并过程中重复解的处理
nums2, nums3, nums5 中是存在重复的解的， 例如 nums2[2] == 3*2, nums3[1] == 2*3 都计算出了 6 这个结果， 所以在合并 3 个有序数组的过程中， 还需要跳过相同的结果， 这也就是为什么在比较的时候， 需要使用 3 个并列的 if... if... if... 而不是 if... else if... else 这种结构的原因。 当比较到元素 6 时， if (dp[i] == dp[p2] * 2)...if (dp[i] == dp[p3] * 3)... 可以同时指向 nums2, nums3 中 元素 6 的下一个元素。

**代码**

```
public int nthUglyNumber(int n) {
    int a = 0, b = 0, c = 0;
    int[] dp = new int[n];
    dp[0] = 1;
    for (int i = 1; i < n; i++) {
        int n2 = dp[a] * 2, n3 = dp[b] * 3, n5 = dp[c] * 5;
        dp[i] = Math.min(Math.min(n2, n3), n5);
        if (dp[i] == n2) {
            a++;
        }
        if (dp[i] == n3) {
            b++;
        }
        if (dp[i] == n5) {
            c++;
        }
    }
    return dp[n - 1];
}
```

### 题035第一个只出现一次的字符

在字符串 s 中找出第一个只出现一次的字符。如果没有，返回一个单空格。 s 只包含小写字母。

示例:

```
s = "abaccdeff"
返回 "b"

s = "" 
返回 "
```

**解法一：哈希表**

```
public char firstUniqChar(String s) {
    HashMap<Character, Boolean> dic = new HashMap<>();
    char[] sc = s.toCharArray();
    for (char c : sc) {
        dic.put(c, !dic.containsKey(c));
    }
    for (char c : sc) {
        if (dic.get(c)) {
            return c;
        }
    }
    return ' ';
}
```

**解法二：有序哈希表**

```
        Map<Character, Boolean> dic = new LinkedHashMap<>();
        char[] sc = s.toCharArray();
        for(char c : sc)
            dic.put(c, !dic.containsKey(c));
        for(Map.Entry<Character, Boolean> d : dic.entrySet()){
            if(d.getValue()) return d.getKey();
        }
        return ' ';
```

**复杂度分析：**
时间和空间复杂度均与 “方法一” 相同，而具体分析时间复杂度：

方法一 O(2N) ： N 为字符串 s 的长度；需遍历 s 两轮；
方法二 O(N) ：遍历 s 一轮，遍历 dic 一轮。

当字符串很长（重复字符很多）时，方法二则效率更高。

**空间复杂度 O(N) ：** HashMap 需存储 N个字符的键值对，使用 O(N) 大小的额外空间。

### 题036数组中的逆序对

在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。

示例 1:

```
输入: [7,5,6,4]
输出: 5
```

[leetcode题解](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/solution/shu-zu-zhong-de-ni-xu-dui-by-leetcode-solution/)

**解法一：分治思想 （归并排序 ）**

合并两个有序数组时，利用数组的部分有序性，一下子计算出一个数之前或者之后元素的逆序的个数。

两者不能同时计算，否则会计算重复。

![image-20200815010916124](..\images\image-20200815010916124.png)

```
class Solution {
    
    public int reversePairs(int[] nums) {
        int len = nums.length;
        
        if (len < 2) {
            return 0;
        }
        
        return sort(nums, 0, len - 1);
    }
    
    public int sort(int[] arr, int left, int right) {
        // 递归终止条件
        if (left >= right) {
            return 0;
        }
        // 获取 left right 之间的中间位置
        int mid = left + ((right - left) / 2);
        // 分治递归
        int leftCount = sort(arr, left, mid);
        int rightCount = sort(arr, mid + 1, right);
        int mergeCount = merge(arr, left, mid, right);
        return leftCount + rightCount + mergeCount;
    }
    
    // 合并数据
    public int merge(int[] arr, int left, int mid, int right) {
        int[] temp = new int[right - left + 1];
        int i = 0;
        int p1 = left;
        int p2 = mid + 1;
        
        int count = 0;
        // 比较左右两部分的元素，哪个小，把那个元素填入temp中
        while (p1 <= mid && p2 <= right) {
            if (arr[p1] <= arr[p2]) {
                temp[i++] = arr[p1++];
            } else {
                temp[i++] = arr[p2++];
                count += (mid - p1 + 1);
            }
        }
        // 上面的循环退出后，把剩余的元素依次填入到temp中
        // 以下两个while只有一个会执行
        while (p1 <= mid) {
            temp[i++] = arr[p1++];
        }
        while (p2 <= right) {
            temp[i++] = arr[p2++];
        }
        // 把最终的排序的结果复制给原数组
        for (i = 0; i < temp.length; i++) {
            arr[left + i] = temp[i];
        }
        return count;
    }
}
```

### 题037两个链表的第一个公共节点

输入两个链表，找出它们的第一个公共节点。

如下面的两个链表：

![image-20200815011624696](..\images\image-20200815011624696.png)

在节点 c1 开始相交。

 

示例 1：

![image-20200815011606388](..\images\image-20200815011606388.png)

```
输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
输出：Reference of the node with value = 8
输入解释：相交节点的值为 8 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,0,1,8,4,5]。在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。
```

**双指针法，浪漫相遇**

我们使用两个指针 node1，node2 分别指向两个链表 headA，headB 的头结点，然后同时分别逐结点遍历，当 node1 到达链表 headA 的末尾时，重新定位到链表 headB 的头结点；当 node2 到达链表 headB 的末尾时，重新定位到链表 headA 的头结点。

    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode node1 = headA;
        ListNode node2 = headB;
        
        while (node1 != node2) {
            node1 = node1 != null ? node1.next : headB;
            node2 = node2 != null ? node2.next : headA;
        }
        return node1;
    }
### 题038数字在排序数组中出现的次数

统计一个数字在排序数组中出现的次数。

**示例 1:**

```
输入: nums = [5,7,7,8,8,10], target = 8
输出: 2
```

**解法：二分**

![image-20200816005334192](..\images\image-20200816005334192.png)

**代码：**

```
public int search(int[] nums, int target) {
    //target的有边界 减去 target-1的右边界
    return helper(nums, target) - helper(nums, target - 1);
}

int helper(int[] nums, int tar) {
    int i = 0, j = nums.length - 1;
    while (i <= j) {
        int m = (i + j) / 2;
        if (nums[m] <= tar) {  //等于的时候 找到右边界
            i = m + 1;
        } else {
            j = m - 1;
        }
    }
    return i;
}
```

### 题039二叉树的深度

输入一棵二叉树的根节点，求该树的深度。从根节点到叶节点依次经过的节点（含根、叶节点）形成树的一条路径，最长路径的长度为树的深度。

例如：

给定二叉树 [3,9,20,null,null,15,7]，

```
    3
   / \
  9  20
    /  \
   15   7
```

返回它的最大深度 3 

> 树的遍历方式总体分为两类：深度优先搜索（DFS）、广度优先搜索（BFS）；
>
> 常见的 DFS ： 先序遍历、中序遍历、后序遍历；
> 常见的 BFS ： 层序遍历（即按层遍历）。
> 求树的深度需要遍历树的所有节点，本文将介绍基于 后序遍历（DFS） 和 层序遍历（BFS） 的两种解法。

**深度优先遍历（DFS）**

树的后序遍历 / 深度优先搜索往往利用 **递归** 或 **栈** 实现，本文使用递归实现

```
public int maxDepth(TreeNode root) {
    //终止条件  最后返回0
    if (root == null) {
        return 0;
    }
    //每层回溯时加1  并判断左右子树最大的返回
    return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
}
```

复杂度： 时间复杂度O(N)   空间复杂度O(N)

**广度优先遍历（BFS）**

1. **特例处理：** 当 `root` 为空，直接返回 深度 00 。
2. **初始化：** 队列 `queue` （加入根节点 `root` ），计数器 `res = 0`。
3. **循环遍历：** 当 `queue` 为空时跳出

- 初始化一个空列表 tmp ，用于临时存储下一层节点；
- 遍历队列： 遍历 queue 中的各节点 node ，并将其左子节点和右子节点加入 tmp；
- 更新队列： 执行 queue = tmp ，将下一层节点赋值给 queue；
- 统计层数： 执行 res += 1 ，代表层数加 11

```
public int maxDepth(TreeNode root) {
    if (root == null) {
        return 0;
    }
    List<TreeNode> queue = new LinkedList<>() {{
        add(root);
    }}, tmp;
    int res = 0;
    while (!queue.isEmpty()) {
        tmp = new LinkedList<>();
        for (TreeNode node : queue) {
            if (node.left != null) {
                tmp.add(node.left);
            }
            if (node.right != null) {
                tmp.add(node.right);
            }
        }
        queue = tmp;
        res++;
    }
    return res;
}
```

### 题040数组中数字出现的次数

一个整型数组 nums 里除两个数字之外，其他数字都出现了两次。请写程序找出这两个只出现一次的数字。要求时间复杂度是O(n)，空间复杂度是O(1)。

示例 1：

```
输入：nums = [4,1,4,6]
输出：[1,6] 或 [6,1]
```

**解法：分组异或**

相同两个数字异或结果为0

由于数组中存在着两个数字不重复的情况，我们将所有的数字异或操作起来，最终得到的结果是这两个数字的异或结果：(相同的两个数字相互异或，值为0)) 最后结果一定不为0，因为有两个数字不重复。

演示：

```
4 ^ 1 ^ 4 ^ 6 => 1 ^ 6

6 对应的二进制： 110
1 对应的二进制： 001
1 ^ 6  二进制： 111
```

此时我们无法通过 111（二进制），去获得 110 和 001。
那么当我们可以把数组分为两组进行异或，那么就可以知道是哪两个数字不同了。
我们可以想一下如何分组：

- 重复的数字进行分组，很简单，只需要有一个统一的规则，就可以把相同的数字分到同一组了。例如：奇偶分组。因为重复的数字，数值都是一样的，所以一定会分到同一组！
- 此时的难点在于，对两个不同数字的分组。
  此时我们要找到一个操作，让两个数字进行这个操作后，分为两组。
  我们最容易想到的就是 & 1 操作， 当我们对奇偶分组时，容易地想到 & 1，即用于判断最后一位二进制是否为 1。来辨别奇偶。
  你看，通过 & 运算来判断一位数字不同即可分为两组，那么我们随便两个不同的数字至少也有一位不同吧！
  我们只需要找出那位不同的数字mask，即可完成分组（ & mask ）操作。

由于两个数异或的结果就是两个数数位不同结果的直观表现，所以我们可以通过异或后的结果去找 mask！
所有的可行 mask 个数，都与异或后1的位数有关。

```
num1:       101110      110     1111
num2:       111110      001     1001
num1^num2:  010000      111     0110

可行的mask:  010000      001     0010
                        010     0100
                        100     
```

为了操作方便，我们只去找最低位的mask:

**代码：**

```
public int[] singleNumbers(int[] nums) {
        //xor用来计算nums的异或和
        int xor = 0;
        
        // 计算异或和 并存到xor
        // e.g. [2,4,2,3,3,6] 异或和：(2^2)^(3^3)^(4^6)=2=010
        for (int num : nums) {
            xor ^= num;
        }
        
        int mask = 1;
        
        // & operator只有1&1时等于1 其余等于0
        // 用上面的e.g. 4和6的二进制是不同的 我们从右到左找到第一个不同的位就可以分组 4=0100 6=0110
        // 根据e.g. 010 & 001 = 000 = 0则 mask=010
        // 010 & 010 != 0 所以mask=010
        // 之后就可以用mask来将数组里的两个数分区分开
    
        //拉去最低位的mask 用于分组  只要有一位为1表明当前位不相同 可以用来区分
        while ((xor & mask) == 0) {
            mask <<= 1;
        }
        
        //两个只出现一次的数字
        int a = 0, b = 0;
        
        for (int num : nums) {
            //根据&是否为0区分将两个数字分区，并分别求异或和
            if ((num & mask) == 0) {
                a ^= num;
            } else {
                b ^= num;
            }
        }
        return new int[] {a, b};
    }
}
```

### 题041和为s的两个数字

输入一个递增排序的数组和一个数字s，在数组中查找两个数，使得它们的和正好是s。如果有多对数字的和等于s，则输出任意一对即可。

**示例 1：**

```
输入：nums = [2,7,11,15], target = 9
输出：[2,7] 或者 [7,2]
```

**解法一：哈希**

hash表方法是遍历我们的数组，然后存到hash表中，这样我们可以在o(1)时间取出，但是我们要维护一个hash表的空间，以及o(n)的时间复杂度。

参考LeetCode001两数之和

```
class Solution {
    
    public int[] twoSum(int[] nums, int target) {
        HashSet<Integer> set = new HashSet<>();
        for (int i = 0; i < nums.length; i++) {
            int temp = target - nums[i];
            //算出差值  看看map里是否有  有的话返回[之前的索引，现在索引]  否则放入map中
            if (set.contains(temp)) {
                return new int[] {temp, nums[i]};
            }
            set.add(nums[i]);
        }
        return new int[] {-1, -1};
    }
}
```

**解法二：双指针**（最高效）

计算和 s = nums[i] + nums[j]；
若 s > target，则指针 j 向左移动，即执行 j = j - 1；
若 s < target，则指针 i 向右移动，即执行 i = i + 1；
若 s = target ，立即返回数组 [nums[i], nums[j]；

```
public int[] twoSum(int[] nums, int target) {
    int i = 0, j = nums.length - 1;
    while (i < j) {
        int s = nums[i] + nums[j];
        if (s < target) {
            i++;
        } else if (s > target) {
            j--;
        } else {
            return new int[] {nums[i], nums[j]};
        }
    }
    return new int[0];
}
```

**解法三：二分法**

e = target - nums[i]  再使用二分查找

```
public int[] twoSum(int[] nums, int target) {
    for (int i = 0; i < nums.length; ++i) {
        int left = i + 1, right = nums.length - 1, e = target - nums[i];
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == e) {
                return new int[] {nums[i], nums[mid]};
            } else if (nums[mid] > e) {
                right = mid - 1;
            } else if (nums[mid] < e) {
                left = mid + 1;
            }
        }
    }
    return new int[] {};
    
}
```

### 题042翻转单词顺序

输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。为简单起见，标点符号和普通字母一样处理。例如输入字符串"I am a student. "，则输出"student. a am I"。

示例 1：

```
输入: "the sky is blue"
输出: "blue is sky the"
```

**解法：双指针**

- 倒序遍历字符串 ss ，记录单词左右索引边界 i , j ；
- 每确定一个单词的边界，则将其添加至单词列表 res ；
- 最终，将单词列表拼接为字符串，并返回即可。

```
public String reverseWords(String s) {
    s = s.trim(); // 删除首尾空格
    int j = s.length() - 1, i = j;
    StringBuilder res = new StringBuilder();
    while (i >= 0) {
        while (i >= 0 && s.charAt(i) != ' ') {
            i--; // 搜索首个空格
        }
        res.append(s.substring(i + 1, j + 1) + " "); // 添加单词
        while (i >= 0 && s.charAt(i) == ' ') {
            i--; // 跳过单词间空格
        }
        j = i; // j 指向下个单词的尾字符
    }
    return res.toString().trim(); // 转化为字符串并返回
}
```

### 题043n个骰子的点数

把n个骰子扔在地上，所有骰子朝上一面的点数之和为s。输入n，打印出s的所有可能的值出现的概率。

你需要用一个浮点数数组返回答案，其中第 i 个元素代表这 n 个骰子所能掷出的点数集合中第 i 小的那个的概率。

示例 1:

```
输入: 1
输出: [0.16667,0.16667,0.16667,0.16667,0.16667,0.16667]
```

**解法：动态规划**

[动态规划初级试炼场](https://mp.weixin.qq.com/s/Ef73zZv6wiaXwiJRnCLpoQ)

单纯使用递归时间复杂度为6^n,会造成超时错误，因为存在重复子结构

使用动态规划解决问题一般分为三步：

1.表示状态

2.找出状态转移方程

3.边界处理

**表示状态**

>  分析问题的状态时，不需要分析整体，只需要分析最后一个阶段即可！因为动态规划问题都是划分为多个阶段的，各个阶段的状态表示都是一样，而我们的最终答案在就是在最后一个阶段。

通过题目我们知道一共投掷 n*n* 枚骰子，那最后一个阶段很显然就是：**当投掷完 n枚骰子后，各个点数出现的次数**。

找出了最后一个阶段，那状态表示就简单了。

- 首先用数组的第一维来表示阶段，也就是投掷完了几枚骰子。

- 然后用第二维来表示投掷完这些骰子后，可能出现的点数。

- 数组的值就表示，该阶段各个点数出现的次数。

  所以状态表示就是这样的：`dp[i][j]` ，表示投掷完 i 枚骰子后，点数 j的次数。

**找出状态转移方程**

> 找状态转移方程也就是找各个阶段之间的转化关系，同样我们还是只需分析最后一个阶段，分析它的状态是如何得到的。

单单看第 n 枚骰子，它的点数可能为 1 , 2, 3, ... , 6，因此投掷完 n 枚骰子后点数 j 出现的次数，可以由投掷完 n-1 枚骰子后，对应点数 j-1, j-2, j-3, ... , j-6 出现的次数之和转化过来。

**当前n个骰子出现的点数之和等于前一次出现的点数之和加上这一次出现的点数**

```
for (第n枚骰子的点数 i = 1; i <= 6; i ++) {
    dp[n][j] += dp[n-1][j - i]
}
```

**边界处理**

> 这里的边界处理很简单，只要我们把可以直接知道的状态初始化就好了。

我们可以直接知道的状态是啥，就是第一阶段的状态：投掷完 1 枚骰子后，它的可能点数分别为 1, 2, 3, ... , 6，并且每个点数出现的次数都是 1.

```
for (int i = 1; i <= 6; i ++) {
    dp[1][i] = 1;
}
```

**代码：**

```
    public double[] twoSum(int n) {
        int dp[][] = new int[n + 1][6 * n + 1];//表示i个骰子掷出s点的次数
        for (int i = 1; i <= 6; i++) {
            dp[1][i] = 1;//边界值   表示一个骰子掷出i点的次数为1
        }
        //第一层循环表示骰子的个数
        for (int i = 2; i <= n; i++) {
            //第二层循环表示可能会出现的点数之和
            for (int j = i; j <= 6 * i; j++) {
                //三层循环是为了找到能得到这种可能和时，对于少一个色子时的所有可能情况，相加即是当前可能总和
                for (int k = 1; k <= 6; k++) {
                    if (i - 1 >= 0 && j - k >= 0) {
                        dp[i][j] += dp[i - 1][j - k];//当前n个骰子出现的点数之和等于前一次出现的点数之和加上这一次出现的点数
                    }
                }
            }
        }
        double total = Math.pow((double) 6, (double) n);//掷出n次点数出现的所有情况、
        //1个筛子和  1-6       6个
        //2个筛子和  2-12      11个
        //3个筛子和  3-18      16个  得出和的总数5*n+1
        double[] ans = new double[5 * n + 1];//因为最大就只会出现5*n+1中点数
        for (int i = n; i <= 6 * n; i++) {
            ans[i - n] = ((double) dp[n][i]) / total;//第i小的点数出现的概率
        }
        return ans;
    }
```

### 题044扑克牌的顺子

从扑克牌中随机抽5张牌，判断是不是一个顺子，即这5张牌是不是连续的。2～10为数字本身，A为1，J为11，Q为12，K为13，而大、小王为 0 ，可以看成任意数字。A 不能视为 14。

示例 1: 

```
输入: [1,2,3,4,5]
输出: True
```

**解题思路：**

![image-20200817055312085](..\images\image-20200817055312085.png)

根据题意，此 5 张牌是顺子的 充分条件 如下：

除大小王外，所有牌 无重复 ；
设此 5 张牌中最大的牌为 max ，最小的牌为 min （大小王除外），则需满足：
`max - min < 5`

**方法一：Set + 遍历**

```
public boolean isStraight(int[] nums) {
    Set<Integer> repeat = new HashSet<>();
    int max = 0, min = 14;
    for (int num : nums) {
        if (num == 0) {
            continue; // 跳过大小王
        }
        max = Math.max(max, num); // 最大牌
        min = Math.min(min, num); // 最小牌
        if (repeat.contains(num)) {
            return false; // 若有重复，提前返回 false
        }
        repeat.add(num); // 添加此牌至 Set
    }
    return max - min < 5; // 最大牌 - 最小牌 < 5 则可构成顺子
}
```

**复杂度分析：**
时间复杂度 O(N) = O(5) = O(1) ： 其中 N为 nums 长度，本题中N≡5 ；遍历数组使用 O(N) 时间。
空间复杂度 O(N) = O(5) = O(1) ： 用于判重的辅助 Set 使用 O(N) 额外空间。

**方法二：排序+遍历**

    public boolean isStraight(int[] nums) {
        int joker = 0;
        Arrays.sort(nums); // 数组排序
        for(int i = 0; i < 4; i++) {
            if(nums[i] == 0) joker++; // 统计大小王数量
            else if(nums[i] == nums[i + 1]) return false; // 若有重复，提前返回 false
        }
        return nums[4] - nums[joker] < 5; // 最大牌 - 最小牌 < 5 则可构成顺子
    }

**复杂度分析**：
时间复杂度 O(N log N) = O(5log 5) = O(1) ： 其中 N 为 nums 长度，本题中 N≡5 ；数组排序使用 )O(NlogN) 时间。
空间复杂度 O(1) ： 变量 joker 使用 O(1) 大小的额外空间。

### 题045圆圈中最后剩下的数字

0,1,,n-1这n个数字排成一个圆圈，从数字0开始，每次从这个圆圈里删除第m个数字。求出这个圆圈里剩下的最后一个数字。

例如，0、1、2、3、4这5个数字组成一个圆圈，从数字0开始每次删除第3个数字，则删除的前4个数字依次是2、0、4、1，因此最后剩下的数字是3。

示例 1：

```
输入: n = 5, m = 3
输出: 3 
```

**解法一：模拟链表O(n^2)**

假设当前删除的位置是 `idx`，下一个删除的数字的位置是 `idx + m` 。但是，由于把当前位置的数字删除了，后面的数字会前移一位，所以实际的下一个位置是 `idx + m - 1`。由于数到末尾会从头继续数，所以最后取模一下，就是 `(idx + m - 1) (mod n)`。

`LinkedList` 虽然删除指定节点的时间复杂度是 O(1)的，但是在 remove 时间复杂度仍然是 O(n)的，因为需要从头遍历到需要删除的位置。那 `ArrayList` 呢？索引到需要删除的位置，时间复杂度是 O(1)，删除元素时间复杂度是 O(n)（因为后续元素需要向前移位）， remove 整体时间复杂度是 O(n)的

`ArrayList` 的 `remove` 操作在后续移位的时候，**其实是内存连续空间的拷贝的！所以相比于`LinkedList`大量非连续性地址访问**性能更好。

**代码：**

```
public int lastRemaining(int n, int m) {
    ArrayList<Integer> list = new ArrayList<>(n);
    for (int i = 0; i < n; i++) {
        list.add(i);
    }
    int idx = 0;
    while (n > 1) {
        idx = (idx + m - 1) % n;
        list.remove(idx);
        n--;
    }
    return list.get(0);
}
```

**解法二：约瑟夫O(n)**

这个问题实际上是约瑟夫问题，这个问题描述是

```
N个人围成一圈，第一个人从1开始报数，报M的将被杀掉，下一个人接着从1开始报。如此反复，最后剩下一个，求最后的胜利者。
```

既然约塞夫问题就是用人来举例的，那我们也给每个人一个编号（索引值），每个人用字母代替

下面这个例子是N=8 m=3的例子

我们定义F(n,m)表示最后剩下那个人的索引号，因此我们只关系最后剩下来这个人的索引号的变化情况即可

![image-20200817052553001](..\images\image-20200817052553001.png)

**反推**

现在我们知道了G的索引号的变化过程，那么我们反推一下
从N = 7 到N = 8 的过程

如何才能将N = 7 的排列变回到N = 8 呢？

我们先把被杀掉的C补充回来，然后右移m个人，发现溢出了，再把溢出的补充在最前面

神奇了 经过这个操作就恢复了N = 8 的排列了！

![image-20200817052820994](..\images\image-20200817052820994.png)

因此我们可以推出递推公式`f(8,3) = [f(7, 3) + 3] % 8`

进行推广泛化，即`f(n,m) = [f(n-1, m) + m] % n`

**代码：**

```
public int lastRemaining(int n, int m) {
    int pos = 0; // 最终活下来那个人的初始位置
    for (int i = 2; i <= n; i++) {
        pos = (pos + m) % i;  // 每次循环右移
    }
    return pos;
}
```

### 题046求1+2+…+n

求 `1+2+...+n` ，要求不能使用乘除法、`for、while、if、else、switch、case`等关键字及条件判断语句`（A?B:C）`。

示例 1：

```
输入: n = 3
输出: 6
```

**解法一：递归 （ 利用短路与的特性）**

```
public int sumNums(int n) {
    //当n不大于0 直接返回n  不执行后边的递归
    boolean flag = n > 0 && (n += sumNums(n - 1)) > 0;
    return n;
}
```

复杂度分析：

时间复杂度：O(n)。递归函数递归 n 次，每次递归中计算时间复杂度为 O(1)，因此总时间复杂度为 O(n)。
空间复杂度：O(n)。递归函数的空间复杂度取决于递归调用栈的深度，这里递归函数调用栈深度为 O(n)，因此空间复杂度为 O(n)。

**解法二：快速乘**

[俄罗斯算法](https://blog.csdn.net/lz0499/article/details/101096928)

```
int ans = 0;
while (b > 0) {
    if ((b & 1) != 0) {
        ans += a;
    }
    b >>= 1;
    a <<= 1;
}
```

回到本题，由等差数列求和公式我们可以知道等价于 n(n+1)/2 =>  n(n+1)>>1

乘法可以使用俄罗斯算法    

因为题目数据范围 n 为 [1,10000]，所以 n 二进制展开最多不会超过 14 位，我们手动展开 14层代替循环即可

```
public int sumNums(int n) {
    int ans = 0, A = n, B = n + 1;
    boolean flag;
    
    flag = ((B & 1) > 0) && (ans += A) > 0;
    A <<= 1;
    B >>= 1;
    
    flag = ((B & 1) > 0) && (ans += A) > 0;
    A <<= 1;
    B >>= 1;
    
    flag = ((B & 1) > 0) && (ans += A) > 0;
    A <<= 1;
    B >>= 1;
    
    flag = ((B & 1) > 0) && (ans += A) > 0;
    A <<= 1;
    B >>= 1;
    
    flag = ((B & 1) > 0) && (ans += A) > 0;
    A <<= 1;
    B >>= 1;
    
    flag = ((B & 1) > 0) && (ans += A) > 0;
    A <<= 1;
    B >>= 1;
    
    flag = ((B & 1) > 0) && (ans += A) > 0;
    A <<= 1;
    B >>= 1;
    
    flag = ((B & 1) > 0) && (ans += A) > 0;
    A <<= 1;
    B >>= 1;
    
    flag = ((B & 1) > 0) && (ans += A) > 0;
    A <<= 1;
    B >>= 1;
    
    flag = ((B & 1) > 0) && (ans += A) > 0;
    A <<= 1;
    B >>= 1;
    
    flag = ((B & 1) > 0) && (ans += A) > 0;
    A <<= 1;
    B >>= 1;
    
    flag = ((B & 1) > 0) && (ans += A) > 0;
    A <<= 1;
    B >>= 1;
    
    flag = ((B & 1) > 0) && (ans += A) > 0;
    A <<= 1;
    B >>= 1;
    
    flag = ((B & 1) > 0) && (ans += A) > 0;
    A <<= 1;
    B >>= 1;
    
    return ans >> 1;
}
```

### 题047不用加减乘除做加法

写一个函数，求两个整数之和，要求在函数体内不得使用 “+”、“-”、“*”、“/” 四则运算符号。

示例:

```
输入: a = 1, b = 1
输出: 2
```

**解法：位运算**

本题考察对位运算的灵活使用，即使用位运算实现加法。
设两数字的二进制形式 a, ba,b ，其求和 s = a + bs=a+b ，a(i)a(i) 代表 aa 的二进制第 ii 位，则分为以下四种情况：

| *a*(*i*) | *b*(*i*) | **无进位和** n(i) | **进位** c(i+1) |
| :------: | :------: | :---------------: | :-------------: |
|    0     |    0     |         0         |        0        |
|    0     |    1     |         1         |        0        |
|    1     |    0     |         1         |        0        |
|    1     |    1     |         0         |        1        |

观察发现，**无进位和** 与 **异或运算** 规律相同，**进位** 和 **与运算** 规律相同（并需左移一位）。因此，无进位和 n与进位 c的计算公式如下；

![image-20200816040505689](..\images\image-20200816040505689.png)

*s*=*a*+*b*⇒*s*=*n*+c  循环求n和c 直到进位为0  

![image-20200816040014215](..\images\image-20200816040014215.png)



**代码：**

```
public int add(int a, int b) {
    while (b != 0) { // 当进位为 0 时跳出
        int c = (a & b) << 1;  // c = 进位
        a ^= b; // a = 非进位和
        b = c; // b = 进位
    }
    return a;
}
```

### 题049把字符串转化为整数

写一个函数 StrToInt，实现把字符串转换成整数这个功能。不能使用 atoi 或者其他类似的库函数。

 

首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。

当我们寻找到的第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字组合起来，作为该整数的正负号；假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成整数。

该字符串除了有效的整数部分之后也可能会存在多余的字符，这些字符可以被忽略，它们对于函数不应该造成影响。

注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换。

在任何情况下，若函数不能进行有效的转换时，请返回 0。

说明：

假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为 [−231,  231 − 1]。如果数值超过这个范围，请返回  INT_MAX (231 − 1) 或 INT_MIN (−231) 。

示例 1:

```
输入: "42"
输出: 42
```

示例 2:

```
输入: "   -42"
输出: -42
解释: 第一个非空白字符为 '-', 它是一个负号。
     我们尽可能将负号与后面所有连续出现的数字组合起来，最后得到 -42 。
```

示例 3:

```
输入: "4193 with words"
输出: 4193
解释: 转换截止于数字 '3' ，因为它的下一个字符不为数字。
```

示例 4:

```
输入: "words and 987"
输出: 0
解释: 第一个非空字符是 'w', 但它不是数字或正、负号。
     因此无法执行有效的转换。
```

示例 5:

```
输入: "-91283472332"
输出: -2147483648
解释: 数字 "-91283472332" 超过 32 位有符号整数范围。 
     因此返回 INT_MIN (−231) 。
```

**解法：**

1. **首部空格**： 删除之即可；

2. **符号位**： 三种情况，即 ''+'' , ''-'' , ''无符号" ；新建一个变量保存符号位，返回前判断正负即可。

3. **非数字字符**： 遇到首个非数字的字符时，应立即返回。

4. **数字字符**：
   字符转数字： “此数字的 ASCII 码” 与 “ 0 的 ASCII 码” 相减即可；
   数字拼接： 若从左向右遍历数字，设当前位字符为 c ，当前位数字为 x ，数字结果为 res ，则数字拼接公式为：

   `res = 10 *res + x`

**代码：**

```
public int strToInt(String str) {
    char[] c = str.trim().toCharArray();
    if (c.length == 0) {
        return 0;
    }
    int res = 0, bndry = Integer.MAX_VALUE / 10;
    int i = 1, sign = 1;
    //符号处理   如果无正负符号从0开始
    if (c[0] == '-') {
        sign = -1;
    } else if (c[0] != '+') {
        i = 0;
    }
    for (int j = i; j < c.length; j++) {
        //不是数字
        if (c[j] < '0' || c[j] > '9') {
            break;
        }
        // 判断是否超过边界
        if (res > bndry || res == bndry && c[j] > '7') {
            return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
        }
        //字符串转化为数字
        res = res * 10 + (c[j] - '0');
    }
    return sign * res;
}
```

### 题050二叉搜索树的最近公共祖先

给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

**百度百科**中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉搜索树:  root = [6,2,8,0,4,7,9,null,null,3,5]

![image-20200816162116037](..\images\image-20200816162116037.png)

示例 1:

```
输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
输出: 6 
解释: 节点 2 和节点 8 的最近公共祖先是 6。
```



本题给定了两个重要条件：① 树为 二叉搜索树 ，② 树的所有节点的值都是 唯一 的。根据以上条件，可方便地判断 p,q与 root 的子树关系，即：

```
若 root.val < p.val ，则 p 在 root 右子树 中；
若 root.val > p.val ，则 p 在 root 左子树 中；
若 root.val = p.val，则 p 和 root 指向 同一节点 。
```

**解法一：迭代**
时间复杂度 O(N) ： 其中 N为二叉树节点数；每循环一轮排除一层，二叉搜索树的层数最小为logN （满二叉树），最大为 N （退化为链表）。
空间复杂度 O(1) ： 使用常数大小的额外空间。

```
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (p.val > q.val) { // 保证 p.val < q.val
        TreeNode tmp = p;
        p = q;
        q = tmp;
    }
    while (root != null) {
        if (root.val < p.val) // p,q 都在 root 的右子树中
        {
            root = root.right; // 遍历至右子节点
        } else if (root.val > q.val) // p,q 都在 root 的左子树中
        {
            root = root.left; // 遍历至左子节点
        } else {
            break;
        }
    }
    return root;
}
```

**解法二：递归**

```
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root.val < p.val && root.val < q.val) {
        return lowestCommonAncestor(root.right, p, q);
    }
    if (root.val > p.val && root.val > q.val) {
        return lowestCommonAncestor(root.left, p, q);
    }
    return root;
}
```