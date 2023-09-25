```
改为toc加入目录
```
# 1. 动态规划

## 1.1 背包

### 01背包

![[Attachments/Images/Pasted image 20230323154556.png]]
![[Attachments/Images/Pasted image 20230323154524.png]]
![[Attachments/Images/Pasted image 20230323154649.png]]

```c
for (int i = 1; i <= n; i++) {//空间压缩
	for (int j = m; j >= w[i]; j--) {//从后往前枚举
		f[j] = max(f[j], f[j - w[i]] + v[i]);//在选和不选中寻找最大值
	}
}
cout << f[m];//f[m]就是体积m时的最大价值
```

### 完全背包

![[Attachments/Images/Pasted image 20230323154801.png]]
![[Attachments/Images/Pasted image 20230323154821.png]]
![[Attachments/Images/Pasted image 20230323154851.png]]

```c
for (int i = 1; i <= n; i++) {
	for (int j = w[i]; j <= m; j++) {
		f[j] = max(f[j], f[j - w[i]] + v[i]);
	}
}
cout << f[m] <<endl;
```

### 多重背包

>题目描述:
>有n种物品和一个容量为m的背包。第i种物品最多有s[i]件可用，每件费用是v[i]，价值是w[i]。求解将哪些物品装入背包可使这些物品的费用总和不超过背包容量，且价值总和最大

#### 二进制优化

![[Attachments/Images/Pasted image 20230323154954.png]]
![[Attachments/Images/Pasted image 20230323155414.png]]

```c
for (int i = 1; i <= n; i++) {
	cin >> v >> w >> s;
	for (int j = 1; j <= s; j <<= 1) {//每件物品的二进制拆分
		vv[num] = j * v;
		ww[num++] = j * w;
		s -= j;
	}
	if (s) {//如果还有剩余,那么把剩下的放进去
		vv[num] = s * v;
		ww[num++] = s * w;
	}
}
for (int i = 1; i < num; i++) {//一共有num-1件物品
	for (int j = m; j >= vv[i]; j--) {//01背包的做法
		f[j] = max(f[j], f[j - vv[i]] + ww[i]);
	}
}
cout << f[m];
```

#### 单调队列优化

![[Attachments/Images/Pasted image 20230305100340.png]]
![[Attachments/Images/Pasted image 20230323155611.png]]

```c
int f[200], g[200], q[200];  

int n, m;  
cin >> n >> m;  
int v, w, s;  

for (int i = 1; i <= n; i++) {  
	cin >> v >> w >> s;  
	memcpy(g, f, sizeof(f));  
	//准备一个备份数组,因为多重背包需要我们从后往前更新,但是单调队列需要我们从前往后更新  
	for (int j = 0; j < v; j++) {  
		int h = 0, t = -1;  
		for (int k = j; k <= m; k += v) {  
			if (h <= t && k - q[h] > s * v) {
				h++;  
			}
			if (h <= t) {
				f[k] = max(g[k], g[q[h]] + (k - q[h]) / v * w);  
			}  
			while (h <= t && g[k] >= g[q[t]] + (k - q[t]) / v * w) {  
				t--;  
			}  
			q[++t] = k;  
		}  
	}  
}  
cout << f[m];  

```

### 混合背包

![[Attachments/Images/Pasted image 20230323155704.png]]
![[Attachments/Images/Pasted image 20230323155730.png]]

```c
int a[100], b[100], c[100], f[100];  
   
int num = 1; 

for (int i = 1; i <= n; i++) {
	cin >> v >> w >> s;  
	if (s == 0) {//完全背包,递推时特殊处理
		a[num] = v;  
		b[num] = w;  
		c[num++] = 0;  
	} else {
		if (s == -1) {//如果是01背包,那么只有一件,转化为多重背包
			s = 1;  
		}
		int k = 1;  
		while (s >= k) {//将完全背包转化为01背包
			a[num] = k * v;  
			b[num] = k * w;  
			c[num++] = 1;  
			s -= k;  
			k <<= 1;  
		}  
		if (s) {
			a[num] = s * v;  
			b[num] = s * w;  
			c[num++] = 1;  
		}  
	}  
}  
for (int i = 1; i < num; i++) {  
	if (c[i] == 1) { //如果是01背包
		for (int j = m; j >= a[i]; j--) { 
			f[j] = max(f[j], f[j - a[i]] + b[i]);  
		}  
	} else {  
		for (int j = a[i]; j <= m; j++) {  //完全背包
			f[j] = max(f[j], f[j - a[i]] + b[i]);  
		}  
	}  
}  
cout << f[m] << endl;  

```

### 二维费用背包

![[Attachments/Images/Pasted image 20230316165921.png]]
![[Attachments/Images/Pasted image 20230323155816.png]]

```c  
for (int i = 1; i <= n; i++) {  
	cin >> v >> m >> w;  
	for (int j = V; j >= v; j--) {//二维01背包
		for (int k = M; k >= m; k--) {
			f[j][k] = max(f[j][k], f[j - v][k - m] + w);  
		}  
	}  
}  
cout << f[V][M]; 
```

### 分组背包

![[Attachments/Images/Pasted image 20230316170303.png]]
![[Attachments/Images/Pasted image 20230323160854.png]]


```c
for (int i = 1; i <= n; i++) {  
	int s;  
	cin >> s;  
	for (int j = 1; j <= s; j++) {  
		cin >> v[j] >> w[j];  
	}  
	for (int j = m; j >= 1; j--) {  //体积
		for (int k = 0; k <= s; k++) {  //选哪件物品,第0件代表不选,第0件物品体积为0
			if (j >= v[k]) {  
				f[j] = max(f[j], f[j - v[k]] + w[k]);  
			}  
		}  
	}  
}  
cout << f[m] << endl;  
```

### 有依赖的背包

![[Attachments/Images/Pasted image 20230316171307.png]]
![[Attachments/Images/Pasted image 20230323161110.png]]

```c
int v[100], w[100], root, a[100][100], b[100], f[100][100];  
int n, m;  

void dfs(int u) {  
    for (int i = v[u]; i <= m; i++) {  
        f[u][i] = w[u];  
    }  
    for (int i = 0; i < b[u]; i++) {  
        int s = a[u][i];  
        dfs(s);  
        for (int j = m; j >= v[u]; j--) {//在选了父节点的前提下,把子节点当做一个分组背包 
            for (int k = 0; k <= j - v[u]; k++) {//分组背包根据数量选,这里则是根据分配给s这个子节点的容量选 
                f[u][j] = max(f[u][j], f[u][j - k] + f[s][k]);  
            }  
        }  
    }  
}  
for (int i = 1; i <= n; i++) {  
	int p;  
	cin >> v[i] >> w[i] >> p;  
	if (p == -1) {  
		root = i;  
	} else {  
		a[p][b[p]++] = i;  //a存的是p的孩子,b存的是孩子编号
	}  
}  
dfs(root);  
cout << f[root][m];  

```

### 背包问题求方案数

![[Attachments/Images/Pasted image 20230323161217.png]]
![[Attachments/Images/Pasted image 20230323161311.png]]

>如何理解这个恰好装满背包的初始化?
>在更新时只有通过f[0],c[0]直接或者间接转移的才能装满背包,因为在递推之前应该出现过填满背包的值

```c
int mod = 1e9 + 7;

for (int i = 0; i <= m; i++) {//不装物品也是一种方案
	c[i] = 1;
	f[i] = 0;
}
for (int i = 1; i <= n; i++) {
	cin >> v >> w;
	for (int j = m; j >= v; j--) {
		if (f[j - v] + w > f[j]) {//只是多装入一件物品,没有改变方案数
			f[j] = f[j - v] + w;
			c[j] = c[j - v];
		} else if (f[j - v] + w == f[j]) {
			c[j] = (c[j] + c[j - v]) % mod;//新增了方案数
		}
	}
}
cout << c[m];
```

### 背包问题求具体方案

![[Attachments/Images/Pasted image 20230323161405.png]]

![[Attachments/Images/Pasted image 20230323161432.png]]

```c
for (int i = n; i >= 1; i--) {  //逆序枚举
	for (int j = 0; j <= m; j++) {  
		f[i][j] = f[i + 1][j];  //更新值
		if (j >= v[i]) {  
			f[i][j] = max(f[i][j], f[i + 1][j - v[i]] + w[i]);  
		}  
	}  
}  
int j = m;  //剩余容量
for (int i = 1; i <= n; i++) {  //找路
	if (j >= v[i] && f[i][j] == f[i + 1][j - v[i]] + w[i]) {  //如果需要选这个
		cout << i << " "; 
		j -= v[i];  //更新剩余容量
	}  
}  
```

## 1.2 最长上升子序列

#### 动态规划

```c
void print(int i) {  
    if (path[i]) print(path[i]);  
    cout << a[i] << " ";  
}  

for (int i = 2; i <= n; i++) {  
	for (int j = 1; j < i; j++) {  //每次从1-i-1里面寻找
		if (a[i] > a[j] && f[j] + 1 > f[i]) { //如果满足上升而且最长 
			f[i] = f[j] + 1;  
			path[i] = j;//记录前驱节点  
		}  
	}  
	if (f[i] > ans) {  
		ans = f[i];  
		maxindex = i;  //记录下最长的序列的最后一个下标
	}  
}  
cout << ans << endl;  
print(maxindex);  
```

#### 二分

![[Attachments/Images/Pasted image 20230323161657.png]]

>备用序列的长度就是最长的长度，但是不能求出具体的序列
>如果是最长不下降子序列也是一样的,不过不同的是当前元素如果大于等于最后一个值就直接添加,小于的时候就需要替换第一个大于的位置

```c
int f(int x) {  //找到第一个大于等于它的元素
    int l = 1, r = len;  
    int mid, res;  
    while (l <= r) {  
        mid = (l + r) / 2;  
        if (p[mid] >= x) {  
            r = mid - 1;  
            res = mid;  
        } else {  
            l = mid + 1;  
        }  
    }  
    return res;  
}  
for (int i = 1; i <= n; i++) {  
	if (b[i] > p[len]) {  
		p[++len] = b[i];  
	} else {  
		int index = f(b[i]);  
		p[index] = b[i];  
	}  
}  
cout << len;  
```

# 2. 字符串

## 2.1 字符串哈希

![[Attachments/Images/Pasted image 20230323161741.png]]
![[Attachments/Images/Pasted image 20230323161755.png]]

```c
typedef unsigned long long ull;//溢出自动取模
const int P = 131;

ull p[2000], h[2000];

void init(string a) { //计算a的哈希值
    p[0] = 1;
    h[0] = 0;
    for (int i = 1; i <= a.length(); i++) {
        p[i] = p[i - 1] * P;//保留p的次方,方便计算子串哈希值时使用
        h[i] = h[i - 1] * P + a[i - 1];
    }
}

ull get(int l, int r) {//计算子串的哈希值
    return h[r] - h[l - 1] * p[r - l + 1];
}

bool substr(int l1,int r1,int l2,int r2){//判断两个子串是否相等
	return get(l1,r1)==get(l2,r2);
}
```

## 2.2 最小表示法

![[Attachments/Images/Pasted image 20230323161829.png]]
![[Attachments/Images/Pasted image 20230323161855.png]]

```c
int n;
char s[100];
cin >> n;
for (int i = 1; i <= n; i++) {//复制一遍
	cin >> s[i];
	s[i + n] = s[i];
}
int i = 1, j = 2, k = 0;
while (i <= n && j <= n) {
	for (k = 0; k < n && s[i + k] == s[j + k]; k++);//如果相等就继续
	s[i + k] > s[j + k] ? i = i + k + 1 : j = j + k + 1;//不相等大的就跳转
	if (i == j) {//如果跳到了一样的位置那么j自增
		j++;
	}
}
cout << min(i, j);
```

## 2.3 KMP

### KMP模板

![[Attachments/Images/Pasted image 20230323161925.png]]
![[Attachments/Images/Pasted image 20230323162015.png]]
![[Attachments/Images/Pasted image 20230323162033.png]]

