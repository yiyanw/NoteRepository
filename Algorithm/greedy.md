# 14 贪心算法 - 1

* 从01：08：00开始
* 最自然智慧的算法
* 找一个局部最优选择，从而得到全局最优解
* 如何证明局部最优解可以得到全局最优解是一个巨大的难点
* 更依赖经验和阅历
* Q1 - 给定由字符串组成的数组strs，必须把所有字符串拼接起来，要求拼接结果字典序最小
  * 字典序 - 可以理解成25进制的数字？
    * 比较不同长度的strs时，短的str在后边补('a'-1)，（就是最小的ascii码）
  * 排序整个数组，但是比较o1+o2 和 o2+o1。
    * 数学证明课里有

# 15贪心算法 - 2

* 