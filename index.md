## DP
### 1. Coin Change(Top Down)
  - sub return value: float('inf')
  - cache type: dict
  - when to fetch the value in the cache

```
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        if amount == 0:
            return 0
        cache = dict()
        
        def findMin(coins,amount):
            if amount == 0:
                return 0
            if amount < 0 :
                return float('inf')
            if amount in cache:
                return cache[amount]
            current_min = float('inf')
            for coin in coins:
                previous_coins = findMin(coins,amount-coin)
                current_min = min(current_min,previous_coins+1)
            cache[amount] = current_min 
            return current_min      
        findMin(coins,amount)
        return cache[amount] if cache[amount] != float('inf') else -1
 ```       
### 2. Coin change(Bottom Up)
```
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        res = [float('inf')] * (amount+1)
        res[0] = 0
        
        for i in range(1,len(res)):
            for coin in coins:
                if i - coin >= 0:
                    res[i] = min(res[i-coin]+1,res[i])
        return res[-1] if res[-1] != float('inf') else -1
```
## Backtracking
### 3. Permute
  - select one value from the choices(nums)
  - decide the terminate condition
```
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        
        res = []
        N = len(nums)
        
        def findAns(nums,path):
            if len(path) == N:
                res.append(list(path))
                return 
            for i,num in enumerate(nums):
                path.append(num)
                findAns(nums[:i]+nums[i+1:],path)
                path.pop()
            
        findAns(nums,[])
        return res
```
```
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        
        res = []
        path = []
        N = len(nums)
        self.findPermute(nums,path,res,N)
        return res
    
    # len(nums) is always changing, so N need to be record at the very first place
    def findPermute(self, nums, path, res,N):
        if len(path) == N:
            res.append(list(path))
            return 
        
        for i, num in enumerate(nums):
            path.append(num)
            left_nums = nums[:i]+nums[i+1:]
            self.findPermute(left_nums,path,res,N)
            path.pop()
```

### 4. N Queen
```
class Solution:
    def solveNQueens(self, n):
        res = []
        row = 0
        path = [-1]*n
        board = []
        self.dfs(row,path,board, res)
        return res
 
    # res is all the valid boards, board is only one board
    # path is for recording the queens col index
    # row is one row in a path
    
    def dfs(self,row, path, board, res):
        if row == len(path):
            res.append(board)
            return  # backtracking
        for i in range(len(path)):
            path[row] = i
            if self.valid(path, row):  # pruning
                tmp = "."*len(path)
                self.dfs(row+1, path, board+[tmp[:i]+"Q"+tmp[i+1:]], res)

    # check whether nth queen can be placed in that column
    def valid(self, path, row):
        for i in range(row):
            if abs(path[i]-path[row]) == row - i or path[i] == path[row]:
                return False
        return True
        
```
### 78. Subsets
```
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        
        res = []
        
        def backtrack(length,path,first):
            if len(path)==length:
                res.append(list(path))
                
            for i in range(first,len(nums)):
                path.append(nums[i])
                backtrack(length,path,i+1)
                path.pop()
        
        for length in range(len(nums)+1):
            backtrack(length,[],0)
            
        return res
        
```
### 215. Kth Largest Element in an Array

- heap method:
```
class Solution:
    def findKthLargest(self, nums: List[int], k: int) -> int:
        # extract sub nums of k length
        k_nums = nums[:k]
        # put the sub num into a heap
        heapq.heapify(k_nums)
        
        # add the rest values into the list. First: push new value Second: pop the smallest value
        for num in nums[k:]:
            heapq.heappushpop(k_nums,num)
        
        return k_nums[0]
```

- Quick Selection

```
class Solution:
    def findKthLargest(self, nums: List[int], k: int) -> int:
        if len(nums) == 0:
            return -1
        
        pivot = nums[0]
        
        left = [num for num in nums if num < pivot]
        middle = [num for num in nums if num == pivot]
        right = [num for num in nums if num > pivot]
        
        if k <= len(right):
            return self.findKthLargest(right,k)
        
        elif len(nums) - k >= len(left):
            return middle[0]
        
        else:
            return self.findKthLargest(left,k-len(right)-len(middle))
```

- Quick Selection(change left, right and middle arrays number)

```
class Solution:
    def findKthLargest(self, nums: List[int], k: int) -> int:
        if len(nums) == 0:
            return -1
        
        pivot = nums[0]
        
        left = [num for num in nums if num > pivot]
        middle = [num for num in nums if num == pivot]
        right = [num for num in nums if num < pivot]
        
        if k <= len(left):
            return self.findKthLargest(left,k)
        elif k <= len(left) + len(middle):
            return middle[0]
        else:
            return self.findKthLargest(right,k-(len(left)+len(middle)))
```

