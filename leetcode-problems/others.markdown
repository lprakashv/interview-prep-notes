# Leetcode other problems

## Q: Implement insert, remove and getRandom in O(1) - RandomizedSet

__Solution:__

- HashMap and ArrayList
- ArrayList
  - To calculate random in O(1).
  - Remove : copy last item to the index of the removed item and reduce ArrayList size.
- HashMap
  - Item --> Index (in array)

```java
class RandomizedSet {
  Map<Integer, Integer> dict;
  List<Integer> list;
  Random rand = new Random();

  /__ Initialize your data structure here. */
  public RandomizedSet() {
    dict = new HashMap();
    list = new ArrayList();
  }

  /__ Inserts a value to the set. Returns true if the set did not already contain the specified element. */
  public boolean insert(int val) {
    if (dict.containsKey(val)) return false;

    dict.put(val, list.size());
    list.add(list.size(), val);
    return true;
  }

  /__ Removes a value from the set. Returns true if the set contained the specified element. */
  public boolean remove(int val) {
    if (! dict.containsKey(val)) return false;

    // move the last element to the place idx of the element to delete
    int lastElement = list.get(list.size() - 1);
    int idx = dict.get(val);
    list.set(idx, lastElement);
    dict.put(lastElement, idx);
    // delete the last element
    list.remove(list.size() - 1);
    dict.remove(val);
    return true;
  }

  /__ Get a random element from the set. */
  public int getRandom() {
    return list.get(rand.nextInt(list.size()));
  }
}
```

## Q: Find smallest divisor, given a threasold

> Input: nums = [1,2,5,9], threshold = 6
Output: 5
Explanation: We can get a sum to 17 (1+2+5+9) if the divisor is 1.
If the divisor is 4 we can get a sum to 7 (1+1+2+3) and if the divisor is 5 the sum will be 5 (1+1+1+2).

__Solution:__

> Binary Search

```java
public int smallestDivisor(int[] A, int threshold) {
    int left = 1, right = (int)1e6;
    while (left < right) {
        int m = (left + right) / 2, sum = 0;
        for (int i : A) {
            sum += (i + m - 1) / m;
        }
        if (sum > threshold) {
            left = m + 1;
        } else {
            right = m;
        }
    }
    return left;
}
```

## Q: Decode string

__Solution:__ 1 - recursion

```java
public String decodeString(String s) {
    Deque<Character> queue = new LinkedList<>();
    for (char c : s.toCharArray()) queue.offer(c);
    return helper(queue);
}

public String helper(Deque<Character> queue) {
    StringBuilder sb = new StringBuilder();
    int num = 0;
    while (!queue.isEmpty()) {
        char c= queue.poll();
        if (Character.isDigit(c)) {
            num = num * 10 + c - '0';
        } else if (c == '[') {
            String sub = helper(queue);
            for (int i = 0; i < num; i++) sb.append(sub);
            num = 0;
        } else if (c == ']') {
            break;
        } else {
            sb.append(c);
        }
    }
    return sb.toString();
}
```

__Solution:__ 2 - using Stack

```java
public String decodeString(String s) {
    Stack<Character> stack = new Stack<>();
    for (int i = 0; i < s.length(); i++) {
        if (s.charAt(i) == ']') {
            List<Character> decodedString = new ArrayList<>();
            // get the encoded string
            while (stack.peek() != '[') {
                decodedString.add(stack.pop());
            }
            // pop [ from the stack
            stack.pop();
            int base = 1;
            int k = 0;
            // get the number k
            while (!stack.isEmpty() && Character.isDigit(stack.peek())) {
                k = k + (stack.pop() - '0') * base;
                base *= 10;
            }
            // decode k[decodedString], by pushing decodedString k times into stack
            while (k != 0) {
                for (int j = decodedString.size() - 1; j >= 0; j--) {
                    stack.push(decodedString.get(j));
                }
                k--;
            }
        } else {
            // push the current character to stack
            stack.push(s.charAt(i));
        }
    }
    // get the result from stack
    char[] result = new char[stack.size()];
    for (int i = result.length - 1; i >= 0; i--) {
        result[i] = stack.pop();
    }
    return new String(result);
}
```

## Q: Can array be splitted into consecutive arrays (with length >= 3)

Given an array nums sorted in ascending order, return true if and only if you can split it into 1 or more subsequences such that each subsequence consists of consecutive integers and has length at least 3.

Example 1:

>Input: [1,2,3,3,4,4,5,5]
Output: True
Explanation:
You can split them into two consecutive subsequences :
1, 2, 3, 4, 5
3, 4, 5

Example 2:

>Input: [1,2,3,4,4,5]
Output: False

__Solution:__

