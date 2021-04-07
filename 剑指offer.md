# 数据结构

## 数组

### 数组中重复的数字-P39

````txt
对于一个长度为n的数组，里面的元素的大小都在0 ~ n-1，里面有任意个元素是重复的，找出任意一个重复的数字。
````

#### 方法一

排列数组，然后去遍历有没有重复的元素，时间O(nlogn),空间O(1)

#### 方法二

利用哈希表，遍历数组，出现则去哈希表中找，若已有，则找到重复的，若无，则加入哈希表，时间O（n），空间O（n）

#### 方法三

对数组内的元素进行重排，遍历数组，若一个元素a[i]的值不等于它在数组位置中的下标i，如果a[i]与a[a[i]]相等，则返回；否则将它与下标为a[i]的元素a[a[i]]进行交换，直到相等。时间O(n), 空间O(1)。

````java
public int duplicate(int []numbers){
        if (numbers == null || numbers.length==0){
            return 0;
        }
        int len = numbers.length;
        for (int i = 0; i < len; i++) { // 元素的值不在0 ~ n-1
            if(numbers[i] < 0 || numbers[i] > len - 1){
                return -1;
            }
        }
        int temp;
        for (int i = 0; i < len; i++) {
            while (numbers[i] != i){
                if(numbers[i] == numbers[numbers[i]]){
                    return numbers[i];
                }
                temp = numbers[numbers[i]];
                numbers[numbers[i]] = numbers[i];
                numbers[i] = temp;
            }
        }
        return -1;
    }
````

### 不修改数组找出重复数字-P42

```` txt
长度为 n+1 的数组里所有数字都在 1 ~ n，不修改数组找出重复的数字
````



#### 方法一

哈希表，但是空间付杂度为O（n）

#### 方法二

把数组分为1~n中间的m为两部分，统计数组中1~m的元素的个数，如果超过m，则证明这个范围内的元素有重复，继续减治。时间O(nlogn)，空间O(1)，相当于时间换空间。

````java
public class Solution {

    public int getDuplication(int []numbers){
        if(numbers == null || numbers.length == 0){
            return -1;
        }
        int start = 1;
        int end = numbers.length - 1;
        while (end >= start) {
            int middle = ((end - start) >> 1) + start;
            int count = countRange(numbers, start, middle);
            if (end == start) {
                if (count > 1) {
                    return start;
                }else {
                    break;
                }
            }
            if (count > (middle - start + 1)) {
                end = middle;
            }else {
                start = middle + 1;
            }

        }
        return -1;
    }

    private int countRange(int []numbers, int start, int end){
        if(numbers == null){
            return 0;
        }
        int count = 0;
        for (int i = 0; i < numbers.length; i++) {
            if(numbers[i] >= start && numbers[i] <= end){
                count++;
            }
        }
        return count;
    }

}
````



### 二维数组中的查找-P44

```` txt
一个二维数组，每一行都升序排序，每一列从上到下都递增，输入一个整数，判断该二维数组中是否含有该整数。
````

```txt
1 2 8  9
2 4 9  12
4 7 10 13
6 8 11 15 
对于这个数组，查找数字7。一般地，选取数组右上角的数字i，若i == 7，则查找结束；若i > 7，则删除i所在的列（i为该列最小元素）；若i < 7，则删除i所在的行（i为该行最大元素）。
```



#### 方法一

~~~java
public class Solution {

    public boolean findNumFromMatrix(int [][]matrix, int find){
        if(matrix == null || matrix.length==0 || (matrix.length == 1 && matrix[0].length == 0)){
            return false;
        }
        int right = matrix[0].length - 1;
        int top = 0;
        int bottom = matrix.length - 1;
        while (right >= 0 && top <= bottom) {
            if(matrix[top][right] == find){
                return true;
            }else if(matrix[top][right] > find){
                right--;
            }else {
                top++;
            }
        }
        return false;
    }
}
~~~

#### 方法二

也可以取左上角元素



## 字符串

### 替换空格-P51

```txt
实现一个函数，把字符串中的每一个空格都替换为 %20。
```

#### 方法一

遍历字符串，遇到空格，则把它替换为 %20，同时将后面的字符串向后移动两位。时间复杂度为O（n^2），较差。

#### 方法二

首先遍历一次字符串，计算空格的数量，然后new一个新数组，接下来一个个替换。

```java
public class Solution {
    // 正则表达式
    public String replaceBlankOne(String str){
        String res = str.replaceAll(" ", "%20");
        return res;
    }

    /**
    * 要比正则表达式快
    */
    public String replaceBlankTwo(String str){
        if(str == null){
            return "";
        }
        int count = 0;
        for (int i = 0; i < str.length(); i++) {//计算空格数量
            if(str.charAt(i) == ' '){
                count++;
            }
        }
        if(count == 0)  return str;
        char[] chars = new char[str.length() + count * 2];
        count = 0;
        for (int i = 0; i < str.length(); i++) {
            if(str.charAt(i) == ' '){
                chars[count++] = '%';
                chars[count++] = '2';
                chars[count++] = '0';
            }else {
                chars[count++] = str.charAt(i);
            }
        }
        return new String(chars);
    }
}
```



## 链表

### 从尾到头打印链表-P58

#### 方法一

利用栈实现

````java
public class Solution {
    public void printListReversinly(ListNode listNode){
        if (listNode == null) {
            return;
        }
        Deque<Integer> deque = new LinkedList<>();
        while (listNode != null) {
            deque.push(listNode.value);
            listNode = listNode.next;
        }
        deque.forEach(System.out::println);
    }
}
````

#### 方法二

递归本质上就是一个栈，要实现反向，我们没访问一个节点时，先递归输出他后面的节点在输出节点自身。注意：如果链表过长，则可能导致栈溢出。

````java
public class Solution {
    public void printListReversinly(ListNode listNode){
        if (listNode != null) {
            if (listNode.next != null) { // 先输出后面的节点
                printListReversinlyTwo(listNode.next);
            }
            System.out.println(listNode.value); // 再输出自己
        }
    }
}
````



## 树

### 构建二叉树-P62

```txt
根据一个二叉树的前序遍历和中序遍历构建出这个二叉树。
例如前序：1,2,4,7,3,5,6,8   中序：4,7,2,1,5,3,8,6 对应的二叉树为：
     1
  2      3
4      5     6
  7        8
  
由于前序1为树的跟节点。此时看中序，1左边的4,7,2则为根1的左子树，1右边的5,3,8,6则为根1的右子树。此时看前序，1右边的2,4,7为根1的左子树，后边剩余的为右子树。此时递归即可构建出二叉树。
```

````java
public class Solution {
    /**
     * @param preOrder 前序序列
     * @param midOrder 中序序列
     * @return 返回构建好的二叉树
     */
    public BinaryTreeNode binaryTreeConstructor(int[] preOrder, int[] midOrder) {
        if (preOrder == null || midOrder == null || preOrder.length != midOrder.length || preOrder.length == 0 || midOrder.length == 0) {
            return null;
        }
        return binaryTreeConstruct(preOrder, 0, preOrder.length - 1, midOrder, 0, midOrder.length - 1);
    }

    /**
     * @param preOrder 前序序列
     * @param preStart 当前树前序开始
     * @param preEnd   当前树前序结束
     * @param midOrder 中序序列
     * @param midStart 当前树中序开始
     * @param midEnd   当前树中序结束
     * @return 返回构建好的二叉树
     */
    private BinaryTreeNode binaryTreeConstruct(int[] preOrder, int preStart, int preEnd, int[] midOrder, int midStart, int midEnd) {
        BinaryTreeNode root = new BinaryTreeNode(preOrder[preStart]);
        // 只有一个节点
        if (preStart == preEnd) {
            if (midStart == midEnd && preOrder[preStart] == midOrder[midStart]) {
                return root;
            } else {
                throw new RuntimeException("Invalid input");
            }
        }

        // 在中序序列中找到根节点
        int midRoot = midStart;
        while (midRoot <= midEnd && midOrder[midRoot] != preOrder[preStart]) {
            midRoot++;
        }

        // 没有在中序序列中找到与根节点值相等的节点
        if (midRoot == midEnd && midOrder[midRoot] != preOrder[preStart]) {
            throw new RuntimeException("Invalid input");
        }
        // 左子树的节点数量
        int leftLength = midRoot - midStart;
        int leftPreEnd = preStart  + leftLength;
        if (leftLength > 0) {
            // 构建左子树
            root.left = binaryTreeConstruct(preOrder, preStart + 1, leftPreEnd, midOrder, midStart, midRoot - 1);
        }
        if (leftLength < preEnd - preStart) {
            // 构建右子树
            root.right = binaryTreeConstruct(preOrder, leftPreEnd + 1, preEnd, midOrder, midRoot + 1, midEnd);
        }
        return root;
    }
}
````

### 二叉树的下一个节点-P65



## 栈和队列

### 用两个栈实现队列-P68

````java
public class Solution {
    private Queue<Integer> stack1;
    private Queue<Integer> stack2;

    public Solution() {
        stack1 = new LinkedList<>();
        stack2 = new LinkedList<>();
    }

    public void add(int i) {
        stack1.add(i);
    }

    public int remove() {
        if (!stack2.isEmpty()) {
            return stack2.poll();
        }
        while (!stack1.isEmpty()) {
            stack2.add(stack1.poll());
        }
        return stack2.poll();
    }
}
````

### 两个队列实现一个栈-P71

````txt
类似
````



# 算法和数据操作

## 循环和递归

### 斐波那契数列-P74

#### 递归

```java
// 递归，效率低且可能造成栈溢出，可加一个记录表，使得计算树退化为链表
    public long fibonacciOne(int n){
        if(n <=0) {
            return 0L;
        }
        if(n==1){
            return 1L;
        }
        return fibonacciOne(n - 1) + fibonacciOne(n-2);
    }
```



#### 递归+记录表

