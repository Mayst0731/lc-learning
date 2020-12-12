## DP
### Markdown
1. Coin Change(Top Down)
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



- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/meist0731/lc-learning/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
