# 139 Word Break

* https://leetcode.com/problems/word-break/

* Given a string `s` and a dictionary of strings `wordDict`, return `true` if `s` can be segmented into a space-separated sequence of one or more dictionary words.

  **Note** that the same word in the dictionary may be reused multiple times in the segmentation.

* ```js
  /**
   * @param {string} s
   * @param {string[]} wordDict
   * @return {boolean}
   */
  var wordBreak = function(s, wordDict) {
      let dp = new Array(s.length + 1).fill(false);
      dp[0] = true;
      // set has the TC O(1) for .has()
      let wordSet = new Set(wordDict);
  
      // dp stores the validity of sub string
      // dp[i] = the validity of [0, i)
      // therefore, the range of end is (0, s.length]
      //            the range of start is [0, s.length) or we can say (0, end)
      for (let end = 1;end <= s.length; end++) {
          for (let start = 0; start < end;start++) {
              let subS = s.slice(start, end);
              if (dp[start] === true && wordSet.has(subS)) {
                  dp[end] = true;
                  break;
              }
  
          }
      }
  
      return dp[s.length];
  };
  ```

* 

# 146 LRU Cache

* https://leetcode.com/problems/lru-cache/
* 感觉题表述的不太清楚

# 148 Sort List

* https://leetcode.com/problems/sort-list/

* quickSort does not work (time limit)

* ```js
  // merge sort
  
  function split(head) {
      let slow = head;
      let fast = head;
  
      while (fast.next && fast.next.next) {
          slow = slow.next;
          fast = fast.next.next;
      }
  
      let left = head;
      let right = slow.next;
      slow.next = null;
      return [left, right];
  }
  
  function merge(left, right) {
      let head = new ListNode(null, null);
      let cur = head;
  
      while (left !== null || right !== null) {
          if (left === null) {
              cur.next = right;
              cur = cur.next;
              right = right.next;
          }
          else if (right === null) {
              cur.next = left;
              cur = cur.next;
              left = left.next;
          }
          else if (left.val < right.val) {
              cur.next = left;
              cur = cur.next;
              left = left.next;
          } else {
              cur.next = right;
              cur = cur.next;
              right = right.next;
          }
      }
      cur.next = null;
      return head.next;
  }
  
  var sortList = function(head) {
      if (head === null || head.next === null) {
          return head;
      }
      
      let [left, right] = split(head);
      return merge(sortList(left), sortList(right))
  };
  ```

# 150 Evaluate Reverse Polish Notation

* https://leetcode.com/problems/evaluate-reverse-polish-notation/