```java
 //递归+记录表
    public long fibonacci(int n) {
        if (n <= 0) {
            return 0L;
        }
        if (n == 1) {
            return 1L;
        }
        long[] array = new long[n];
        array[0] = 0;
        array[1] = 1;
        Arrays.fill(array, -1);
        return fibonacci(array, n - 1) + fibonacci(array, n - 2);
    }

    private long fibonacci(long[] array, int n) {
        if (n <= 0) {
            return 0L;
        }
        if (n == 1) {
            return 1L;
        }
        return ((array[n - 1] != -1) ? (array[n - 1]) : (array[n - 1] = fibonacci(array, n - 1))) +
                ((array[n - 2] != -1) ? (array[n - 2]) : (array[n - 2] = fibonacci(array, n - 2)));
    }
```



#### 动态规划

````java
public class Solution {
   
    // 循环，效率高
    public long fibonacciTwo(int n){
        if(n <=0) {
            return 0L;
        }
        if(n==1){
            return 1L;
        }
        long a = 0;
        long b = 1;
        long temp;
        for(int i = 1;i<=n;i++){
            temp = a+b;
            a = b;
            b = temp;
        }
        return a;
    }
}
````

### 青蛙跳台阶-P77

青蛙跳台阶，每次可以跳一步或者两步，问跳到n阶一共有多少种跳发。

前面n-2阶已经跳完，最后一步有两种跳法，则f(n) = f(n - 1) + f(n - 2)，即为斐波那契数列问题。

## 查找和排序

### 快速排序-P79

````java
public void quickSort(int[] array, int left, int right) {
        if (left > right) {
            return;
        }
        int base = array[left];
        int i = left;
        int j = right;
        while (i < j) {
            while (array[j] >= base && i < j) { //找到一个比base小的数
                j--;
            }
            while (array[i] <= base && i < j) { //找到一个比base大的数
                i++;
            }
            if (i < j) {
                int temp = array[i];
                array[i] = array[j];
                array[j] = temp;
            }
        }
        array[left] = array[i];
        array[i] = base;
        quickSort(array, left, i - 1);
        quickSort(array, i + 1, right);

    }
````

### 旋转数组的最小数字-P82

```txt
把一个有序的数字的若干个元素搬到数组的末尾，称为数组的旋转。例如{3,4,5,1,2}为{1,2,3,4,5}的一个旋转。输出旋转数组的最小元素。
```

````txt
采用双指针法，第一个指针指向数组最左边，第二个指向数组最右边。然后取两个指针中间的元素。若该元素的值大于左指针所指向的值，则将左指针指向中间的元素；若中间的元素小于右指针的值所指向的值，则将右指针指向中间的元素。最后两指针将相邻，且右指针所指向的值为数组最小值。
    
````

````java
public int findMin(int[] array) {
        int left = 0;
        int right = array.length - 1;
        while (left < right) {
            int mid = (right + left) / 2;
            if (array[mid] > array[left]) {
                left = mid;
            } else {
                right = mid;
            }
            if (right - 1 == left) {
                break;
            }
        }
        return array[right];
    }
````

``` txt
注意：上述代码并不能解决所有的问题，特例{0,1,2,3,4,5}也是一个特殊的旋转数组。还有，对于{1,0,1,1,1,1}或者{1,1,1,0,1}这种特殊的旋转数组，上述代码也无能为力。
```

##### 改进

````java
public int findMin(int[] array) {
        int left = 0;
        int right = array.length - 1;
        while (left < right) {
            if (array[left] < array[right]) { //解决特例{0,1,2,3,4,5}
                return array[left];
            }
            int mid = (right + left) / 2;
            if (array[mid] > array[left]) {
                left = mid;
            } else if(array[mid] < array[right]) {
                right = mid;
            }else {//array[mid]==array[right]， 解决特例 {1,0,1,1,1,1}或者{1,1,1,0,1} 
                left++;
            }
            if (right - 1 == left) {
                break;
            }
        }
        return array[right];
    }
````

## 回溯法

### 矩阵中的路径-P89

````txt
求一个矩阵中是否存在一条包含某字符串所有字符的路径，矩阵上每个位置只能用一次，路径可以重任意位置开始，每次可以向上、左、下、右中选择移动一格。遍历过程如图。
````

![2021031610340834325](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/2021031610340834325.jpg)

````java
public class Solution {
    public boolean hasPath(char[] matrix, int rows, int cols, char[] str) {
        if (matrix == null || matrix.length == 0 || str == null || str.length == 0 || matrix.length != rows * cols) {
            return false;
        }
        // 标志位，判断这一步有没有走过
        boolean[] flags = new boolean[matrix.length];
        for (int i = 0; i < rows; i++) { // 从任意位置开始
            for (int j = 0; j < cols; j++) {
                // 循环遍历二维数组，找到起点等于str第一个元素的值，再递归判断四周是否有符合条件的----回溯法
                if (hasPath(matrix, i, j, rows, cols, flags, str, 0)) {
                    return true;
                }
            }
        }
        return false;
    }

    private boolean hasPath(char[] matrix, int i, int j, int rows, int cols, boolean[] flags, char[] str, int k) {
        // 先根据i和j计算匹配的第一个元素转为一维数组的位置
        int index = i * cols + j;
        // 递归终止条件
        if (i < 0 || j < 0 || i >= rows || j >= cols || matrix[index] != str[k] || flags[index] == true) {
            return false;
        }

        // 若k已经到达str末尾了，说明之前的都已经匹配成功了，直接返回true即可
        if (k == str.length - 1) {
            return true;
        }

        // 标识这一步已经走过
        flags[index] = true;
        
        // 回溯，递归寻找，每次找到了就给k加一，找不到，还原
        if (hasPath(matrix, i - 1, j, rows, cols, flags, str, k + 1) ||
                hasPath(matrix, i + 1, j, rows, cols, flags, str, k + 1) ||
                hasPath(matrix, i, j - 1, rows, cols, flags, str, k + 1) ||
                hasPath(matrix, i, j + 1, rows, cols, flags, str, k + 1)) {
            return true;
        }
        // 走到这，说明这一条路不通，还原，再试其他的路径
        flags[index] = false;
        return false;
    }
}
````

### 机器人运动范围-P92

```txt
地上有m行n列的方格，机器人从（0,0）开始移动，可向四个方向移动一格，但是不能进入行坐标和列坐标各位之和大于k的格子。求机器人可走进的格子数。
例k=18，能进入（35,37），因为3+5+7+3=18，但是不能进入（35,38）。
```

类似上述例子

````java
public class Solution {
    public int movingCount(int threshold, int rows, int cols) {
        if (threshold < 0 || rows < 0 || cols < 0) {
            return 0;
        }

        boolean[] flags = new boolean[rows * cols]; // 标记该位置有没有走过
        return movingCount(threshold, rows, cols, 0, 0, flags);
    }

    private int movingCount(int threshold, int rows, int cols, int i, int j, boolean[] flags) {
        // 递归出口
        if (i < 0 || j < 0 || i >= rows || j >= cols || flags[i * cols + j] || (cal(i) + cal(j)) > threshold) {
            return 0;
        }
        // 标记当前位置已走过
        flags[i * cols + j] = true;
       // 回溯
        return 1 + movingCount(threshold, rows, cols, i + 1, j, flags)
                + movingCount(threshold, rows, cols, i - 1, j, flags)
                + movingCount(threshold, rows, cols, i, j + 1, flags)
                + movingCount(threshold, rows, cols, i, j - 1, flags);
    }

    private int cal(int i) {
        int res = 0;
        while (i > 0) {
            res += i % 10;
            i /= 10;
        }
        return res;
    }
}

````

## 动态规划和贪心算法

### 剪绳子

```txt
有一根长度为n的绳子，将它剪开为m段，每段长度为k[i],求这些剪开后的绳子的最大乘积。时间O(n^2)，空间O(n)
```

#### 方法一 动态规划-P96

````java
public class Solution {
    public int maxProductAfterCuttingOne(int len){
        if(len < 2){
            return 0;
        }
        if(len == 2){
            return 1;
        }
        if(len == 3){
            return 2;
        }
        int max = 0;
        int[] dp = new int[len + 1];
        dp[0] = 0;
        dp[1] = 1;
        dp[2] = 2;
        dp[3] = 3;

        for (int i = 4; i <= len; i++) {
            max = 0;
            for (int j = 1; j <= i / 2 ; j++) {// 长度为 i 的绳子的所有剪法
                int temp = dp[j] * dp[i - j];
                if (max < temp) {
                    max = temp;
                }
            }
            dp[i] = max;
        }
        return dp[len];
    }
}
````

#### 方法二 贪心算法-P97

````txt
当 n >= 5时，尽可能剪下长度为3的绳子。当剩下的绳子长度为4时，对半剪。时间、空间O(1)
````

````java 
public int maxProductAfterCuttingTwo(int len) {
        if (len < 2) {
            return 0;
        }
        if (len == 2) {
            return 1;
        }
        if (len == 3) {
            return 2;
        }
        int timesOf3 = len / 3;  // 剪3的次数
        if (len - timesOf3 * 3 == 1) { //剩下的绳子长度为4是，应该剪为2*2
            timesOf3 -= 1;
        }
        int timesOf2 = (len - timesOf3 * 3) / 2;
        return (int) (Math.pow(3, timesOf3) * Math.pow(2, timesOf2));
    }
````

## 位运算

### 二进制中1的个数-P101

#### 常规解法

````java
public class Solution {
    public int  numberOfOne(int n){
        int res = 0;
        while(n!=0){
            if((n & 1) == 1){// 支持负数
                res++;
            }
            n = n >>> 1; // 无符号右移
        }
        return res;
    }

    // 或者将1左移
    
}
````

#### 惊喜解法

````java
public int numberOfOne(int n) {
        int res = 0;
        while(n ！= 0){ // 支持负数
            res++;
            n = (n-1) & n; // 减1后再 & 自身则会把 n 最右边的 1 变为 0
        }
        return res;
    }
````





# 高质量的代码

## 代码的规范性

```txt
代码规范的命名、书写、布局
```



## 代码的完整性

### 数值的整数次方-P110

```` txt
实现double pow(double base, int exponent)，无需考虑大数问题。
````

