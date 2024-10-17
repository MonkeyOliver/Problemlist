# 994. Rotting Oranges

[Link](https://leetcode.com/problems/rotting-oranges/)

## Solution

裸BFS，先枚举一下将所有烂橘子入队。

## Code

    class Solution {
    public:
        int dx[4] = {0, 1, 0, -1};
        int dy[4] = {1, 0, -1, 0};

        int bfs(vector<vector<int>>& grid)
        {
            int maxd = 0;
            queue<tuple<int, int, int>> q;
            for (int i = 0; i < grid.size(); i++) {
                for (int j = 0; j < grid[i].size(); j++) {
                    if (grid[i][j] == 2) q.push({i, j, 0});
                }
            }
            while (!q.empty()) {
                tuple<int, int, int> u = q.front();
                q.pop();
                for (int i = 0; i < 4; i++) {
                    int tx = std::get<0>(u) + dx[i];
                    int ty = std::get<1>(u) + dy[i];
                    int d = std::get<2>(u) + 1;
                    if (tx >= 0 && ty >= 0 && tx < grid.size() &&
                        ty < grid[0].size()) {
                        if (grid[tx][ty] == 1) {
                            grid[tx][ty]++;
                            maxd = max(maxd, d);
                            q.push({tx, ty, d});
                        }
                    }
                }
            }
            for (int i = 0; i < grid.size(); i++) {
                for (int j = 0; j < grid[i].size(); j++) {
                    if (grid[i][j] == 1) return -1;
                }
            }
            return maxd;
        }

        int orangesRotting(vector<vector<int>>& grid) { return bfs(grid); }
    };