```c
//p:模式串,s:主串   
ne[1] = 0;  
int n = strlen(p + 1);  
int m = strlen(s + 1);  
for (int i = 2, j = 0; i <= n; i++) {  //计算ne数组
	while (j && p[i] != p[j + 1]) {  
		j = ne[j];  
	}  
	if (p[i] == p[j + 1]) {  
		j++;  
	}  
	ne[i] = j;  
} 

for (int i = 1, j = 0; i <= m; i++) {  //计算子串在主串中出现的位置
	while (j && s[i] != p[j + 1]) {  
		j = ne[j];  
	}  
	if (s[i] == p[j + 1]) {  
		j++;  
	}  
	if (j == n) {  
		cout << i - n + 1 << endl;  
	}  
}  
```

### 最小循环节

![[Attachments/Images/Pasted image 20230314234700.png]]
![[Attachments/Images/Pasted image 20230314234939.png]]

```c
for (int i = 2; i <= n; i++) {  //输出最小循环节的长度
    if (i % (i - ne[i]) == 0 && ne[i]) {  
        cout << i << " " << i / (i - ne[i]) << endl;  
    }  
}
```

## 扩展KMP

![[Attachments/Images/Pasted image 20230317234831.png]]
![[Attachments/Images/Pasted image 20230317235740.png]]

![[Attachments/Images/Pasted image 20230318000854.png]]

>T中的l,r与s中的l,r-l+1是相等的,z[i-l+1]代表的是对应相等的长度

```c
int z[20000005], p[20000005];
char s[20000005], t[20000005];

void getz(int n) {
    z[1] = n;
    for (int i = 2, l, r = 0; i <= n; i++) {
        if (i <= r) {//如果在盒子里面,那么取最小的
            z[i] = min(z[i - l + 1], r - i + 1);
        }
        while (s[1 + z[i]] == s[i + z[i]]) {//然后暴力枚举
            z[i]++;
        }
        if (i + z[i] - 1 > r) {//最后更新l,r
            l = i;
            r = i + z[i] - 1;
        }
    }
}

void getp(int n, int m) {
    for (int i = 1, l, r = 0; i <= m; i++) {
        if (i <= r) {
            p[i] = min(z[i - l + 1], r - i + 1);
        }
        while (1 + p[i] <= n && i + p[i] <= m && s[1 + p[i]] == t[i + p[i]]) {
            p[i]++;
        }
        if (i + p[i] - 1 > r) {
            l = i;
            r = i + p[i] - 1;
        }
    }
}
```

## 2.4 Manacher

![[Attachments/Images/Pasted image 20230323162122.png]]
![[Attachments/Images/Pasted image 20230323162203.png]]

```c
//扩展数组  
int k = 0;  
s[k++] = '#';  
for (int i = 0; i < n; i++) {  
	s[k++] = ori[i];  
	s[k++] = '#';  
}  

n = k;  

d[0] = 1;  
int l, r = 0;  
for (int i = 1; i < n; i++) { //枚举每一位 
	if (i <= r) {  //如果在盒子里面
		d[i] = min(d[r - i + l], r - i + 1);  
	}  
	while (i - d[i] != -1 && s[i - d[i]] == s[i + d[i]]) {//加上前置条件,以防数组越界  
		d[i]++;  
	}  
	if (i + d[i] - 1 > r) {  //更新
		l = i - d[i] + 1;  
		r = i + d[i] - 1;  
	}  
}  
```

## 2.5 字典树

### Trie

![[Attachments/Images/Pasted image 20230318110536.png]]
![[Attachments/Images/Pasted image 20230323162259.png]]

```c
int ch[100][26];  
int cnt[100], idx;  

void insert(char a[]) {  
    int p = 0;  
    for (int i = 0; a[i]; i++) {  //枚举a的每一位
        int j = a[i] - 'a';  //取得每一位对应的数字a-0,b-1...
        if (!ch[p][j]) {  //如果没有这个分支
            ch[p][j] = ++idx;  //创建一个新的节点
        }  
        p = ch[p][j];  //走到这个节点
    }  
    cnt[p]++;  //这个单词串的数量增加一个
}  
  
int query(char a[]) {  
    int p = 0;  
    for (int i = 0; a[i]; i++) {  
        int j = a[i] - 'a';  
        if (!ch[p][j]) {  
            return 0;  
        }  
        p = ch[p][j];  
    }  
    return cnt[p];  
}
```

### 01Trie

![[Attachments/Images/Pasted image 20230314142333.png]]

```c
int pre[100005];  //记录下前面的异或和
int arr[100005];  
  
int trie[4000000][35];  
int cnt[4000000];  //每个节点中的数量
int idx;  
  
int n, m;  
  
  
void insert(int a, int c) {//c=1,添加,c=-1,删除  
    int p = 0;  
    for (int i = 30; i >= 0; i--) {  
        int j = 1 & (a >> i);  
        if (!trie[p][j]) {  
            trie[p][j] = ++idx;  
        }  
        p = trie[p][j];  
        cnt[p] += c;  
    }  
}  
  
int query(int a) {  
    int p = 0;  
    int res = 0;  
    for (int i = 30; i >= 0; i--) {  
        int j = 1 & (a >> i);  
        if (cnt[trie[p][!j]] > 0) {  //如果存在相反的数
            res += 1 << i;  //取这个相反的值
            p = trie[p][!j];  
        } else {  
            p = trie[p][j];  
        }  
    }  
    return res;  
}  
  
signed main() {  
    ios::sync_with_stdio(false);  
    cin.tie(nullptr);  
  
    cin >> n >> m;  
  
    for (int i = 1; i <= n; i++) {  
        cin >> arr[i];  
        pre[i] = pre[i - 1] ^ arr[i];  
    }  
  
    int res = 0;  
  
    insert(0, 1);//插入0保证前m个数进行异或和答案是自己  
  
    for (int i = 1; i <= n; i++) {//维护一个m长的窗口,并且用新到来的值去计算窗口内的最大异或值  
        if (i > m && i != m + 1) {//如果大于m的话每走一步就要减去一个不在窗口内的值  
            insert(pre[i - m - 1], -1);  
        }  
        res = max(res, query(pre[i]));//查询  
        insert(pre[i], 1);//将这个值插入窗口内  
    }  
    cout << res << endl;  
}
```

# 3. 图

## 3.1 图的存储

![[Attachments/Images/Pasted image 20230323162406.png]]

## 3.2 DFS

![[Attachments/Images/Pasted image 20230323162441.png]]

>注意在主函数调用的时候应该标记起点已经走过

```c 
int dx[4] = {-1, 0, 1, 0};  
int dy[4] = {0, 1, 0, -1};  
int vis[200][200];  
void dfs(int nowx, int nowy) {  
    if (nowx == x && nowy == y) {  //如果到达了目的点
        res++;  
        return;  
    }  
    for (int i = 0; i < 4; i++) {  
        int xx = nowx + dx[i];  
        int yy = nowy + dy[i];  
        if (xx >= 1 && xx <= n && yy >= 1 && yy <= m && vis[xx][yy] == 0 && arr[xx][yy] == 0) {//如果不越界并且没有走过并且可以走
            vis[xx][yy] = 1;  //标记已经走过
            dfs(xx, yy);  
            vis[xx][yy] = 0;  //取消标记,还可以继续走
        }  
    }  
}  
```

## 3.3 BFS

![[Attachments/Images/Pasted image 20230318113322.png]]
![[Attachments/Images/Pasted image 20230318113833.png]]

```c 
int dx[4] = {0, 0, 1, -1};  
int dy[4] = {1, -1, 0, 0};  
pair<int, int> ps[200][200];  
  
void print(int i, int j) {  //递归输出路径
    if (i == a && j == b) {  
        return;  
    } else {  
        print(ps[i][j].first, ps[i][j].second);  
        cout << ps[i][j].first << " " << ps[i][j].second << endl;  
    }  
}  
  
void bfs(int i, int j) {  
    queue<pair<int, int>> q;  
    q.push({i, j});  
    while (q.size()) {  
        pair<int, int> cur = q.front();  
        q.pop();  
        if (cur.first == x && cur.second == y) {  
            print(x, y);  
            cout << x << " " << y << endl;  
            return;  
        }  
        for (int k = 0; k < 4; k++) {  
            int xx = cur.first + dx[k];  
            int yy = cur.second + dy[k];  
            if (xx >= 1 && xx <= n && yy >= 1 && yy <= m && arr[xx][yy] == 0) {  
                arr[xx][yy] = 1;  //标记已经走过了
                q.push({xx, yy});  
                ps[xx][yy] = cur;  //记录前驱的节点
            }  
        }  
    }  
}  
```

## 3.4 拓扑排序

### 求拓扑序(卡恩算法)

![[Attachments/Images/Pasted image 20230323162517.png]]

>如果要求字典序最小的拓扑序,需要将vector换成优先队列(小根堆)

```c

int n, m;  
vector<int> e[100], tp;  
int din[100];  

bool toposort() {  
    queue<int> q;  
    for (int i = 1; i <= n; i++) {  
        if (din[i] == 0) {  //找到所有的入度为0的点入队
            q.push(i);  
        }  
    }  
    while (q.size()) {  
        int cur = q.front();  
        q.pop();  
        tp.push_back(cur);  //加入结果数组
        for (int i: e[cur]) {  
            if (--din[i] == 0) {  //每个邻点的入度-1,如果为0也入队
                q.push(i);  
            }  
        }  
    }  
    return tp.size() == n;//如果拓扑序列里面的值的个数等于n那么就无环  
}  
```

### 染色法

![[Attachments/Images/Pasted image 20230323162600.png]]

```c
vector<int> e[100], tp;
int c[100];//染色数组
int n, m;

bool dfs(int x) {
    c[x] = -1;//第一次染色
    for (int i: e[x]) {
        if (c[i] == -1) {//如果回到了之前的点,那么有环
            return 0;
        } else if (c[i] == 0) {//下一个点继续dfs
            if (!dfs(i)) {
                return 0;
            }
        }
    }
    c[x] = 1;//染色成功
    tp.push_back(x);//加入队列
    return 1;//返回1结束
}

bool toposort() {//防止图不连通
    memset(c, 0, sizeof(c));
    for (int x = 1; x <= n; x++) {
        if (!c[x]) {
            if (!dfs(x)) {
                return 0;
            }
        }
    }
    reverse(tp.begin(), tp.end());
    return 1;
}
```

## 3.5 最短路

![[Attachments/Images/Pasted image 20230318130328.png]]
![[Attachments/Images/Pasted image 20230318130356.png]]
![[Attachments/Images/Pasted image 20230318131412.png]]

### Dijkstra

![[Attachments/Images/Pasted image 20230318125032.png]]
![[Attachments/Images/Pasted image 20230318125129.png]]

![[Attachments/Images/Pasted image 20230318125528.png]]

```c  
struct edge {  
    int v;  
    int w;  
};  
  
vector<edge> e[100005];  
int d[100005], vis[100005];  
priority_queue<pair<int, int>> q;  
int n, m;  

void dijkstra(int s) {  
    for (int i = 0; i <= n; i++) {  //赋超大值
        d[i] = pow(2, 31) - 1;  
    }  
    d[s] = 0;  //源点距离自己为0
    q.push({0, s});  //入队
    while (q.size()) {  
        auto t = q.top();  
        q.pop();  
        if (vis[t.second]) {  //如果看过就跳过
            continue;  
        }  
        vis[t.second] = 1;  //标记
        for (auto ed: e[t.second]) {  //枚举每一条边
            int v = ed.v;  
            int w = ed.w;  
            if (d[v] > d[t.second] + w) {  //如果能更新就更新
                d[v] = d[t.second] + w;  
                q.push({-d[v], v});  //更新之后的点进入队列
            }  
        }  
    }  
}  
```

### Bellman-Ford

![[Attachments/Images/Pasted image 20230318125605.png]]
![[Attachments/Images/Pasted image 20230318125903.png]]

### SPFA

![[Attachments/Images/Pasted image 20230318125941.png]]

