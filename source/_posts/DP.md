---
title: 动态规划--背包问题
date: 2023-07-09 14:40:59
tags: 
  - '算法'
  - '动态规划'
  - '背包问题'
description: '背包问题详解，包括01背包，完全背包，分组背包，多重背包的解法'
categories: '算法分析'
cover: "./img/cover1.png"
---

# 动态规划之背包问题
### 0-1背包问题
有 N 件物品和一个容量是 V 的背包。每件物品只能使用一次。
二维

```cpp
#include<iostream>
#include<algorithm>

using namespace std;

const int N = 1010;

int n,m;
int v[N],w[N];
int f[N][N]; 

int main(){
    scanf("%d%d",&n,&m);
    for(int i=1;i<=n;i++) scanf("%d%d",&v[i],&w[i]);
    //这里i从1开始取，因为i=0时也就是0个物品可选肯定是0
    for(int i=1;i<=n;i++){//必须有等号，因为要能够选择前n个物品，且容量为m
        for(int j=0;j<=m;j++){
            f[i][j]=f[i-1][j];
             //注意要判断第i个物品能不能选
            if(j>=v[i]) f[i][j]=max(f[i][j],f[i-1][j-v[i]]+w[i]);
        }
    }
    printf("%d",f[n][m]);
    
    return 0;
}
```
二维转化成一维
>注意去掉一维的时候要将j从大到小遍历，因为f[j]更新的时候会使用f[j-v[i]]，如果从小到大更新那么f[j-v[i]]在使用之前就已经被更新过了，因此就会造成结果错误。
```cpp
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 1010;
int f[N];
int v[N],w[N];

int main()
{
    int n,m;
    cin>>n>>m;
    for(int i=1;i<=n;i++) cin>>v[i]>>w[i];
    for(int i=1;i<=n;i++){
        for(int j=m;j>=v[i];j--){
            f[j]=max(f[j],f[j-v[i]]+w[i]);
        }
    }
    cout<<f[m]<<endl;
    return 0;
}
```
### 完全背包
有 N 件物品和一个容量是 V 的背包。每件物品能使用无限次。
>与0-1背包的对比：
>
>0-1： f[i][j]=max(f[i-1][j],**f[i-1][j-v[i]]**+w[i])
完全：f[i][j]=max(f[i-1][j]**,f[i][j-v[i]]**+w[i])

二维
```cpp
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 1010;
int f[N][N];
int v[N],w[N];

int main()
{
    int n,m;
    cin>>n>>m;
    for(int i=1;i<=n;i++) cin>>v[i]>>w[i];
    for(int i=1;i<=n;i++)
        for(int j=0;j<=m;j++){
            f[i][j]=f[i-1][j];//先用f[i-1][j]来更新一遍
            if(j>=v[i]) f[i][j]=max(f[i][j],f[i][j-v[i]]+w[i]);//注意这里的状态转换公式
        }
    
    cout<<f[n][m]<<endl;
    return 0;
}
```
二维转换成一维

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 1010;
int f[N];
int v[N],w[N];

int main()
{
    int n,m;
    cin>>n>>m;
    for(int i=1;i<=n;i++) cin>>v[i]>>w[i];
    for(int i=1;i<=n;i++)
        for(int j=v[i];j<=m;j++){
        //f[i][j]=max(f[i][j],f[i][j-v[i]]+w[i]); 因为j是从小到大更新的，因此f[i][j-v[i]]是已经被更新过的，可以使用
            f[j]=max(f[j],f[j-v[i]]+w[i]);//注意这里的状态转换公式
        }
    
    cout<<f[m]<<endl;
    return 0;
}
```
**因此在一维表示的情况下，0-1背包和完全背包的代码的差别就在j是从小到大遍历还是从大到小遍历**

### 多重背包
有 N 种物品和一个容量是 V 的背包。
第 i 种物品最多有 si 件，每件体积是 vi，价值是 wi。

二维 且数据范围很小的时候，可以直接使用三重循环

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 110;
int f[N][N];
int v[N],w[N],s[N];

int main()
{
    int n,m;
    cin>>n>>m;
    for(int i=1;i<=n;i++) cin>>v[i]>>w[i]>>s[i];
    for(int i=1;i<=n;i++){
        for(int j=0;j<=m;j++){
            f[i][j]=f[i-1][j];
            for(int k=1;k<=s[i];k++){
                if(j>=k*v[i]){
                    f[i][j]=max(f[i][j],f[i-1][j-k*v[i]]+k*w[i]);
                }
            }
        }
    }
    cout<<f[n][m]<<endl;
    return 0;
}
```
二维变成一维

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 110;
int f[N];
int v[N],w[N],s[N];

