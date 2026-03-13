# 算法与数据结构

算法是计算机科学的核心，掌握常用算法和数据结构是提升编程能力的关键。

## 时间复杂度

| 复杂度 | 说明 | 示例 |
|--------|------|------|
| O(1) | 常数时间 | 数组访问 |
| O(log n) | 对数时间 | 二分查找 |
| O(n) | 线性时间 | 线性查找 |
| O(n log n) | 线性对数时间 | 快速排序 |
| O(n²) | 平方时间 | 冒泡排序 |
| O(2ⁿ) | 指数时间 | 递归斐波那契 |

## 数据结构

### 数组

```java
// 固定大小数组
int[] arr = new int[10];
arr[0] = 1;
int value = arr[0];

// 动态数组
List<Integer> list = new ArrayList<>();
list.add(1);
list.get(0);
list.remove(0);
```

### 链表

```java
class ListNode {
    int val;
    ListNode next;
    
    ListNode(int val) {
        this.val = val;
    }
}

// 遍历链表
ListNode head = new ListNode(1);
ListNode current = head;
while (current != null) {
    System.out.println(current.val);
    current = current.next;
}
```

### 栈

```java
// 使用 ArrayDeque 实现栈
Deque<Integer> stack = new ArrayDeque<>();
stack.push(1);      // 压栈
int top = stack.peek(); // 查看栈顶
int pop = stack.pop();  // 出栈
```

### 队列

```java
// 使用 LinkedList 实现队列
Queue<Integer> queue = new LinkedList<>();
queue.offer(1);     // 入队
int front = queue.peek(); // 查看队首
int poll = queue.poll();  // 出队
```

### 哈希表

```java
Map<String, Integer> map = new HashMap<>();
map.put("key", 1);
Integer value = map.get("key");
map.remove("key");
boolean contains = map.containsKey("key");

Set<String> keys = map.keySet();
Collection<Integer> values = map.values();
```

### 树

```java
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    
    TreeNode(int val) {
        this.val = val;
    }
}

// 前序遍历
void preorder(TreeNode root) {
    if (root == null) return;
    System.out.print(root.val + " ");
    preorder(root.left);
    preorder(root.right);
}

// 中序遍历
void inorder(TreeNode root) {
    if (root == null) return;
    inorder(root.left);
    System.out.print(root.val + " ");
    inorder(root.right);
}

// 后序遍历
void postorder(TreeNode root) {
    if (root == null) return;
    postorder(root.left);
    postorder(root.right);
    System.out.print(root.val + " ");
}

// 层序遍历
void levelOrder(TreeNode root) {
    if (root == null) return;
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    
    while (!queue.isEmpty()) {
        TreeNode node = queue.poll();
        System.out.print(node.val + " ");
        if (node.left != null) queue.offer(node.left);
        if (node.right != null) queue.offer(node.right);
    }
}
```

## 排序算法

### 快速排序

```java
public void quickSort(int[] arr, int low, int high) {
    if (low < high) {
        int pi = partition(arr, low, high);
        quickSort(arr, low, pi - 1);
        quickSort(arr, pi + 1, high);
    }
}

private int partition(int[] arr, int low, int high) {
    int pivot = arr[high];
    int i = low - 1;
    
    for (int j = low; j < high; j++) {
        if (arr[j] < pivot) {
            i++;
            swap(arr, i, j);
        }
    }
    swap(arr, i + 1, high);
    return i + 1;
}

private void swap(int[] arr, int i, int j) {
    int temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
}
```

### 归并排序

```java
public void mergeSort(int[] arr, int left, int right) {
    if (left < right) {
        int mid = left + (right - left) / 2;
        mergeSort(arr, left, mid);
        mergeSort(arr, mid + 1, right);
        merge(arr, left, mid, right);
    }
}

private void merge(int[] arr, int left, int mid, int right) {
    int n1 = mid - left + 1;
    int n2 = right - mid;
    
    int[] L = new int[n1];
    int[] R = new int[n2];
    
    for (int i = 0; i < n1; i++) {
        L[i] = arr[left + i];
    }
    for (int j = 0; j < n2; j++) {
        R[j] = arr[mid + 1 + j];
    }
    
    int i = 0, j = 0, k = left;
    while (i < n1 && j < n2) {
        if (L[i] <= R[j]) {
            arr[k] = L[i++];
        } else {
            arr[k] = R[j++];
        }
        k++;
    }
    
    while (i < n1) {
        arr[k++] = L[i++];
    }
    while (j < n2) {
        arr[k++] = R[j++];
    }
}
```

## 搜索算法

### 二分查找

```java
public int binarySearch(int[] arr, int target) {
    int left = 0;
    int right = arr.length - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] == target) {
            return mid;
        } else if (arr[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return -1;
}
```