### 695. Max Area of Island
- recursive
```
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        if len(grid) == 0:
            return 0
        
        max_area = 0
        for row in range(len(grid)):
            for col in range(len(grid[0])):
                if grid[row][col] == 1:
                    area = self.dfs(grid,row,col)
                    max_area = max(area,max_area) 
        return max_area
    
    
    def dfs(self,grid,row,col):
        if row < 0 or row >= len(grid) or col < 0 or col >= len(grid[0]):
            return 0
        
        if grid[row][col] == 0:
            return 0
        
        if grid[row][col] == 1:
            # after checking the unit, it should be set to be 0 immediately to avoid endless recursion
            grid[row][col] = 0
            left = self.dfs(grid,row,col-1)
            right = self.dfs(grid,row,col+1)
            up = self.dfs(grid,row-1,col)
            down = self.dfs(grid,row+1,col)
            area = 1 + left + right + up + down
        return area          
```

### 295. Find Median from Data Stream

```
class MedianFinder:

    def __init__(self):
        """
        initialize your data structure here.
        """
        # Initialize two heaps for storing nums
        self.small = []
        self.large = []

    def addNum(self, num: int) -> None:
        
        if len(self.small) == 0:
            heapq.heappush(self.small,-num)
            # this return is important as the num will be added into small twice without this stop word.
            return 
        # add numbers into small or large arrs
        if num <= -self.small[0]:
            heapq.heappush(self.small, -num)
        else:
            heapq.heappush(self.large, num)
        # balance size between small and large heaps, the difference between them should be limited to at most 1
        
        if len(self.small) - len(self.large) == 2:
            heapq.heappush(self.large,-heapq.heappop(self.small))
        
        if len(self.large) - len(self.small) == 2:
            heapq.heappush(self.small,-heapq.heappop(self.large)) 

    def findMedian(self) -> float:
        if len(self.small) == len(self.large):
            median = (self.large[0]-self.small[0])/2
        
        if len(self.small) > len(self.large):
            median = -float(self.small[0])
            
        if len(self.large) > len(self.small):
            median = float(self.large[0])
        
        return median
```

### 241. Different Ways to Add Parentheses

```
class Solution:
    def diffWaysToCompute(self, input: str) -> List[int]:
        memo = dict()
        res = self.getResult(input,memo)
        return res
    
    def getResult(self,input,memo):
        if input in memo:
            return memo[input]
        
        # don't forget to turn string into int for calculation
        if input.isdigit():
            memo[input] = [int(input)]
            return [int(input)]
        res = []
        operators = '+-*'
        for i, char in enumerate(input):
            if char in operators:
                left = self.getResult(input[:i],memo)
                right = self.getResult(input[i+1:],memo)
                
                for l_num in left:
                    for r_num in right:
                        single_res = self.operate(char,l_num,r_num)
                        res.append(single_res)
        memo[input] = res     
        return res
        
    
    def operate(self,operator,num1,num2):
        switcher = {
            '+': num1+num2,
            '-': num1-num2,
            '*': num1*num2,
        }
        return switcher.get(operator)
```

### 453. Minimum Moves to Equal Array Elements
```
class Solution:
    def minMoves(self, nums: List[int]) -> int:

        return sum(nums) - min(nums)*len(nums)
```

### 53. Maximum Subarray

```
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        
        max_sum = nums[0]
        pre_sum = nums[0]
        
        for i,num in enumerate(nums[1:]):
            if pre_sum < 0:
                pre_sum = num
            else:
                pre_sum += num
            max_sum = max(pre_sum,max_sum)
        return max_sum
```
### 152. Maximum Product Subarray
```
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        
        if len(nums) == 0:
            return 0
        
        
        pre_min = nums[0]
        pre_max = nums[0]
        res = nums[0]
        for i,num in enumerate(nums[1:]):
            cur_min = min(num,pre_min*num,pre_max*num)
            cur_max = max(num,pre_min*num,pre_max*num)
            
            pre_min = cur_min
            pre_max = cur_max
            
            res = max(cur_max,res)
        return res
```
### 134. Gas Station
```
class Solution:
    def canCompleteCircuit(self, gas: List[int], cost: List[int]) -> int:
        
        gas_in_tank, gas_needed, start_point = 0,0,0
        
        for i,(g,c) in enumerate(zip(gas,cost)):
            gas_in_tank += g - c
            if gas_in_tank < 0:
                start_point = i + 1
                # since gas_in_tank is negative, we need to substract the value instead of add
                gas_needed -= gas_in_tank
                # re count gas in tank
                gas_in_tank = 0
                
        
        return start_point if gas_in_tank >= gas_needed else -1
```

### 1047. Remove All Adjacent Duplicates In String

- Like matching game
```
class Solution:
    def removeDuplicates(self, S: str) -> str:
        res = []
        for c in S:
            # if the num at the top of stack is the same as current char, they are matched and need to be erased together, so pop off it from the stack and continue looping through the array
            if res and res[-1] == c:
                res.pop()
            else:
                res.append(c)
        return ''.join(res)
```

### 1209. Remove All Adjacent Duplicates in String II

```
class Solution:
    def removeDuplicates(self, s: str, k: int) -> str:
        
        stack = []
        
        for char in s:
            if not stack:
                stack.append([char,1])
            elif stack and stack[-1][0] != char:
                stack.append([char,1])    
            elif stack and stack[-1][0] == char and stack[-1][1] == k-1:
                stack.pop() 
            elif stack and stack[-1][0] == char and stack[-1][1] < k-1:
                stack[-1][1] += 1
                
            
        
        res = ''
        for c,q in stack:
            res += c*q
            
        return res    
```