```java
public static double pow(double base, int exponent) {
        if(base == 0){ // 当底数为0
            return 0;
        }
        if (exponent == 0){ // 当指数为0
            return 1;
        }
        double res = base;
        boolean flag = false;
        if (exponent < 0) { // 当指数为负数
            exponent = -exponent;
            flag = true;
        }
        for (int i = 1; i < exponent; i++) {
            res *= base;
        }
        if (flag) {
            res = 1 / res;
        }
        return res;
    }
```

### 打印1到最大的n位数-P114

```txt
改题目需要考虑大数问题，利用数组，或字符串解决。
```

##### 方法一 数组

```java
public class Solution {
    public boolean Increment(int[] number){                 //  这个方法是用来实现对数加1操作
        boolean isOverflow = false; // 是否溢出
        int nTakeOver=0; // 是否进位
        for(int i=number.length-1;i>=0;i--){ //从最后一位开始
            int nSum = number[i]+nTakeOver;
            if(i==number.length-1)//如果是最后一位，加一
                nSum++;
            if(nSum>=10){ //要进位
                if(i==0) isOverflow=true; //如果是第一位要进位，溢出
                else{
                    nTakeOver=1;
                    nSum=nSum-10;
                    number[i]=nSum;
                }
            }
            else{
                number[i]=nSum;
                break;
            }
        }
        return isOverflow;
    }

    public void PrintNumber(int[] number){   //该方法是负责打印一个正类，千万不要尝试将数组变成一个整数
        boolean isBeginning=true;
        for(int i=0;i<number.length;i++){
            if(isBeginning&&number[i]!=0)
                isBeginning=false;
            if(!isBeginning){
                System.out.print(number[i]);
            }
        }
    }

    public void Test(int n){     //打印从1到最大的n位整数
        if(n<=0)
            System.out.println("输入出错，请重新输入！");
        int[] number = new int[n];

        while(!Increment(number)){
            PrintNumber(number);
        }
    }
}
```

##### 方法二 递归全排列

````java
public class Solution {
    private void PrintMaxOfNdigits(int[] number,int length,int index){
        if(index ==length-1){
            PrintNumber(number);
            return;
        }
        for(int i=0;i<10;i++){ //第index+1层
            number[index+1]=i;
            PrintMaxOfNdigits(number, length, index+1);
        }
    }

    public void prinNumber(int n){
        if(n<=0)
            return;
        int[] number = new int[n];
        for(int i=0;i<10;i++){ // 第0层
            number[0]=i;
            PrintMaxOfNdigits(number, n, 0);
        }
    }
}
````

### 删除链表的节点-P119

一个单链表，给出头结点和要删除的节点指针，O（1）时间删除该节点。

````java
class ListNode{
    int value;
    ListNode next;
}

public class Solution {
    public boolean deleteNode(ListNode head, ListNode toBeDeleted){
        if(head == null || toBeDeleted==null){
            return false;
        }
        // 要删除的节点不是尾节点
        if (toBeDeleted.next != null) {
            ListNode next = toBeDeleted.next;
            // 将要删除的节点的下一个节点的值覆盖要删除的节点的值。
            toBeDeleted.value = next.value;
            toBeDeleted.next = next.next;
        }//又是尾节点又是头节点
        else if (head == toBeDeleted){
            head = null;
        }
        //是尾节点但不是头节点
        else{
            ListNode node = head;
            while(node.next != toBeDeleted){
                node = node.next;
            }
            node.next = null;
        }
    }
}
````



### 删除链表中重复的节点-P122

```java
public class Solution {
    public void deleteDuplication(ListNode head){
        if(head == null ){
            return;
        }
        ListNode preNode = null;
        ListNode curNode = head;
        while (curNode != null) {
            ListNode nextNode = curNode.next;
            boolean needDeleted = false;
            if (nextNode != null && nextNode.value == curNode.value) {  // 需要删除
                needDeleted = true;
            }
            if (!needDeleted) { // 不需要删除，都往后移
                preNode = curNode;
                curNode =  curNode.next;
            }else {
                ListNode toBeDeleted = curNode;
                int value = curNode.value;
                while (toBeDeleted != null && toBeDeleted.value == value){ // 循环删除
                    nextNode = toBeDeleted.next;
                    toBeDeleted = nextNode;
                }
                if (preNode == null) { // 将断的链表连起来
                    head = nextNode;
                }else {
                    preNode.next = nextNode;
                }
                curNode = nextNode; // 更新curNode
            }
        }
    }
}
```

### 正则表达式匹配-P125

```txt
实现一个函数来匹配包含 . 和 * 的正则表达式。匹配是指所有的字符匹配整个模式。例如 aaa 匹配模式 a.a ，ab*ac*a，但与 aa.a, ab*a不匹配。
```

```java
递归算法逻辑关系分析：
一、如果模式串存在下一个字符，且下个字符是∗
1.如果字符串当前字符和模式串当前字符匹配：
1⃣️ 认为∗前面的字符出现0次，i不变,j+2
2⃣️ 认为∗前面的字符出现1次，i+1，j+1
3⃣️ 认为∗前面的字符出现多次，i+1，j不变
2.如果字符串当前字符和模式串当前字符不匹配
1⃣️ 认为∗前面的字符出现0次，i不变，j+2
    
二、如果下个字符不是∗：
1.如果字符串当前字符和模式串当前字符匹配
1⃣️ 匹配下个字符，i+1，j+1
2. 如果字符串当前字符和模式串当前字符不匹配
1⃣️ 字符串和模式串不匹配

public class Solution {
    public boolean match(String str, String pattern) {
        if (str == null || pattern == null){
            //有一个是空串 返回false
            return false;
        }
        
        return matchCore(str.toCharArray(), 0, pattern.toCharArray(), 0);//递归地匹配
    }

    private boolean matchCore(char[] str, int strIndex, char[] pattern, int patternIndex) {
        //如果匹配同时到达末尾，则匹配成功
        if (strIndex == str.length && patternIndex == pattern.length) return true;
        //如果模式串先到达末尾，则匹配不成功
        if (strIndex != str.length && patternIndex == pattern.length) return false;
        /*如果下一个字符是'*'*/
        if (patternIndex + 1 < pattern.length && pattern[patternIndex + 1] == '*') {
            //如果当前字符是匹配的
            if (strIndex != str.length && (str[strIndex] == pattern[patternIndex] || pattern[patternIndex] == '.')){
                return matchCore(str, strIndex, pattern, patternIndex + 2) || //认为'*'前的字符出现0次
                        matchCore(str, strIndex + 1, pattern, patternIndex + 2) ||//认为'*'前的字符出现1次
                        matchCore(str, strIndex + 1, pattern, patternIndex);//认为'*'前的字符出现多次
            }
            else{
                //如果当前字符不匹配的 认为'*'前的字符出现0次
                return matchCore(str, strIndex, pattern, patternIndex + 2);
            }
        }
        /*如果下一个字符不是'*'*/
        //当前字符是匹配的
        if (strIndex != str.length && (str[strIndex] == pattern[patternIndex] || pattern[patternIndex] == '.')){

            return matchCore(str, strIndex + 1, pattern, patternIndex + 1);
        }
        else{
            //当前字符是不匹配的
            return false;
        }
    }
}
```



### 表示数值的字符串-P127



### 调整数组顺序使得奇数位于偶数前面-P129

```txt
调整数组里数字的顺序，使得所有的奇数都位于数组的前半部分，所有的偶数位于数组的后半部分。
```



#### 初级解法-双指针

```java
public class Solution {
    public void reorderOddEven(int[] array) {
        if (array == null) {
            return;
        }
        int i = 0;
        int j = array.length - 1;
        while (i < j) {
            while (i < j && (array[i] & 0x1) == 1) { (1)
                i++;
            }
            while (i < j && (array[j] & 0x1) == 0) { (2)
                j--;
            }
            //此种写法可能会导致问题：如果 a 和 b 引用的是同一个变量的话，使用这种方法会使得这个变量变为0。	
            //array[i] = array[i] ^ array[j]; 
		   //array[j] = array[i] ^ array[j];
		   //array[i] = array[i] ^ array[j];
            int temp = array[i];
            array[i] = array[j];
            array[j] = temp;
        }
    }
}
```



#### 可扩展法-P132

```txt
将（1）和（2）的中的判断逻辑抽象出来，传入一个函数指针，可解决其他类型的问题。
例如把数组中所有的负数都放到非负数前面，或者把所有能被3整除的放到不能被3整除的数后面。
```

## 代码的鲁棒性

### 链表中倒数第k个节点-P135

````txt
输入一个链表，输出该链表中倒数第k个节点。如有链表1,2,3,4,5,6 则倒数第3个节点是4.
````

````java
// 双指针法
public ListNode findKthToTail(ListNode head, int k) {
        if(head == null || k < 1){
            return null;
        }
        int step = k - 1;
        ListNode post =head;
        while(step > 0){
            post = post.next;
            if(post == null){ // 链表长度小于k
                return null;
            }
            step--;
        }
        ListNode pre = head;
        while(post.next != null){
            pre = pre.next;
            post = post.next;
        }
        return pre;
    }
````

### 链表的中间节点

````txt
快慢指针，两个链表同时从head往右走，一个一次走一步，一个走两步，则一个到达链表尾部时，另一个在链表中间。
````

### 链表中环的入口节点-P139

