*此题很考验抽象建模能力和对栈的理解，是一道值得思考的难题*

###算法
1. 栈：如果我们只用第一个栈，那么输出字典序必定最小，但是我们有时必须使用第二个栈。这是怎样的情况呢？设 *i<j<k* ，有序列 *a[k]<a[i]<a[j]* ，由题意他们三者的入栈顺序为 *a[i]、a[j]、a[k]* ，出栈顺序为 *a[k]、a[i]、a[j]* ，所以必定是当a[i]和a[j]都已经入栈后a[k]才可能出栈，此时这个出栈队列显然是不可能实现的，此时a[k]与a[i]和a[j]要放到不同的栈。
1. 模型转化：于是该题就变成了—— **找出所有不能放在一起的a[i]和a[j]之后，如果有三个元素互相不能放在一起，则无解，否则把不能放在一起的两个元素放到不同的栈中就可以生成出方案了** 。我们将它转换为 **图论模型** ——若a[i]和a[j]不能放在一起，则在点i和点j间连边。如果生成出的图不是二分图，则无解，否则把二分图一边的节点进第一个栈，另一边的进第二个栈即可。
1. 二分图染色：如何判断一个图是否是二分图呢？我们可以用二分图DFS染色算法。染色按照1~N的顺序，由于我们要的是字典序最小的序列，所以如果i未被染色，那么我们就把它染色为1，然后开始访问所有能访问到的顶点。若由X染色Y,那么Y染为相反颜色。若Y已经被染色，且与X相同颜色，染色失败，无解。

###优化
建图的时候，如果暴力枚举i、j、k进行判断，时间复杂度为 *O(n^3 )* ，必定超时。所以要进行优化。我们可以记录下 *a[i~n]* 的最小值c[i]，如果对于每队i、j有 *a[i]<a[j]* 且 *a[i]>c[i+1]* ，则说明存在 *a[k]<a[i]<a[j]* ，即点i与点j间有边。这样时间复杂度为 *O(n^2 )* ，就不会超时了。

###字典序
为了保证最终答案是字典序最小的，我们该怎么做呢？

1. 这点已经提到过了。染色的时候优先把前面的节点染成1，后面的染成2，即可使前面的节点进入第一个栈，即必要时a出现在c之前。
1. 最后通过模拟求最终答案的时候，要严格遵循 *进第一个栈（a）* 、 *出第一个栈（b）* 、 *进第二个栈（c）* 、 *出第二个栈（d）* 的顺序，这样才能保证最终的结果字典序最小。

###标程

```cpp
#include <cstdio>
#include <cstdlib>
#include <climits>
#include <stack>
using namespace std;

inline int op(int c) {return c==1?2:1;}
inline int min(int a,int b) {return a<b?a:b;}
int n,a[1010],ans,i,j,k,c[1010],col[1010],p;
bool g[1010][1010];
char way[2010];
stack <int> s1,s2;

void dfs(int x,int c)
{
    int i;
    col[x]=c;
    for (i=1;i<=n;i++)
        if (g[x][i]&&x!=i)
        {
            if (col[i]==c)
            {
                printf("0\n");
                exit(0);
            }
            if (!col[i]) dfs(i,op(c));
        }
}

int main()
{
    scanf("%d",&n);
    for (i=1;i<=n;i++) scanf("%d",&a[i]);
    c[n+1]=INT_MAX;
    for (i=n;i>=1;i--) c[i]=min(c[i+1],a[i]);
    for (j=2;j<n;j++)
        for (i=1;i<j;i++)
            if (c[j+1]<a[i]&&a[i]<a[j])
                g[i][j]=g[j][i]=true;
    for (i=1;i<=n;i++)
        if (!col[i]) dfs(i,1);
    for (i=1,p=1;i<=n;i++)
    {
        if (col[i]==1) 
        {
            s1.push(a[i]);
            way[++ans]='a';
        }
        while (!s1.empty()&&s1.top()==p)
            {s1.pop(); p++; way[++ans]='b';}
        if (col[i]==2)
        {
            s2.push(a[i]);
            way[++ans]='c';
        }
        while (!s2.empty()&&s2.top()==p)
            {s2.pop(); p++; way[++ans]='d';}
    }
    while (p<=n)
    {
        if (!s1.empty()&&s1.top()==p)
            {s1.pop(); p++; way[++ans]='b';}
        else if (!s2.empty()&&s2.top()==p)
            {s2.pop(); p++; way[++ans]='d';}
    }
    for (i=1;i<ans;i++) printf("%c ",way[i]);
    printf("%c\n",way[ans]);
    return 0;
}

```
