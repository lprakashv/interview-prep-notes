# Bitwise Tricks

Test kth bit:

```java
s & (1 << k)
```

Set kth bit:

```java
s |= (1 << k)
```

Turn off kth bit:

```java
s &= ~(1 << k)
```

Toggle kth bit:

```java
s ^= (1 << k)
```

Multiple by 2n:

```java
s << n
```

Divide by 2n:

```java
s >> n
```

Intersection:

```java
s & t
```

Union:

```java
s | t
```

Set Subtraction:

```java
s & ~t
```

Extract lowest set bit:

```java
s & (-s)
```

Extract lowest unset bit:

```java
~s & (s + 1)
```

Swap Values:

```java
x ^= y; y ^= x; x ^= y
```
