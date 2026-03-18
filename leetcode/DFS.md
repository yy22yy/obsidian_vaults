深度优先搜索
广度优先搜索：[[BFS]]
[[回溯法]]




```cpp
vector<int> path;
bool used[100];

void dfs(int depth){

    if(depth == n){
        输出path
        return;
    }

    for(int i=0;i<n;i++){

        if(used[i]) continue;

        used[i] = true;
        path.push_back(a[i]);

        dfs(depth+1);

        path.pop_back();
        used[i] = false;
    }
}
```