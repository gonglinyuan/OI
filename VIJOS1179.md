*这是一道经典的背包+DFS的混合题，十分考验技巧*

###背包问题
我们先解决这个小问题。如果已经有了贴邮票的方案，那么如何判断它的MAX呢？这显然是一个二维的完全背包问题：

- 二维：第一维是贴的邮票的张数。每个贴邮票的位置都可以看作背包的一种“容量”，每张邮票都会占用一个位置。第二维就是邮票的金额了，这很好理解。所以状态就可以表示为 *f[i][j][k]* ，即到了价值为i的邮票，贴了j张邮票，能否达到k的金额。众所周知，第一维是可以优化掉的。
- 完全背包：因为每种邮票的张数不限，所以这是一个完全背包问题。至于贴邮票总张数的限制，已经在“二维”中解决了。所以状态转移方程也很简单： *f[j][k]=f[j][k] or f[j-1][k-i]* 。

###DFS
最朴素的想法：枚举出每种选择邮票种类的方案，然后做背包，判断最大值并输出方案。
这显然是效率极低的，我们必须优化它。

- 观察这个DFS，它显然是没有终止条件的，你可以枚举任意大小的邮票面值，但其实很多都是无效的，这就导致了DFS无法进行。那我们应该如何确定枚举上界呢？经观察不难发现，假设前几种面值的邮票可以表示的最大面值为MAX，那么这张邮票的面值最大是MAX+1。因为如果这张邮票的面值为MAX+2，那么MAX+1的价值就取不到了。所以枚举上界设为MAX+1为止。
- 按照上面一条，我们必须在DFS的过程中动态地求出MAX，也就是说我们要在DFS的过程中动态地计算背包。好在这并不复杂，只要注意保存状态，就很容易实现。

###标程

```cpp
#include <cstdio>
#include <cstring>

const int MAX=500,MAXN=15,MAXK=15;
int N,K,i,a[MAXK+1],ans[MAXK+1],max;
bool f[MAXN+1][MAX+1],stk[MAXK][MAXN+1][MAX+1];

void dfs(int depth)
{
    int r,i,j,k;
    for (r=0;r<=MAX;r++)
            if (!f[N][r])
                break;
    if (depth==K)
    {
        if (r-1>max)
        {
            max=r-1;
            memcpy(ans,a,sizeof(ans));
        }
        return;
    }
    for (i=(depth?(a[depth-1]+1):1);i<=r;i++)
    {
        memcpy(stk[depth],f,sizeof(stk[depth]));
        a[depth]=i;
        for (j=1;j<=N;j++)
            for (k=i;k<=MAX;k++)
                f[j][k]=f[j][k]||f[j-1][k-i];
        dfs(depth+1);
        memcpy(f,stk[depth],sizeof(f));
    }
}

int main()
{
    scanf("%d%d",&N,&K);
    for (i=0;i<=N;i++) f[i][0]=true;
    dfs(0);
    for (i=0;i<K-1;i++) printf("%d ",ans[i]);
    printf("%d\n",ans[K-1]);
    printf("MAX=%d\n",max);
    return 0;
}

```

###小问题
VIJOS上的数据有点问题，它要求输出字典序靠后的方案，而不是靠前的。所以要将16行的 *r-1>max* 修改为 *r-1>=max* 。才能在VIJOS上AC（否则只能过9个点，第六个过不了）。