```java
/**
 * 第一步：判断链表中是否有环。
 *   设置连个指针同时指向头节点，一个指针每次走两步，一个指针每次走一步，如果存在环，则两个指针一定可以相遇，相遇的节点一定是*   *  环内的某一点。
 * 第二部：确定环中节点数目。
 *   从第一步中可以得到环中的一个节点，设置一个指针指向这个节点，遍历环一次，统计出有多少个节点。
 * 第三步：找出环的入口。
 *   因为环的长度已知了（设为k），那么就是知道了入口所在的位置（倒数的位置），可以参考上一题的思想链表中倒数第k个节点，
 *   设置两个指针指向头结点，先让一个指针走k步，然后两个指针一起走。与上一题不同之处在于，
 *   我们不需要判断是否到达了链表尾部，只需考虑两个指针是否指向了同一个对象即可。
 */
class ListNode {
    int val;
    ListNode next = null;
    ListNode(int val) {
        this.val = val;
    }
}

public class Solution {

    public ListNode EntryNodeOfLoop(ListNode pHead)
    {
        ListNode aNodeInLoop = IsLoop(pHead);
        int numInLoop = 0;
        if(aNodeInLoop == null) {
            return null;
        }
        else {
            numInLoop = NumNodeInLoop(aNodeInLoop);
        }
        ListNode first = pHead;
        ListNode second = pHead;
        while(numInLoop > 0) {
            first = first.next;
            numInLoop--;
        }
        while(first != second){
            first = first.next;
            second = second.next;
        }
        return first;

    }
    //判断是否有环，如果有，返回两个指针相遇的节点，否则返回null
    private ListNode IsLoop(ListNode pHead) {
        if(pHead == null) {
            return null;
        }
        ListNode slow = pHead.next;
        if(slow == null) {
            return null;
        }
        ListNode fast = pHead.next.next;
        if(fast == null) {
            return null;
        }
        while(slow != null && fast != null) {
            if(slow == fast) {
                return slow;
            }
            slow = slow.next;
            fast = fast.next;
            if(fast != null) {
                fast = fast.next;
            }
        }
        return null;
    }

    //环中的节点数目
    private int NumNodeInLoop(ListNode pHead) {
        ListNode temp = pHead.next;
        int res = 1;
        while(temp != pHead) {
            temp = temp.next;
            res++;
        }
        return res;
    }
}
```

### 反转链表-P142

```txt
给一个链表head，输出该链表反转后的head。
```

![image-20210321115112274](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210321115112274.png)

```txt
每次反转需要3个指针，如上图所示。当要反转的节点为i时，需要知道i的前一个节点（i需要指向前一个节点）和i后一个节点（避免链表断裂）
```

```java
public ListNode reverseList(ListNode head){
        if(head == null){
            return null;
        }
        // 只有一个节点
        if(head.next==null){
            return head;
        }
        ListNode pre = head;
        ListNode cur = head.next;
        ListNode post = cur.next;
        // 第一个节点的next为null
        pre.next = null;
        // 只有两个节点
        if (post == null) {
            cur.next = pre;
            return cur;
        }
        while (post != null){
            cur.next = pre;
            pre = cur;
            cur = post;
            post = post.next;
        }
        cur.next = pre;
        return cur;
    }
```

### 合并两个排序的链表-P145

```txt
和并两个递增的链表，合并后的新链表任然是递增的，返回新链表的头指针。
```

```java
public ListNode mergeOrderedLists(ListNode head1, ListNode head2) {
        if (head1 == null && head2 != null) {
            return head2;
        } else if (head1 != null && head2 == null) {
            return head1;
        } else if (head1 == null && head2 == null) {
            return null;
        }

        ListNode phead1 = head1;
        ListNode phead2 = head2;
        ListNode cur = null;
        ListNode head = null;
        // 初始化第一个节点
        if (phead1.val <= phead2.val) {
            cur = phead1;
            phead1 = phead1.next;
        } else {
            cur = phead2;
            phead2 = phead2.next;
        }
        head = cur;
        // 合并链表
        while (phead1 != null && phead2 != null) {
            if (phead1.val <= phead2.val) {
                cur.next = phead1;
                phead1 = phead1.next;
            } else {
                cur.next = phead2;
                phead2 = phead2.next;
            }
            cur = cur.next;
        }
        // 合并剩下的链表
        ListNode p = (phead1 == null) ? phead2 : phead1;
        while (p != null) {
            cur.next = p;
            cur = cur.next;
            p = p.next;
        }
        return head;

    }
```

### 树的子结构-P148

```txt
输入两棵二叉树A，B，判断B是不是A的子结构。
```

```java
public class Solution {
    // 第一步在treeNode1中找与treeNode2根节点值一样的节点
    public boolean hasSubTree(TreeNode treeNode1, TreeNode treeNode2) {
        boolean res = false;
        if (treeNode1 != null && treeNode2 != null) { //递归终止条件
            if (Double.compare(treeNode1.val, treeNode2.val) == 0) {
                res = tree1HasTree2(treeNode1, treeNode2); // 本层
            }
            // 下层
            if (!res) {
                res = hasSubTree(treeNode1.left, treeNode2);
            }
            if (!res) {
                res = hasSubTree(treeNode1.rigth, treeNode2);
            }
        }
        return res;
    }

    // 第二步判断treeNode1中以R为根节点的子树是否与treeNode2有相同的结构
    private boolean tree1HasTree2(TreeNode treeNode1, TreeNode treeNode2) {
        // 递归终止条件
        if (treeNode2 == null) {
            return true;
        }
        if (treeNode1 == null) {
            return false;
        }
        //本层
        if (Double.compare(treeNode1.val, treeNode2.val) != 0) {
            return false;
        }
        // 下层
        return tree1HasTree2(treeNode1.left, treeNode2.left) &&
                tree1HasTree2(treeNode1.rigth, treeNode2.rigth);
    }
}
```

# 解决面试题的思路

## 画图让抽象的问题形象化

### 二叉树的镜像-P157

![image-20210323090803466](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210323090803466.png)

```java
public void mirrorRecursively(TreeNode head) {
        // 递归结束条件
        if (head == null) {
            return;
        }
        if (head.left == null && head.rigth == null) {
            return;
        }
        // 本层
        TreeNode temp = head.left;
        head.left = head.rigth;
        head.rigth = temp;
        // 下层
        if (head.left != null) {
            mirrorRecursively(head.left);
        }
        if (head.rigth != null) {
            mirrorRecursively(head.rigth);
        }

    }
```

### 对称的二叉树-P159

```
通过定义一种遍历方法（根、右、左）发现遍历出来的序列和前序遍历序列一直，则这棵二叉树就是对称的二叉树。
如第一棵树前序：8,6,5,7,6,7,5  对称前序遍历为: 8,6,5,7,6,7,5。 一致则为对称二叉树。对于第三棵树，考虑null即可。
```

![image-20210323093326776](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210323093326776.png)

```java
public class Solution {
    public boolean isSymmetrical(TreeNode head){
        return isSymmetrical(head, head);
    }
    private boolean isSymmetrical(TreeNode head1, TreeNode head2){
        // 递归结束
        if (head1 == null && head2 == null) {
            return true;
        }
        if (head1 == null || head2 == null) {
            return false;
        }
        // 本层
        if (head1.val != head2.val) {
            return false;
        }
        // 下层
        return isSymmetrical(head1.left, head2.rigth) &&
                isSymmetrical(head1.rigth, head2.left);
    }
}
```

### 顺时针打印矩阵-P161

```
输入一个矩阵，从外到内顺时针打印矩阵。如
1  2  3  4
5  6  7  8
9  10 11 12
13 14 15 16， 打印顺序为1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10。
```

```java
public class Solution {
    public void printMatrixClockwisely(int[][] array) {
        if (array == null || array.length == 0 || (array.length == 1 && array[0].length == 0)) {
            return;
        }
        int rows = array.length;
        int cols = array[0].length;
        int start = 0;
        while (cols > start * 2 && rows > start * 2) {  // 终止条件
            printMatrixClockwisely(array, start, rows, cols);
            start++;
        }
    }

    private void printMatrixClockwisely(int[][] array, int start, int rows, int cols) {
        int endX = cols - 1 - start;
        int endY = rows - 1 - start;
        // 从左到右打印一行
        for (int i = start; i <= endX; i++) {
            System.out.print(array[start][i]+" ");
        }
        // 从上到下打印一行
        // 终止行大于起始行
        if(endY > start) {
            for (int i = start+1; i <= endY; i++) {
                System.out.print(array[i][endX]+" ");
            }
        }
        // 从右到左打印一行
        // 终止行大于起始行 && 终止列大于起始列
        if (endY > start && endX > start) {
            for (int i = endX-1; i >= start; i--) {
                System.out.print(array[endY][i]+" ");
            }
        }
        // 从下到上打印一行
        // 终止行大于起始行+1(至少三行) && 终止列大于起始列
        if (endY > start + 1 && endX > start) {
            for (int i = endY - 1; i >= start+1 ; i--) {
                System.out.print(array[i][start]+" ");
            }
        }
    }
} 
```

## 举例让抽象问题具体化

### 包含min函数的栈-P165

```
定义栈数据结构，支持min、push、pop函数，且时间复杂度都是o(1)。
该题仅仅用一个变量记录最小值解决不了问题，因为如果将最小值pop后，将无法得到次最小值，所以此题应该建立一个辅助栈。

tips：当题目有时间复杂度要求且常规方法的不到解时，我们可以从增加空间复杂度的思路去思考如何解决问题。

```

```java
class MyStack{
    private Deque<Integer> stack;
    private Deque<Integer> minStack;
    public MyStack(){
        stack = new LinkedList<>();
        minStack = new LinkedList<>();
    }

    public int min(){
        if(minStack.isEmpty()){
            System.out.println("stack is empty!");
            return Integer.MIN_VALUE;
        }else {
            return minStack.peek();
        }
    }

    public void push(int i){
        stack.push(i);
        if(minStack.isEmpty()){
            minStack.push(i);
            return;
        }
        if(i < minStack.peek()){
            minStack.push(i);
        }else {
            minStack.push(minStack.peek());
        }
    }
    public int pop(){
        minStack.pop();
        return stack.pop();
    }
}
```

### 栈的压入、弹出序列-P168

```txt
输入两个整数序列，第一个表示栈的压入序列，第二个表示栈的弹出序列。判断第二个是否为该栈的弹出序列。假设压入栈的所有数字均不相等。例如，压栈序列1,2,3,4,5  则序列4,5,3,2,1是该栈的一个出栈序列，而4,3,5,1,2就不是该栈的弹出序列。
```