### 621. Task Scheduler

```
class Solution:
    def leastInterval(self, tasks: List[str], n: int) -> int:
        count_tasks = Counter(tasks)
        all_slots = 0
        h = []
        # build a max-heap
        for k,v in count_tasks.items():
            heapq.heappush(h,[-v,k])
        
        # loop through the heap
        while h:
            # i is for counting which slot is going to be taken in current bucket
            i = 0
            # store the items needed to be counted the next loop
            temp =[]
            # n is the slots needed in this bucket
            while i <= n:
                # the total slot should be counted
                all_slots += 1
                # if h has items to be added into the bucket, it's good to add the item
                if h:
                    num,task = heapq.heappop(h)
                    # the frequency should be substracted (freq is negative, so add 1)
                    num += 1
                    # if freq reach to 0, no need to add the item into temp for the next loop
                    if num < 0:
                        temp.append([num,task])
                # when the current bucket is the last bucket, no need to continue loop
                if not h and not temp: break
                # the next slot to be filled then
                i += 1
            for freq_task in temp:
                heapq.heappush(h,freq_task)
                
        return all_slots 
```

### 767. Reorganize String

```
class Solution:
    def reorganizeString(self, S: str) -> str:
        res, c = [], Counter(S)
        legal_heap = [(-v,k) for k,v in c.items()]
        heapq.heapify(legal_heap)
        illegal_freq, illegal_char = 0, ''
        while legal_heap:
            freq, char = heapq.heappop(legal_heap)
            res.append(char)
            if illegal_freq < 0:
                heapq.heappush(legal_heap, (illegal_freq, illegal_char))
            freq += 1
            illegal_freq, illegal_char = freq, char
        res = ''.join(res)
        if len(res) != len(S): return ""
        return res       
```

### 1197. Minimum Knight Moves


```
class Solution:
    def minKnightMoves(self, x: int, y: int) -> int:
        # initialize a queue for bfs iteration
        q = collections.deque()
        q.append([0,0,0])
        # record visited unit
        visited = set()
        # the (0,0) should be added into the visited record as initialization
        visited.add((0,0))
        # begin bfs
        while q:
            cur_x,cur_y,steps = q.popleft()
            if cur_x==x and cur_y==y:
                return steps
            # loop through each choice, there are 8 choices for each unit, for each choice there should be added one step, and add the unit into visited and queue for the next loop's check
            dirs = [(cur_x-1,cur_y-2),(cur_x-2,cur_y-1),(cur_x-2,cur_y+1),(cur_x-1,cur_y+2),(cur_x+1,cur_y-2),(cur_x+2,cur_y-1),(cur_x+1,cur_y+2),(cur_x+2,cur_y+1)]
            for i,j in dirs:
                if (i,j) not in visited:
                    q.append([i,j,steps+1])
                    visited.add((i,j))
        return -1 
```


### 1507. Reformat Date


```
class Solution:
    def reformatDate(self, date: str) -> str:
        date_lst = date.split()
        
        d = date_lst[0]
        m = date_lst[1]
        y = date_lst[2]
        
        special_date = ['1st','2nd','3rd']
        
        if d in special_date:
            d = str(special_date.index(d)+1)
        
        else:
            d = d[:-2]
            
        if len(d) < 2:
            d = '0' + d
            
        m = self.month(m)
        
        y = str(y)
        
        res = y + "-" + m + "-" + d
        return res
    
    def month(self,m):
        return {
            "Jan":'01',
            "Feb":'02',
            "Mar":"03", 
            "Apr":"04",
            "May":"05",
            "Jun":"06", 
            "Jul":"07",
            "Aug":"08",
            "Sep":"09",
            "Oct":"10",
            "Nov":"11", 
            "Dec":"12"
        }.get(m)       
```

### 669. Trim a Binary Search Tree
- iterative
```
class Solution:
    def trimBST(self, root: TreeNode, low: int, high: int) -> TreeNode:
        
        if not root:
            return None
        
        # find the proper root
        while root:
            if root.val < low and root.right:
                root = root.right
            elif root.val > high and root.left:
                root = root.left
            else:
                break
        # trim root's children
        # trim left child
        parent = root
        node = root.left
        
        while node:
            if node.val < low:
                parent.left = node.right
                node = node.right
                
            else:
                parent = node
                node = node.left
        # trim right child        
        parent = root
        node = root.right
        while node:
            if node.val > high:
                parent.right = node.left
                node = node.left
            else:
                parent = node
                node = node.right
        return root
```

- Recursive

```
class Solution:
    def trimBST(self, root: TreeNode, low: int, high: int) -> TreeNode:
        
        if not root:
            return None
        
        if root.val < low:
            root = self.trimBST(root.right,low,high)
        
        elif root.val > high:
            root = self.trimBST(root.left, low,high)
            
        else:
            root.left = self.trimBST(root.left,low,high)
            root.right = self.trimBST(root.right,low,high)
        
        return root
```
