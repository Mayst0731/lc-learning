## DP
### 1. Coin Change(Top Down)
  - sub return value: float('inf')
  - cache type: dict
  - when to fetch the value in the cache

```markdown
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        if amount == 0:
            return 0
        
        cache = dict()
        
        def findMin(coins,amount):
            if amount == 0:
                return 0
            if amount < 0:
                return float('inf')
            
            if amount in cache:
                return cache[amount]
            
            minCoins = float('inf')
            for coin in coins:
                previous_coins = findMin(coins,amount-coin)
                minCoins = min(minCoins,previous_coins+1)
            
            cache[amount] = minCoins
            return minCoins
        
        findMin(coins,amount)
        return cache[amount] if cache[amount] != float('inf') else -1
 ```       
### 2. Coin change(Bottom Up)