```java
public class Solution {
    public boolean isPopOrder(int[] pushArrays, int[] popArrays){
        if(pushArrays == null || popArrays==null || (pushArrays.length != popArrays.length)){
            return false;
        }
        Deque<Integer> stack = new LinkedList<>();
        int j = 0;
        for (int i = 0; i < pushArrays.length; i++) {
            if(stack.isEmpty()){
                stack.push(pushArrays[i]);
            }else {
                while (!stack.isEmpty() && j < popArrays.length && stack.peek()==popArrays[j]){
                    stack.pop();
                    j++;
                }
                if (!stack.isEmpty() && j < popArrays.length && stack.peek()!=popArrays[j]){
                    stack.push(pushArrays[i]);
                    continue;
                }
                if (!stack.isEmpty() && j >= popArrays.length) {
                    return false;
                }
            }
        }
        while (!stack.isEmpty() && j < popArrays.length && stack.peek()==popArrays[j]){
            stack.pop();
            j++;
        }
        if(stack.isEmpty() && j == popArrays.length){
            return true;
        }else {
            return false;
        }
    }
}
```



### 从上到下打印二叉树-P171

```txt
宽序遍历二叉树。如下图二叉树，遍历序列为8,6,10,5,7,9,11.
```

<img src="https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/image-20210324093856223.png" alt="image-20210324093856223" style="zoom:50%;" />

```java
// 我的实现 添加完再打印，递归 辣鸡
public class Solution {

    public void printTreeBFS(TreeNode head) {
        if(head == null){
            return;
        }
        Queue<Integer> queue = new LinkedList<>();
        queue.offer(head.val); // 先放头结点
        printTreeBFS(head, queue);
        queue.forEach((s)->System.out.print(s+" "));

    }
    private void printTreeBFS(TreeNode head, Queue<Integer> queue) {
        if(head == null){ // 递归结束条件
            return;
        }
        // 本层
        if(head.left != null){
            queue.offer(head.left.val);
        }
        if(head.right != null){
            queue.offer(head.right.val);
        }
        // 下层
        printTreeBFS(head.left, queue);
        printTreeBFS(head.right, queue);
    }
}

// 别人的实现，边打印边添加到队列，循环 牛逼
public void printTreeBFS(TreeNode head) {
        if (head == null) {
            return;
        }
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(head);
        while (!queue.isEmpty()) {
            TreeNode poll = queue.poll();
            if (poll.left != null) {
                queue.offer(poll.left);
            }
            if (poll.right != null) {
                queue.offer(poll.right);
            }
            System.out.print(poll.val+" ");
        }
}
```

### 分行从上到下打印二叉树-P175

```java
// 分行打印树的每一行
public void printTreeBFS(TreeNode head) {
    if (head == null) {
        return;
    }
    int curNum = 1; // 记录本层要打印的数量
    int nextNum = 0; // 记录下层要打印的数量
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(head);
    while (!queue.isEmpty()) {
        TreeNode poll = queue.poll();
        System.out.print(poll.val+" ");
        curNum--;
        if (poll.left != null) {
            nextNum++;
            queue.offer(poll.left);
        }
        if (poll.right != null) {
            nextNum++;
            queue.offer(poll.right);
        }
        if (curNum == 0) {
            System.out.println();
            curNum = nextNum;
            nextNum = 0;
        }
    }
}
```

### 之字型打印二叉树-P176

<img src="https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327111222.png" alt="image-20210324103955132" style="zoom:50%;" />

```txt
使用两个栈来保存节点，一个栈保存当前遍历的节点，另一个栈保存下层遍历的节点。
规律：如果当前是奇数层（1,3）时，先保存左孩子，然后保存右孩子（栈遍历时会从右到左）；
如果当前是偶数层（2,4）时，先保存右孩子，再保存左孩子（栈遍历会从左到右）。
```

```java
public class Solution {
    public void printTreeInChineseZhi(TreeNode head) {
        if (head == null) {
            return;
        }
        Deque<TreeNode> stack1 = new LinkedList<>();
        Deque<TreeNode> stack2 = new LinkedList<>();
        stack1.offer(head);
        while (!stack1.isEmpty() || !stack2.isEmpty()) {
            while (!stack1.isEmpty()) {
                TreeNode temp = stack1.pop();
                if (temp.left != null) {
                    stack2.push(temp.left);
                }
                if (temp.right != null) {
                    stack2.push(temp.right);
                }
                System.out.print(temp.val + " ");
            }
            System.out.println();
            while (!stack2.isEmpty()) {
                TreeNode temp = stack2.pop();
                if (temp.right != null) {
                    stack1.push(temp.right);
                }
                if (temp.left != null) {
                    stack1.push(temp.left);
                }
                System.out.print(temp.val + " ");
            }
            System.out.println();
        }
    }
}
```

### 二叉搜索树的后序遍历序列-P179

```
输入一个整数数组，任意两个元素都不一样。判断该数组是不是某个二叉搜索树的后续遍历序列结果。
例如，输入{5,7,6,9,11,10,8}，返回true，它是下图的后序遍历序列；输入{7,4,6,5}，返回false。
```

<img src="https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327111203.png" alt="image-20210327092343944" style="zoom:67%;" />

```java
public class Solution {
    public boolean verifySequenceOfBST(int[] sequence) {
        if (sequence == null || sequence.length == 0) {
            return false;
        }
        return isBST(sequence, 0, sequence.length - 1);
    }

    private boolean isBST(int[] seq, int start, int end) {
        if (start >= end) {
            return true;
        }
        int val = seq[end];
        int split = start;
        for (; split < end && seq[split] < val; split++) ;
        for (int i = split; i < end; i++) {
            if (seq[i] < val) {
                return false;
            }
        }
        return isBST(seq, start, split - 1) &&
                isBST(seq, split, end - 1);
    }
}
```

### 二叉树中和为某一值得路径-P182

<img src="https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210327111203.png" alt="image-20210327110322493" style="zoom:50%;" />

```
输入一棵二叉树和一个值，打印出二叉树中节点值和为目标值得所有路径（从根节点一直到叶子节点构成一条路径）。
```

```java
public class Solution {
    public void findPath(TreeNode head, int value) {
        if (head == null) {
            return;
        }
        Deque<Integer> stack = new LinkedList<>();
        findPath(head, value, stack, 0);
    }

    private void findPath(TreeNode head, int value, Deque<Integer> stack, int curSum) {
        curSum += head.val;
        stack.push(head.val);
        // 找到一个路径
        if (value == curSum && head.left==null && head.right==null) {
            stack.forEach((s)-> System.out.print(s+" "));
            System.out.println();
        }
        //下层
        if (head.left != null) {
            findPath(head.left,value,stack,curSum);
        }
        if (head.right != null) {
            findPath(head.right,value,stack,curSum);
        }
        // 返回父节点前，在路径上删除当前节点
        stack.pop();
    }
}
```



## 分解让复杂问题简单化

### 复杂链表的复制-P187

```
实现函数ComplexListNode clone(ComplexListNode head),复制一个复杂链表。在复杂链表中，每个节点有next指针指向下一个节点，还有random指针随机指向一个节点或null。
```

#### 普通人思路

```
先复制主干链表，再循环寻找random指针所指向的。时间复杂度O(n^2)
```

#### 进阶思路

```
在复制主干链表的每个节点S的同时，将（S，S'）存入哈希表。接下来再次遍历链表，可在O（1）的时间找到S的random指针指向的N,则找到对应的N'。时间O（n），空间O（n）,相当于时间换空间。
```

#### 终极思路

```
分成三步解决这个问题：
	1、复制主干链表并将每个节点S对应的S’的插到S的后面。
	2、遍历一次，连接random，对于S的random，S‘的random’为random.next。
	3、拆分链表，在偶数位（0,2,4...）的即为原链表，在奇数位（1,3,5...）的为复制后的链表。
时间O(n)，空间O(1)
```

![image-20210328085246609](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328085253.png)

![](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328085253.png)

![image-20210328085308352](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328085308.png)

```java
class ComplexListNode {
	public int m_nValue;
	ComplexListNode m_pNext;
	ComplexListNode m_pSibling;
}

class Solution{
   
    //第一步： 根据原始结点A在其后面创建A'。
    private void cloneNodes(ComplexListNode pHead){
        ComplexListNode pNode = pHead;
        while(pNode != null){
            //创建pCloned结点即A'结点使其指向原始链表中A结点的下一结点B,不过A'的m_pSibling设置为null
            ComplexListNode pCloned = new ComplexListNode();
            pCloned.m_nValue = pNode.m_nValue;
            pCloned.m_pNext = pNode.m_pNext;
            pCloned.m_pSibling = null;
            //将A结点指向A’结点
            pNode.m_pNext = pCloned;
            //使pNode指向A的下一结点B并以此循环修改(此时中间已将克隆结点A‘插入了原始列表)
            pNode = pCloned.m_pNext;
        }
    }

    // 第二步：设置每个结点的m_pSibling(注：m_pSibling为空结点不做修改)
    private void connectSiblingNodes(ComplexListNode pHead){
        ComplexListNode pNode = pHead;
        while(pNode!=null){
            ComplexListNode pCloned = pNode.m_pNext;
            if(pNode.m_pSibling!=null){
                pCloned.m_pSibling = pNode.m_pSibling.m_pNext;
            }
            pNode = pCloned.m_pNext;
        }
    }

    // 第三步：拆分链表
    private ComplexListNode reconnectNodes(ComplexListNode pHead){
        ComplexListNode pNode = pHead;
        ComplexListNode pClonedHead = null;
        ComplexListNode pClonedNode = null;
        if(pNode!=null){
            pClonedHead = pClonedNode = pNode.m_pNext;
            pNode.m_pNext = pClonedNode.m_pNext;
            pNode = pNode.m_pNext;
        }
        while(pNode!=null){
            pClonedNode.m_pNext = pNode.m_pNext;
            pClonedNode = pClonedNode.m_pNext;
            pNode.m_pNext = pClonedNode.m_pNext;
            pNode = pNode.m_pNext;
        }
        return pClonedHead;
    }

    //上面的三步合起来即为复杂链表的复制
    public ComplexListNode clone(ComplexListNode pHead){
        cloneNodes(pHead);
        connectSiblingNodes(pHead);
        return reconnectNodes(pHead);
    }
    
}
```