```c
struct edge {  
    int v;  
    int w;  
};  

vector<edge> e[100005];  
int d[100005], vis[100005];  
  
int n, m;  
int times[100000];//记录每个点最短路径所含的边数  
  
bool spfa(int s) {  
    queue<int> q;  
    memset(d, 0x3f, sizeof(d));  
    d[s] = 0;  
    vis[s] = 1;  //标记在队中
    q.push(s);  
    while (q.size()) {  
        int u = q.front();  
        q.pop();  
        vis[u] = false;  //标记不在队中
        for (edge ed: e[u]) {  
            int v = ed.v;  
            int w = ed.w;  
            if (d[v] > d[u] + w) {//如果能更新就更新  
                d[v] = d[u] + w;  
                times[v] = times[u] + 1;  //记录从s走到v的边数
                if (times[v] >= n) {//如果一个点的最短路径所含的边数大于等于n,那么一定有负环  
                    return true;  
                }  
                if (!vis[v]) {  //入队,并标记
                    vis[v] = true;  
                    q.push(v);  
                }  
            }  
        }  
    }  
    return false;  
}  
```

### Floyd

![[Attachments/Images/Pasted image 20230318130511.png]]
![[Attachments/Images/Pasted image 20230318130726.png]]

![[Attachments/Images/Pasted image 20230318132549.png]]

### Johnson

![[Attachments/Images/Pasted image 20230318131134.png]]
![[Attachments/Images/Pasted image 20230318131204.png]]

```c
struct edge {  
    int v, w;  
};  
  
vector<edge> e[3005];  
int vis[3005];  
int n, m;  
int d[3005], times[3005], h[3005];  
  
bool spfa(int s) {  
    queue<int> q;  
    memset(vis, 0, sizeof vis);  
    for (int i = 0; i <= n; i++) {  
        h[i] = 1e9;  
    }  
    h[s] = 0;  
    vis[s] = 1;  
    q.push(s);  
    while (q.size()) {  
        int u = q.front();  
        q.pop();  
        vis[u] = false;  
        for (edge ed: e[u]) {  
            int v = ed.v;  
            int w = ed.w;  
            if (h[v] > h[u] + w) {  
                h[v] = h[u] + w;  
                times[v] = times[u] + 1;  
                if (times[v] > n) {//建立虚拟源点之后一共有n+1个点  
                    return true;  
                }  
                if (!vis[v]) {  
                    vis[v] = true;  
                    q.push(v);  
                }  
            }  
        }  
    }  
    return false;  
}  
  
void dijkstra(int s) {  
    priority_queue<pair<int, int>> q;  
    memset(vis, 0, sizeof vis);  
    for (int i = 0; i <= n; i++) {  
        d[i] = 1e9;  
    }  
    d[s] = 0;  
    q.push({0, s});  
    while (q.size()) {  
        auto t = q.top();  
        q.pop();  
        if (vis[t.second]) {  
            continue;  
        }  
        vis[t.second] = 1;  
        for (auto ed: e[t.second]) {  
            int v = ed.v;  
            int w = ed.w;  
            if (d[v] > d[t.second] + w) {  
                d[v] = d[t.second] + w;  
                q.push({-d[v], v});  
            }  
        }  
    }  
}  
  
signed main() {  
    ios::sync_with_stdio(false);  
    cin.tie(nullptr);  
  
    cin >> n >> m;  
    for (int i = 1; i <= m; i++) {  
        int a, b, c;  
        cin >> a >> b >> c;  
        e[a].push_back({b, c});  
    }  
  
    for (int i = 1; i <= n; i++) {  //连一条边权为0的边
        e[0].push_back({i, 0});  
    }  
  
    if (spfa(0)) {  //跑一遍spfa求出0号点到其他所有点的最短路
        cout << "-1 " << endl;  
        return 0;  
    }  
  
    for (int i = 1; i <= n; i++) {  //改造边权
        for (auto &ed: e[i]) {  
            ed.w += h[i] - h[ed.v];  
        }  
    }  
  
    for (int i = 1; i <= n; i++) {  //对每一个点跑dj
        int ans = 0;  
        dijkstra(i);  
        for (int j = 1; j <= n; j++) {  
            cout<<(d[j] + h[j] - h[i])<<" ";
        }  
        cout << ans << endl;  
    }  
}
```

## 3.6 最小生成树

![[Attachments/Images/Pasted image 20230318134449.png]]

### Prim

![[Attachments/Images/Pasted image 20230318132749.png]]
![[Attachments/Images/Pasted image 20230318132825.png]]

```c
int n, m, ans, cnt;  
struct edge {  
    int v;  
    int w;  
};  
  
vector<edge> e[10000];  
int d[10000];  
int vis[10000];  
priority_queue<pair<int, int> > q;  
  
bool prim(int s) {  
    for (int i = 0; i <= n; i++) {  
        d[i] = 0x3f3f3f3f;  
    }  
    d[s] = 0;  
    q.push({0, s});  
    while (q.size()) {  
        int u = q.top().second;  
        q.pop();  
        if (vis[u]) {  
            continue;  
        }  
        vis[u] = 1;  
        ans += d[u];  
        cnt++;  
        for (auto ed: e[u]) {//对于u点的所有的边的点,更新他们的距离  
            int v = ed.v;  
            int w = ed.w;  
            if (d[v] > w) {  
                d[v] = w;  
                q.push({-d[v], v});  
            }  
        }  
    }  
    return cnt == n;//如果能够组成最小生成树返回true;  
}  
  
signed main() {  
    ios::sync_with_stdio(false);  
    cin.tie(nullptr);  
  
    cin >> n >> m;  
    for (int i = 1; i <= m; i++) {  
        int a, b, c;  
        cin >> a >> b >> c;  
        e[a].push_back({b, c});  
        e[b].push_back({a, c});  
  
    }  
    if (prim(1)) {  
        cout << ans << endl;  
    } else {  
        cout << "orz" << endl;  
    }  
}
```

### Kruskal

![[Attachments/Images/Pasted image 20230318134249.png]]

```c
struct edge {  
    int u, v, w;  
  
    bool operator<(const edge &t) const {  
        return w < t.w;  
    }  
    
} e[N];  
  
int n, m;  
int fa[N], ans, cnt;  
  
int find(int x) {  
    if (fa[x] == x) {  
        return x;  
    } else {  
        return fa[x] = find(fa[x]);  
    }  
}  
  
bool Kruskal() {  
    sort(e + 1, e + 1 + 2 * m); //对边排序 
    for (int i = 1; i <= n; i++) {  //并查集
        fa[i] = i;  
    }  
    for (int i = 1; i <= 2 * m; i++) {  //枚举每一条边
        int x = find(e[i].u);  
        int y = find(e[i].v);  
        if (x != y) {//如果x,y不在同一个集合里面  
            fa[x] = y;  
            ans += e[i].w;  
            cnt++;  
        }  
    }  
    return cnt == n - 1;//需要结合n-1次才能保证连通  
}  
```

## 3.7 最近公共祖先

![[Attachments/Images/Pasted image 20230318142100.png]]
![[Attachments/Images/Pasted image 20230318142116.png]]

### 倍增算法

![[Attachments/Images/Pasted image 20230318134601.png]]

![[Attachments/Images/Pasted image 20230318134649.png]]

```c
void dfs(int u, int father) {  //深搜
    dep[u] = dep[father] + 1;  //深度处理
    fa[u][0] = father;  //记录跳一下
    for (int i = 1; i <= 31; i++) {  
        fa[u][i] = fa[fa[u][i - 1]][i - 1];  //dp
    }  
    for (int v: e[u]) {  
        if (v != father) {  //继续搜
            dfs(v, u);  
        }  
    }  
  
}  
  
int lca(int u, int v) {  
    if (dep[u] < dep[v]) {//确保u为深度更大的点  
        swap(u, v);  
    }  
    for (int i = 31; i >= 0; i--) {//先跳到同一层  
        if (dep[fa[u][i]] >= dep[v]) {//如果u跳完,v还在u的上面,那就可以跳  
            u = fa[u][i];  
        }  
    }  
    if (u == v) {  //跳到同一层了,如果相等,那么就直接是答案
        return u;  
    }  
    for (int i = 31; i >= 0; i--) {  //从大往小跳
        if (fa[u][i] != fa[v][i]) {//跳完之后不相等的话,那就可以跳  
            u = fa[u][i];  
            v = fa[v][i];  
        }  
    }  
    return fa[u][0];  //再跳一次就是所求的
}  
```

### Tarjan

![[Attachments/Images/Pasted image 20230318135125.png]]


```c
#define N 600000  
#define M 600000  
vector<int> e[N];  
vector<pair<int, int>> query[N];  
int fa[N], vis[N], ans[M];  

int find(int u) {  //并查集
    if (fa[u] == u) {  
        return u;  
    } else {  
        return fa[u] = find(fa[u]);  
    }  
}  
  
void tarjan(int u) {  //深搜
    vis[u] = true;  //标记见过
    for (auto v: e[u]) {  //继续深搜
        if (!vis[v]) {  
            tarjan(v);  
            fa[v] = u;  //并查集
        }  
    }  
    for (auto q: query[u]) {  //回到u点时,u左下角的所有的点都被看过了
        int v = q.first;  //u的查询节点
        int i = q.second;  //第几对查询
        if (vis[v]) {  //如果见过了v
            ans[i] = find(v);  //v的fa就是查询u,v的答案,也就是u上一级的交点
        }  
    }  
}  
```

### 树链剖分

![[Attachments/Images/Pasted image 20230318140758.png]]

```c
#define N 600000  
#define M 600000  
  
vector<int> e[N];  
int fa[N], dep[N], son[N], sz[N];  
int top[N];  
int n, m, s;  
  
void dfs1(int u, int father) {  
    fa[u] = father;  //记录父亲
    dep[u] = dep[father] + 1; //记录深度 
    sz[u] = 1;  //记录子节点数
    for (int v: e[u]) {  
        if (v == father) {  
            continue;  
        }  
        dfs1(v, u);  
        sz[u] += sz[v];  //加上子节点数
        if (sz[son[u]] < sz[v]) {  //如果这个子节点的大小大于记录的,替换
            son[u] = v;  
        }  
    }  
}  
  
void dfs2(int u, int t) {  
    top[u] = t;  //u所在重链的顶点
    if (!son[u]) {  //如果没有重孩子就返回
        return;  
    }  
    dfs2(son[u], t);  
    for (int v: e[u]) {  
        if (v == fa[u] || v == son[u]) {  
            continue;  
        }  
        dfs2(v, v);  //轻儿子的链头为自己
    }  
}  
  
int lca(int u, int v) {  
    while (top[u] != top[v]) {//不在一条链上  
        if (dep[top[u]] < dep[top[v]]) {//保证始终是u往上跳  
            swap(u, v);  
        }  
        u = fa[top[u]];  
    }  
    return dep[u] < dep[v] ? u : v;//在同一条重链上,深度较浅的返回  
}  
```

## 3.8 SCC,EDCC,VDCC

![[Attachments/Images/Pasted image 20230318150029.png]]
![[Attachments/Images/Pasted image 20230318150043.png]]

### 有向图的强联通分量(SCC)

![[Attachments/Images/Pasted image 20230318143535.png]]
![[Attachments/Images/Pasted image 20230318143953.png]]

```c
#define N 100000  
vector<int> e[N];  
int dfn[N], low[N], tot;  
stack<int> s;  
int ins[N];  
int scc[N], siz[N], cnt;  
  
void tarjan(int x) {  
    dfn[x] = low[x] = ++tot;  //dfn和low的初始值
    s.push(x);  //入栈
    ins[x] = 1;  //在栈中
    for (int y: e[x]) {  
        if (!dfn[y]) {//如果y没有访问过  
            tarjan(y);  
            low[x] = min(low[x], low[y]);//回x的时候用y更新low,y能访问到x也一定能访问
        } else if (ins[y]) {//访问过并且在栈中  
            low[x] = min(low[x], dfn[y]);//更新low  
        }  
    }  
    //离开x的时候记录scc  
    if (dfn[x] == low[x]) {  //如果x是scc的根
        int y = -1;  
        cnt++;//scc编号  
        while (x != y) {  
            y = s.top();  
            s.pop();  
            ins[y] = 0;  //标记不在栈中
            scc[y] = cnt;  //记录进强联通分量里面
            siz[cnt]++;  //scc大小
        }  
    }  
}  
```

### 割点