```java
public boolean isPossible(int[] nums) {
    Map<Integer,Integer> possibility = new HashMap<>();
    Map<Integer,Integer> counts = new HashMap<>();
    for(int num:nums){
        counts.put(num,counts.getOrDefault(num,0)+1);
    }
    for(int num:nums){
        if(counts.get(num)==0)continue;
        if(possibility.getOrDefault(num,0)>0){
            possibility.put(num,possibility.getOrDefault(num,0)-1);
            possibility.put(num+1,possibility.getOrDefault(num+1,0)+1);
        }
        else if( counts.getOrDefault(num+1,0)>0 && counts.getOrDefault(num+2,0)>0 ){
            possibility.put(num+3,possibility.getOrDefault(num+3,0)+1);
            counts.put(num+1,counts.getOrDefault(num+1,0)-1);
            counts.put(num+2,counts.getOrDefault(num+2,0)-1);
        }
        else{
            return false;
        }
        counts.put(num,counts.get(num)-1);
    }
    return true;
}
```

## Q: LISP Parser

Expressions allowed:

- `(add e1 e2)`
- `(mult e1 e2)`
- `(let k1 e1 k2 e2 .. kn en expr)`

__My Solution:__

```java
private int evContext(String expression, Map<String, String> context) {
    if (expression.startsWith("(")) {
        String[] splitted = expression.substring(1, expression.length()-1).split(" ");

        List<String> terms = new ArrayList<>();
        terms.add(splitted[0]);

        StringBuilder sb = new StringBuilder();
        int indent = 0;
        for (int i=1; i<splitted.length; i++) {
            sb.append(" ");
            sb.append(splitted[i]);
            if (splitted[i].startsWith("(")) {
                indent++;
            }
            if (splitted[i].contains(")")) {
                indent -= (splitted[i].length() - splitted[i].indexOf(")"));
            }
            if (indent == 0) {
                terms.add(sb.toString().substring(1));
                sb = new StringBuilder();
            }
        }
        int l = terms.size();

        if (terms.get(0).equals("add")) {
            return evContext(terms.get(1), context) + evContext(terms.get(2), context);
        }
        if (terms.get(0).equals("mult")) {
            return evContext(terms.get(1), context) * evContext(terms.get(2), context);
        }
        if (terms.get(0).equals("let")) {
            Map<String, String> map = new HashMap<>(context);
            for (int i=1; i<l-1; i+= 2) {
                map.put(terms.get(i), String.valueOf(evContext(terms.get(i+1), map)));
            }
            return evContext(terms.get(l-1), map);
        }
    }

    return Integer.parseInt(
        context.getOrDefault(expression, expression)
    );
}

public int evaluate(String expression) {
    return evContext(expression, new HashMap<>());
}
```

## Q: Median of integer stream

__Solution:__

Heap

`if x < minHeap.peek()` => `maxHeap.offer(x)`
`if maxHeap.size > minHeap.size + 1` => `minHeap.offer(maxHeap.poll())`

`if x > maxHeap.peek()` => `minHeap.offer(x)`
`If minHeap.size > maxHeap.size + 1` => `maxHeap.offer(minHeap.poll())`

```java
class MedianOfIntegerStream {

    private Queue<Integer> minHeap, maxHeap;

    MedianOfIntegerStream() {
        minHeap = new PriorityQueue<>();
        maxHeap = new PriorityQueue<>(Comparator.reverseOrder());
    }

    void add(int num) {
        if (!minHeap.isEmpty() && num < minHeap.peek()) {
            maxHeap.offer(num);
            if (maxHeap.size() > minHeap.size() + 1) {
                minHeap.offer(maxHeap.poll());
            }
        } else {
            minHeap.offer(num);
            if (minHeap.size() > maxHeap.size() + 1) {
                maxHeap.offer(minHeap.poll());
            }
        }
    }

    double getMedian() {
        int median;
        if (minHeap.size() < maxHeap.size()) {
            median = maxHeap.peek();
        } else if (minHeap.size() > maxHeap.size()) {
            median = minHeap.peek();
        } else {
            median = (minHeap.peek() + maxHeap.peek()) / 2;
        }
        return median;
    }
}
```

## LRU Cache

__Solution:__

__Extending LinkedHashMap:__

- `super(capacity, 0.75F, true);`
- `get(int key);`
- `put(int key, int value);`
- `@Override protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest);`

__“HashMap + DoublyLinkedList”:__

- Create a DLLnode with add, remove, popTail methods.
- Create a cache map with entry = ( key -> DLLnode ).
- Keep a tail and head as DLLnodes.
- If size > capacity: tail = popTail; cache.remove(tail.key).

## Q: Min intervals need to be removed to make non-overlapping intervals

__Solution__ - Greedy with sorting

```java
class MyComparator implements Comparator<int[]> {
    public int compare(int[] a, int[] b) {
        return a[1] - b[1];
    }
}

public int eraseOverlapIntervals(int[][] intervals) {
    if (intervals.length == 0) {
        return 0;
    }

    Arrays.sort(intervals, new MyComparator());
    int end = intervals[0][1], prev = 0, count = 0;

    for (int i = 1; i < intervals.length; i++) {
        if (intervals[prev][1] > intervals[i][0]) {
            if (intervals[prev][1] > intervals[i][1]) {
                prev = i;
            }
            count++;
        } else {
            prev = i;
        }
    }
    return count;
}
```
