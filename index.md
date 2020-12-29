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
