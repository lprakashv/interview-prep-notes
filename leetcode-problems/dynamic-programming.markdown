# Leetcode Notable Dynamic Programmming problems

NOTES:

* Subsequence = list (need not be contiguous)
* Subarray = array (contiguous);

## Q: Max sum subarray

> Kadane's Algorithm.

```java
public int maxSubArray(int[] nums) {
    int n = nums.length;
    int maxSum = nums[0];

    for(int i = 1; i < n; ++i) {
        if (nums[i - 1] > 0) {
            nums[i] += nums[i - 1];
        }
        maxSum = Math.max(nums[i], maxSum);
    }
    return maxSum;
}
```

## Q: Find string inside a string

> KMP

```java
int n = s.length();
int[] dp = new int[n];

// Construct partial match table (lookup table).
// It stores the length of the proper prefix that is also a proper suffix.

// ex. ababa --> [0, 0, 1, 2, 1]
// ab --> the length of common prefix / suffix = 0
// aba --> the length of common prefix / suffix = 1
// abab --> the length of common prefix / suffix = 2
// ababa --> the length of common prefix / suffix = 1

for (int i = 1; i < n; ++i) {
    int j = dp[i - 1];
    while (j > 0 && s.charAt(i) != s.charAt(j)) {
        j = dp[j - 1];
    }
    if (s.charAt(i) == s.charAt(j)) {
        ++j;
    }
    dp[i] = j;
}
```

## Q: Check if string contains repetitions

__Solution:__ 1 - Concatenation

```java
(S + S).substring(1, length-1).contains(S)
```

__Solution:__ 2 - KMP

```java
public boolean repeatedSubstringPattern(String s) {
    //return (s + s).substring(1, 2 * s.length() - 1).contains(s);

    int[] dp = new int[s.length()];

    for (int i=1; i<s.length(); i++) {
        int j = dp[i-1];
        while (j > 0 && s.charAt(i) != s.charAt(j)) {
            j = dp[j-1];
        }
        if (s.charAt(i) == s.charAt(j)) {
            j++;
            dp[i] = j;
        }
    }

    int l = dp[s.length()-1];

    return l != 0 && s.length() % (s.length() - l) == 0;
}
```

## Q: Max product subarray

```java
public int maxProduct(int[] nums) {
    if (nums.length == 0) return 0;

    int max_so_far = nums[0];
    int min_so_far = nums[0];
    int result = max_so_far;

    for (int i = 1; i < nums.length; i++) {
        int curr = nums[i];
        int temp_max = Math.max(curr, Math.max(max_so_far * curr, min_so_far * curr));
        min_so_far = Math.min(curr, Math.min(max_so_far * curr, min_so_far * curr));

        max_so_far = temp_max;

        result = Math.max(max_so_far, result);
    }

    return result;
}
```

## Q: Product of array elements without self

__Solution:__

1. Create left and right product arrays
    1. `left[i]` will have products of all the elements to the left, similarly right.
    2. 1 to N-1 :=> `left[i] = input[i-1] * left[i-1]`
    3. N-2 to 0 :=> `right[i] = right[i+1] * right[i+1]`
2. `output[i] = left[i] * right[i]`

## Q: Coin change problem

```java
  public int coinChange(int[] coins, int amount) {
    int max = amount + 1;
    int[] dp = new int[amount + 1];
    Arrays.fill(dp, max);
    dp[0] = 0;
    for (int i = 1; i <= amount; i++) {
      for (int j = 0; j < coins.length; j++) {
        if (coins[j] <= i) {
          dp[i] = Math.min(dp[i], dp[i - coins[j]] + 1);
        }
      }
    }
    return dp[amount] > amount ? -1 : dp[amount];
  }
```

## Q: Longest increasing subsequence “LIS”

> Input: [10,9,2,5,3,7,101,18]
>> Output: 4
>>> The longest increasing subsequence is [2,3,7,101], therefore the length is 4.

