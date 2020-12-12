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