![[Attachments/Images/Pasted image 20230318145029.png]]
![[Attachments/Images/Pasted image 20230318145055.png]]


```c
#define N 100000  
vector<int> e[N];  
int dfn[N], low[N], tot;  
int root;  
int cut[N];  
  
  
void tarjan(int x) {  
    dfn[x] = low[x] = ++tot;  
    int child = 0;  
    for (int y: e[x]) {  
        if (!dfn[y]) {  
            tarjan(y);  
            low[x] = min(low[x], low[y]);  
            if (low[y] >= dfn[x]) {  
                child++;  
                if (x != root || child > 1) {  
                    cut[x] = true;  
                }  
            }  
        } else {  
            low[x] = min(low[x], dfn[y]);  
        }  
    }  
}  
```

### 点双连通分量(VDCC)

![[Attachments/Images/Pasted image 20230318145851.png]]
![[Attachments/Images/Pasted image 20230318145937.png]]

```c
#include<bits/stdc++.h>  
  
#define int long long  
#define double long double  
#define endl '\n'  
using namespace std;\  
  
#define N 100000  
vector<int> e[N], ne[N];  
int dfn[N], low[N], tot;  
stack<int> s;  
vector<int> dcc[N];  
int cut[N], root, cnt, num, id[N];  
  
void tarjan(int x) {  
    dfn[x] = low[x] = ++tot;  
    s.push(x);  
    if (!e[x].size()) {//孤立点  
        dcc[++cnt].push_back(x);  
        return;  
    }  
    int child = 0;  
    for (int y: e[x]) {  
        if (!dfn[y]) {  
            tarjan(y);  
            low[x] = min(low[x], low[y]);  
            if (low[y] >= dfn[x]) {//满足割点的条件  
                child++;  
                if (x != root || child > 1) {  
                    cut[x] = true;  
                }  
                cnt++;  
                int z = -1;  
                while (z != y) {//只要这条路满足割点的条件,那就开始出栈  
                    z = s.top();  
                    s.pop();  
                    dcc[cnt].push_back(z);  
                }  
                dcc[cnt].push_back(x);//x是割点,加入,但不出栈  
            }  
        } else {  
            low[x] = min(low[x], dfn[y]);  
        }  
    }  
}  
  
  
signed main() {  
    ios::sync_with_stdio(false);  
    cin.tie(nullptr);  
  
    int n, m;  
    cin >> n >> m;  
    for (int i = 1; i <= m; i++) {  
        int a, b;  
        cin >> a >> b;  
        e[a].push_back(b);  
        e[b].push_back(a);  
    }  
    for (root = 1; root <= n; root++) {  
        if (!dfn[root]) {  
            tarjan(root);  
        }  
    }  
    num = cnt;  
    for (int i = 1; i <= n; i++) {  
        if (cut[i]) {//如果是割点,那么给割点编号  
            id[i] = ++num;  
        }  
    }  
    for (int i = 1; i <= cnt; i++) {//枚举所有的点双连通分量vdcc  
        for (int j = 0; j < dcc[i].size(); j++) {//枚举这个点双连通分量的所有点  
            int x = dcc[i][j];  
            if (cut[x]) {//找到该vdcc中的割点,给割点的新标号和该vdcc的标号建立一条无向边  
                ne[i].push_back(id[x]);  
                ne[id[x]].push_back(i);  
            }  
        }  
    }  
}
```


### 割边

![[Attachments/Images/Pasted image 20230318145248.png]]
![[Attachments/Images/Pasted image 20230318145305.png]]



```c
#include<bits/stdc++.h>  
  
#define int long long  
#define double long double  
#define endl '\n'  
using namespace std;  
  
#define N 10000  
  
struct edge {  
    int u;  
    int v;  
};  
  
vector<edge> e;  
vector<int> h[N];  
int dfn[N], low[N], tot, cnt;  
  
struct bridge {  
    int x;  
    int y;  
  
    bool operator<(const bridge &b) const {  
        if (x == b.x) {  
            return y < b.y;  
        }  
        return x < b.x;  
    }  
} bridge[2 * N];  
  
void add(int a, int b) {  
    e.push_back({a, b});  
    h[a].push_back(e.size() - 1);  
}  
  
void tarjan(int x, int in_edge) {  
    dfn[x] = low[x] = ++tot;  
    for (int i = 0; i < h[x].size(); i++) {  
        int j = h[x][i];  
        int y = e[j].v;  
        if (!dfn[y]) {//如果y没有访问过  
            tarjan(y, j);  
            low[x] = min(low[x], low[y]);  
            if (low[y] > dfn[x]) {//满足割边条件  
                bridge[++cnt] = {x, y};  
            }  
        } else if (j != (in_edge ^ 1)) {//如果访问过并且不是走的反边  
            low[x] = min(low[x], dfn[y]);  
        }  
    }  
}  
  
signed main() {  
    ios::sync_with_stdio(false);  
    cin.tie(nullptr);  
  
    int n, m;  
    cin >> n >> m;  
    for (int i = 1; i <= m; i++) {  
        int a, b;  
        cin >> a >> b;  
        add(a, b);  
        add(b, a);  
    }  
    for (int i = 1; i <= n; i++) {  
        if (!dfn[i]) {  
            tarjan(i, -2);  
        }  
    }  
    sort(bridge + 1, bridge + 1 + cnt);  
    for (inti = 1; i <= cnt; i++) {  
        cout << bridge[i].x << " " << bridge[i].y << endl;  
    }  
}
```

### 边双连通分量(EDCC)

![[Attachments/Images/Pasted image 20230318145433.png]]
![[Attachments/Images/Pasted image 20230318145540.png]]


```c
#include<bits/stdc++.h>  
  
#define int long long  
#define double long double  
#define endl '\n'  
using namespace std;  
  
#define N 10000  
#define M 20000  
  
struct edge {  
    int v;  
    int ne;  
} e[M];  
  
int h[N], idx = 1;  
int dfn[N], low[N], tot;  
stack<int> s;  
int dcc[N], cnt;  
int bri[M], d[N];  
  
  
void add(int a, int b) {  
    e[++idx].v = b;  
    e[idx].ne = h[a];  
    h[a] = idx;  
}  
  
void tarjan(int x, int in_edge) {  
    dfn[x] = low[x] = ++tot;  
    s.push(x);  
    for (int i = h[x]; i; i = e[i].ne) {//割边  
        int y = e[i].v;  
        if (!dfn[y]) {  
            tarjan(y, i);  
            low[x] = min(low[x], low[y]);  
            if (low[y] > dfn[x]) {  
                bri[i] = bri[i ^ 1] = true;  
            }  
        } else if (i != (in_edge ^ 1)) {  
            low[x] = min(low[x], dfn[y]);  
        }  
    }  
    if (dfn[x] == low[x]) {//缩点  
        cnt++;  
        while (1) {  
            int y = s.top();  
            s.pop();  
            dcc[y] = cnt;  
            if (y == x) {  
                break;  
            }  
        }  
    }  
}  
  
signed main() {  
    ios::sync_with_stdio(false);  
    cin.tie(nullptr);  
  
    int n, m;  
    cin >> n >> m;  
    for (int i = 1; i <= m; i++) {  
        int a, b;  
        cin >> a >> b;  
        add(a, b);  
        add(b, a);  
    }  
    tarjan(1, 0);  
    for (int i = 2; i <= idx; i++) {  
        if (bri[i]) {  
            d[dcc[e[i].v]]++;  
        }  
    }  
    int sum = 0;  
    for (int i = 1; i <= cnt; i++) {  
        if (d[i] == 1) {  
            sum++;  
        }  
    }  
    cout << (sum + 1) / 2;  
  
}
```

## 3.9 网络流

### EK

![[Attachments/Images/Pasted image 20230318171855.png]]
![[Attachments/Images/Pasted image 20230318171912.png]]


```c
#define M 100000  
#define N 100000  
  
struct edge {  
    int v;  
    int c;  
    int ne;  
} e[M];  
  
int h[N], idx = 1;  
int mf[N], pre[N];  
int s, t;  
  
void add(int a, int b, int c) {  
    e[++idx] = {b, c, h[a]};  
    h[a] = idx;  
}  
  
bool bfs() {  
    memset(mf, 0, sizeof mf);//初始化每个点的流量上限为0  
    queue<int> q;  
    q.push(s);  
    mf[s] = 1e9;//起点的流量设为无穷大  
    while (q.size()) {  
        int u = q.front();  
        q.pop();  
        for (int i = h[u]; i; i = e[i].ne) { //枚举每一条边 
            int v = e[i].v;  
            if (mf[v] == 0 && e[i].c) {//如果这个点没有被访问过并且有流量可以走到这条边  
                mf[v] = min(mf[u], e[i].c);//更新  
                pre[v] = i;//记录该点的前驱边  
                q.push(v);  
                if (v == t) {//如果到达了汇点,返回  
                    return true;  
                }  
            }  
        }  
    }  
    return false;  
}  
  
int EK() {  
    int flow = 0;  
    while (bfs()) {//只要能找到可行流就继续找  
        int v = t;  
        while (v != s) {//逆序更新残留网  
            int i = pre[v];  
            e[i].c -= mf[t];//前驱边的容量减去相应数值  
            e[i ^ 1].c += mf[t];//相反边增加数值  
            v = e[i ^ 1].v;//v等于相反边的下一个点  
        }  
        flow += mf[t];  
    }  
    return flow;  
}  
```

### Dinic

![[Attachments/Images/Pasted image 20230318172912.png]]

```c
#define M 1000006  
#define N 1000006  
  
struct edge {  
    int v;  
    int c;  
    int ne;  
} e[M];  
  
int h[N], idx = 1;  
int s, t;  
int d[N], cur[N];  
  
void add(int a, int b, int c) {  
    e[++idx] = {b, c, h[a]};  
    h[a] = idx;  
}  
  
bool bfs() {//寻找增广路,并且按照图层划分  
    memset(d, 0, sizeof d);//每一个图层置0  
    queue<int> q;  
    q.push(s);  
    d[s] = 1;//源点图层为1  
    while (q.size()) {  
        int u = q.front();  
        q.pop();  
        for (int i = h[u]; i; i = e[i].ne) {//枚举边  
            int v = e[i].v;  
            if (d[v] == 0 && e[i].c) {//如果没访问过并且有边可以去  
                d[v] = d[u] + 1;  
                q.push(v);  
                if (v == t) {  
                    return true;  
                }  
            }  
        }  
    }  
    return false;  
}  
  
int dfs(int u, int mf) {  
    if (u == t) {  
        return mf;  
    }  
    int sum = 0;  
    for (int i = cur[u]; i; i = e[i].ne) {//当前弧优化,cur[u]存的是之前u点最后访问的一条边,因为已经访问过的边不需要再访问  
        cur[u] = i;  
        int v = e[i].v;  
        if (d[v] == d[u] + 1 && e[i].c) {//如果在下一层并且有路可以走  
            int f = dfs(v, min(mf, e[i].c));//对子节点dfs,mf去较小值  
            e[i].c -= f;//构建残余网  
            e[i ^ 1].c += f;  
            sum += f;  
            mf -= f;//余量减小  
            if (mf == 0) {  
                break;  
            }  
        }  
    }  
    if (sum == 0) {//说明这个点不能到达t点,使其无法访问即可  
        d[u] = 0;  
    }  
    return sum;  
}  
  
int dinic() {  
    int flow = 0;  
    while (bfs()) {//如果有可行流  
        memcpy(cur, h, sizeof h);//每次遍历之前都重置cur  
        flow += dfs(s, 1e9);  
    }  
    return flow;  
}  
```

### 最小割

![[Attachments/Images/Pasted image 20230318175411.png]]
![[Attachments/Images/Pasted image 20230318175627.png]]
![[Attachments/Images/Pasted image 20230318181125.png]]

>最小割问题:求最大流
>最小割的划分:在求解完最大流之后所剩的那张残留网,对齐进行dfs,并且打上标记,能访问到的点就是最小割中S集合的点
>最小割的最小边数:
>1. 先跑一遍最大流,在残余网络中在遍历一遍正向边,流量跑满的边边权重新赋为1,否则为无穷大,再跑一遍最大流就是答案
>2. 将边权乘以一个大数并+1,一般是边数+1,跑出最大流后对这个数取模