* You are given an array of strings `tokens` that represents an arithmetic expression in a [Reverse Polish Notation](http://en.wikipedia.org/wiki/Reverse_Polish_notation).

  Evaluate the expression. Return *an integer that represents the value of the expression*.

  **Note** that:

  - The valid operators are `'+'`, `'-'`, `'*'`, and `'/'`.
  - Each operand may be an integer or another expression.
  - The division between two integers always **truncates toward zero**.
  - There will not be any division by zero.
  - The input represents a valid arithmetic expression in a reverse polish notation.
  - The answer and all the intermediate calculations can be represented in a **32-bit** integer.

* ```js
  // quite straightforward 
  var evalRPN = function(tokens) {
      let stack = [];
      for (let token of tokens) {
          if (!isNaN(token)) {
              stack.push(Number(token));
          } else {
              let second = stack.pop();
              let first = stack.pop();
              switch (token) {
                  case "+":
                      stack.push(first + second);
                      break;
                  case "-":
                      stack.push(first - second);
                      break;
                  case "*":
                      stack.push(first * second);
                      break;
                  case "/":
                      stack.push(Math.trunc(first / second));
              }
          }
      }
  
      return stack.pop();
  };
  ```

# 152 Maximum Product Subarray

* https://leetcode.com/problems/maximum-product-subarray/description/

* Given an integer array `nums`, find a subarray that has the largest product, and return *the product*.

  The test cases are generated so that the answer will fit in a **32-bit** integer.

* ```js
  // Because the minimum value (if negative) can be the largest value when meet the other negative value. 
  // So we need to maintain both maximum and minimum value.
  var maxProduct = function(nums) {
      let maxVal = nums[0];
      let minVal = nums[0];
      let result = nums[0];
  
      for (let i=1;i<nums.length;i++) {
          let vals = [maxVal * nums[i], minVal * nums[i], nums[i]];
          maxVal = Math.max(...vals);
          minVal = Math.min(...vals);
  
          result = Math.max(result, maxVal);
      }
  
      return result;
  };
  ```

# 155 Min Stack

* Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

  Implement the `MinStack` class:

  - `MinStack()` initializes the stack object.
  - `void push(int val)` pushes the element `val` onto the stack.
  - `void pop()` removes the element on the top of the stack.
  - `int top()` gets the top element of the stack.
  - `int getMin()` retrieves the minimum element in the stack.

  You must implement a solution with `O(1)` time complexity for each function.

* ```js
  var MinStack = function() {
      // initialize stack
      this.stack = []
  };
  
  MinStack.prototype.push = function(val) {
      // min stack's element know the min value when they are on the top of stack
      let cur = {val: val, curMin: val}
      if (this.stack.length !== 0 && this.getMin() < cur.curMin) {
          cur.curMin = this.getMin();
      }
  
      this.stack.push(cur);
  };
  
  MinStack.prototype.pop = function() {
      this.stack.pop()
  };
  
  MinStack.prototype.top = function() {
      return this.stack[this.stack.length - 1].val;
  };
  
  MinStack.prototype.getMin = function() {
      return this.stack[this.stack.length - 1].curMin;
  };
  ```

# 162 Find Peak Element

* https://leetcode.com/problems/find-peak-element/description/

* A peak element is an element that is strictly greater than its neighbors.

  Given a **0-indexed** integer array `nums`, find a peak element, and return its index. If the array contains multiple peaks, return the index to **any of the peaks**.

  You may imagine that `nums[-1] = nums[n] = -∞`. In other words, an element is always considered to be strictly greater than a neighbor that is outside the array.

  You must write an algorithm that runs in `O(log n)` time.

* ```js
  var findPeakElement = function(nums) {
      let left = 0;
      let right = nums.length - 1;
      while (left < right) {
          let mid = Math.floor((right + left) / 2);
          if (nums[mid] > nums[mid + 1]) {
              // this means that range of [0, mid] definitely has a peak
              
              // why not (right = mid - 1) ?
              // because mid can be the possible idx
              right = mid;
          } else {
              // this means that range of (mid, nums.length - 1] definitely has a peak
              
              // because mid does not satisfy the requirement
              // so we can start from mid + 1
              left = mid + 1;
          }
      }
  	// while loop always terminates when left === right
      return left;
  };
  
  // because the nums[-1] = nums[n] = -infinite
  // so the two end elements also can be a valid answer.
  ```

# 189 House Robber

* https://leetcode.com/problems/house-robber/

* You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security systems connected and **it will automatically contact the police if two adjacent houses were broken into on the same night**.

  Given an integer array `nums` representing the amount of money of each house, return *the maximum amount of money you can rob tonight **without alerting the police***.

* ```js
  var rob = function(nums) {
      // if total length is 2 or less, only can rob one house
      if (nums.length <= 2) {
          return Math.max(...nums);
      }
  
      // use DP to store the max value for the certain length
      let dp = new Array(nums.length).fill(0);
      dp[0] = nums[0];
      dp[1] = Math.max(nums[0], nums[1]);
      for (let i=2;i<nums.length;i++) {
          // two conditions:
          // 1. not choose current house, so I can rob adjacent house
          // 2. choose current house, so I cannot rob adjacent house
          dp[i] = Math.max(dp[i-1], nums[i] + dp[i-2]);
      }
  
      return dp[nums.length-1];
  };
  ```

# 494