### 二叉搜索树与双向链表-P191

```
输入一颗二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向，比如输入下图中左边的二叉搜索树，则输出转换之后的排序双向链表。
```

![image-20210328095344485](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328095344.png)

![image-20210328095417601](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328095417.png)

```java
public class Solution {
    private TreeNode head = null; //定义链表当前结点
    private TreeNode realHead = null; //定义链表头部的结点

    //中序递归遍历修改链表指针即可实现
    public TreeNode convert(TreeNode pRootOfTree) {
        if (pRootOfTree == null) {
            return null;
        }
        convert(pRootOfTree.left); //左

        if (head == null) { //根
            head = pRootOfTree;
            realHead = pRootOfTree; // realHead指向最左
        } else {
            head.right = pRootOfTree; // 当前节点连在head的右边
            pRootOfTree.left = head;
            head = pRootOfTree; // 更新head
        }

        convert(pRootOfTree.right); //右
        return realHead;
    }
}


//思路二： 非递归借助栈实现
public class Solution {
    public TreeNode convert(TreeNode pRootOfTree){
        if(pRootOfTree == null){
            return null;
        }
        TreeNode list = null;
        Stack<TreeNode> stack = new Stack<TreeNode>();
        while(pRootOfTree != null || !stack.isEmpty()){
            // 右 根 左
            if(pRootOfTree != null){ // 右
                stack.push(pRootOfTree);
                pRootOfTree = pRootOfTree.right;
            }else{
                pRootOfTree = stack.pop(); // 根
                if(list == null){
                    list = pRootOfTree;
                }else{ // 每次往list的左边插入
                    list.left = pRootOfTree;
                    pRootOfTree.right = list;
                    list = pRootOfTree;
                }
                pRootOfTree = pRootOfTree.left; // 左
            }
        }
        return list;
    }
}
```

### 序列化二叉树-P194

```txt
实现两个函数，分别用来序列化和反序列化二叉树。
```

```java
/*
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;
    public TreeNode(int val) {
        this.val = val;
    }
}
/*
    算法思想：根据前序遍历规则完成序列化与反序列化。
    所谓序列化指的是遍历二叉树为字符串；所谓反序列化指的是依据字符串重新构造成二叉树。
    依据前序遍历序列来序列化二叉树，因为前序遍历序列是从根结点开始的。
    当在遍历二叉树时碰到Null指针时，这些Null指针被序列化为一个特殊的字符“#”。
    另外，结点之间的数值用逗号隔开。
*/
public class Solution {
    private StringBuilder sb = new StringBuilder();
    //序列化二叉树	
	public String Serialize(TreeNode root) {
		if(root == null){
			sb.append("#,");
			return sb.toString();
		}
		sb.append(root.val+",");
		sb.append(Serialize(root.left));
		sb.append(Serialize(root.right));
		return sb.toString();
	}
    
    
	private int index = -1;
	//反序列化二叉树
	public TreeNode Deserialize(String str) {
		index++;
		int len = str.length();
		if(index>=len){
			return null;
		}
		String [] strr = str.split(",");
		TreeNode node = null;
		if(!strr[index].equals("#")){
			node = new TreeNode(Integer.valueOf(strr[index]));
			node.left = Deserialize(str);
			node.right = Deserialize(str);			
		}
		return node;
	}
	
}
```

### 数字全排列

```java
public class Solution {

    public void generate(int n) {
        int[] arrays = new int[n];
        boolean[] isUsed = new boolean[n];
        generate(0, n, arrays, isUsed);
    }

    private void generate(int cur, int n, int[] arrays, boolean[] isUsed) {
        if (cur == n) {
            System.out.println(Arrays.toString(arrays));
            return;
        }
        for (int i = 0; i < n; i++) { // 每个cur位置可选值
            if (isUsed[i] == false) {
                arrays[cur] = i;
                isUsed[i] = true; 
                generate(cur+1,n,arrays,isUsed);
                isUsed[i] = false;
            }
        }
    }
}
```



### 字符串的全排列-P197

```txt
输入一个字符串，打印字符串中所有字符的全排列。
例如abc，打印abc、acb、bac、bca、cab、cba。
```

```java
public class Solution {
    public void generate(String s) {
        char[] arrays = new char[s.length()];
        boolean[] isUsed = new boolean[s.length()];
        generate(0, s.length(),s.toCharArray(), arrays, isUsed);
    }

    private void generate(int cur, int n,char[] chars, char[] arrays, boolean[] isUsed) {
        if (cur == n) {
            System.out.println(Arrays.toString(arrays));
            return;
        }
        for (int i = 0; i < n; i++) { // 每个cur位置可选值
            if (isUsed[i] == false) {
                arrays[cur] = chars[i];
                isUsed[i] = true;
                generate(cur+1,n,chars,arrays,isUsed);
                isUsed[i] = false;
            }
        }
    }
}

// 方法二
 /*
     * 参数arrayA:给定字符串的字符数组
     * 参数start:开始遍历字符与其后面各个字符将要进行交换的位置
     * 参数end:字符串数组的最后一位
     * 函数功能：输出字符串数字的各个字符全排列
     */
    public void recursionArrange(char[] arrayA,int start,int end){
        if(end <= 1)   
             return;
        if(start == end){
            for(int i = 0;i < arrayA.length;i++)
                System.out.print(arrayA[i]);
            System.out.println();
        }
        else{
            for(int i = start;i <= end;i++){
                swap(arrayA,i,start);
                recursionArrange(arrayA,start+1,end);
                swap(arrayA,i,start);
            }
        }
        
    }
    //交换数组m位置和n位置上的值
    public void swap(char[] arrayA,int m,int n){
        if(m==n) return;
        char temp = arrayA[m];
        arrayA[m] = arrayA[n];
        arrayA[n] = temp;
    }
```

# 优化时间和空间效率

## 时间效率

### 数字中出现次数超过一半的数字-P205

```
数组中有一个数字出现的次数超过数组长度的一半，找出这个数字。
例如{1,2,3,2,2,2,5,4,2}，输出2.
```

#### 基于Partition函数的时间O（n）的算法

```java
public class Solution {

    public int moreThanHalfNum(int[] arrays) {
        if (arrays == null || arrays.length == 0) {
            throw new RuntimeException("input invalid！");
        }
        int mid = arrays.length / 2;
        int start = 0;
        int end = arrays.length - 1;
        int index = partition(arrays, start, end);
        while (index != mid) {
            if (index > mid) {
                end = index - 1;
                index = partition(arrays, start, end);
            } else {
                start = index + 1;
                partition(arrays, start, end);
            }
        }
        return arrays[index];
    }
}
```



#### 根据数组特点的O（n）算法

```
由于要寻找的这个数的个数大于其他数的个数之和。则可以定义两个变量，一个记录数S，一个记录S出现的次数。
```

![image-20210328123019873](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210328123020.png)

```java
public class Solution {
    public int moreThanHalfNum(int[] arrays) {
        if (arrays == null || arrays.length == 0) {
            throw new RuntimeException("input invalid！");
        }
        int res = arrays[0];
        int times = 1;
        for (int i = 0; i < arrays.length; i++) {
            if (times == 0) {
                res = arrays[i];
                times = 1;
            } else if (arrays[i] == res) {
                times++;
            } else {
                times--;
            }
        }
        if (times > 0) {
            return res;
        }
        else {
            throw new RuntimeException("input invalid！");
        }
    }
}
```

### 最小的k个数P-209

```txt
输入n个整数，找出其中最小的k个数。
如{4,5,1,6,2,7,3,8,}，则最小的4个数是{1,2,3,4}
```

#### 方法一

```
对数组进行排序，时间复杂度O（nlogn）
```

#### 方法二

```
该方法会修改输入的数组。基于partition函数来解决，直到(index=partition() == k-1)，得到的k个最小数不一定是排序的。时间复杂度O(n)
```

#### 方法三

```
如果不能修改输入的数据。我们定义一个容量为k的容器。遍历输入，若容器为空直接插入；若容器不为空，取容器中最大的数与待插入的数进行比较，若待插入的数小于最大的数，则替换最大的数，否则直接抛弃该带插入的数。另外，这个容器使用大根堆实现。适合处理海量数据。时间复杂度O(nlogk)
```

```java
public class Solution {
    public Integer[] getLeastNumbers(int[] arrays, int k) {
        if (arrays == null || arrays.length == 0 || arrays.length < k) {
            return null;
        }
        // 默认是最小堆，定制为最大堆
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(k, (x, y) -> y - x);

        for (int i = 0; i < arrays.length; i++) {
            if (maxHeap.size() != k) {
                maxHeap.offer(arrays[i]);
            } else if (maxHeap.peek() > arrays[i]) {
                maxHeap.poll();
                maxHeap.offer(arrays[i]);
            }
        }
        Integer[] res = new Integer[k];
        maxHeap.toArray(res);
        return res;
    }
}
```

​	

### 数据流中的中位数-P214

```
如果数据流中读出了奇数个数值，那么中位数就是排序后的中间的数值；如果数据流中读出了偶数个数值，那么中位数就是排序后中间两个数的平均值。
```

|    数据结构    |   插入时间复杂度    | 得到中位数的时间复杂度 |
| :------------: | :-----------------: | :--------------------: |
| 没有排序的数组 |        O(1)         |          O(n)          |
|    排序数组    |        O(n)         |          O(1)          |
|    排序链表    |        O(n)         |          O(1)          |
|   二叉搜索树   | 平均O(logn)最差O(n) |  平均O(logn)最差O(n)   |
|     AVL树      |       O(logn)       |          O(1)          |
|   最大最小堆   |       O(logn)       |          O(1)          |

```
我们可以把输入的数据流分为两个部分，左边的部分使用最大堆存储，右边的部分使用最小堆存储。所以可以快速地得到中位数。
```