### 费用流

![[Attachments/Images/Pasted image 20230318181405.png]]
![[Attachments/Images/Pasted image 20230318181737.png]]

```c
#define N 50000

struct edge {
    int v;
    int c;
    int w;
    int ne;
} e[2000000];

int h[N], idx = 1;
int d[N], mf[N], pre[N], vis[N];
int flow, cost;
int s, t;

void add(int a, int b, int c, int x) {
    e[++idx] = {b, c, x, h[a]};
    h[a] = idx;
}

bool spfa() {
    memset(d, 0x3f, sizeof d);//存的最小费用
    memset(mf, 0, sizeof mf);//存的最小流
    queue<int> q;
    q.push(s);
    d[s] = 0;
    mf[s] = 0x3f3f3f3f;
    vis[s] = 1;
    while (q.size()) {
        int u = q.front();
        q.pop();
        vis[u] = 0;
        for (int i = h[u]; i; i = e[i].ne) {
            int v = e[i].v;
            int c = e[i].c;
            int w = e[i].w;
            if (d[v] > d[u] + w && c) {//更新最小的费用
                d[v] = d[u] + w;
                mf[v] = min(mf[u], c);
                pre[v] = i;
                if (!vis[v]) {
                    q.push(v);
                    vis[v] = 1;
                }
            }
        }
    }
    return mf[t] > 0;//可行流能到达t点
}

void EK() {
    while (spfa()) {
        for (int v = t; v != s;) {
            int i = pre[v];
            e[i].c -= mf[t];
            e[i ^ 1].c += mf[t];
            v = e[i ^ 1].v;
        }
        flow += mf[t];
        cost += mf[t] * d[t];//累加费用
    }
}
```

## 3.10 二分图
### 二分图判定(染色法)

![[Attachments/Images/Pasted image 20230320102610.png]]
![[Attachments/Images/Pasted image 20230320102635.png]]


```c
#define N 10000  
struct edge {  
    int v;  
    int ne;  
} e[N];  
  
int h[N], idx;  
int color[N];  
  
void add(int a, int b) {  
    e[++idx] = {b, h[a]};  
    h[a] = idx;  
}  
  
bool dfs(int u, int c) {  
    color[u] = c;  
    for (int i = h[u]; i; i = e[i].ne) {  
        int v = e[i].v;  
        if (!color[v]) {  
            if (dfs(v, 3 - c)) {//染成另一种色,如果返回1说明有奇数环
                return 1;  
            }  
        } else if (color[v] == c) {//如果和他相连的边的颜色和他相同,那么就不符合二分图的定义  
            return 1;  
        }  
    }  
    return 0;  
}  
```

### 二分图最大匹配(Dinic)

![[Attachments/Images/Pasted image 20230320103819.png]]

```c
#define N 100000

int d[N];
int s, t;
int vis[N];
struct edge {
    int v;
    int c;
    int ne;
} e[2 * N];
int h[N], idx = 1;

bool bfs() {
    memset(d, 0, sizeof d);
    d[s] = 1;
    queue<int> q;
    q.push(s);
    vis[s] = 1;
    while (q.size()) {
        int cur = q.front();
        q.pop();
        for (int i = h[cur]; i; i = e[i].ne) {
            int v = e[i].v;
            if (d[v]==0 && e[i].c) {
                q.push(v);
                d[v] = d[cur] + 1;
                if (v == t) {//放在里面才行
                    return true;
                }
            }
        }
    }
    return false;
}

int cur[N];

int dfs(int u, int mf) {
    if (u == t) {
        return mf;
    }
    int sum = 0;
    for (int i = cur[u]; i; i = e[i].ne) {
        cur[u] = i;
        int v = e[i].v;
        if (d[v] == d[u] + 1 && e[i].c) {
            int f = dfs(v, min(mf, e[i].c));
            e[i].c -= f;
            e[i ^ 1].c += f;
            sum += f;
            mf -= f;
            if (mf == 0) {
                break;
            }
        }
    }
    if (sum == 0) {
        d[u] = 0;
    }
    return sum;
}

int dinic() {
    int flow = 0;
    while (bfs()) {
        memcpy(cur, h, sizeof h);
        flow += dfs(s, 1e9);
    }
    return flow;
}

void add(int a, int b, int c) {
    e[++idx] = {b, c, h[a]};
    h[a] = idx;
}


signed main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m, x;
    cin >> n >> m >> x;
    for (int i = 1; i <= x; i++) {
        int a, b;
        cin >> a >> b;
        add(a, b + n, 1);//每条正向边的边权都为1
        add(b + n, a, 0);//反向边的边权为0
    }
    s = 0;
    t = n + m + 1;
    for (int i = 1; i <= n; i++) {
        add(s, i, 1);//超级源点到左边点的距离为1
        add(i, s, 0);
    }
    for (int i = 1; i <= m; i++) {
        add(i + n, t, 1);//右边点到超级汇点的距离为1
        add(t, i + n, 0);
    }
    cout << dinic();
}
```

### 二分图最大匹配(匈牙利算法)

![[Attachments/Images/Pasted image 20230320103008.png]]
![[Attachments/Images/Pasted image 20230320103142.png]]

```c
#define M 100000
#define N 100000
int n, m, k, ans;
struct edge {
    int v;
    int ne;
} e[M];

int h[N], idx;
int vis[N], match[N];

void add(int a, int b) {
    e[++idx] = {b, h[a]};
    h[a] = idx;
}

bool dfs(int u) {
    for (int i = h[u]; i; i = e[i].ne) {//枚举每一个邻点
        int v = e[i].v;
        if (vis[v]) {
            continue;
        }
        vis[v] = 1;
        if (!match[v] || dfs(match[v])) {//如果这个点没有匹配或者匹配这个点的节点能够重新匹配新的点
            match[v] = u;//可以匹配
            return 1;//返回可以匹配的标志
        }
    }
    return 0;
}

signed main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    cin >> n >> m >> k;
    for (int i = 1; i <= k; i++) {
        int a, b;
        cin >> a >> b;//加一条单项边就可以了
        add(a, b);
    }
    for (int i = 1; i <= n; i++) {
        memset(vis, 0, sizeof vis);//所有的邻点都可以选
        if (dfs(i)) {
            ans++;
        }
    }
    cout << ans;
}
```

### 二分图最大权完美匹配

![[Attachments/Images/Pasted image 20230320111620.png]]

![[Attachments/Images/Pasted image 20230320112029.png]]

```c
#include<bits/stdc++.h>  
  
#define int long long  
#define double long double  
#define endl '\n'  
using namespace std;  
  
#define INF 0x3f3f3f3f  
#define N 1000  
int match[N];  
int va[N], vb[N];  
int la[N], lb[N];  
int w[N][N], d[N];  
  
int n;  
  
bool dfs(int x) {  
    va[x] = 1;//标记x在交替路中  
    for (int y = 1; y <= n; y++) {  
        if (!vb[y]) {  
            if (la[x] + lb[y] - w[x][y] == 0) {//如果是相等子图  
                vb[y] = 1;//y在交替路中  
                if (!match[y] || dfs(match[y])) {  
                    match[y] = x;//配对  
                    return 1;  
                }  
            } else {//不是相等子图就记录下最小的d[y]  
                d[y] = min(d[y], la[x] + lb[y] - w[x][y]);  
            }  
        }  
    }  
    return 0;  
}  
  
int KM() {  
    for (int i = 1; i <= n; i++) {  
        la[i] = -INF;  
    }  
    for (int i = 1; i <= n; i++) {  
        for (int j = 1; j <= n; j++) {  
            la[i] = max(la[i], w[i][j]);//左顶点值为最大边权  
        }  
    }  
    for (int i = 1; i <= n; i++) {  
        lb[i] = 0;//右顶点值为0  
    }  
    for (int i = 1; i <= n; i++) {  
        while (true) {  
            memset(va, 0, sizeof va);//每个点进去时状态重置  
            memset(vb, 0, sizeof vb);  
            for (int j = 1; j <= n; j++) {  
                d[j] = INF;  
            }  
            if (dfs(i)) {//如果成功配对,配对下一个  
                break;  
            }  
            int delta = INF;  
            for (int j = 1; j <= n; j++) {  
                if (!vb[j]) {  
                    delta = min(delta, d[j]);//如果没有成功配对,取出最小的delta  
                }  
            }  
            for (int j = 1; j <= n; j++) {  
                if (va[j]) {  
                    la[j] -= delta;//对每一个在交替路中的顶点值进行修改  
                }  
                if (vb[j]) {  
                    lb[j] += delta;  
                }  
            }  
        }  
    }  
    int res = 0;  
    for (int i = 1; i <= n; i++) {//累加结果  
        res += w[match[i]][i];  
    }  
    return res;  
}  
  
  
signed main() {  
    ios::sync_with_stdio(false);  
    cin.tie(nullptr);  
  
    cin >> n;  
    int m;  
    cin >> m;  
    for (int i = 1; i <= n; i++) {  
        for (int j = 1; j <= n; j++) {  
            w[i][j] = -INF;  
        }  
    }  
    for (int i = 1; i <= m; i++) {  
        int a, b, c;  
        cin >> a >> b >> c;  
        w[a][b] = c;  
    }  
    cout << KM() << endl;  
}
```

# 4. 树

## 4.1 并查集

![[Attachments/Images/Pasted image 20230323162719.png]]
![[Attachments/Images/Pasted image 20230323162739.png]]

```c
int fa[10005];  

int find(int i) {  
    if (fa[i] == i) {  
        return i;  
    } else {  
        return fa[i] = find(fa[i]);  
    }  
}   

void unionset(int x, int y) {  
    fa[find(x)] = find(y);  
}  
```

## 4.2 线段树

![[Attachments/Images/Pasted image 20230323162820.png]]
![[Attachments/Images/Pasted image 20230323162905.png]]
![[Attachments/Images/Pasted image 20230323162942.png]]

```c
#define lc p<<1  
#define rc (p<<1)+1  
#define N 100005  
  
int w[N];  
  
struct node {  
    int l, r, sum, add;  
} tr[4 * N];  
  
void pushup(int p) {  
    tr[p].sum = tr[lc].sum + tr[rc].sum;  
}  
  
void pushdown(int p) {//懒标记下发  
    if (tr[p].add) {  
        tr[lc].sum += tr[p].add * (tr[lc].r - tr[lc].l + 1);//左孩子区间加上  
        tr[rc].sum += tr[p].add * (tr[rc].r - tr[rc].l + 1);//右孩子区间加上  
        tr[lc].add += tr[p].add;//左孩子区间累加标记不下发  
        tr[rc].add += tr[p].add;//右孩子区间累加标记不下发  
        tr[p].add = 0;//父的标记清空  
    }  
}  
  
void build(int p, int l, int r) {  
    tr[p] = {l, r, w[l], 0};  
    if (l == r) {  
        return;  
    }  
    int m = (l + r) / 2;  
    build(lc, l, m);  
    build(rc, m + 1, r);  
    pushup(p);  
}  
  
void update(int p, int x, int y, int k) {  
    if (x <= tr[p].l && tr[p].r <= y) {//如果全部覆盖,打上标记,自己的sum更新  
        tr[p].sum += (tr[p].r - tr[p].l + 1) * k;  
        tr[p].add += k;  
        return;  
    }  
    int m = (tr[p].l + tr[p].r) / 2;//没有全部覆盖那就给子区间进行相同的操作  
    pushdown(p);//先下发标记  
    if (x <= m) {  
        update(lc, x, y, k);  
    }  
    if (y > m) {  
        update(rc, x, y, k);  
    }  
    pushup(p);//没有完全覆盖的情况下,在进行完子区间的操作之后自己还需要更新  
}  
  
int query(int p, int x, int y) {  
    if (x <= tr[p].l && tr[p].r <= y) {  
        return tr[p].sum;//如果覆盖了这个区间,那么这个区间全要,直接返回  
    }  
    int m = (tr[p].l + tr[p].r) / 2;//没有全部覆盖就找孩子区间继续往复  
    pushdown(p);  
    int sum = 0;  
    if (x <= m) {//如果左孩子的区间和查询区间有重叠  
        sum += query(lc, x, y);  
    }  
    if (y > m) {//如果右孩子的区间和查询区间有重叠  
        sum += query(rc, x, y);  
    }  
    return sum;  
}
```

