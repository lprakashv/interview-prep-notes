# Java Snippets

```java
Arrays.fill(array, value);

map.getOrDefault(key, defaultVal);

map.getOrDefault(key, defaultVal);
map.putIfAbsent(key, value);

//smallest
treeMap.firstEntry();
// greatest key less than ‘k’
treeMap.floorKey(int k);
// greatest entry with key less than ‘k’
treeMap.floorEntry(int k);

/** Similar to tree map => tree set: */
// smallest
treeSet.first();
// greatest element less than ‘k’
treeSet.floor(int k);
// smallest element with key greater than ‘k’
treeSet.ceiling(int k);


PriorityQueue minHeap = new PriorityQueue<>();
PriorityQueue maxHeap = new PriorityQueue<>(Comparator.reverseOrder());

/** Copy array: */
System.arraycopy(
 int[]    source_array,
 int      start,
 int[]    dest_array,
 int      start2,
 int      end
);
```