```java
public class Solution {

   //生成最大堆
    PriorityQueue<Integer> maxHeap = new PriorityQueue<Integer>(11,new maxHeapComparator());
    //生成最小堆
    PriorityQueue<Integer> minHeap = new PriorityQueue<Integer>(11,new minHeapComparator());
    
    int maxSize = 0;//初始化堆的大小，防止NullPointerException
    int minSize = 0;
    
    public void reshapeHeapSize(){//平衡两堆元素的数目
        if(this.maxHeap.size() == this.minHeap.size() + 2){
            this.minHeap.add(this.maxHeap.poll());
        }
        if(this.minHeap.size() == this.maxHeap.size() + 2){
            this.maxHeap.add(this.minHeap.poll());
        }
    }
    
    public void Insert(Integer num) {
        
        if(this.maxHeap.isEmpty()){
            this.maxHeap.add(num);
        }else{
            if(this.maxHeap.peek() > num){
                this.maxHeap.add(num);
            }else{
                if(this.minHeap.isEmpty()){
                    this.minHeap.add(num);
                }else{
                    if(this.minHeap.peek() <= num){
                        this.minHeap.add(num);
                    }else{
                        this.maxHeap.add(num);
                    }
                }
            }
        }
        reshapeHeapSize();
    }

    public Double GetMedian() {
    	if(!maxHeap.isEmpty()){
    		 maxSize = this.maxHeap.size();
    	}
    	if(!minHeap.isEmpty()){
    		minSize = this.minHeap.size();
    	}
        if(maxSize+minSize == 0){
            throw new RuntimeException();
        }
        Integer maxHeapNum = this.maxHeap.peek();
        Integer minHeapNum = this.minHeap.peek();
        if(((maxSize + minSize) % 2) == 0){
            return (double)((maxHeapNum+minHeapNum))/2;
        }else{
            return (double)(maxSize > minSize ? maxHeapNum : minHeapNum);
        }
    }
    
    public class minHeapComparator implements Comparator<Integer>{
        public int compare(Integer o1,Integer o2){
            return o1-o2;
        }
    }
    public class maxHeapComparator implements Comparator<Integer>{
        public int compare(Integer o1,Integer o2){
        	return o2-o1;
        }
    }
}
```

### 连续子数组的最大和-P218

```
输入一个整型数组。求所有子数组的和的最大值。要求时间复杂度O(n)
例如{1，-2,3,10，-4,7,2，-5}的和最大子数组为{3,10，-4,7,2}，最大和为18。
```

#### 分析数据规律

![image-20210329103639656](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329103646.png)

```java
public int findMaxSumOfSubArray(int[] numbers) {
    if (numbers == null || numbers.length == 0) {
        throw new RuntimeException("input invalid!");
    }
    int maxSum = Integer.MIN_VALUE;
    int curSum = 0;
    for (int i = 0; i < numbers.length; i++) {
        curSum += numbers[i];
        if (curSum < 0) {
            curSum = 0;
        }
        if (curSum > maxSum) {
            maxSum = curSum;
        }
    }
    return maxSum;
}	
```

#### 动态规划

![image-20210329104344174](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210329104344.png)

```java
public int findMaxSumOfSubArray(int[] numbers) {
    if (numbers == null || numbers.length == 0) {
        throw new RuntimeException("input invalid!");
    }
    // 1, -2, 3, 10, -4, 7, 2, -5
    int[] dp = new int[numbers.length + 1];
    dp[0] = 0;
    for (int i = 0; i < numbers.length; i++) {
        if (i == 0 || dp[i] <= 0) {
            dp[i + 1] = numbers[i];
        } else {
            dp[i + 1] = dp[i] + numbers[i];
        }
    }
    return Arrays.stream(dp).max().getAsInt();
}
```



### 1~n整数中1出现的次数-P221

```
输入一个整数n，求1~n这n个整数的十进制表示中出1出现的次数。
例如，输入了12，则包含1的数字有1,10,11,12,所以1一共出现了5次。
```

![image-20210331091839767](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210331091846.png)

![image-20210331091855507](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210331091855.png)

```java
public static int count(int n) {
        if (n < 1) {
            return 0;
        }

        int count = 0; //计数器
        int base = 1;  //从个位数开始基数为1，十位数的基数为10...
        int round = n;

        while (round > 0) {
            //表示十进制的每一位，从各位数开始
            int weight = round % 10;

            //表示每一次weight位上的数要从0-9变换多少个轮回
            round = round / 10;

            //当weight位上为0的时候
            count = count + round * base;

            if (weight == 1) {
                count = count + (n % base) + 1;
            } else if (weight > 1) {
                count = count + base;
            }
            //下一位的基数为前一位基数的十倍
            base = base * 10;
        }
        return count;
    }
```



### 数字序列中某一位出现的数字-P225

```
数字以0123456789101112131415……的格式序列化到一个字符序列中。从0开始计数，第5位是5，第13位是1，第19位是4.求任意第n位对应的数字。
```

![image-20210331105833478](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210331105833.png)



```java
public static int findNthDigit(int n) {
        if (n < 0) {
            return -1;
        }
        if (n == 0) {
            return 0;
        }
        //数字的长度为len; 从长度为1的数字开始, 也就是从个位数开始
        int len = 1;
        //长度为len的数字有count个
        long count = 9;
        //长度为len的第一个数字
        int start = 1;
        //确定第n位对应的数字的长度
        while (n > len * count) {
            n -= len * count; 
            //update
            len++;
            start = start * 10;
            count = count * 10;
        }
        //确定第n位对应的数字是哪个长度为len的数字
        start = start + (n % len == 0 ? n / len - 1 : n / len);
        //取出该数字的第(n-1)%len位
        String s = Integer.toString(start);
        return Character.getNumericValue(s.charAt(n % len == 0 ? len - 1 : n % len - 1));
    }
```

### 把数组排查最小的数-P227

```
输入一个正整数数组，打印数组里所有数字拼接出来最小的数。
例如{3,32,321} ，打印321323
```

```java
public static void printMinNumber(Integer[] numbers) {
        List<Integer> nums = new ArrayList<>(Arrays.asList(numbers));
        nums.stream().map(String::valueOf).sorted((x, y) -> x.concat(y).compareTo(y.concat(x))
        ).forEach(System.out::print);
}
```

### 把数字翻译成数字-P231

```
给定一个数字，按照规则把它翻译成字符串，0翻译为a，1翻译为b。。。25翻译为z。一个数字可能有多种翻译。如1228可翻译为bccfi、bwfi、bczi、mcfi、mzi。请计算一个数字有多少种不同的翻译方法。
```



```java
public class Solution {
    public int getTranslationCount(int number) {
        if (number < 0)
            return 0;
        if (number == 1)
            return 1;
        return getCount(Integer.toString(number));
    }

    //动态规划，从右到左计算。
    //f(r-2) = f(r-1)+g(r-2,r-1)*f(r);
    //如果r-2，r-1能够翻译成字符，则g(r-2,r-1)=1，否则为0
    private int getCount(String number) {
        int f1 = 0, f2 = 1, g = 0;
        int temp;
        for (int i = number.length() - 2; i >= 0; i--) {
            if (Integer.parseInt(number.charAt(i) + "" + number.charAt(i + 1)) < 26)
                g = 1;
            else
                g = 0;
            temp = f2;
            f2 = f2 + g * f1;
            f1 = temp;
        }
        return f2;
    }
}
```

### 礼物的最大值-P233

![image-20210402100147266](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210402100154.png)

```java
public class Solution {
    public static int getMaxValue(int[] values, int rows, int cols) {
        if (values == null || values.length == 0 || values.length < rows * cols) {
            return 0;
        }
        int[][] dp = new int[rows + 1][cols + 1];
        // base case
        for (int i = 0; i <= rows; i++) {
            dp[i][0] = 0;

        }
        for (int i = 0; i <= cols; i++) {
            dp[0][i] = 0;
        }
        
        
        for (int i = 1; i <= rows ; i++) {
            for (int j = 1; j <= cols ; j++) {
                dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]) + values[(i - 1) * cols + j - 1];
            }
        }
        return dp[rows][cols];
    }
}
```



### 最长不含重复字符串的子字符串-P236

```
从一个字符串中找出一个人最长的不包含重复字串的子字符串，计算该子字符串的长度。
如字符串arabcacfr，最长的不含重复字符的子字符串的长度是acfr，长度为4.
```

```java
/**
 * 主要思路：使用动态规划，记录当前字符之前的最长非重复子字符串长度f(i-1)，其中i为当前字符的位置。每次遍历当前字符时，分两种情况：
 *
 * 1）若当前字符第一次出现，则最长非重复子字符串长度f(i) = f(i-1)+1。
 * 2）若当前字符不是第一次出现，则首先计算当前字符与它上次出现位置之间的距离d。
 *
 *   a. 若d大于f(i-1)：即说明前一个非重复子字符串中没有包含当前字符，则可以添加当前字符到前一个非重复子字符串中，所以，f(i) = f(i-1)+1。
 *   b. 若d小于或等于f(i-1)：即说明前一个非重复子字符串中已经包含当前字符，则不可以添加当前字符，所以，f(i) = d。
 *
 * 关键点：动态规划，两个重复字符的距离
 */

public class Solution {
    public static int findLongestSubstringLength(String str) {
        if (str == null || str.length() == 0) {
            return 0;
        }
        int maxLength = 0;
        int curLength = 0;
        int[] positions = new int[26];
        // 字符位置初始化为-1，负数表示没出现过
        Arrays.fill(positions, -1);

        for (int i = 0; i < str.length(); i++) {
            int curChar = str.charAt(i) - 'a';
            int prePosition = positions[curChar];
            //当前字符与它上次出现位置之间的距离
            int distance = i - prePosition;
            //当前字符第一次出现，或者前一个非重复子字符串中没有包含当前字符
            if (prePosition < 0 || distance > curLength) {
                curLength++;
            } else {

                //更新最长非重复子字符串的长度
                if (curLength > maxLength) {
                    maxLength = curLength;
                }
                // 当前长度更新为上一次出现的距离
                curLength = distance;
            }
            //更新字符出现的位置
            positions[curChar] = i;
        }
        return (maxLength > curLength) ? maxLength : curLength;
    }
}
```



## 时间效率与空间效率的平衡

### 丑数-P240

