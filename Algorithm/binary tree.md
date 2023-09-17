# 11 二叉树基本算法 - 1

* 先序 preorder(头左右) & 中序inorder（左头右） & 后序postorder（左右头）

  * 其实还有 头右左 右头左  右左头

* 递归实现二叉树遍历

  * 二叉树的遍历方式本质是递归序
    * 能够回到一个节点三次

* 二叉树中某个Node X的所有祖先节点

  * （先序遍历到x 的集合） intersect （后序遍历到x的集合） = x 的所有祖先节点
    * 先序遍历到x 的集合 [(...... x) ......]
    * 后序遍历到x的集合 [...... (x ......)]
    * 证明 有点复杂 似懂非懂

* 非递归实现二叉树遍历

  * stack

    * 先序：出栈**执行**，有右压右，有左压左

    * 中序：

      1. 设root节点为cur
      2. cur入栈，cur -> cur.left. 
         1. 重复step2，直到cur == null
      3. 出栈**执行**，设cur = cur.right。回到step2. 
         1. stack为空时退出

    * 后序：a出栈不执行但是压进b栈，有**左**压左进a栈，有**右**压右进a栈。然后依次**执行**b栈

      * 如何用一个栈完成后序 - 拓展

# 12 二叉树基本算法 - 2

* 二叉树BFS

  * 队列实现 - 创建一个队列q，出q**执行**，有左入q，有右入q

    ```java
    public static void binaryTreeBFS(Node root) {
        Queue<Node> queue = new LinkedList<>();
    
        queue.add(root);
        while (!queue.isEmpty()) {
            Node cur = queue.poll();
            System.out.print(cur.val + " ");
            if (cur.left != null) {
                queue.add(cur.left);
            }
            if (cur.right != null) {
                queue.add(cur.right);
            }
        }
    }
    ```

* 查看某一层是否全部执行结束了

  * 可以通过设置flag来实现

    ```java
    public static void checkEndOfLayer(Node root) {
        Queue<Node> queue = new LinkedList<>();
        queue.add(root);
        Node curEnd = root;
        Node nextEnd = null;
        int layerNum = 0;
        
        while (!queue.isEmpty()) {
            Node cur = queue.poll();
            if (cur.left != null) {
                queue.add(cur.left);
                nextEnd = cur.left;
            }
            if (cur.right != null) {
                queue.add(cur.right);
                nextEnd = cur.right;
            }
            if (cur == curEnd) {
                System.out.println("end of line " + layerNum++ + ", last node's val is " + cur.val );
                curEnd = nextEnd;
                nextEnd = null;
            }
        }
    }
    ```