```java
public int lengthOfLIS(int[] nums) {
    if (nums.length == 0) {
        return 0;
    }
    int[] dp = new int[nums.length];
    dp[0] = 1;
    int maxans = 1;
    for (int i = 1; i < dp.length; i++) {
        int maxval = 0;
        for (int j = 0; j < i; j++) {
            if (nums[i] > nums[j]) {
                maxval = Math.max(maxval, dp[j]);
            }
        }
        dp[i] = maxval + 1;
        maxans = Math.max(maxans, dp[i]);
    }
    return maxans;
}
```

## Q: Decode num of strings can be decoded from integer sequence (1 -> A, 26 -> Z)

```java
public int numDecodings(String s) {

    if(s == null || s.length() == 0) {
        return 0;
    }

    // DP array to store the subproblem results
    int[] dp = new int[s.length() + 1];
    dp[0] = 1;
    // Ways to decode a string of size 1 is 1. Unless the string is '0'.
    // '0' doesn't have a single digit decode.
    dp[1] = s.charAt(0) == '0' ? 0 : 1;

    for(int i = 2; i < dp.length; i += 1) {

        // Check if successful single digit decode is possible.
        if(s.charAt(i-1) != '0') {
            dp[i] += dp[i-1];  
        }

        // Check if successful two digit decode is possible.
        int twoDigit = Integer.valueOf(s.substring(i-2, i));
        if(twoDigit >= 10 && twoDigit <= 26) {
            dp[i] += dp[i-2];
        }
    }
    return dp[s.length()];

}
```

## Q: Word can be broken into all the words in dict

```java
public boolean wordBreak(String s, List<String> wordDict) {
    Set<String> wordDictSet=new HashSet(wordDict);
    boolean[] dp = new boolean[s.length() + 1];
    dp[0] = true;
    for (int i = 1; i <= s.length(); i++) {
        for (int j = 0; j < i; j++) {
            if (dp[j] && wordDictSet.contains(s.substring(j, i))) {
                dp[i] = true;
                break;
            }
        }
    }
    return dp[s.length()];
}
```

## Q: Word can be broken into all the words in dict (all the sentences formed)

> like above with slight modification.

```java
public List<String> wordBreak(String s, List<String> wordDict) {
    Set<Character> ss = new HashSet<>();
    Set<Character> ws = new HashSet<>();
    for (char c : s.toCharArray()) ss.add(c);
    for (String w: wordDict) {
        for (char c : w.toCharArray()) ws.add(c);
    }
    if (!ws.containsAll(ss)) return new ArrayList<>();

    // actual logic starts here
    Set<String> dict = new HashSet<>(wordDict);
    List<List<List<String>>> dp = new ArrayList<>();
    for (int i=0; i<=s.length(); i++) {
        dp.add(null);
    }
    dp.set(0, Arrays.asList(Arrays.asList("")));

    for (int i=1; i<=s.length(); i++) {
        for (int j=0; j<i; j++) {
            if (dp.get(j) != null && dict.contains(s.substring(j, i))) {
                if (dp.get(i) == null) {
                    dp.set(i, new ArrayList<>());
                }
                for (List<String> l : dp.get(j)) {
                    List<String> temp = new ArrayList<>(l);
                    temp.add(s.substring(j, i));
                    dp.get(i).add(temp);
                }
                // no breaking as we need all the previous results instead of just validity!
            }
        }
    }

    List<String> res = new ArrayList<>();
    if (dp.get(s.length()) == null) return res;

    for (List<String> l : dp.get(s.length())) {
        res.add(String.join(" ", l).substring(1));
    }
    return res;
}
```

## Q: All confusing (number rotated will have another meaning) numbers till ’N’

__Solution:__

DFS

* Move in all the branches by appending each confusing digit the current number (starting from 0).
* Keep updating global count if current number is confusing.

