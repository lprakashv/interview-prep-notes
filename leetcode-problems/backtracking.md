# Leetcode Notable Backtracking problems

## Q: max jumps from an array indices given, find if traversal possible or not

- Backtracking (2^n, n)
- DP (n^2, n)
- Greedy (n, 1)

[solution](https://leetcode.com/problems/jump-game/solution/)

## Q: Phone number combinations

### Leetcode solution

```java
  Map<String, String> phone = new HashMap<String, String>() {{
    put("2", "abc");
    put("3", "def");
    put("4", "ghi");
    put("5", "jkl");
    put("6", "mno");
    put("7", "pqrs");
    put("8", "tuv");
    put("9", "wxyz");
  }};

  List<String> output = new ArrayList<String>();

  public void backtrack(String combination, String next_digits) {
    // if there is no more digits to check
    if (next_digits.length() == 0) {
      // the combination is done
      output.add(combination);
    }
    // if there are still digits to check
    else {
      // iterate over all letters which map
      // the next available digit
      String digit = next_digits.substring(0, 1);
      String letters = phone.get(digit);
      for (int i = 0; i < letters.length(); i++) {
        String letter = phone.get(digit).substring(i, i + 1);
        // append the current letter to the combination
        // and proceed to the next digits
        backtrack(combination + letter, next_digits.substring(1));
      }
    }
  }

  public List<String> letterCombinations(String digits) {
    if (digits.length() != 0)
      backtrack("", digits);
    return output;
  }
```

### My solution

```java
public List<String> letterCombinations(String digits) {
    if (digits == null || digits.length() == 0) return new ArrayList<>();

    Map<Character, List<Character>> map = new HashMap<>();

    map.put('2', Arrays.asList('a', 'b', 'c'));
    map.put('3', Arrays.asList('d', 'e', 'f'));
    map.put('4', Arrays.asList('g', 'h', 'i'));
    map.put('5', Arrays.asList('j', 'k', 'l'));
    map.put('6', Arrays.asList('m', 'n', 'o'));
    map.put('7', Arrays.asList('p', 'q', 'r', 's'));
    map.put('8', Arrays.asList('t', 'u', 'v'));
    map.put('9', Arrays.asList('w', 'x', 'y', 'z'));

    List<String> res = Arrays.asList("");

    for (char sc: digits.toCharArray()) {
        List<String> localList = new ArrayList<>();
        for (String s: res) {
            for (char c : map.get(sc)) {
                localList.add(s + c);
            }
        }
        res = localList;
    }

    return res;
}
```

## Q: Generate parenthesis, given ’n’

```java
public List<String> generateParenthesis(int n) {
    List<String> ans = new ArrayList();
    if (n == 0) {
        ans.add("");
    } else {
        for (int c = 0; c < n; ++c)
            for (String left: generateParenthesis(c))
                for (String right: generateParenthesis(n-1-c))
                    ans.add("(" + left + ")" + right);
    }
    return ans;
}
```

## Q: Permutations of an array

```java
  public void backtrack(int n,
                        ArrayList<Integer> nums,
                        List<List<Integer>> output,
                        int first) {
    // if all integers are used up
    if (first == n)
      output.add(new ArrayList<Integer>(nums));
    for (int i = first; i < n; i++) {
      // place i-th integer first
      // in the current permutation
      Collections.swap(nums, first, i);
      // use next integers to complete the permutations
      backtrack(n, nums, output, first + 1);
      // backtrack
      Collections.swap(nums, first, i);
    }
  }

  public List<List<Integer>> permute(int[] nums) {
    // init output list
    List<List<Integer>> output = new LinkedList();

    // convert nums into list since the output is a list of lists
    ArrayList<Integer> nums_lst = new ArrayList<Integer>();
    for (int num : nums)
      nums_lst.add(num);

    int n = nums.length;
    backtrack(n, nums_lst, output, 0);
    return output;
  }
```

## Q: Word search in matrix (any snaky)

> backtracking / DFS with reset visited on mismatch

```java
private boolean util(char[][] board, boolean[][] visited, int i, int j, String word, int wi) {
    if (wi >= word.length()) return true;

    if (i < 0 || i >= board.length || j < 0 || j >= board[0].length
    || visited[i][j]
    || word.charAt(wi) != board[i][j]) return false;

    visited[i][j] = true;

    if (util(board, visited, i-1, j, word, wi+1)
        || util(board, visited, i, j-1, word, wi+1)
        || util(board, visited, i+1, j, word, wi+1)
        || util(board, visited, i, j+1, word, wi+1)) return true;

    visited[i][j] = false; // reset for backtracking
    return false;
}

public boolean exist(char[][] board, String word) {
    if (board == null || board.length == 0 || board[0].length == 0) return false;
    if (word == null || word.length() == 0) return true;

    int l = board.length;
    int w = board[0].length;

    for (int i=0; i<l; i++) {
        for (int j=0; j<w; j++) {
            if (board[i][j] == word.charAt(0) && util(board, new boolean[l][w], i, j, word, 0)) {
                return true;
            }
        }
    }

    return false;
}
```

## Q: Palindrome partitioning

```java
public List<List<String>> partition(String s) {
   List<List<String>> list = new ArrayList<>();
   backtrack(list, new ArrayList<>(), s, 0);
   return list;
}

public void backtrack(List<List<String>> list, List<String> tempList, String s, int start){
   if(start == s.length())
      list.add(new ArrayList<>(tempList));
   else{
      for(int i = start; i < s.length(); i++){
         if(isPalindrome(s, start, i)){
            tempList.add(s.substring(start, i + 1));
            backtrack(list, tempList, s, i + 1);
            tempList.remove(tempList.size() - 1);
         }
      }
   }
}

public boolean isPalindrome(String s, int low, int high){
   while(low < high)
      if(s.charAt(low++) != s.charAt(high--)) return false;
   return true;
}
```

## Q: Robot clean the room

Given functions:

1. `boolean move()`
2. `void clean()`
3. `void turnRight()`
4. `void turnLeft()`

### My solution

```java
// private fields:

Robot robot;
int[][] deltas = new int[][]{{1, 0}, {0, 1}, {-1, 0}, {0, -1}}; //clockwise deltas to be added in x,y
Map<Integer, Set<Integer>> visited = new HashMap<>();

// utility methods:

boolean visited(int x, int y) {
    return visited.containsKey(x) && visited.get(x).contains(y);
}

void visit(int x, int y) {
    visited.putIfAbsent(x, new HashSet<>());
    visited.get(x).add(y);
}

void goBackAndTurnForward() {
    robot.turnRight();
    robot.turnRight();
    robot.move();
    robot.turnRight();
    robot.turnRight();
}

// main backtracking method:

void backTrack(int x, int y, int dir) {
    visit(x, y);
    robot.clean();

    for (int i=0; i<4; i++) {
        int newDir = (dir + i) % 4;
        int newX = x + deltas[newDir][0]; //next spot calculation
        int newY = y + deltas[newDir][1];

        if (!visited(newX, newY) && robot.move()) {
          //if next spot is not visited and can move there
            backTrack(newX, newY, newDir);
            goBackAndTurnForward(); //backtracking
        }

        robot.turnRight(); // next direction clockwise
    }
}

public void cleanRoom(Robot robot) {
    this.robot = robot;
    backTrack(0, 0, 0);
}
```