* 二叉树序列化&反序列化

  * 序列化 - 用symbol代表null(比如#)，node之间有分隔符

    * 先序 + stack 实现

      ```java
      public static String serializeBinaryTree(Node root) {
          Stack<Node> stack = new Stack<>();
          stack.push(root);
          StringBuilder sb = new StringBuilder();
          while (!stack.isEmpty()) {
              Node cur = stack.pop();
              if (cur == null) {
                  sb.append("#,");
              }
              else {
                  sb.append(cur.val).append(",");
                  stack.push(cur.right);
                  stack.push(cur.left);
              }
      
      
          }
          sb.deleteCharAt(sb.length() - 1);
      
          System.out.println(sb.toString());
          return sb.toString();
      }
      ```

  * 反序列化

    * 先序 + 双队列实现

      ```java
      public static Node deserializeBinaryTree(String str) {
          String[] arrStr = str.split(",");
          if (arrStr.length == 0) {
              return null;
          }
      
          Node head = new Node(Integer.parseInt(arrStr[0]));
      
          Stack<Node> stackL = new Stack<>();
          Stack<Node> stackR = new Stack<>();
          stackL.push(head);
      
          for (int i = 1;i < arrStr.length;i++) {
              Node newNode = null;
              Node cur;
              if (!arrStr[i].equals("#")) {
                  newNode = new Node(Integer.parseInt(arrStr[i]));
              }
              if (!stackL.isEmpty()) {
                  cur = stackL.pop();
                  cur.left = newNode;
                  stackR.push(cur);
              } else {
                  cur = stackR.pop();
                  cur.right = newNode;
              }
              if (newNode != null) {
                  stackL.push(newNode);
              }
          }
          return head;
      }
      ```

  * 还可以按层序列化、反序列化

    * 通过队列实现，
    * 序列化，创建队列，且在node入队时序列化 （入队还是出队列系列化都可以，看具体实现）
    * 反序列化，也是靠队列就可以。

* 多叉树转二叉树，二叉树转多叉树

  * 多转二 - 当前节点的直接子节点全部在左节点的右边界上 （深度优先遍历）

    <img src="binary tree/image-20230603170757383.png" alt="image-20230603170757383" style="zoom:50%;" />

  * 二转多 - 也是深度优先遍历

* 找到最宽层并返回宽度

  * 递归很好实现, 做个map count就行了

  * 非递归使用队列，空间上可以省一个map，且不需要系统栈 - 需要用到上面的检查layer结束的flag

    1. 将root加入队列，设curCount = 0， curEnd = root，nextEnd = null，maxCount = 0
    2. 如果队列不为空，弹出队头element a，curCount += 1
       1. 将a的子节点加入队列，nextEnd = 队列队尾
       2. 如果a == curEnd，
          1. 记录maxCount = MAX(maxCount, curCount), 
          2. curCount = 0
          3. curEnd = nextEnd, nextEnd = null
    3. 回到step2，直至队列为空
    4. 返回maxCount

    ```java
    public static int countMaxBreath(Node root) {
        Queue<Node> queue = new LinkedList<>();
        queue.add(root);
        int curCount = 0;
        Node curEnd = root;
        Node nextEnd = null;
        int maxCount = 0;
    
        while (!queue.isEmpty()) {
            Node curNode = queue.poll();
            curCount++;
            if (curNode.left != null) {
                queue.add(curNode.left);
                nextEnd = curNode.left;
            }
            if (curNode.right != null) {
                queue.add(curNode.right);
                nextEnd = curNode.right;
            }
            if (curNode == curEnd) {
                maxCount = Math.max(maxCount, curCount);
                curCount = 0;
                curEnd = nextEnd;
                nextEnd = null;
            }
        }
        return maxCount;
    }
    ```


* 找到某个节点的后继节点，这里后继指中序遍历循序情况下的目标node后边的node，比如 [1,2,3,4,5] (BFS顺序), 5的后继为1

  * 这个需要node有一个额外的指针指向父节点
  * 对于目标节点a
    * 如果有右节点，那么找到右节点的最左节点
    * 如果没有右节点，就一直向上找，直到节点不是父节点的右孩子，那么这个父节点就是a的后继节点
      * 含义为：节点x的左孩子的最右节点，就是节点x的前继节点
    * 否则没有后继节点


* 折纸问题

  * <img src="binary tree/image-20230602144403594.png" alt="image-20230602144403594" style="zoom:50%;" />

  * 相当于第n次折痕的数量是第n-1次折痕数量的二倍，

  * 且第n-1次折痕中的每个折痕的上边的新折痕为凹下面的为凸。

  * 由此可以得到一颗二叉树，左孩子全部为凹，右孩子全部为凸。深度为折了几次

  * 中序遍历打印整棵树

  * 优化：不需要真的有一个树，用递归满足中序遍历的逻辑就可以了

    ```java
    public static void printFoldDirection(int count, boolean isUp) {
        if (count <= 0) {
            return;
        }
        printFoldDirection(count-1, false);
        System.out.println(isUp? "up": "down");
        printFoldDirection(count-1, true);
    }
    ```


# 13 二叉树基本算法 + 二叉树的递归套路

* 几乎所有二叉树的递归问题都可以用下面这种递归方式处理 - 树形dp

  1. 假设以x节点为头，假设可以向x左树和x右树要任何信息

  1. 讨论以x为头节点的树所有可能的答案

  1. 列出所有可能性后，确定需要向子树要什么信息

  4. 获得左树和右树的信息，求全集S（就是计算自己的信息并返回）

* 判断二叉树是否为完全二叉树

  * 从左到右，按层遍历所有节点

    * 如果某个节点有右孩子没有左孩子 - 一定不是完全二叉树
    * 当第一次遇到左右孩子不双全的时候，剩下的节点必须全部都是叶子节点

  * （14课补充）递归，需要

    * 左树是否为完全二叉树

    * 右树是否为完全二叉树

    * 左树size + height

    * 右树size + height

    * 是完全二叉树的可能情况

      1. 左是满二叉树，右是满二叉树， 左height = 右height 
      2. 左是完全二叉树，右是满二叉树，左height - 右height = 1
      3. 左是满二叉树，右是满二叉树， 左height - 右height = 1
      4. 左是满二叉树，右是完全二叉树，左height = 右height

      * 1 和 3可以合成一个

* 判断二叉树是否为平衡二叉树 - 每一个节点左子树和右子树的高度差不超过1

  * 递归，需要：

    * 左树是否为平衡二叉树

    * 右树是否为平衡二叉树
    * 左树高度
    * 右树高度

* 判断二叉树是否为搜索二叉树 - 每个节点左左子树最大值比自己小，右子树最小值比自己大

  * 方法1 - 中序遍历一遍，看看是否一直处于上升趋势
  * 方法2 - 递归，需要：
    * 左树是否为搜索树
    * 右树是否为搜索树
    * 左树最大值
    * 右树最大值

* 给定一颗二叉树的头节点head，任何两个节点之间都存在距离，返回二叉树的最大距离

  * 递归，需要
    * 左树高度
    * 右树高度
    * 左树最远距离
    * 右树最远距离

* 判断满二叉树 - 如果高度是h，那么node数量一定是2^h - 1

* 找到二叉树中最大搜索二叉树的size

  * 递归，需要：
    * 左树max搜索二叉树size
    * 右树max搜索二叉树size
    * 左树max
    * 右树min
    * 左树size
    * 右树size

* （14课补充了一部分没讲完的）

* 使用递归判断完全二叉树 - 补在上边了

* 返回最大搜索二叉树的头节点

* 找到两个节点a和b的最低公共祖先

  * 方法1 - 容器，存a的所有父节点，然后遍历b的所有父节点，第一个出现在容器中的就是最低公共
  * 递归，需要
    * 是否存在a
    * 是否存在b
    * a和b的最低公共节点，没有为null
    * 情况
      1. 找到了最低公共节点
      2. 找到a和b

* 多叉树，选择nodes，如果node的父节点被选择，那么node就不能被选择。最大化sum of vals

  * 递归，需要
    * 选择当前节点的最大sum
    * 不选择当前节点的最大sum
  * 情况
    1. 选择当前节点 = 当前节点val + 不选择所有子节点的max sum
    2. 不选择当前节点 = max(选择子节点a的max， 不选择子节点a的max) + max(...)