```java
Map<Integer, Integer> map = new HashMap<>();
int[] num = {0, 1, 6, 8, 9};
int res = 0;

public int confusingNumberII(int N) {
    map.put(0, 0);
    map.put(1, 1);
    map.put(6, 9);
    map.put(8, 8);
    map.put(9, 6);
    dfs(0, N);
    return res;
}

private void dfs(long start, int N){
    if(start > N){
        return;
    }
    if(start <= N && isConfused(start, map)){
        res++;
    }
    int i = start == 0 ? 1 : 0;
    for(; i < 5; i++){
        dfs(start * 10 + num[i], N);
    }
}

private boolean isConfused(long s, Map<Integer, Integer> map){
    long res = 0, x = s;
    while(x > 0){
        int i = (int) (x % 10);
        if(i == 2 || i == 3 || i == 4 || i == 5 || i == 7){
            return false;
        }
        long digit = map.get(i);
        res = res * 10 + digit;
        x = x / 10;
    }
    return res != s;
}
```

## Q: Balloon burst, burst “ith” and `coins_got = a[I] * a[I-1] * a[I+1]`

```java
public int maxCoins(int[] nums) {
    // reframe the problem
    int n = nums.length + 2;
    int[] new_nums = new int[n];

    for(int i = 0; i < nums.length; i++){
        new_nums[i+1] = nums[i];
    }

    new_nums[0] = new_nums[n - 1] = 1;

    // dp will store the results of our calls
    int[][] dp = new int[n][n];

    // iterate over dp and incrementally build up to dp[0][n-1]
    for (int left = n-2; left > -1; left--)
        for (int right = left+2; right < n; right++) {
            for (int i = left + 1; i < right; ++i)
                // same formula to get the best score from (left, right) as before
                dp[left][right] = Math.max(dp[left][right],
                new_nums[left] * new_nums[i] * new_nums[right] + dp[left][i] + dp[i][right]);
        }

    return dp[0][n - 1];
}
```

## Q: max “square”

__Solution:__

* If `cur == 1` :=> `dp[cur] = 1 + min(top, left, top left); else 0`.
* Keep cur max.
* Answer = `curmax ^ 2`.

## Q: Longest Palindromic substring

> Expand around centre(1 for odd and 2 for even)

```java
public String longestPalindrome(String s) {
    if (s == null || s.length() < 1) return "";
    int start = 0, end = 0;
    for (int i = 0; i < s.length(); i++) {
        int len1 = expandAroundCenter(s, i, i);
        int len2 = expandAroundCenter(s, i, i + 1);
        int len = Math.max(len1, len2);
        if (len > end - start) {
            start = i - (len - 1) / 2;
            end = i + len / 2;
        }
    }
    return s.substring(start, end + 1);
}

private int expandAroundCenter(String s, int left, int right) {
    int L = left, R = right;
    while (L >= 0 && R < s.length() && s.charAt(L) == s.charAt(R)) {
        L--;
        R++;
    }
    return R - L - 1;
}
```

## Q: Max rectangle in a matrix

> dp = histogram with column heights

```java
public int leetcode84(int[] heights) {
    Stack < Integer > stack = new Stack < > ();
    stack.push(-1);
    int maxarea = 0;
    for (int i = 0; i < heights.length; ++i) {
        while (stack.peek() != -1 && heights[stack.peek()] >= heights[i])
            maxarea = Math.max(maxarea, heights[stack.pop()] * (i - stack.peek() - 1));
        stack.push(i);
    }
    while (stack.peek() != -1)
        maxarea = Math.max(maxarea, heights[stack.pop()] * (heights.length - stack.peek() -1));
    return maxarea;
}

public int maximalRectangle(char[][] matrix) {

    if (matrix.length == 0) return 0;
    int maxarea = 0;
    int[] dp = new int[matrix[0].length];

    for(int i = 0; i < matrix.length; i++) {
        for(int j = 0; j < matrix[0].length; j++) {

            // keep track of the number of consecutive 1’s

            dp[j] = matrix[i][j] == '1' ? dp[j] + 1 : 0;
        }
        // update maxarea with the maximum area from this row's histogram
        maxarea = Math.max(maxarea, leetcode84(dp));
    } return maxarea;
}
```