### 深度优先搜索 (DFS)

```java
public void dfs(TreeNode root) {
    if (root == null) return;
    
    // 访问当前节点
    System.out.print(root.val + " ");
    
    // 递归访问子节点
    dfs(root.left);
    dfs(root.right);
}

// 图的 DFS
public void dfsGraph(int[][] graph, int node, boolean[] visited) {
    visited[node] = true;
    System.out.print(node + " ");
    
    for (int neighbor : graph[node]) {
        if (!visited[neighbor]) {
            dfsGraph(graph, neighbor, visited);
        }
    }
}
```

### 广度优先搜索 (BFS)

```java
public void bfs(TreeNode root) {
    if (root == null) return;
    
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    
    while (!queue.isEmpty()) {
        TreeNode node = queue.poll();
        System.out.print(node.val + " ");
        
        if (node.left != null) queue.offer(node.left);
        if (node.right != null) queue.offer(node.right);
    }
}

// 图的 BFS
public void bfsGraph(int[][] graph, int start) {
    boolean[] visited = new boolean[graph.length];
    Queue<Integer> queue = new LinkedList<>();
    
    visited[start] = true;
    queue.offer(start);
    
    while (!queue.isEmpty()) {
        int node = queue.poll();
        System.out.print(node + " ");
        
        for (int neighbor : graph[node]) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                queue.offer(neighbor);
            }
        }
    }
}
```

## 动态规划

### 斐波那契数列

```java
// 递归（带记忆化）
public int fibonacci(int n) {
    int[] memo = new int[n + 1];
    return fibHelper(n, memo);
}

private int fibHelper(int n, int[] memo) {
    if (n <= 1) return n;
    if (memo[n] != 0) return memo[n];
    
    memo[n] = fibHelper(n - 1, memo) + fibHelper(n - 2, memo);
    return memo[n];
}

// 迭代
public int fibonacciIterative(int n) {
    if (n <= 1) return n;
    
    int[] dp = new int[n + 1];
    dp[0] = 0;
    dp[1] = 1;
    
    for (int i = 2; i <= n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }
    
    return dp[n];
}

// 空间优化
public int fibonacciOptimized(int n) {
    if (n <= 1) return n;
    
    int prev = 0, curr = 1;
    for (int i = 2; i <= n; i++) {
        int next = prev + curr;
        prev = curr;
        curr = next;
    }
    
    return curr;
}
```

### 最长公共子序列 (LCS)

```java
public int longestCommonSubsequence(String text1, String text2) {
    int m = text1.length();
    int n = text2.length();
    
    int[][] dp = new int[m + 1][n + 1];
    
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (text1.charAt(i - 1) == text2.charAt(j - 1)) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
            } else {
                dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }
    
    return dp[m][n];
}
```

## 常用算法技巧

### 双指针

```java
// 两数之和
public int[] twoSum(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    
    while (left < right) {
        int sum = nums[left] + nums[right];
        if (sum == target) {
            return new int[]{left + 1, right + 1};
        } else if (sum < target) {
            left++;
        } else {
            right--;
        }
    }
    
    return new int[]{};
}

// 快慢指针（链表环检测）
public boolean hasCycle(ListNode head) {
    ListNode slow = head;
    ListNode fast = head;
    
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) {
            return true;
        }
    }
    
    return false;
}
```

### 滑动窗口

```java
// 最长子串无重复字符
public int lengthOfLongestSubstring(String s) {
    Set<Character> set = new HashSet<>();
    int maxLen = 0, left = 0;
    
    for (int right = 0; right < s.length(); right++) {
        while (set.contains(s.charAt(right))) {
            set.remove(s.charAt(left));
            left++;
        }
        set.add(s.charAt(right));
        maxLen = Math.max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

### 回溯算法

```java
// 全排列
public List<List<Integer>> permute(int[] nums) {
    List<List<Integer>> result = new ArrayList<>();
    backtrack(result, new ArrayList<>(), nums, new boolean[nums.length]);
    return result;
}

private void backtrack(List<List<Integer>> result, List<Integer> tempList, 
                     int[] nums, boolean[] used) {
    if (tempList.size() == nums.length) {
        result.add(new ArrayList<>(tempList));
        return;
    }
    
    for (int i = 0; i < nums.length; i++) {
        if (used[i]) continue;
        used[i] = true;
        tempList.add(nums[i]);
        backtrack(result, tempList, nums, used);
        tempList.remove(tempList.size() - 1);
        used[i] = false;
    }
}
```

## 参考资料

- [LeetCode](https://leetcode.com/)
- [算法导论](https://mitpress.mit.edu/books/introduction-algorithms-third-edition)
- [GeeksforGeeks](https://www.geeksforgeeks.org/)

---

*最后更新时间: {docsify-updated}*