## 4.3 二叉树

### 已知中序后序求层序

```c
int n;  
int a[100], b[100];  
int l[100], r[100];  
  
  
int getmid(int midl, int midr, int postl, int postr) {  
    if (midr < midl) {  
        return 0;  
    }  
    if (postr < postl) {  
        return 0;  
    }  
  
    int x = a[postr];//找到根  
  
    int index;//在中序遍历中找到根所在的下标  
    for (int i = midl; i <= midr; i++) {  
        if (b[i] == x) {  
            index = i;  
            break;  
        }  
    }  
  
    int cnt = index - midl;  
    int left = getmid(midl, index - 1, postl, postl + cnt - 1);//递归左边,返回左边的根  
    l[x] = left;  
  
    cnt = midr - index;  
    int right = getmid(index + 1, midr, postr - cnt, postr - 1);//递归右边,返回右边的根  
    r[x] = right;  
  
    return x;  
}  
  
  
signed main() {  
    ios::sync_with_stdio(false);  
    cin.tie(nullptr);  
    cin >> n;  
    for (int i = 1; i <= n; i++) {  
        cin >> a[i];  
    }  
    for (int i = 1; i <= n; i++) {  
        cin >> b[i];  
    }  
  
    getmid(1, n, 1, n);  
    queue<int> q;  
    q.push(a[n]);  
    while (q.size()) {  
        int cur = q.front();  
        q.pop();  
        if (l[cur]) {  
            q.push(l[cur]);  
        }  
        if (r[cur]) {  
            q.push(r[cur]);  
        }  
        cout << cur << " ";  
    }  
}
```

# 5. 数论

## 5.1 高精度

### 加法

![[Attachments/Images/Pasted image 20230320112608.png]]

```c
const int N = 100005;  
int A[N], B[N], C[N];  
int la, lb, lc;  
  
void add() {  
    for (int i = 0; i < lc; i++) {  
        C[i] += A[i] + B[i];  
        C[i + 1] += C[i] / 10;  
        C[i] %= 10;  
    }  
    if (C[lc]) lc++;  
}  
  
signed main() {  
    ios::sync_with_stdio(false);  
    cin.tie(nullptr);  
  
    string a, b;  
    cin >> a >> b;  
    la = a.size();  
    lb = b.size();  
    lc = max(la, lb);  
    for (int i = la - 1; i >= 0; i--) {  
        A[la - 1 - i] = a[i] - '0';  
    }  
    for (int i = lb - 1; i >= 0; i--) {  
        B[la - 1 - i] = b[i] - '0';  
    }  
    add();  
    for (int i = lc - 1; i >= 0; i--) {  
        cout << C[i];  
    }  
}
```

### 减法

![[Attachments/Images/Pasted image 20230320113113.png]]

```c
const int N = 100005;  
int A[N], B[N], C[N];  
int la, lb, lc;  
  
bool cmp() {  //比较大小,确定符号与被减数
    if (la != lb) {  
        return la > lb;  
    }  
    for (int i = la - 1; i >= 0; i--) {  
        if (A[i] != B[i]) {  
            return A[i] > B[i];  
        }  
    }  
    return true;  
}  
  
void sub() {  
    for (int i = 0; i < lc; i++) {  
        if (A[i] < B[i]) {  
            A[i + 1]--;  
            A[i] += 10;  
        }  
        C[i] = A[i] - B[i];  
    }  
}  
  
  
signed main() {  
    ios::sync_with_stdio(false);  
    cin.tie(nullptr);  
  
    string a, b;  
    cin >> a >> b;  
    la = a.size();  
    lb = b.size();  
    lc = max(la, lb);  
    for (int i = la - 1; i >= 0; i--) {  
        A[la - 1 - i] = a[i] - '0';  
    }  
    for (int i = lb - 1; i >= 0; i--) {  
        B[la - 1 - i] = b[i] - '0';  
    }  
    if (!cmp()) {  
        swap(A, B);  
        cout << '-';  
    }  
    sub();  
    for (int i = lc - 1; i >= 0; i--) {  
        cout << C[i];  
    }  
}
```

### 乘法

![[Attachments/Images/Pasted image 20230320113819.png]]


```c
const int N = 100005;  
int A[N], B[N], C[N];  
int la, lb, lc;  
  
void mul() {  
    for (int i = 0; i < la; i++) {  
        for (int j = 0; j < lb; j++) {  
            C[i + j] += A[i] * B[j];  
            C[i + j + 1] += C[i + j] / 10;  
            C[i + j] %= 10;  
        }  
    }  
    while (lc && C[lc] == 0) {  
        lc--;  
    }  
}  
  
signed main() {  
    ios::sync_with_stdio(false);  
    cin.tie(nullptr);  
  
    string a, b;  
    cin >> a >> b;  
    la = a.size();  
    lb = b.size();  
    lc = la + lb;  
    for (int i = la - 1; i >= 0; i--) {  
        A[la - 1 - i] = a[i] - '0';  
    }  
    for (int i = lb - 1; i >= 0; i--) {  
        B[la - 1 - i] = b[i] - '0';  
    }  
    mul();  
    for (int i = lc; i >= 0; i--) {  
        cout << C[i];  
    }  
}
```

### 除法

![[Attachments/Images/Pasted image 20230320113943.png]]

```c
const int N = 100005;  
int A[N], C[N];  
int la, lc;  
  
void div(int b) {  
    int r = 0;  
    for (int i = la - 1; i >= 0; i--) {  
        r = r * 10 + A[i];  
        C[la - 1 - i] = r / b;  
        r %= b;  
    }  
    reverse(C, C + lc);  
    while (lc && C[lc] == 0) {  
        lc--;  
    }  
}  
  
signed main() {  
    ios::sync_with_stdio(false);  
    cin.tie(nullptr);  
  
    string a;  
    int b;  
    cin >> a >> b;  
    lc = la = a.size();  
  
    for (int i = la - 1; i >= 0; i--) {  
        A[la - 1 - i] = a[i] - '0';  
    }  
    div(b);  
  
    for (int i = lc; i >= 0; i--) {  
        cout << C[i];  
    }  
  
}
```

## 5.2 快速幂

![[Attachments/Images/Pasted image 20230320115540.png]]

```c
int quickpow(int a, int n, int p) {  
    int res = 1;  
    while (n) {  
        if (n & 1) {  
            res = res * a % p;
        }  
        a = a * a % p; 
        n >>= 1;  
    }  
    return res;  
}
```

## 5.3 最大公约数(欧几里得算法)

![[Attachments/Images/Pasted image 20230320131422.png]]

```c
int gcd(int a, int b) {  
    return b == 0 ? a : gcd(b, a % b);  
}
```

>最小公倍数就是a*b/gcd(a,b)

## 5.4 不定方程(裴蜀定理, 扩展欧几里得算法)

![[Attachments/Images/Pasted image 20230320144033.png]]
![[Attachments/Images/Pasted image 20230320140530.png]]
![[Attachments/Images/Pasted image 20230320140931.png]]
![[Attachments/Images/Pasted image 20230320141453.png]]

```c
int gcd(int a, int b) {  
    return b == 0 ? a : gcd(b, a % b);  
}  
  
int exgcd(int a, int b, int &x, int &y) {  
    if (b == 0) {  //b=0时达到递归边界
        x = 1;  
        y = 0;  
        return a;  
    }  
    int x1, y1, d;  
    d = exgcd(b, a % b, x1, y1);  
    x = y1;  
    y = x1 - a / b * y1;  
    return d;  
}
```

## 5.5 质数

### 判定质数(试除法)

![[Attachments/Images/Pasted image 20230320131502.png]]

```c
bool is_prime(int x) {  
    if (x == 1) {  
        return 0;  
    }  
    for (int i = 2; i <= sqrt(x) ; i++) {  
        if (x % i == 0) {  
            return 0;  
        }  
    }  
    return 1;  
}
```

### 判定质数(埃氏筛法)

![[Attachments/Images/Pasted image 20230320132602.png]]

```c
const int N = 10000;  
int vis[N];  
int prim[N];  
int cnt;  
  
void Eratosthenes(int n) {  
    for (int i = 2; i <= n; i++) {  
        if (!vis[i]) {  
            prim[++cnt] = i;  
            for (int j = i * i; j <= n; j += i) {  
                vis[j] = 1;  
            }  
        }  
    }  
}
```

### 判定质数(欧拉筛)

![[Attachments/Images/Pasted image 20230320132707.png]]


```c
const int N = 10000;  
int vis[N];  
int prim[N];  
int cnt;  
  
void get_prim(int n) {  
    for (int i = 2; i <= n; i++) {  
        if (!vis[i]) {  
            prim[++cnt] = i;  
        }  
        for (int j = 1; i * prim[j] <= n; j++) {  //越界中断
            vis[i * prim[j]] = 1;  //标记不是质数
            if (i % prim[j] == 0) { //如果是最小的质数,中断  
                break;  
            }  
        }  
    }  
}
```

### 判定单个质数(威尔逊定理)

![[Attachments/Images/Pasted image 20230320140022.png]]
![[Attachments/Images/Pasted image 20230320140255.png]]

### 分解质因数(唯一分解定理)

![[Attachments/Images/Pasted image 20230320132442.png]]

```c
int a[100];  
  
void decompose(int x) {  
    for (int i = 2; i <= sqrt(x); i++) {  
        while (x % i == 0) {  
            a[i]++;  
            x /= i;  //除尽,下次循环能碰到的就是质数
        }  
    }  
    if (x > 1) {  
        a[x]++;  
    }  
}
```

### 欧拉函数

![[Attachments/Images/Pasted image 20230320132908.png]]

```c
int phi(int n){
    int res = n;  
    for (int i = 2; i * i <= n; i++) {  
        if (n % i == 0) {  
            res = res / i * (i - 1);  //如果是质数
            while (n % i == 0) {  //除尽
                n /= i;  
            }  
        }  
    }  
    if (n > 1) {  //剩余的数就是最后一个质数
        res = res / n * (n - 1);  
    }  
    return res;  
}
```

![[Attachments/Images/Pasted image 20230320133932.png]]

```c
const int N = 10000;  
int p[N], vis[N], cnt;  
int phi[N];  
  
void get_phi(int n) {  
    phi[1] = 1;  
    for (int i = 2; i <= n; i++) {  
        if (!vis[i]) {  
            p[cnt++] = i;  
            phi[i] = i - 1;  //质数的欧拉函数
        }  
        for (int j = 0; i * p[j] <= n; j++) {  
            int m = i * p[j];  
            vis[m] = 1;  
            if (i % p[j] == 0) {  //如果能整除
                phi[m] = p[j] * phi[i];  
                break;  
            } else {  
                phi[m] = (p[j] - 1) * phi[i];  //不能整除
            }  
        }  
    }  
}
```

### 莫比乌斯函数

![[Attachments/Images/Pasted image 20230320134644.png]]

```c
const int N = 1000010;  
int p[N], vis[N], cnt;  
int mu[N];  
  
void get_mu(int n) {  
    mu[1] = 1;  
    for (int i = 2; i <= n; i++) {  
        if (!vis[i]) {  
            p[++cnt] = i;  
            mu[i] = -1;  
        }  
        for (int j = 1; i * p[j] <= n; j++) {  
            int m = i * p[j];  
            vis[m] = 1;  
            if (i % p[j] == 0) {  
                mu[m] = 0;  
                break;  
            } else {  
                mu[m] = -mu[i];  
            }  
        }  
    }  
}
```

### 求数的超大幂次(欧拉定理)

![[Attachments/Images/Pasted image 20230320135733.png]]
![[Attachments/Images/Pasted image 20230320135658.png]]