## Q: Max area under histogram (array with bar heights given)

__Solution:__s

1. Brute force = n^3 (2 loops to find subarray and 3rd one to find min height).
2. Better brute force = n^2 by merging 2nd and min loop.
3. Optimised: stack based (better DnQ).

```java
public int calculateArea(int[] heights, int start, int end) {
    if (start > end)
        return 0;
    int minindex = start;
    for (int i = start; i <= end; i++)
        if (heights[minindex] > heights[i])
            minindex = i;
    return Math.max(heights[minindex] * (end - start + 1), Math.max(calculateArea(heights, start, minindex - 1), calculateArea(heights, minindex + 1, end)));
}
public int largestRectangleArea(int[] heights) {
    return calculateArea(heights, 0, heights.length - 1);
}
```

Most optimal:

```java
public int maximalRectangle(char[][] matrix) {
    if(matrix.length == 0) return 0;
    int m = matrix.length;
    int n = matrix[0].length;

    int[] left = new int[n]; // initialize left as the leftmost boundary possible
    int[] right = new int[n];
    int[] height = new int[n];

    Arrays.fill(right, n); // initialize right as the rightmost boundary possible

    int maxarea = 0;
    for(int i = 0; i < m; i++) {
        int cur_left = 0, cur_right = n;
        // update height
        for(int j = 0; j < n; j++) {
            if(matrix[i][j] == '1') height[j]++;
            else height[j] = 0;
        }
        // update left
        for(int j=0; j<n; j++) {
            if(matrix[i][j]=='1') left[j]=Math.max(left[j],cur_left);
            else {left[j]=0; cur_left=j+1;}
        }
        // update right
        for(int j = n - 1; j >= 0; j--) {
            if(matrix[i][j] == '1') right[j] = Math.min(right[j], cur_right);
            else {right[j] = n; cur_right = j;}
        }
        // update area
        for(int j = 0; j < n; j++) {
            maxarea = Math.max(maxarea, (right[j] - left[j]) * height[j]);
        }
    return maxarea;
}
```

## Q: Can str1 be transformed into another str2

```java
public boolean canConvert(String str1, String str2) {
    /*
    Scan s1 and s2 at the same time,
    record the transform mapping into a map/array.
    The same char should transform to the same char.
    Otherwise we can directly return false.

    To realise the transformation:

    transformation of link link ,like a -> b -> c:
    we do the transformation from end to begin, that is b->c then a->b

    transformation of cycle, like a -> b -> c -> a:
    in this case we need a tmp
    c->tmp, b->c a->b and tmp->a
    Same as the process of swap two variable.

    In both case, there should at least one character that is unused,
    to use it as the tmp for transformation.
    So we need to return if the size of set of unused characters < 26.


    Complexity
    Time O(N) for scanning input
    Space O(26) to record the mapping
    running time can be improved if count available character during the scan.
    */
    if (str1.equals(str2)) return true;

    Map<Character, Character> dp = new HashMap<>();
    for (int i=0; i<str1.length(); i++) {
        if (dp.getOrDefault(str1.charAt(i), str2.charAt(i)) != str2.charAt(i))
            return false;
        dp.put(str1.charAt(i), str2.charAt(i));
    }

    return new HashSet<>(dp.values()).size() < 26;
}
```

## Q: Check if hand has ‘W’ straight hands

```java
public boolean isNStraightHand(int[] hand, int W) {
    if (hand.length % W != 0) return false;

    TreeMap<Integer, Integer> map = new TreeMap<>();

    for (int card : hand) {
        map.put(card, map.getOrDefault(card, 0)+1);
    }

    while (!map.isEmpty()) {
        int first = map.firstKey();
        for (int i=first; i<first+W; i++) {
            if (!map.containsKey(i)) return false;
            int val = map.get(i);
            if (val == 1) map.remove(i);
            else map.replace(i, map.get(i)-1);
        }
    }
    return true;
}
```

