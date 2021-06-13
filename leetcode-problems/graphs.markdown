# Leetcode notable Graph problems

## Q: Sortest Path in Binary Matrix (0 for open 1 for blocked) with 8 direction movement

__Solution 1__ - BFS

```java
private int dir[][] = new int[][]{{0,1},{0,-1},{1,0},{-1,0},{1,-1},{-1,1},{-1,-1},{1,1}};

public int shortestPathBinaryMatrix(int[][] grid) {

    int m = grid.length;
    int n = grid[0].length;

    if(grid[0][0]==1 || grid[m-1][n-1]==1) {
        return -1;
    }

    boolean[][] visited = new boolean[m][n];
    visited[0][0] = true;

    Queue<int[]> queue = new LinkedList<>();
    queue.add(new int[]{0,0});

    int length=0;

    while (!queue.isEmpty()) {
        // important!
        int size = queue.size();

        for(int i=0;i<size;i++) {
            int[] pop = queue.poll();

            if(pop[0]==m-1 && pop[1]==n-1) {
                return length+1;
            }

            for (int k=0;k<8;k++) {
                int nextX = dir[k][0]+pop[0];
                int nextY = dir[k][1]+pop[1];

                if(nextX>=0 && nextX<m && nextY>=0 && nextY<n && !visited[nextX][nextY] && grid[nextX][nextY]==0) {
                    queue.add(new int[]{nextX,nextY});
                    visited[nextX][nextY]=true;
                }
            }
        }
        length++;
    }
    return -1;
}
```

__Why does DFS not work?__

person 1:
> Unfortunately, using dfs you'd have to try every possible path to the end.
__You'd have to mark a cell as unvisited after recurring the neighbors (after the for-loop)__. But doing this in this problem would lead to __TLE__.
For this reason, BFS is the best choice here.

person 2:
> __whenever you see 'shortest' in path problem, dfs should be out of consideration__. Think about what djkstra's algo solves and remind yourself whether it is bfs or dfs.

## Q: Path with maximum minimum value

Given a matrix of integers A with R rows and C columns, find the maximum score of a path starting at [0,0] and ending at [R-1,C-1].

The score of a path is the minimum value in that path.  For example, the value of the path 8 →  4 →  5 →  9 is 4.

A path moves some number of times from one visited cell to any neighbouring unvisited cell in one of the 4 cardinal directions (north, east, west, south).

![Example 1](./images/graphmaxminpath1.jpeg) -- ![Example 2](./images/graphmaxminpath2.jpeg) -- ![Example 3](./images/graphmaxminpath3.jpeg)

__Solution 1__ - BFS + PQ

```java
public int maximumMinimumPath(int[][] A) {
    // x, y, min
    PriorityQueue<int[]> pq = new PriorityQueue<>((a, b)->(b[2] - a[2]));

    int n = A.length;
    int m = A[0].length;

    int[][] vv = new int[n][m];
    for (int[] v : vv) {
        Arrays.fill(v, -1);
    }

    pq.add(new int[]{0, 0, A[0][0]});
    vv[0][0] = A[0][0];

    int[] dx = {0, 0, 1, -1};
    int[] dy = {1, -1, 0, 0};

    while (!pq.isEmpty()) {
        int[] p = pq.poll();

        for (int i = 0; i < 4; i++) {
            int nx = p[0] + dx[i];
            int ny = p[1] + dy[i];

            if (nx < 0 || ny < 0 || nx >= n || ny >= m || vv[nx][ny] != -1) {
                continue;
            }

            vv[nx][ny] = Math.min(p[2], A[nx][ny]);

            if (nx == n - 1 && ny == m - 1) {
                return vv[nx][ny];
            }

            pq.add(new int[]{nx, ny, vv[nx][ny]});
        }
    }
    return -1;
}
```

__Solution 2__ - Binary search

```java
private int binarySearch(int[][] A) {
    int min = 0;
    int max = Math.min(A[0][0], A[m - 1][n - 1]);
    int ans = 0;
    while (max - min > 1) {
        int mid = (min + max) / 2;
        boolean[][] visited = new boolean[m][n];
        visited[0][0] = true;
        if (hasPath(A, 0, 0, visited, mid)) {
            min = mid;
            ans = mid;
        } else {
            max = mid;
        }
    }
    boolean[][] visited = new boolean[m][n];
    visited[0][0] = true;
    if (hasPath(A, 0, 0, visited, max)) ans = max;
    return ans;
}

// DFS
private boolean hasPath(int[][] A, int r, int c, boolean[][] visited, int limit) {
    visited[r][c] = true;
    if (r == m - 1 && c == n - 1) return true;
    for (int i = 0; i < 4; i++) {
        int nr = r + dr[i];
        int nc = c + dc[i];
        if (nr >= 0 && nr < m && nc >= 0 && nc < n && !visited[nr][nc] && A[nr][nc] >= limit) {
            if (hasPath(A, nr, nc, visited, limit)) return true;
        }
    }
    return false;
}
```
