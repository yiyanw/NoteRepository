# 少壮不努力，老大学算法

* [算法数据结构体系学习 - 左程云](https://ke.qq.com/user/index/index.html#/plan/cid=3067253&tid=103187834&term_id=103187834)
* 进阶版 - leetcode专题部分
* 边界控制不好写
  * 对数器

# 02 复杂度，对数器，二分法

## 复杂度

* 评估算法的核心指标
  * 时间复杂度
    * O(1) O(logN) O(N) O(NlogN) O(N^2) O(N^3) O(N^k) O(2^N) O(3^N) O(k^N) O(N!)
    * 通常要求时间复杂度在10^8计算次数以内
      * 比如数据量n为10^3, 那么O(n^3)的算法计算量为10^9，所以大概率通过不了
      * 比如数据量n为10^3, 那么O(n^2)的算法计算量为10^6，所以大概率可以通过
  * 空间复杂度
  
* 如何分析
  * 将算法分解到常数时间操作 O(1)
  * 忽略高阶项，和常数
    * 比如：等差数列求和公式 Sum([N, N-1, ... 1]) = aN^2 + bN + b = O(N^2)
  * 寻找最差情况
    * 平均情况和最好情况一般不用
  * 如何分析两个复杂度相等的算法
    * 先分析常数项谁低
    * 如果常数项一致，则进行实际运算，计算时间消耗
      * 因为不同常数操作的耗时并不一样
        * 比如：^ > + > /

## 对数器

* 通过**必对的较差算法**对新的算法进行答案比较，如果一直正确，说明新算法有效
  * 这样就不需要必须知道答案才能测试了，也就不需要依赖coding练习平台了

## 二分法

* 存在排他性就可以尝试进行二分法
* 细节
  * **while边界条件**可以按照自己习惯定义
    * 我这里学老师的，按照左右至少包含两个element为界定 代表[left, right]
    * while(left < right)
      * 需要while结束后，需要额外判断一次left或者right的idx代表的值
        * 因为有两种情况导致结束
          * 第一种情况	left = n, right = n+1 ---> mid = n
          * 结果为 left = right = n + 1
          * 或者 left = n, right = n - 1
          * 第二种情况  left = n, right = n + 2 ---> mid = n + 1
          * 结果为 left = right = n + 2
          * 或者 left = right = n
        * 综上，可以总结为两种结束情况
          * left = right - check哪个都行
          * left = right + 1 - 没有需要check的，也可以理解为就算check 也不会有任何影响
        * 所以我们需要额外check一次保证最后一个值不被漏掉
  * **如何取中** mid = (L + R) / 2
    * 有溢出风险: L+R > MAX_INT
    * 所以改为 mid = L + (R - L) / 2
    * 进一步把 / 改为 >> mid = L + ((R - L) >> 1);

* 问题

  * 在有序数组中，找某个数 - O(logN)
    * 排他性：当前element大于target的话，一定不在右边。反之亦然

  * 在有序数组中，找>=某个数最左侧的位置 - 二分到死，记录最左位置
    * 排他性：当前element大于target的话，一定不在右边。反之亦然

  * 在有序数组中，找<=某个数最右侧的位置 - 和上面类似
    * 排他性：当前element大于target的话，一定不在右边。反之亦然

  * 无序数列，但是相邻不相等。寻找局部最小值。
    * 排他性：
      * 当前element (idx = i)左边的element大于当前element的话，在[i, n-1]中一定有答案
      * 当前element (idx = i)右边的element大于当前element的话，在[0, i]中一定有答案

# 03 异或运算 (exclusive or EOR)

* `^`异或运算可以理解为无进位相加
  
  * 00111 = 7
  * 01101 = 13
  * 01010
* 特性
  * 0 ^ N = N
  * N ^ N = 0
  * 满足交换律 a ^ b  = b ^ a
  * 满足结合律 (a ^ b) ^ c = a ^ (b ^ c)
* 其他一会会用到的bit operation
  * ~ 取反
  * ~a + 1 = -a （反码和补码的知识，有空再看）

* 相关题目

  1. swap

     * 不需要额外空间

     * 不过只能用于primitive 类型

     * 但是必须保证两个element不是同一个address，相等的话会变成0

  ```java
  int[] arr = int[]{1,2,3,4,5};
  int i = 0;
  int j = 1;
  arr[i] = arr[i] ^ arr[j]; // a = a ^ b |||  b = b
  arr[j] = arr[i] ^ arr[j]; // a = a ^ b ||| b = b ^ a ^ b = a ^ 0 = a
  arr[i] = arr[i] ^ arr[j]; // a = a ^ b ^ a = b ^ 0 = b ||| b = a
  ```

  2. 一个数组中有一个数字出现奇数次，其他都是偶数次，找到这个数字
     1. ^所有数字，最后就是这个数字，因为N ^ N = 0 and 0 ^ N = N

  3. 如何取到a 二进制中保留第一个1的数字？
     * 举例
       * a = 01101110010000 通过计算得到 result = 00000000010000
     * result = a & (~a + 1) = a & (-a)
  4. (Q2变种 - 1)一个数组中有两种数出现了奇数次，其他数字出现了偶数次，找到这两个数字a和b
     1. ^所有数字，得到eor (eor = a ^ b)
     2. 获取eor某个为1的bit的idx i，将数组按照数字在idx i的位置上是否为0分成两个数组arrA arrB (Q3)
        * 因为eor为1的bit代表a和b在这个bit上一定不一样
     3. ^所有arrA的数字，得到eor’。 然后 a = eor'
     4. b = eor' ^ eor = eor & a = a ^ b ^ a = b

  5. (Q2变种 - 2)一个数组中有一个数出现K次，其他数都出现了M次. K < M

     * 简单做法 - hashmap

     1. 创建一个int[32] tally 记录idx=i的bit上为1的数字的数量 （32是因为int长度为32）
     2. 如果tally[i] % M 不等于0，那么说明target数字在这个位置上为1

     * 技术细节

       * 如何检查某数字第i位是否为1

         ```java
         if ((num >> i) & 1 == 1) { ... } 
         ```

       * 获得到tally后如何组装target数字 (或者说如何把第i位设置为1)

         ```java
         ans |= (1 << i);
         ```

# 04 数据结构

## Linked List

* 单链表 & 双链表
* 相关题目
  * 单双链表反转
    * 通常是需要in place完成
    * 对数器 - 容器实现
  * 删除给定值
    * 对数器 - 容器实现

## 栈和队列 Stack & Queue

* 相关题目

  * 通过linked list实现队列

  * 用数组实现不超过固定大小的队列和数组
    * 队列：环形数组
      * 设置size控制进队出队，让头指针和尾指针解耦
    * 栈: 正常数组

  * 实现特殊栈，在基本功能之上，实现能够获取最小元素的功能 要求TC = O(1)

    1. 准备两个栈，一个数据栈，一个最小栈

    2. 当push一个element e，判断是否小于当前栈顶element t
       1. 如果小于，将e压入最小栈
       2. 如果大于等于，将t压入最小栈
    3. pop时同步pop两个栈
    4. getMin返回最小栈

  * 如何用队列实现栈

    * 通常不会直接这么问，而是包装一下
      * 通过队列实现DFS
    * 两个queue： A queue + B queue
      * 可以在两个queue之间倒数据
      * 只从当前size为1的queue pop
      * 只向当前不为空的queue push
      * 可以设置main queue和helper queue，让push总是在main上执行，pop总是在helper上执行
        * 每次pop时：
          * 将main中的数据倒入helper，但是剩一个
          * pop main中最后一个element
          * 交换main和helper

  * 如何用栈实现队列

    * 通常不会直接这么问，而是包装一下
      * 通过栈实现BFS
    * 两个stack： push stack + pop stack
      * 可以在两个栈之间倒数据
      * 只有在push stack为空时才能pop
      * 只有在pop stack为空时才能push

## 递归 Recursion

* 任何递归都可以改成非递归
* base case 何时结束
* Master公式
  * 用于分析时间复杂度
  * 子问题规模一致时，可以使用master直接计算TC
    * T(N) = a * T(N / b) + O(N ^ d)
  * if $log_b a < d$ , then O(N^d)
  * if $log_ba > d$, then $O(N^{log_ba})$
  * otherwise, $O(N^dlog_2N)$

## 哈希表 Hash Map

* java的hashmap，原生type直接存值(不知道为啥包含了String)
  * 其他类型存reference
* 有序表TreeMap - 红黑树，avl，size-balanced， 跳表
  * operation的TC是O(logN)
  * firstKey() 最小k
  * lastKey() 最大K

# 05 归并排序

* merge sort

  * merge part

    ```java
    public static int[] merge(int[] arr, int l, int mid, int r) {
        int[] temp = new int[r - l + 1];
        int cur = 0;
        int p1 = l;
        int p2 = mid + 1;
        while (p1 <= mid && p2 <= r) {
            temp[cur++] = arr[p1] < arr[p2] ? arr[p1++] : arr[p2++];
        }
    
        // p1 out of bound or p2 out of bound
        while (p1 <= mid) {
            temp[cur++] = arr[p1++];
        }
        while (p2 <= r) {
            temp[cur++] = arr[p2++];
        }
    
        cur = l;
        for (int each: temp) {
            arr[cur++] = each;
        }
    
        return arr;
    }
    ```

* 递归merge sort

  ```java
  public static int[] mergeSort(int[] arr, int l, int r) {
      if (l >= r) {
          return arr;
      }
      int mid = l + (r - l) / 2;
      mergeSort(arr, l, mid);
      mergeSort(arr, mid + 1, r);
      return merge(arr, l, mid, r);
  }
  ```

* 迭代merge sort

  ```java
  public static int[] mergeSortIter(int[] arr) {
      for (int step = 1; step < arr.length; step = step * 2) {
          for (int l = 0;; l += 2 * step) {
              int mid = Math.min(l + step, arr.length) - 1;
              int r = Math.min(l + 2 * step, arr.length) - 1;
              merge(arr, l, mid, r);
              if (r == arr.length - 1) {
                  break;
              }
          }
          // 如果arr特别大，那么step再乘二可能会溢出
          if (step > arr.length / 2) {
              break;
          }
      }
      return arr;
  }
  ```

* 小和问题 - 计算arr中每个位置左边比自己小的数的和

  * 这种题可以理解成一种类型

    * 对**每个位置**的**某一侧**进行一些**比较或操作**，然后利用merge sort一定有序的特性，减少计算量
    * 比如 - 计算arr中每个位置右边*2依然比当前位置小的数量

  * 举例：[4,3,5,7], 0 + 0 + (4 + 3) + (4 + 3 + 5)

  * 这道题可以理解为：当前位置右边有几个比自己大的数字，有几个就加几次自己

    ```java
    public static int leftSmallerSum(int[] arr, int l, int r) {
        if (r <= l) {
            return 0;
        }
        int mid = l + (r - l) / 2;
        int sum = 0;
        sum += leftSmallerSum(arr, l, mid);
        sum += leftSmallerSum(arr, mid + 1, r);
        return sum + mergeAndCount(arr, l, mid, r);
    }
    
    private static int mergeAndCount(int[] arr, int l, int mid, int r) {
        int sum = 0;
        int[] temp = new int[r - l + 1];
        int cur = 0;
        int p1 = l;
        int p2 = mid + 1;
        while (p1 <= mid && p2 <= r) {
            sum += arr[p1] < arr[p2] ? (r - p2 + 1) * arr[p1] : 0;
            // 相等时优先取p2的值，因为q2后面可能有比当前值更大的值，需要和p1计算sum时使用
            temp[cur++] += arr[p1] < arr[p2] ? arr[p1++] : arr[p2++];
        }
    
        // p1 out of bound or p2 out of bound
        while (p1 <= mid) {
            temp[cur++] = arr[p1++];
        }
        while (p2 <= r) {
            temp[cur++] = arr[p2++];
        }
    
    
        for (int i=0;i<temp.length;i++) {
            arr[l + i] = temp[i];
        }
        return sum;
    }
    ```

# 06 归并排序附加题、随机快速排序

* 归并排序附加题 - [求一个arr中有多少个subarr的sum在给定范围中](https://leetcode.com/problems/count-of-range-sum/)

  * 暴力算法：比较所有subarr O(n^3)

  * 求 前缀和数组preSum - 第i位代表sum(arr[0], ..., arr[i]);

    * 通过这个preSum可以把暴力算法优化至O(n^2)

  * 优化算法：

    1. 等价问题 - 求arr中每个位置i中，每个以i结尾的sub arr[j, i]的sum是否在范围[lower, upper]中。j<=i
  
       * 如果用mergesort，我们可以直接将arr替换为preSum，然后我们可以理解为
         * sub arr是在merge阶段左边部分中的preSum值减掉右边部分中的preSum值
  
       * 每次merge时，loop每个右半部分的element 设定为i
           * loop每个左半部分的element，看看是否满足新的区间
       * 因为没有处理sub arr[0, i]的情况，所以放到base case中单独处理一次
         * 因为每个位置L=R的base case只会触发一次，所以不会多算。
         * PS. 这个也可以通过设置特殊的presum来解决
           * preSum.length = arr.length + 1
           * preSum[0] = 0, 表示sub arr[0, i]
           * 但是需要额外处理一下边界问题，因为每个值都错位了一位
  
       * ```java
         public static int countSubArrSumInRange(int[] arr, int lowerBound, int upperBound) {
             if(arr == null || arr.length == 0) {
                 return 0;
             }
             long[] preSum = new long[arr.length];
             preSum[0] = arr[0];
             for (int i=1;i<arr.length;i++) {
                 preSum[i] = preSum[i-1] + arr[i];
             }
         
             return countSubArrSumInRangeHelper(preSum, 0, preSum.length - 1, upperBound, lowerBound);
         
         }
         public static int countSubArrSumInRangeHelper(long[] preSum, int l, int r, int ub, int lb) {
             if (r == l) {
                 return preSum[l] <= ub && preSum[l] >= lb ? 1 : 0;
             }
             int mid = l + ((r - l) >> 1);
             return
                     countSubArrSumInRangeHelper(preSum, l, mid, ub, lb)
                     +
                     countSubArrSumInRangeHelper(preSum, mid + 1, r, ub, lb)
                     +
                     countSubArrSumInRangeMerge(preSum, l, mid, r, ub, lb);
         }
         
         public static int countSubArrSumInRangeMerge(long[] preSum, int l, int mid, int r, int ub, int lb) {
             int count = 0;
             for (int i = mid + 1;i<=r;i++) {
                 for (int j = l;j<=mid;j++) {
                     long val = preSum[i] - preSum[j];
                     count += val <= ub && val >= lb? 1: 0;
                 }
             }
             long[] temp = new long[r - l + 1];
             int cur = 0;
             int p1 = l;
             int p2 = mid + 1;
         
             while (p1 <= mid && p2 <= r) {
                 temp[cur++] = preSum[p1] < preSum[p2] ? preSum[p1++] : preSum[p2++];
             }
             while (p1 <= mid) {
                 temp[cur++] = preSum[p1++];
             }
             while (p2 <= r) {
                 temp[cur++] = preSum[p2++];
             }
         
             for(int i=0;i<temp.length;i++) {
                 preSum[i + l] = temp[i];
             }
         
             return count;
         }
         ```
  
    2. 等价问题 - 求arr中每个位置i中，每个以i结尾的sub arr[j, i]不要的那部分的sum preSum(j)是否在范围[preSum[i] - upper, preSum[i] - lower]中
  
       * 也就是求每个位置i左边的preSum(0),...preSum(i-1)是否在[preSum(i) - upper, preSum(i) - lower]中
  
       * 推导
  
         * sum(sub arr[j, i]) = preSum(i) - preSum(j)
  
         * lower <= preSum(i) - preSum(j) <= upper
  
         * lower - preSum(i) <= -preSum(j) <= upper - preSum(i)
  
         * preSum(i) - lower => preSum(j) => preSum(i) - upper
  
         * preSum(i) - upper <= preSum(j) <= preSum(i) - lower
  
       * 通过这个 我们就可以进一步优化算法counting部分
  
         * 因为mergesort左右两边都是默认有序，所以可以使用滑动窗口进一步省计算
  
  
  ```java
  public static int countSubArrSumInRangeMerge(long[] preSum, int l, int mid, int r, int ub, int lb) {
       int count = 0;
       for (int i = mid + 1;i<=r;i++) {
          // 因为l - mid是有序的，所以也可以用滑动窗口来计算count，能节省一些计算
           long newUB = preSum[i] - lb;
           long newLB = preSum[i] - ub;
           int windR = l;
           int windL = l;
           while(windR <= mid && preSum[windR] <= newUB) {
               windR++;
           }
           while(windL <= mid && preSum[windL] < newLB) {
               windL++;
           }
           count += windR - windL;
       }
  
       ...
   }        
  
  ```
  

## 快速排序

* 荷兰国旗问题Dutch National Flag Problem - 根据目标数将小于的放左边，大于的放右边

  * 假定使用最后一个element作为target

  * 设置两个指针L和C

  * 分两种情况 

    * 小于等于
      * swap L 和 C的element
      * C++， L++
    * 大于
      * C++

  * ```java
    public static int partition(int[] arr, int l, int r) {
        if (l > r) {
            return -1;
        }
        if (l == r) {
            return l;
        }
        int L = l;
        int C = l;
        while (C < r) {
            if (arr[C] <= arr[r]) {
                swap(arr, L, C);
                L++;
            }
            C++;
        }
        swap(arr, L, r);
        L++;
        return L;
    }
    ```

* 荷兰国旗变种 - 等于的放中间

  * 设置三个指针L, C, R

  * 分三种情况 - 小于 等于 大于

    * 小于：
      * swap(L, C)
      * L++ C++
    * 等于：
      * C++;
    * 大于
      * swap(C, R)
      * R-- (C不可以加1，因为R是右边还没有判断过的值)

  * ```java
    public static int[] partition3Pointers(int[] arr, int l, int r) {
        if (l > r) {
            return new int[]{-1, -1};
        }
        if (l == r) {
            return new int[]{l, l};
        }
        int L = l;
        int C = l;
        int R = r - 1;
        while (C <= R) {
            if (arr[C] < arr[r]) {
                swap(arr, L++, C++);
            } else if (arr[C] == arr[r]) {
                C++;
            } else {
                swap(arr, C, R--);
            }
        }
        swap(arr, ++R, r);
        return new int[]{L, R};
    }
    ```

* quickSort

  * 1.0 - 划分小于等于 和 大于 的区间，然后保证分割两个区间的值等于目标值，然后再划分这两个区间，直至区间仅剩一个element。

  * 2.0 - 划分小于 等于 大于 的区间，然后再小于和大于区间再进行划分，直至区间仅剩一个element。

    * 这样就不用重复排序与target val相等的elements了

  * 3.0 - 随机快排

    * 上面两种都默认使用当权区间最右边的element作为target
      * 最差情况为O(n^2)

    * 为了避免最差情况，使用区间内随机位置的element作为target
      * 然后swap这个随机位置和最有位置的值
      * TC的数学期望变为O(nlogn)

    

# 07 堆和堆排序

## 比较器Comparator

* compare方法
  * 返回负数，认为第一个obj排前面
  * 正数，认为第二个obj排前面
  * 0无所谓
  * 比如升序数字就是 int1 - int2

## Heap 堆

* 完全二叉树 complete binary tree

  * 要求只能最深一层缺node
  * 且必须缺的nodes在右边

* 使用array实现完全二叉树

  * i的左孩子2i + 1
  * i的右孩子2i + 2
  * i的上层节点(i-1)/2
  * 使用heapSize控制这个二叉树的大小

* 大根堆 & 小根堆

  * 大根堆：每个子树的root大于下面的孩子节点
  * 小根堆：每个子树的root小于下面的孩子节点

* heapInsert - 插入新的node时，调用 （这里演示大根堆）

  ```java
  public static void heapInsert(int[] heap, int newNode, int heapSize) {
      heap[heapSize] = newNode;
      
      int i = heapSize;
      while (heap[i] > heap[(i-1)/2]) {
          swap(heap, i, (i-1/2))
          i = (i-1)/2;
      }
     
      heapSize++; // 需要更新heapSize的，但是这里只是个独立的demo
  }
  ```

* heapify - 取出root节点时，需要将最后一位提至root位置，再调用heapify用于保证堆性质

  ```java
  // 假设调用之前已经将最后一位放在了root
  // 这版是老师实现的，我不太喜欢把逻辑放在while里。所以下面有一版我写的
  static static void heapify(int[] heap, int index, int heapSize) {
      int left = index * 2 + 1; // left child
      while (left < heapSize) {
          // find the largest child's index
          int largest = left + 1 < heapSize && heap[left + 1] > arr[left] ? left + 1: left;
          
          // compare target node and its largest child
          largest = heap[largest] > arr[index] ? largest : index;
          
          // if target node is the largest compared to his child
          // heapify process terminates
          if (largest == index) {
              break;
          }
          
          // swap
          swap(heap, largest, index);
          
          // update 
          index = largest;
          left = index * 2 + 1;
      }
  }
  
  // 这一版是我实现的， 我把terminating的逻辑放在了while loop里面
  public void heapify(int idx) {
      int cur = idx;
      while (true) {
          int left = cur * 2 + 1;
          int right = left + 1;
          int largest = cur;
          if (left < size) {
              largest = comparator.compare(heap[largest], heap[left]) > 0 ? left: largest;
          }
          if (right < size) {
              largest = comparator.compare(heap[largest],heap[right]) > 0 ? right: largest;
          }
  
          if (largest == cur) {
              break;
          }
  
          swap(heap, cur, largest);
          cur = largest;
      }
  }
  ```

* Java有现成的PriorityQueue，默认是小根堆

* 如果堆中的某个值发生了修改，可以分别对当前位置进行

  * heapInsert的操作 - 向上比较
  * Heapify的操作 - 向下比较

* 堆排序 TC = O(nlogn)

  * 方法一 - 把所有数字放入堆中，然后依次拿出来，就有序了

  * 方法二 - SC = O(1)

    1. 形成堆
    2. 头和尾交换，size--, 头heapify
    3. 重复step2直至size = 0

    * 这个是in place的排序

  * 方法三 - 提升建堆速度为O(n) - 自下而上

    * 将所有数字先放入完全二叉树
    * 从最后一个数字开始依次heapify
    * 推导
      * 因为叶节点数量为N/2, 这些节点heapify只看自己 * 1
      * 链接叶节点的节点数量为N/4, heapify工作量翻倍 * 2
      * 以此类推 N/8 对应  * 3， N/16 对应 * 4
      * 所以TC为 = N/2 * 1 + N/4 * 2 + N/8 * 3 + ....
      * 2TC = N * 1 + N/2 * 2 + N/4 * 3 + ....
      * TC = 2TC - TC = N + N/2 + N/4 + ..... = N (等比数列)
      * 说人话就是
        * 原本的方法是层数越高的节点成本越高，新的方法是层数越高的节点成本越低
        * 层数越高，节点越多
        * 所以总的来说更省了

* 给定一个几乎有序的数组 长度为n，指变成有序后，每个element移动的距离不超过k 。 k < n

  * 创建一个size为k+1的小根堆heap，
  * 按顺序将数组中的数字加入heap
  * 当堆满了，就弹出最小值
  * 如果数组已经全部加入过heap，就一直弹最小值
  * 然后就有序了

# 08 加强堆

* 相较于普通堆，加强堆添加了反向索引表
  * 可以直接查找到obj所在的位置
    * 这样就允许对堆中某个目标obj进行调整并保持堆结构成立
  * 可以删除堆中某个具体的obj，且保证堆结构成立
  * 基础类型会有问题，因为反向索引表使用map实现的，会自动unwrap基础类的包装类 (已验证)
    * 可以自己定义一个内部wrap类 
      * 存表查表还是会有问题，因为wrap是内部类，外边不感知。
      * 所以查表的我们需要重新包装一个wrap，hashCode就对不上了。
        * 因为没办法直接获取基础类的地址，好像在java中属于不安全操作。
      * 所以得是一个外部包装类才行

* 给定n个线段的左右边界$[a_1,b_i],...[a_n,b_n]$ 求最大重合数
  * [0, 1]和[1, 2]不算重叠 - 重叠长度需要 > 0
  * 方法
    * 创建小根堆heap， 记录最大重叠数maxNum
    * 按照$a_i$从小到大排序线段
    * loop所有线段，取当前线段为$[a_i, b_i]$
      1. 删除heap中存在比$a_i$小的数字
      2. 插入$b_i$到heap
      3. maxNum = Max(maxNum, heap.size)
    * return maxNum
* 笔试题(业务类) - 给定一个整型数组表示用户的id: int[] arr 和一个布尔类型数组表示购买T还是退货F: boolean[] op。
  * 设定一个size为k的中奖区，和无限大的候选区
  * 每次购买或者退货发生时，输出一个中奖区的list
  * 购买数为0时删除
  * 进入时间某个区时设定时间
  * 如果出现购买数量一样多的情况，
    * 候选区谁先进谁先出
    * 中奖区谁先进谁先出
  * coding还没写，之后补

# 09 前缀树、不基于比较的排序、排序稳定性

## 前缀树 

* (别名prefix tree， trie）

* 值存在边上而不是节点上

* 每个node有2个参数

  * visitedNum：有多少个路径经过这个节点

  * endNum：有多少路径在这个node结束

* 实现 - 假定存仅包含26个字母的字符串
  * 节点包含26条通路，按照通路下方节点是否存在来判断通路是否存在
  * 删除字符串的时候要把visitedNum为0的node删除，防止内存溢出\泄露

* 前缀树问题1 - 给定一堆str，找某个前缀出现过几次
* 前缀树问题1 - 给定一堆str，找str出现过几次

## 非比较排序

* 假设有一个数组，包含员工年龄，请从小到大排序 
  * 方法1 - 计数排序 -  Counting Sort -  O(n) 
    * 首先假设员工年龄范围[0, 200]，创建一个arr tally = new int[201];
    * 遍历整个数组，并count年龄。然后就有序了
    * PS 需要数据范围特殊
  * 方法2 - 基数排序 - Radix sort
    * 找到最大的数字，其余数字高位补零
    * 创建10个桶0桶-9桶 (队列)
    * 从左到右 根据个位数进桶 - 比如： 103 进3桶， 10进0桶
    * 然后从0桶到9桶依次清空桶，记录出桶顺序
    * 再从左到右 根据十位数进桶
    * 再依次清空，记录出桶顺序
    * 以此类推直至最高位，最后一次清空时，整个数组increasing有序
* **听到 1：34：00**

# 10 排序总结、链表相关面试题

## 排序部分

* 排序算法总结
  * 最求速度使用快速排序
  * 省空间用堆排序
  * 需要稳定性用归并排序
    * 相同元素的顺序不变

* 工程上对于排序的改进
  * 稳定性
  * 充分利用O(NLogN和O(N^2)排序的各自优势
    * 比如quicksort时间复杂度很优秀但是常熟大，插入排序实现复杂度很差但是常熟小，所以可以在小数量级使用插入排序，大数量级使用quicksort


## 链表部分

* 算法简单，但是coding复杂
  * 边界条件的处理能力
* 常见算法
  * 容器存链表
  * 快慢指针
* 常见问题
  1. 输入链表头节点，奇数长度返回中点，偶数长度返回上中点
  2. 输入链表头节点，奇数长度返回中点，偶数长度返回下中点
  3. 输入链表头节点，奇数长度返回中点前一个，偶数长度返回上中点前一个
  4. 输入链表头节点，奇数长度返回中点前一个，偶数长度返回下中点前一个

* 链表问题1 - 判断链表是否为回文
  * 用容器解决 - 把所有ele放入stack。从头重新遍历链表然后和stack.pop比较
    * 因为stack中是逆序的链表，所有能够全部对应上的话就是回文

  * (快慢指针)可以先找到中间节点，如果是偶数就是上中节点
    * 将中间节点指向null
    * 然后将后半部分指针反转 - 子节点指向父节点
      * A -> B -> C -> B -> A ===> A -> B -> C -> null and C <- B <-A

    * 设置两个指针L和R，看看能否一一对应
      * L = left A and R = right A

    * 记得把链表改回去

* 链表问题2 - 链表长度为n，分成两段L1 -> L2 -> L3 -> L4 -> R1 -> R2 -> R3 -> R4
  * 希望变成 L1 -> R4 -> L2 -> R3 -> L3 -> R3 -> L4 -> R4
  * 也可以用上面那道题的方法，做一个穿插重连

* 链表问题3 - 将单项链表按照某个值划分为左边小 中间相等 右边大的形式
  * 方法一 - 放入数组中进行quick sort的partition - 笔试可以用这个，面试可以将这个思路但是最好不要写这个
  * 方法二 - 分成小 中 大三部分，再把各个部分之间串起来  （感觉这个更简单呢？）
    * 小头 - 小尾 and 等头 - 等尾 and 大头 - 大尾
    * 分好之后小尾指向等头，等尾指向大头
      * 这里的难点是边界处理，因为每个部分都有可以能出现null的情况
* 链表问题4 - 复制一个链表，且每个node有randNext，也需要克隆
  * 容器方法 - 做一个map让旧的ele 映射到新的ele。然后串一遍
  * 不用容器的方法
    * 把新ele挂到老ele的next上 A -> B -> C ===> A -> A' -> B -> B' -> C -> C'
    * 重新遍历链表，并设置randNext
    * 分离两个链表
* （下面是11课讲了一部分没讲完的）
* 链表题目5 - 给定两个可能有环也可能无环的单链表，头节点head1和head2. 请实现一个函数，如果两个链表相交，请返回相交的 第一个节点。如果不相交返回null。要求两个链表长度之和为N，时间复杂度为O(N),额外空间复杂度达到O(1)
  * 用容器解决 - 使用set储存其中一个链表的所有node，然后遍历另一个链表。但是不满足空间复杂度O(1)的条件
  * 快慢指针
    * 如果没有环，fast会先遇到null
    * sub question - 如果存在环，快慢指针一定会有指向同一个node的时机。
      * 当快慢相等时，将其中一个指针指向head，并且两个指针都变成慢指针。两个指针一定在环开始的时候位置相遇。
    * 讨论相交情况, 通过sub question的结果，分出以下情况
      * 两个都无环
        * 不相交，返回null
          * 如果两个链表的最后一位的内存地址不相等，一定不相等
        * 相交，返回第一个相交节点
          * 如果不相交的判断为false，我们得知一定相交。
          * 那么设置两个指针P1 P2分别指向两个链表的开头
          * 假设链表1长度为80， 链表2长度为100 - 那么P2先走20次，然后P1 P2一起走，第一个地址相同的node一定是首个相交点。
      * 某一个链表有环，另一个无环 - 一定不相交
      * 两个都是有环链表
        * 情况一 各自独立
        * 情况二 环在相交节点后边 (入环节点相同)
          * 在环入口截断两个链表，然后当作无环处理
        * 情况三 入环节点不同
          * 走其中一个指针，如果走回自己之前遇到了另一个指针，说明相交。返回两个指针任意一个。
            * 如果遇到了自己，说明两个链表各自独立。
* 链表问题6 - 在不给head的情况下，删除链表中的目标节点
  * 可以改target的val为next的val，然后target.next = target.next.next
    * 但是如果target是链表最后一个node，就办不到了

# 11-13 [二叉树基本算法 ](binary tree.md)

# 14-15 [贪心算法](greedy.md)

* 从01：08：00开始

# 16 并查集 Disjoint-set 

* 15课后半开始讲了一部分并查集01：28：00
* 什么是并查集
  * 每个element有一个next指针，代表节点next指向自己，能够通过next找到的elements都属于同一个集合
  * 提供两个功能
    * isSameSet - 查询两个element是否属于一个集合 - O(1)
      * 通过findHead判断两个elements的代表节点是否为一个
        * 是的话就是一个集合
        * 否则就不是
    * union - 将两个element所在的集合 合成一个集合 - O(1)
  * 优化
    * union时，小集合的代表节点挂在大集合的代表节点上
    * 找到node的集合的代表节点的同时，让路过的节点都直接指向代表节点
  * 代码 todo
    * hashmap实现
    * 数组实现
* [leetcode547](https://leetcode.com/problems/number-of-provinces/) - 给定一个矩阵，表示两点是否联通。求有几个独立的group。
* [leetcode200](https://leetcode.com/problems/number-of-islands/) - 岛问题 - 上下左右的岛算connected，求有几片岛。
  * 方法1 - 遍历所有位置，如果为1，找到相连的所有1，并全改为0，count++
  * 方法2 - 也可以用并查集处理
    * 所有岛单独成为集合
    * 查每个岛**上边和左边**是否为岛
      * 是的话就合并
  * 这俩都是最优解，但是方法1的常数更小
* leetcode305 - 岛问题2 - 给了matrix的size，给了list of岛的坐标，每添加一个岛，返回当前有几片岛
  * 这里并查集就优于上题的方法1了
  * 优化
    * 如果matrix很大，但是岛很少，可以用hashmap字符串代替二维数组
* 岛问题3 (拓展题) - 给定leetcode200的设定，但是matrix极大极大，如何并行计算
  * 分块并查集
  * 关注每块的边界，合并边界链接的位置


# 17 图

* 所有图都是有向图

  * 无向图可以转有向图

* 表达方式

  * 方式很多，最好熟练其中一种，剩下的转化

  * 邻接表

    ```
    a: b
    b : c
    c: a e f
    e:
    f: e
    ```

  * 邻接矩阵  - 数字是权重

    |      | a    | b    | c    | e    | f    |
    | ---- | ---- | ---- | ---- | ---- | ---- |
    | a    | 0    | 1    | inf  | inf  | inf  |
    | b    | inf  | 0    | 1    | inf  | inf  |
    | c    | ...  | ...  | 0    | ...  | ...  |
    | e    | ...  | ...  | ...  | 0    | ...  |
    | f    | ...  | ...  | ...  | ...  | 0    |

  * Edge List

    * [[1, 'a', 'b'],[1, 'b', 'c'], ....] 第一位是权重，第二三是从二到三的路径

  * ...

  * 固定一种表达式，这里就用老师的了

    ```java
    public class Graph {
        public Map<Integer, Node> nodes;
        public Set<Edge> edges;
    }
    
    public class Node {
        public int value;
        public int in; // 有多少个node指向当前node
        public int out; // 当前node指向多少个node
        public ArrayList<Node> nexts; // 当前node指向的nodes
        public ArrayList<Edge> edges; // 当前node指向的nodes之间的edge
        
        public Node(int value) {
            this.value = value;
            in = 0;
            out = 0;
            nexts = new ArrayList<>();
            edges = new ArrayList<>();
        }
    }
    
    public class Edge {
        public int weight;
        public Node from;
        public Node to;
       
        public Edge(int weight, Node from, Node to) {
            this.weight = weight;
            this.from = from;
            this.to = to;
        }
    }
    ```

  * 其他的做转化

    * Edge List

      ```java
      public static Graph creatGraph(int[][] matrix) {
          Graph graph = new Graph();
          for (int i=0;i<matrix.length;i++) {
              int weight = matrix[i][0];
              int from = matrix[i][1];
              int to = matrix[i][2];
              if (!graph.nodes.containsKey(from)) {
                  graph.nodes.put(from, new Node(from));
              }
              if (!graph.nodes.containsKey(to)) {
                  graph.nodes.put(to, new Node(to));
              }
      
              Node fromNode = graph.nodes.get(from);
              Node toNode = graph.nodes.get(to);
      
              Edge newEdge = new Edge(weight, fromNode, toNode);
              fromNode.nexts.add(toNode);
              fromNode.out++;
              toNode.in++;
              fromNode.edges.add(newEdge);
              graph.edges.add(newEdge);
          }
          return graph;
      }
      ```

* 相关问题

  * BFS

    * 需要一个queue储存当前可走的路
    * 设置一个visited set检测哪些node去过，避免无限
    * **出stack**的时候视作visited
      * 也就是说相关要进行逻辑在这里执行

  * DFS

    * 需要一个stack存储当前可走的路
    * 也需要一个visited set
    * **进stack**的时候就视作visited
      * 也就是说相关要进行逻辑在这里执行

  * 拓扑排序 - 有向无环

    * 方法一 - 总是找in degree为0的node

    * 方法二 - node能到所有的点的数量越多，那么拓扑序越小 (越靠近起点)
      * 重复计算，如果a能到b和c，b能到c。那么c算两次。a的拓扑序是4
      * node能到所有的点的数量 
        * 重复计算，如果a能到b和c，b能到c。那么c算两次。a的拓扑序是4
    * 方法三 - node能到达的深度越深，拓扑序越小

  * 最小生成树MST Minimum Spanning Tree- 无向图

    * 定义：在保证所有点连通的情况下，最小化边的权重和
    * 方法1：Kruskal 贪心策略
      * 将所有边按照权重排序
      * 依次拿出边，并检查是否会形成环(并查集)。如果不会，那么添加回图中。
    * 方法2：Prim 贪心策略
      * 设置一个边的优先队列Q，一个node的Hash set
      * 随便选一个start node，并将node加入node set, 将这个node的所有边加入edge queue
      * 选一个最小且两边的node至少有一个不在node set中
      * 将新的node加入set，新的edge加入queue
      * 回到第三步，直至所有node加入set

  * 找start到所有点的最短路径 无负权重边

    * 方法1：dijkstra's algorithm
      * 创建visited set，distanceMap
      * 在distanceMap插入（start，0）
      * 找到距离start最近且没有visit过的node n
      * 将n能够到达的nodes加入distanceMap，并计算最小路径值
      * 回到第三步，直到能visit都visit过
    * 方法2：dijkstra + 加强堆（小根堆 ）
      * 优化第三步

# 18 - 24 [暴力递归到动态规划](recursion and dp.md)

# 25 单调栈

# 其他

## 求GCD 

* GCD = greatest common divisor 

* 欧几里得演算法(辗转相除法) Euclidean Algorithm

```js
function gcd(a, b) {
    if (b === 0) {
        return a;
    }
   	return gcd(b, a % b); // 因为 gcd(A, B) = gcd(B, R) = gcd(B, A % B)
}
```

* given A >= B, and A % B = R, q = Math.floor(A/B);
* So, we know that A = B * q + R
* if A and B has GCD g, then a' * g= (b' * g * q) + R
  * R = a' * g - b' * g * q
  * R = g * (a' - b' * q)
* 所以R也可以被g除



# Resume todo list

add more white space

education background

change contribute to code or create



bywave.io

clash	