## Q: Next lexicographical permutation

__Solution:__

1. Iterate from left.
2. Stop at first decreasing element => I.
3. Find element just larger than I by iterating to the right => J.
4. Swap I and J.
5. Reverse array from I till length.

## Q: Longest common subsequence“LCS”

```java
public int longestCommonSubsequence(String text1, String text2) {
    int[][] dp = new int[text1.length()+1][text2.length()+1];

    for (int i=1; i<dp.length; i++) {
        for (int j=1; j<dp[0].length; j++) {

        // if char matches
            if (text1.charAt(i-1) == text2.charAt(j-1)) {
                dp[i][j] = 1 + dp[i-1][j-1];

            } else {

                dp[i][j] = Math.max(
                    dp[i-1][j],
                    dp[i][j-1]
                );
            }
        }
    }

    return dp[dp.length-1][dp[0].length-1];
}
```

## Q: Find min window in “S” chose subsequence is “T”

> 2 pointers

```java
public String minWindow(String S, String T) {
    int i=-1;
    String res="";
    while (i<S.length()){
        for (char c: T.toCharArray()){
            i=S.indexOf(c, i+1);
            if (i==-1) return res;
        }
        int lastPlusOne=++i;
        for (int j=T.length()-1; j>-1; j--){
            i=S.lastIndexOf(T.charAt(j), i-1);
        }
        if (res=="" || res.length()>lastPlusOne-i) res=S.substring(i++, lastPlusOne);
    }
    return res;
}
```

## Q: No. Of subarrays having sum = K

__Solution:__ 1 - n^2, constant space (kinda brute-force)

```java
public int subarraySum(int[] nums, int k) {
    int count = 0;
    for (int start = 0; start < nums.length; start++) {
        int sum=0;
        for (int end = start; end < nums.length; end++) {
            sum+=nums[end];
            if (sum == k)
                count++;
        }
    }
    return count;
}
```

__Solution:__ 2 - O(N), N space

```java
public int subarraySum(int[] nums, int k) {
    int count = 0, sum = 0;
    HashMap < Integer, Integer > map = new HashMap < > ();
    map.put(0, 1);
    for (int i = 0; i < nums.length; i++) {
        sum += nums[i];
        if (map.containsKey(sum - k))
            count += map.get(sum - k);
        map.put(sum, map.getOrDefault(sum, 0) + 1);
    }
    return count;
}
```

## Q: Number of delete operations to make 2 strings equal

__Solution:__ 1 - DP with LCS

```java
public int minDistance(String s1, String s2) {
    int[][] dp = new int[s1.length() + 1][s2.length() + 1];
    for (int i = 0; i <= s1.length(); i++) {
        for (int j = 0; j <= s2.length(); j++) {
            if (i == 0 || j == 0)
                continue;
            if (s1.charAt(i - 1) == s2.charAt(j - 1))
                dp[i][j] = 1 + dp[i - 1][j - 1];
            else
                dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
        }
    }

    // actual formula => { dels = l1 + l2 - 2 * LCS }

    return s1.length() + s2.length() - 2 * dp[s1.length()][s2.length()]; /// actual formula
}
```

__Solution:__ 2 - DP without LCS

```java
public int minDistance(String word1, String word2) {
    int[][] dp = new int[word1.length() + 1][word2.length() + 1];
    for (int i = 0; i <= word1.length(); i++) {
        for (int j = 0; j <= word2.length(); j++) {
            if (i == 0 || j == 0)
                dp[i][j] = i + j; // check this! (Rest all looks like LCS)
            else if (word1.charAt(i - 1) == word2.charAt(j - 1))
                dp[i][j] = dp[i - 1][j - 1]; // diagonally previous result when matched
            else
                dp[i][j] = 1 + Math.min(dp[i - 1][j], dp[i][j - 1]); // min instead of max in LCS
        }
    }
    return dp[word1.length()][word2.length()];
}
```