```c
string s;//b  
  
int get_phi(int m) {//筛法求欧拉函数  
    int res = m;  
    for (int i = 2; i * i <= m; i++) {  
        if (m % i == 0) {  
            res = res / i * (i - 1);  
            while (m % i == 0) {  
                m /= i;  
            }  
        }  
    }  
    if (m > 1) {  
        res = res / m * (m - 1);  
    }  
    return res;  
}  
  
int depow(int phi) {//降幂  
    bool flag = 0;  
    int b = 0;  
    for (int i = 0; i < s.length(); i++) {  
        b = b * 10 + (s[i] - '0');  
        if (b >= phi) {  
            flag = 1;  
            b %= phi;//大数的取模  
        }  
    }  
    if (flag) {  //如果前面降过幂,就要加上
        b += phi;  
    }  
    return b;  
}  
  
int quickpow(int a, int b, int p) {  
    int res = 1;  
    while (b) {  
        if (b & 1) {  
            res = res * a % p;  
        }  
        a = a * a % p;  
        b >>= 1;  
    }  
    return res;  
}  
  
signed main() {  
    ios::sync_with_stdio(false);  
    cin.tie(nullptr);  
  
    int a, m;  
  
    cin >> a >> m >> s;  
  
    int oulam = get_phi(m);  
  
    int b = depow(oulam);  
  
    cout << quickpow(a, b, m);  
}
```

### 大数素性判断Miller Rabin

```c++
ll qmul(ll a,ll b,ll mod)//快速乘
{
    ll c=(ld)a/mod*b;
    ll res=(ull)a*b-(ull)c*mod;
    return (res+mod)%mod;
}
ll qpow(ll a,ll n,ll mod)//快速幂
{
    ll res=1;
    while(n)
    {
        if(n&1) res=qmul(res,a,mod);
        a=qmul(a,a,mod);
        n>>=1;
    }
    return res;
}
bool MRtest(ll n)//Miller Rabin Test
{
    if(n<3||n%2==0) return n==2;//特判
    ll u=n-1,t=0;
    while(u%2==0) u/=2,++t;
    ll ud[]={2,325,9375,28178,450775,9780504,1795265022};
    for(ll a:ud)
    {
        ll v=qpow(a,u,n);
        if(v==1||v==n-1||v==0) continue;
        for(int j=1;j<=t;j++)
        {
            v=qmul(v,v,n);
            if(v==n-1&&j!=t){v=1;break;}//出现一个n-1，后面都是1，直接跳出
            if(v==1) return 0;//这里代表前面没有出现n-1这个解，二次检验失败
        }
        if(v!=1) return 0;//Fermat检验
    }
    return 1;
}
```

### Pollard-Rho

![[Attachments/Images/Pasted image 20230727222335.png]]

```c++
#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

int t;
long long max_factor, n;

long long gcd(long long a, long long b) {
  if (b == 0) return a;
  return gcd(b, a % b);
}

long long quick_pow(long long x, long long p, long long mod) {  // 快速幂
  long long ans = 1;
  while (p) {
    if (p & 1) ans = (__int128)ans * x % mod;
    x = (__int128)x * x % mod;
    p >>= 1;
  }
  return ans;
}

bool Miller_Rabin(long long p) {  // 判断素数
  if (p < 2) return 0;
  if (p == 2) return 1;
  if (p == 3) return 1;
  long long d = p - 1, r = 0;
  while (!(d & 1)) ++r, d >>= 1;  // 将d处理为奇数
  for (long long k = 0; k < 10; ++k) {
    long long a = rand() % (p - 2) + 2;
    long long x = quick_pow(a, d, p);
    if (x == 1 || x == p - 1) continue;
    for (int i = 0; i < r - 1; ++i) {
      x = (__int128)x * x % p;
      if (x == p - 1) break;
    }
    if (x != p - 1) return 0;
  }
  return 1;
}

long long Pollard_Rho(long long x) {
  long long s = 0, t = 0;
  long long c = (long long)rand() % (x - 1) + 1;
  int step = 0, goal = 1;
  long long val = 1;
  for (goal = 1;; goal *= 2, s = t, val = 1) {  // 倍增优化
    for (step = 1; step <= goal; ++step) {
      t = ((__int128)t * t + c) % x;
      val = (__int128)val * abs(t - s) % x;
      if ((step % 127) == 0) {
        long long d = gcd(val, x);
        if (d > 1) return d;
      }
    }
    long long d = gcd(val, x);
    if (d > 1) return d;
  }
}

void fac(long long x) {
  if (x <= max_factor || x < 2) return;
  if (Miller_Rabin(x)) {              // 如果x为质数
    max_factor = max(max_factor, x);  // 更新答案
    return;
  }
  long long p = x;
  while (p >= x) p = Pollard_Rho(x);  // 使用该算法
  while ((x % p) == 0) x /= p;
  fac(x), fac(p);  // 继续向下分解x和p
}

int main() {
  scanf("%d", &t);
  while (t--) {
    srand((unsigned)time(NULL));
    max_factor = 0;
    scanf("%lld", &n);
    fac(n);
    if (max_factor == n)  // 最大的质因数即自己
      printf("Prime\n");
    else
      printf("%lld\n", max_factor);
  }
  return 0;
}
```
## 5.6 乘法逆元

### 费马小定理

![[Attachments/Images/Pasted image 20230320135401.png]]

### 扩展欧几里得算法

![[Attachments/Images/Pasted image 20230320141612.png]]
![[Attachments/Images/Pasted image 20230320141731.png]]

## 5.7 矩阵

### 矩阵快速幂

![[Attachments/Images/Pasted image 20230320115751.png]]

### 矩阵加速递推

![[Attachments/Images/Pasted image 20230320131253.png]]

### 求解线性方程组(高斯消元法)

![[Attachments/Images/Pasted image 20230321105012.png]]
![[Attachments/Images/Pasted image 20230321105207.png]]

```c
int n;  
int a[100][100];  
  
bool gauss() {  
    for (int i = 1; i <= n; i++) {//枚举列  
        int r = i;  
        for (int k = i; k <= n; k++) {//找到非0行  
            if (a[k][i] != 0) {  
                r = k;  
                break;  
            }  
        }  
        if (r != i) {  
            swap(a[r], a[i]);  
        }  
        if (a[i][i] == 0) {  
            return 0;  
        }  
        for (int j = n + 1; j >= i; j--) {//变为1  
            a[i][j] /= a[i][i];  
        }  
        for (int k = i + 1; k <= n; k++) {//变为0  
            for (int j = n + 1; j >= i; j--) {  
                a[k][j] -= a[k][i] * a[i][j];  
            }  
        }  
    }  
    for (int i = n - 1; i >= 1; i--) {//回代求解  
        for (int j = i + 1; j <= n; j++) {  
            a[i][n + 1] -= a[i][j] * a[j][n + 1];  
        }  
    }  
    return 1;  
}
```

### 求解线性方程组(高斯约旦消元法)

![[Attachments/Images/Pasted image 20230321105317.png]]

```c
bool Gauss_Jordan() {  
    for (int i = 1; i <= n; i++) {//枚举列  
        int r = i;  
        for (int k = i; k <= n; k++) {//找到非0行  
            if (a[k][i] != 0) {  
                r = k;  
                break;  
            }  
        }  
        if (r != i) {  
            swap(a[r], a[i]);  
        }  
        if (a[i][i] == 0) {  
            return 0;  
        }  
        for (int k = 1; k <= n; k++) {//对角化,枚举行  
            if (k == i) {  
                continue;  
            }  
            double t = a[k][i] / a[i][i];  
            for (int j = i; j <= n + 1; j++) {//枚举列  
                a[k][j] -= t * a[i][j];  
            }  
        }  
    }  
    for (int i = 1; i <= n; i++) {//除一下  
        a[i][n + 1] /= a[i][i];  
    }  
    return 1;  
}
```

### 矩阵求逆(高斯约旦消元法)

![[Attachments/Images/Pasted image 20230321105406.png]]


![[Attachments/Images/Pasted image 20230321105430.png]]

## 5.8 组合数

### 求小组合数(递推法)

![[Attachments/Images/Pasted image 20230321105540.png]]

```c
void getc() {  
    for (int i = 0; i < n; i++) {  
        for (int j = 0; j <= i; j++) {  
            if (j == 0) {  
                c[i][j] = 1;  
            } else {  
                c[i][j] = c[i - 1][j] + c[i - 1][j - 1];  
            }  
        }  
    }  
}
```

### 求大组合数(高精度,线性筛,唯一分解定理)

![[Attachments/Images/Pasted image 20230321110246.png]]


```c
int get(int n, int p) {//n!中p的个数  
    int s = 0;  
    while (n) {  
        s += n / p;  
        n /= p;  
    }  
    return s;  
}  
  
int getc(int n, int m, int p) {//c中的p的个数  
    return get(n, p) - get(m, p) - get(n - m, p);  
}  
  
void mul(int c[], int p, int &len) {//高精度  
    int t = 0;  
    for (int i = 0; i < len; i++) {//高精度乘低精度  
        t += c[i] * p;  
        c[i] = t % 10;  
        t /= 10;  
    }  
    while (t) {  
        c[len++] = t % 10;  
        t /= 10;  
    }  
}  
  
int prim[100], cnt, vis[100];  
  
void get_prim(int n) {//获取1-n的所有的质数  
    for (int i = 2; i <= n; i++) {  
        if (!vis[i]) {  
            prim[cnt++] = i;  
            for (int j = i * i; j <= n; j += i) {  
                vis[j] = 1;  
            }  
        }  
    }  
}  
  
signed main() {  
    ios::sync_with_stdio(false);  
    cin.tie(nullptr);  
  
    int c[100], len = 1;  
    c[0] = 1;  
    int n, m;  
    cin >> n >> m;  
    get_prim(n);  
    for (int i = 0; i < cnt; i++) {//枚举质数  
        int p = prim[i];  
        int s = getc(n, m, p);  
        while (s--) {  
            mul(c, p, len);  
        }  
    }  
    for (int i = len - 1; i >= 0; i--) {  
        cout << c[i];  
    }  
}
```

### 求组合数取模

>n,m与p均互质且p是质数才能使用这种方法

![[Attachments/Images/Pasted image 20230321105708.png]]


```c
int quickpow(int a, int b,int p) {  
    int res = 1;  
    while (b) {  
        if (b & 1) {  
            res = res * a % p;
        }  
        a = a * a % p;
        b >>= 1;  
    }  
    return res;  
}  

void init(int n,int p) {  
    f[0] = g[0] = 1;  
    for (int i = 1; i <= n; i++) {  
        f[i] = f[i - 1] * i % p;  
        g[i] = g[i - 1] * quickpow(i, p - 2) % p;  
    }  
}

int getc(int n,int m,int p) {  
    return f[n] * g[m] % p * g[n - m] % p;  
}
```

### 求大组合数取模(卢卡斯定理)

>不用保证n,m与p互质,只需要p是质数

![[Attachments/Images/Pasted image 20230321110016.png]]

```c
int f[100001], g[100001];  
  
int quickpow(int a, int b, int p) {  
    int res = 1;  
    while (b) {  
        if (b & 1) {  
            res = res * a % p;  
        }  
        a = a * a % p;  
        b >>= 1;  
    }  
    return res;  
}  
  
void init(int p) {  
    f[0] = g[0] = 1;  
    for (int i = 1; i <= p; i++) {//对p取模一定比p小  
        f[i] = f[i - 1] * i % p;  
        g[i] = g[i - 1] * quickpow(i, p - 2, p) % p;  
    }  
}  
  
int getc(int n, int m, int p) {  
    return f[n] * g[m] % p * g[n - m] % p;  
}  
  
int lucas(int n, int m, int p) {  
    if (m == 0) {  
        return 1;  
    }  
    return lucas(n / p, m / p, p) * getc(n % p, m % p, p) % p;  
}
```

### 求线性不定方程的整数解的组数(隔板法)

![[Attachments/Images/Pasted image 20230309223927.png|]]

## 5.9 容斥原理

### 集合的并(拥有至少一种性质)

![[Attachments/Images/Pasted image 20230321111217.png]]
![[Attachments/Images/Pasted image 20230321111238.png]]