```java
/**
 * 由丑数的定义：丑数应该是另一个丑数乘以2、3或者5的结果（1除外）。
 * 因此我们可以创建一个数组，里面的数字是排好序的丑数，每一个丑数都是前面的丑数乘以2、3或者5得到的。
 * 直观的优化措施就是看我们能不能降低时间复杂度，即只在丑数上花时间，而不在非丑数上浪费时间。
 * 故根据上面的丑数定义和思路，我们开辟O(n)的空间来得到时间复杂度为O(n）的算法。
 */
public class Solution {
    public static int getUglyNumber(int index) {
        if (index <= 0) {
            return 0;
        }
        List<Integer> list = new ArrayList<Integer>();
        list.add(1);
        int m2 = 0, m3 = 0, m5 = 0, i2 = 0, i3 = 0, i5 = 0, min = 0;
        while (list.size() < index) {
            m2 = list.get(i2) * 2;
            m3 = list.get(i3) * 3;
            m5 = list.get(i5) * 5;
            min = Math.min(m2, Math.min(m3, m5));
            list.add(min);
            if (min == m2) {
                ++i2;
            }
            if (min == m3) {
                ++i3;
            }
            if (min == m5) {
                ++i5;
            }
        }
        return list.get(index - 1);
    }
}
```

### 第一个只出现一次的字符

```
字符串中第一个只出现一次的字符。
如abaccdeff，输出b。
```

```java
// 利用哈希表可以容易计算出每个字符的个数，那么怎么取出第一个只出现一个的字符呢？
// 再遍历一次！！
public class Solution {
    public static char findFirstNotRepeatedChar(String str) {
        if (str == null || str.length() == 0) {
            // '#'代表无重复字符
            return '#';
        }
        Map<Character, Integer> map = new HashMap<>();
        for (int i = 0; i < str.length(); i++) {
            if(map.containsKey(str.charAt(i))){
                map.put(str.charAt(i), 1 + map.get(str.charAt(i)));
            }else {
                map.put(str.charAt(i), 1 );
            }
        }
        char res = '#';
        for (int i = 0; i < str.length(); i++) {
            if (map.get(str.charAt(i)) == 1) {
                res = str.charAt(i);
                break;
            }
        }
        return res;
    }
}
```

### 数组中的逆序对-P249

```
输入一个数组，求这个数组中逆序对的数量。
如{7,5,6,4}，有5对逆序对：{7,6}，{7,5}，{7,4}，{6,4}，{5,4}
```

```java
public class Solution {
	// 归并排序
    public static int inversePairs(int[] array) {
        if (array == null || array.length <= 0) {
            return 0;
        }
        int count = getCount(array, 0, array.length - 1);
        return count;
    }

    private static int getCount(int[] array, int start, int end) {
        if (start >= end) {
            return 0;
        }
        int mid = (end + start) >> 1;
        int left = getCount(array, start, mid);
        int right = getCount(array, mid + 1, end);
        
        //计数器
        int count = 0;
        //左边区域的指针
        int i = mid;
        //右边区域的指针
        int j = end;
        //临时区域
        int[] temp = new int[end - start + 1];
        //临时区域的指针
        int k = end - start;
        
        //合并
        while (i >= start && j >= mid + 1) {
            if (array[i] > array[j]) {
                //由于子数组是已排序的，所以count+=右边子数组剩余的长度
                count += (j - mid); 
                temp[k--] = array[i--];
            } else {
                temp[k--] = array[j--];
            }
        }
        while (i >= start) { //子数组中剩下的
            temp[k--] = array[i--];
        }
        while (j >= mid + 1) {
            temp[k--] = array[j--];
        }
        for (k = 0; k < temp.length; k++) {
            array[k + start] = temp[k];
        }

        return count + left + right;
    }

}
```

### 两个链表中的第一个公共节点-P253

![image-20210405094451313](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210405094458.png)

#### 快慢指针

```java
public static ListNode findFirstCommonNode(ListNode head1, ListNode head2) {
        if (head1 == null || head2 == null) {
            return null;
        }
        ListNode p = head1;
        int count1 = 0;
        while (p != null) {
            p = p.next;
            count1++;
        }
        p = head2;
        int count2 = 0;
        while (p != null) {
            p = p.next;
            count2++;
        }
        ListNode p1 = head1;
        ListNode p2 = head2;
        //快慢指针
        if (count1 >= count2) {
            int i = count1 - count2;
            for (int j = i; j > 0; j--) {
                p1 = p1.next;
            }
        } else {
            int i = count2 - count1;
            for (int j = i; j > 0; j--) {
                p2 = p2.next;
            }
        }
        while (p1 != null && p2 != null) {
            if (p1 == p2) {
                return p1;
            }
            p1 = p1.next;
            p2 = p2.next;
        }
        return null;
    }
```

#### 利用堆栈



#### 利用哈希表



# 面试中各项能力



## 知识迁移能力

### 在排序数组中查找数字-P263

#### 数字在排序数组中出现的次数-P263

```
在一个排序的数组中找到一个数字k出现的次数。
例如{1,2,3,3,3,3,4,5}，数字3出现了4次，输出4。
```



```java
//采用二分查找找到第一个k和最后一个k的位置
public class Solution {
    public int getNumberOfK(int[] nums, int k) {
        if (nums == null || nums.length == 0) {
            return -1;
        }
        int firstK = getFirstK(nums, 0, nums.length - 1, k);
        int lastK = getLastK(nums, 0, nums.length - 1, k);
        int res = -1;
        if (firstK != -1 && lastK != -1) {
            res = lastK - firstK + 1;
        }
        return res;

    }

    private int getFirstK(int[] nums, int start, int end, int k) {
        if (start > end) {
            return -1;
        }
        int mid = (start + end) / 2;
        // 不知为什么这样为stack overflow
        //int mid = (start + end) >> 2;
        if (nums[mid] > k) {
            return getFirstK(nums, start, mid - 1, k);
        } else if (nums[mid] < k) {
            return getFirstK(nums, mid + 1, end, k);
        } else {
            if (mid == 0 || nums[mid - 1] != k) { //k的前一个数不是k
                return mid;
            } else {
                return getFirstK(nums, start, mid - 1, k);
            }
        }

    }

    private int getLastK(int[] nums, int start, int end, int k) {
        if (start > end) {
            return -1;
        }
        int mid = (start + end) / 2;
        if (nums[mid] > k) {
            return getLastK(nums, start, mid - 1, k);
        } else if (nums[mid] < k) {
            return getLastK(nums, mid + 1, end, k);
        } else {
            if (mid == nums.length - 1 || nums[mid + 1] != k) {//k的后一个数不是k
                return mid;
            } else {
                return getLastK(nums, mid + 1, end, k);
            }
        }
    }
}


```

#### 0 ~ n-1中缺失的数字-P266

```
在长度为n-1的排序数组中所有数字都是唯一的，并且每个数字都在范围0 ~ n-1内。有且只有一个数字不在该数组中。
数字中第一个值和下标不相等下标就是缺失的数字，采用 二分+减治 解决即可
```



#### 数字中数值和下标相等的数字-P267

```
单调递增的数组里每个元素都是整数且唯一。找出任意数值等于其下标的元素。
二分查找即可。
```



### 二叉搜索树的第 k 大节点-P269

```java
public class E54KthBSTNode {
    //二叉搜索树中第k个节点
    private static int index;
    public static BinaryTreeNode getKthNode(BinaryTreeNode root, int k){
        if (root == null)
            return null;
        index = k;
        return getKthNodeCore(root);
    }

    private static BinaryTreeNode getKthNodeCore(BinaryTreeNode root) {
        //采用二叉树的中序遍历顺序查找第k个节点
        BinaryTreeNode target = null;
        if (root.left != null)
            target = getKthNodeCore(root.left);
        if (target == null){
            if (index == 1)
                target = root;
            index--;
        }
        if (target == null && root.right != null)
            target = getKthNodeCore(root.right);
        return target;
    }

}
```



### 二叉树的深度-P271

```
输入一棵二叉树，求树的深度。从根节点到叶子节点依次经过的节点构成的路径，最长的路径长度为树的深度。
```

```java
public class Solution {
    public static int treeDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int left = treeDepth(root.left);
        int right = treeDepth(root.right);
        return (left > right) ? (left + 1) : (right + 1);
    }
}
```



### 平衡二叉树-P273

```
输入一个树的根节点，判断该树是不是平衡二叉树（任意节点的左右子树的深度相差不超过1）。
```

递归求深度，效率差，节点多次访问

```java
public class Solution {
    public static int treeDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int left = treeDepth(root.left);
        int right = treeDepth(root.right);
        return (left > right) ? (left + 1) : (right + 1);
    }

    public static boolean isBalanced(TreeNode root) {
        if (root == null) {
            return true;
        }
        int left = treeDepth(root.left);
        int right = treeDepth(root.right);
        if (Math.abs(left - right) > 1) {
            return false;
        }
        return isBalanced(root.left) && isBalanced(root.right);
    }
}
```

后续遍历，每个节点只访问一次

```java

public class Solution {
    private boolean isBalanced = false;//最后的返回值
    public boolean IsBalanced_Solution(TreeNode root) {
            getDepth(root);
            return isBalanced;
    }
    
    public int getDepth(TreeNode root) {
        if(root == null) {
            isBalanced = true;
            return 0;
        }
        int left = getDepth(root.left);//左子树
        int right = getDepth(root.right);//右子树
        int depth = (left > right ? left : right) + 1;
        if(Math.abs(left - right) <= 1) {
            isBalanced = true;
        } else {
            isBalanced = false;
        }
        return depth;//下层的深度，上层可以接着用免得再遍历
    }
}
```

### 数组中数组出现的次数-P275

#### 数组中只出现一次的两个数字

==任意两个相同的数进行异或运算都等于0==

```
一个整型数组里除了两个数字外，其他的数字都出现了两次。找出这两个只出现一次的数字。
要求时间O(n)，空间O(1)。
如果只有一个数字出现一次，其他数字都出现两次，对数组里所有数进行异或运算的结果就是那个只出现一次的数字。
```

