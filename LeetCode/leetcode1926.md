# 1926. Nearest Exit from Entrance in Maze

[Link](https://leetcode.com/problems/nearest-exit-from-entrance-in-maze)

## Solution

裸BFS。注意边界判断

## Code

    class Solution {
    public:
        int dx[4] = {0, 1, 0, -1};
        int dy[4] = {1, 0, -1, 0};

        int vis[110][110];

        int bfs(vector<vector<char>>& maze, tuple<int, int, int> s, int m, int n)
        {
            memset(vis, 0, sizeof(vis));
            vis[std::get<0>(s)][std::get<1>(s)] = 1;
            queue<tuple<int, int, int>> q;
            q.push(s);
            while (!q.empty()) {
                tuple<int, int, int> u = q.front();
                q.pop();
                for (int i = 0; i < 4; i++) {
                    int tx = std::get<0>(u) + dx[i];
                    int ty = std::get<1>(u) + dy[i];
                    int d = std::get<2>(u) + 1;
                    if (tx >= 0 && ty >= 0 && tx < n && ty < m &&
                        ((tx == 0 && ty < m) || (ty == 0 && tx < n) ||
                        (tx == n - 1 && ty >= 0) || (tx >= 0 && ty == m - 1))) {
                        if (!vis[tx][ty] && maze[tx][ty] == '.') return d;
                    }
                    else {
                        if (tx >= 0 && ty >= 0 && tx < n && ty < m)
                            if (!vis[tx][ty] && maze[tx][ty] == '.') {
                                vis[tx][ty] = 1;
                                q.push({tx, ty, d});
                            }
                    }
                }
            }
            return -1;
        }

        int nearestExit(vector<vector<char>>& maze, vector<int>& entrance)
        {
            tuple<int, int, int> s{entrance[0], entrance[1], 0};
            return bfs(maze, s, maze[0].size(), maze.size());
        }
    };