```c
int n, m, prim[20];  
  
int calc() {  
    int res = 0;  
    for (int i = 1; i < 1 << m; i++) {//枚举所有的状态  
        int t = 1, sign = -1;//控制符号,具体问题具体分析  
        for (int j = 0; j < m; j++) {//枚举每一个具体的状态  
            if (i & 1 << j) {//如果这个状态被选中  
                if (t * prim[j] > n) {//当前乘积大于了n,没有意义  
                    t = 0;  
                    break;  
                }  
                t *= prim[j];//已选质数的积  
                sign = -sign;  
            }  
        }  
        if (t) {//交集的和  
            res += n / t * sign;  
        }  
    }  
    return res;  
}
```

### 集合的交(拥有所有性质)

![[Attachments/Images/Pasted image 20230321111430.png]]

## 5.10 卡特兰数

![[Attachments/Images/Pasted image 20230321111754.png]]
![[Attachments/Images/Pasted image 20230321111813.png]]

## 5.11 整除分块[n/i]

![[Attachments/Images/Pasted image 20230321111907.png]]

## 5.12 生成函数

### 多重集的组合问题(普通生成函数)

![[Attachments/Images/Pasted image 20230321112014.png]]
![[Attachments/Images/Pasted image 20230321112034.png]]
![[Attachments/Images/Pasted image 20230321112053.png]]

```c
int n, m;  
int a[100], b[100], c[100], d[100];  
  
int calc() {  
    for (int i = 0; i <= m; i++) {//清空所有的系数数组  
        c[i] = d[i] = 0;  
    }  
    for (int i = a[1]; i <= b[1]; i++) {//填充第一项的系数  
        c[i] = 1;  
    }  
    for (int i = 2; i <= n; i++) {//从第二项开始枚举每一项  
        for (int j = 0; j <= m; j++) {//用之前的系数乘现在这一项的各个系数  
            for (int k = a[i]; k <= b[i]; k++) {  
                d[j + k] += c[j];  
            }  
        }  
        for (int j = 0; j <= m; j++) {//转存c,清空d  
            c[j] = d[j];  
            d[j] = 0;  
        }  
    }  
    return c[m];  
}
```

### 多重集的排列问题(指数生成函数)

![[Attachments/Images/Pasted image 20230321112249.png]]
![[Attachments/Images/Pasted image 20230321112306.png]]

```c
void init() {//求阶乘  
    fac[0] = fac[1] = 1;  
    for (int i = 2; i <= 11; i++) {  
        fac[i] = fac[i - 1] * i;  
    }  
}  
  
double calc() {  
    for (int i = 0; i <= m; i++) {  
        c[i] = d[i] = 0;  
    }  
    for (int i = 0; i <= a[1]; i++) {//第一项的系数  
        c[i] = 1.0 / fac[i];  
    }  
    for (int i = 2; i <= n; i++) {//往后枚举每一项  
        for (int j = 0; j <= m; j++) {  
            for (int k = 0; k <= a[i]; k++) {  
                d[j + k] += c[j] / fac[k];  
            }  
        }  
        for (int j = 0; j <= m; j++) {  
            c[j] = d[j];  
            d[j] = 0;  
        }  
    }  
    return c[m] * fac[m];  
}
```

### 生成函数的应用

![[Attachments/Images/Pasted image 20230321113213.png]]

## 约数

### 约数个数

![[Attachments/Images/Pasted image 20230320134230.png]]

```c
const int N = 1000010;  
int p[N], vis[N], cnt;  
int a[N];//a[i]记录i的最小质因子的次数  
int d[N];//d[i]记录i的约数个数  
  
void get_d(int n) {//筛法求约数个数  
    d[1] = 1;  
    for (int i = 2; i <= n; i++) {  
        if (!vis[i]) {  
            p[++cnt] = i;  
            a[i] = 1;  
            d[i] = 2;  
        }  
        for (int j = 1; i * p[j] <= n; j++) {  
            int m = i * p[j];  
            vis[m] = 1;  
            if (i % p[j] == 0) {  
                a[m] = a[i] + 1;  
                d[m] = d[i] / a[m] * (a[m] + 1);  
                break;  
            } else {  
                a[m] = 1;  
                d[m] = d[i] * 2;  
            }  
        }  
    }  
}
```

### 筛法求约数和

![[Attachments/Images/Pasted image 20230320134441.png]]

```c
const int N = 1000010;  
int p[N], vis[N], cnt;  
//g[i]表示i的最小质因子的1+p^1+...+p^k  
int g[N], f[N];//f[i]表示i的约数和  
void get_f(int n) {  
    g[1] = f[1] = 1;  
    for (int i = 2; i <= n; i++) {  
        if (!vis[i]) {  
            p[++cnt] = i;  
            g[i] = f[i] = i + 1;  
        }  
        for (int j = 1; i * p[j] <= n; j++) {  
            int m = i * p[j];  
            vis[m] = 1;  
            if (i % p[j] == 0) {  
                g[m] = g[i] * p[j] + 1;  
                f[m] = f[i] / g[i] * g[m];  
                break;  
            } else {  
                g[m] = p[j] + 1;  
                f[m] = f[i] * g[m];  
            }  
        }  
    }  
}
```

## 线性同余方程组

### 中国剩余定理

![[Attachments/Images/Pasted image 20230320143122.png]]
![[Attachments/Images/Pasted image 20230320143249.png]]

```c
int n;  
  
int exgcd(int a, int b, int &x, int &y) {  
    if (b == 0) {  
        x = 1;  
        y = 0;  
        return b;  
    }  
    int d, x1, y1;  
    d = exgcd(b, a % b, x1, y1);  
    x = y1;  
    y = x1 - a / b * y1;  
    return d;  
}  
  
  
int crt(int m[], int r[]) {  
    int M = 1, ans = 0;  //计算所有模数的积
    for (int i = 1; i <= n; i++) {  
        M *= m[i];  
    }  
    for (int i = 1; i <= n; i++) {  
        int c = M / m[i];  
        int x, y;  
        exgcd(c, m[i], x, y);  //计算逆元
        ans = (ans + r[i] * c * x % M) % M;  //累加表达式
    }  
    return (ans % M + M) % M;  
}
```

### 扩展中国剩余定理

![[Attachments/Images/Pasted image 20230320143517.png]]
 ![[Attachments/Images/Pasted image 20230320143926.png]]
 
```c
int n;  
int m[100005], r[100005];  
  
int exgcd(int a, int b, int &x, int &y) {  
    if (b == 0) {  
        x = 1;  
        y = 0;  
        return a;  
    }  
    int d, x1, y1;  
    d = exgcd(b, a % b, x1, y1);  
    x = y1;  
    y = x1 - a / b * y1;  
    return d;  
}  
  
int mul(int a, int b, int p) {  //龟速乘
    int ans = 0;  
    while (b) {  
        if (b & 1) {  
            ans = (ans + a) % p;  
        }  
        a = (a + a) % p;  
        b >>= 1;  
    }  
    return (ans % p + p) % p;  
}  
  
int excrt(int m[], int r[]) {  
    int m1, m2, r1, r2, p, q;  
    m1 = m[1], r1 = r[1];  
    for (int i = 2; i <= n; i++) {  
        m2 = m[i];  
        r2 = r[i];  
        int d = exgcd(m1, m2, p, q);  
        int c = m2 / d;  //根据p的通解得到的取余数
        if ((r2 - r1) % d != 0) {  
            return -1;  
        }  
        p = (p % c + c) % c;//转化为最小的正数  
        p = mul((r2 - r1) / d, p, c);  //龟速乘防止溢出
  
        r1 = m1 * p + r1;  //更新r1和m1
        m1 = m1 / d * m2;  //千万小心溢出!
    }  
    return (r1 % m1 + m1) % m1;  
}  
  
signed main() {  
    ios::sync_with_stdio(false);  
    cin.tie(nullptr);  
    cin >> n;  
    for (int i = 1; i <= n; i++) {  
        cin >> m[i] >> r[i];  
    }  
    cout << excrt(m, r) << endl;  
}
```

## 高次同余方程

### BSGS

![[Attachments/Images/Pasted image 20230321102955.png]]
![[Attachments/Images/Pasted image 20230321103717.png]]
### 扩展BSGS算法

![[Attachments/Images/Pasted image 20230321104403.png]]
![[Attachments/Images/Pasted image 20230321104425.png]]

# 6. 杂

## priority_queue

priority_queue<Type, Container, Functional>

Type 就是数据类型，Container 就是容器类型（Container必须是用数组实现的容器，比如vector,deque等等，但不能用 list。STL里面默认用的是vector），Functional 就是比较的方式，当需要用自定义的数据类型时才需要传入这三个参数，使用基本数据类型时，只需要传入数据类型，默认是大顶堆 )

```c
//小根堆
priority_queue <int,vector<int>,greater<int> > q;
//大根堆
priority_queue <int,vector<int>,less<int> >q;
//对于基础类型 默认是大顶堆 
priority_queue<int> a;
//pari先比较第一个再比较第二个,默认大顶堆
priority_queue<pair<int, int> > a
//对于自定义的类型,可以通过重载运算符或者传入比较函数
```

## 排序

```c
struct stu {  
    int score;  
  
    bool operator<(const stu &s) {  
        return this->score < s.score;  
    }  
};  
  
bool cmp(stu a, stu b) {  
    return a.score < b.score;  
}
```

## __int128

10^38

```c
  
void scan(__int128 &x)//输入  
{  
    x = 0;  
    int f = 1;  
    char ch;  
    if ((ch = getchar()) == '-') f = -f;  
    else x = x * 10 + ch - '0';  
    while ((ch = getchar()) >= '0' && ch <= '9')  
        x = x * 10 + ch - '0';  
    x *= f;  
}  
  
void print(__int128 x) {  
    if (x < 0) {  
        x = -x;  
        putchar('-');  
    }  
    if (x > 9) print(x / 10);  
    putchar(x % 10 + '0');  
}
```

## 单调栈

![[Attachments/Images/Pasted image 20230327150548.png]]

>找到右边第一个比这个数大的数的下标

```c
for (int i = 1; i <= n; i++) {  
    while (s.size() && arr[s.top()] < arr[i]) { //栈顶的值如果小于当前值,那么一直出列并且得出结果
        res[s.top()] = i;  
        s.pop();  
    }  
    s.push(i);  
}  
while (s.size()) {  //最后的那批数,右边没有比他们大的
    res[s.top()] = 0;  
    s.pop();  
}
```

## 树状数组

![[Attachments/Images/Pasted image 20230327153445.png]]
![[Attachments/Images/Pasted image 20230327155156.png]]
![[Attachments/Images/Pasted image 20230327155210.png]]

![[Attachments/Images/Pasted image 20230327153515.png]]

```c
int arr[N], c[N];  
int n;  
  
int lowbit(int i) {  
    return i & (-i);  
}  
  
void update(int i, int x) {  
    for (; i <= n; i += lowbit(i)) {  
        c[i] += x;  
    }  
}  
  
int presum(int i) {  
    int sum = 0;  
    for (; i >= 1; i -= lowbit(i)) {  
        sum += c[i];  
    }  
    return sum;  
}  
  
int query(int l, int r) {  
    return presum(r) - presum(l - 1);  
}  
  
signed main() {  
    int m;  
    cin >> n >> m;  
    for (int i = 1; i <= n; i++) {  
        cin >> arr[i];  
        update(i, arr[i]);  
    }  
  
    for (int i = 1; i <= m; i++) {  
        int a, b, d;  
        cin >> a >> b >> d;  
        if (a == 1) {  
            update(b, d);  
        } else {  
            cout << query(b, d) << endl;  
        }  
    }  
}
```

## 滑动窗口最大值

>给定一个长度为n的数组和一个大小为m的滑动窗口(0<m<=n),请找出所有滑动窗口里的最大值

![[Attachments/Images/Pasted image 20230305103519.png]]

```c++
int h = 0, t = -1;  
for (int i = 1; i <= n; i++) {  
	cin >> nums[i];  
	if (h <= t && i - q[h] > m - 1) {//队头元素不在窗口内
		h++;  
	}  
	while (h <= t && nums[i] >= nums[q[t]]) {//如果更大,依次出队
		t--;  
	}  
	q[++t] = i;  
	if (i > m - 1) {  
		cout << nums[q[h]];  
	}  
}  
```


![[Attachments/Images/Pasted image 20230811223622.png]]