int main()
{
    int n,m;
    cin>>n>>m;
    for(int i=1;i<=n;i++) cin>>v[i]>>w[i]>>s[i];
    for(int i=1;i<=n;i++){
        for(int j=m;j>=0;j--){
            for(int k=1;k<=s[i];k++){
                if(j>=k*v[i]){
                    f[j]=max(f[j],f[j-k*v[i]]+k*w[i]);
                }
            }
        }
    }
    cout<<f[m]<<endl;
    return 0;
}
```
### 多重背包的二进制优化
当数据范围达到10^3的时候，三重循环就会超时，可以考虑使用二进制优化。
>数据范围
0<N≤1000
0<V≤2000
0<vi,wi,si≤2000

先来考虑如何将多重背包转化成0-1背包：如果将体积为v，价值为w的s个物品拆开，不看成s个物品处理，而是看成s个1来处理，这样组成的物品就是0-1背包问题了。
但是这样1000 * 2000 * 2000复杂度是10^9，依然会超时，因此要考虑不拆成s个1来处理，而是使用2的倍数，可以证明在log(s)的级别拆出的数能够表示出小于等于s的所有数字。
>举个例子来说明：
>比如数字7：可以使用1 2 4的组合来表示出所有小于等于7的数字
>0 都不选
>1 选1
>2 选 2
>3 选 1 2
>4 选 4
>5 选 1 4
>6 选 2 4
>7 选 1 2 4

如果是不能恰好是2的幂次组合能够表示的数，比如10，那么最后如果剩下的数比2的幂次小就直接加进来，10的话可以直接拆成1 2 4 3，这样就能保证只能表示出小于等于10的数，因为最大也只是到10。
**全部拆完之后相当转换成了0-1背包问题**


一维滚动数组：
代码：

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 2010;
int f[N];
struct Goods{
    int v,w;
};

int main()
{
    vector<Goods> goods;
    int n,m;
    cin>>n>>m;
    for(int i=0;i<n;i++){
        int v,w,s;
        cin>>v>>w>>s;
        for(int j=1;j<=s;j*=2){
            s-=j;//每次减去j，最后的s剩余加进去即可
            goods.push_back({j*v,j*w});
        }
        if(s>0){
            goods.push_back({s*v,s*w});
        }
     
    }
    for(auto good:goods){
        for(int j=m;j>=good.v;j--){
            f[j]=max(f[j],f[j-good.v]+good.w);
        }
    }
    cout<<f[m]<<endl;
    return 0;
}
```
### 分组背包问题
分组背包问题是给定N组物品，每组物品中只能选择一种，放进容量是V的背包中，最大价值是多少。
那么分析可知**f[i,j]来表示从前i组物品中选，总价值不超过j的选法**。
那么相应的状态转移方程应该是从第i组物品中选哪一个，**f[i,j] = max(f[i-1,j],f[i-1,j-v[i,0]]+w[i,0],.....f[i-1.j-v[i,k]]+w[i,k])**

分组背包只能使用3重循环求解组数较小的情况，没有较好的优化方式。

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 110;
int f[N];
int v[N][N],w[N][N],s[N];

int main()
{
    int n,m;
    cin>>n>>m; 
    for(int i=1;i<=n;i++){
        cin>>s[i];
        for(int j=0;j<s[i];j++){
            cin>>v[i][j]>>w[i][j];
        }
    }
    
    for(int i=1;i<=n;i++)//枚举物品的所有组数
        for(int j=m;j>=0;j--)//枚举滚动数组
            for(int k=0;k<s[i];k++)//枚举第i组里面的所有物品
                if(j>=v[i][k])
                    f[j]=max(f[j],f[j-v[i][k]]+w[i][k]);
    
    cout<<f[m]<<endl;
    return 0;
                
}
```
