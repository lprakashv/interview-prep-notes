# Dynamic Programming Patterns

## 1. Min/Max cost/path/sum to Reach a Target

### Approach

1. Choose minimum (maximum) path among all possible paths before the current state, then add value for the current state.
2. `routes[i] = min(routes[i-1], routes[i-2], ... , routes[i-k]) + cost[i]`.

```java
for (int i = 1; i <= target; ++i) {
   for (int j = 0; j < ways.size(); ++j) {
       if (ways[j] <= i) {
           dp[i] = min(dp[i], dp[i - ways[j]] + cost / path / sum) ;
       }
   }
}

return dp[target]
```

### Example problems

* Min cost of climbing stairs
* Min path sum
* Coin change problem

## 2. Distinct ways - no of all possible ways to rech the target

### Approach

1. “Sum” of all possible ways to reach the current state (i).
2. `routes[i] = routes[i-1] + routes[i-2], ... , + routes[i-k]`

```java
for (int i = 1; i <= target; ++i) {
   for (int j = 0; j < ways.size(); ++j) {
       if (ways[j] <= i) {
           dp[i] += dp[i - ways[j]];
       }
   }
}

return dp[target]
```

### Example problems

* Climbing stairs
* Unique paths
* no. of dice rolls with target sum.
* Knight probability in chessboard
* Partition equal subset sum.

## 3. Merging intervals

### Approach

Find all optimal solutions for every
interval and return the best possible answer.

### Code approach

1. From i to j
2. `dp[i][j] = dp[i][k] + result[k] + dp[k+1][j]`

> Get the best from the left and right sides and add a solution for the current position.

```java
for(int l = 1; l<n; l++) {
   for(int i = 0; i<n-l; i++) {
       int j = i+l;
       for(int k = i; k<j; k++) {
           dp[i][j] = max(dp[i][j], dp[i][k] + result[k] + dp[k+1][j]);
       }
   }
}

return dp[0][n-1]
```

### Example problems

* Min cost tree from leaf value
* Unique binary search trees.
* Min cost to merge stones

## 4. DP on Strings

> Given 2 strings where lengths are not too big

### 2 strings

```java
// i - indexing string s1
// j - indexing string s2

for (int i = 1; i <= n; ++i) {
   for (int j = 1; j <= m; ++j) {
       if (s1[i-1] == s2[j-1]) {
           dp[i][j] = /*code*/;
       } else {
           dp[i][j] = /*code*/;
       }
   }
}
```

### 1 string

```java
for (int l = 1; l < n; ++l) {
   for (int i = 0; i < n-l; ++i) {
       int j = i + l;
       if (s[i] == s[j]) {
           dp[i][j] = /*code*/;
       } else {
           dp[i][j] = /*code*/;
       }
   }
}
```

### Example problems

* Longest common subsequence (LCS)
* Palindromic substrings
* Longest palindromic subsequence
* Min ascii delete sum of 2 strings

## 5. Decision making

> Given a set of values find an answer with an option to choose or ignore the current value

### Approach

If you decide to choose the current value use the previous result where the value was ignored; vice-versa, if you decide to ignore the current value use previous result where value was used.

```java
// i - indexing a set of values
// j - options to ignore j values

for (int i = 1; i < n; ++i) {
   for (int j = 1; j <= k; ++j) {
       dp[i][j] = max({dp[i][j], dp[i-1][j] + arr[i], dp[i-1][j-1]});
       dp[i][j-1] = max({dp[i][j-1], dp[i-1][j-1] + arr[i], arr[i]});
   }
}
```

### Example problems

* House robber
* Best time to buy and sell stocks