## Q: Longest palindromic “subsequence”

__Solution:__ : LCS of self and reverse

## Q: Longest continuous subarray with “absolute difference” <= limit

### Soluton 1

```java
public int longestSubarray(int[] nums, int limit) {
    TreeMap<Integer, Integer> map = new TreeMap<>();
    int max = 0;

    int windowleft = 0;
    for (int windowright = 0; windowright < nums.length; windowright++) {
        map.put(nums[windowright], 1 + map.getOrDefault(nums[windowright], 0));

        if (map.lastKey() - map.firstKey() > limit) {
            map.put(nums[windowleft], map.get(nums[windowleft])-1);
            if (map.get(nums[windowleft]) == 0) {
                map.remove(nums[windowleft]);
            }
            windowleft++;
        } else {
            max = Math.max(windowright-windowleft+1, max);
        }
    }

    return max;
}
```

__Solution:__s 2

```java
public int longestSubarray(int[] nums, int limit) {
    Deque<Integer> maxd = new ArrayDeque<>();
    Deque<Integer> mind = new ArrayDeque<>();
    int i = 0, j;
    for (j = 0; j < nums.length; ++j) {
        while (!maxd.isEmpty() && nums[j] > maxd.peekLast()) maxd.pollLast();
        while (!mind.isEmpty() && nums[j] < mind.peekLast()) mind.pollLast();
        maxd.add(nums[j]);
        mind.add(nums[j]);
        if (maxd.peek() - mind.peek() > limit) {
            if (maxd.peek() == nums[i]) maxd.poll();
            if (mind.peek() == nums[i]) mind.poll();
            ++i;
        }
    }
    return j - i;
}
```

## Q: Longest increasing path in matrix

> DFS with memoization

```java
private static final int[][] dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
private int m, n;

public int longestIncreasingPath(int[][] matrix) {
    if (matrix.length == 0) return 0;
    m = matrix.length; n = matrix[0].length;
    int[][] cache = new int[m][n];
    int ans = 0;
    for (int i = 0; i < m; ++i)
        for (int j = 0; j < n; ++j)
            ans = Math.max(ans, dfs(matrix, i, j, cache));
    return ans;
}

private int dfs(int[][] matrix, int i, int j, int[][] cache) {
    if (cache[i][j] != 0) return cache[i][j];
    for (int[] d : dirs) {
        int x = i + d[0], y = j + d[1];
        if (0 <= x && x < m && 0 <= y && y < n && matrix[x][y] > matrix[i][j])
            cache[i][j] = Math.max(cache[i][j], dfs(matrix, x, y, cache));
    }
    return ++cache[i][j];
}
```

## Q: Sum or 2 non-overlapping subarray each having sum = target

> 2 pass + map

```java
public int minSumOfLengths(int[] arr, int target) {
    int lsize = Integer.MAX_VALUE; // left subarray size
    int result = Integer.MAX_VALUE; // right subarray size + lsize
    Map<Integer, Integer> sumIndex = new HashMap<>();

    int sum = 0;
    sumIndex.put(0, -1); // very important!

    for (int i=0; i<arr.length; i++) {
        sum += arr[i];
        sumIndex.put(sum, i);
    }

    sum = 0;
    for (int i=0; i<arr.length; i++) {
        sum += arr[i];
        if (sumIndex.containsKey(sum-target)) {
            lsize = Math.min(
                lsize,
                i-sumIndex.get(sum-target)
            );
        }

        if (lsize < Integer.MAX_VALUE && sumIndex.containsKey(sum+target)) {
            result = Math.min(
                result,
                sumIndex.get(sum+target)-i+lsize
            );
        }
    }

    return (result == Integer.MAX_VALUE) ? -1 : result;
}
```
